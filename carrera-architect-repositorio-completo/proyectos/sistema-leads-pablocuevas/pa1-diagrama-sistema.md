# DIAGRAMA DE SISTEMA — Sistema de Gestión de Leads
## pablocuevas.it — Proyecto Ancla 1

**Versión**: 4.2  
**Fecha**: 27 de marzo 2026  
**Estado**: ✅ Diseño validado — pendiente implementación Bloques 5-8  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Supersede**: diagrama-sistema.md v4.1

---

## VISIÓN GENERAL DEL SISTEMA

```
[Formulario pablocuevas.it]
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 1 — SEGURIDAD                                 │
│  Validación de firma HMAC del webhook               │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 2 — PERSISTENCIA INICIAL                      │
│  Deduplicación con ventana de tiempo (24h)          │
│  CTE atómica — devuelve es_nuevo + timestamp_creacion│
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 3 — PROCESAMIENTO                             │
│  Abstract API → Clasificación Nivel 1 / Nivel 2     │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 4 — NOTIFICACIÓN                              │
│  Telegram (Pablo) — extensible a email al lead      │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 5 — REGISTRO DE EVENTOS                       │
│  Trazabilidad completa en PostgreSQL                │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 6 — MANTENIMIENTO Y OBSERVABILIDAD            │
│  Reportes + Retención GDPR + Health Check + Backup  │
└─────────────────────────────────────────────────────┘
```

**Principio rector:** Un lead que falla en cualquier capa queda 
registrado. Nunca se pierde en el vacío.

---

## CAPA 1 — SEGURIDAD

**Propósito:** Garantizar que solo peticiones legítimas del 
formulario entren al sistema.

**Implementación:** Header `X-Webhook-Signature` con firma HMAC.
El formulario firma cada request con un secreto compartido.
N8N verifica la firma antes de procesar cualquier dato.

```
[Webhook recibe request]
    → [Verifica header X-Webhook-Signature con HMAC]
        → (firma inválida) → [Registrar intento rechazado con IP y timestamp] → STOP
        → (firma válida)   → continúa a Capa 2
```

**Datos que llegan al webhook (todos los campos):**

| Campo | Tipo | ¿Es PII? | Notas |
|-------|------|----------|-------|
| nome | texto | ✅ SÍ | PII directo |
| cognome | texto | ✅ SÍ | PII directo |
| email | texto | ✅ SÍ | PII directo — identificador principal |
| azienda | texto | ❌ NO | Dato de empresa |
| servizio | texto | ❌ NO | Tipo de servicio solicitado |
| messaggio | texto | ✅ SÍ | Puede contener datos personales |
| timestamp_roma | fecha/hora | ❌ NO | Generado por el sistema |

**Credenciales involucradas:**
- Secreto HMAC — vault de N8N (nunca en el workflow)

**Estado actual:** ⚠️ Pendiente — se implementa en Bloque 7

---

## CAPA 2 — PERSISTENCIA INICIAL (Deduplicación)

**Propósito:** Registrar el lead antes de cualquier procesamiento 
y detectar duplicados con criterio de negocio real.

### Flujo completo de deduplicación

```
[PostgreSQL — CTE atómica]
    INSERT INTO leads (...) ON CONFLICT (email, servizio) DO NOTHING
    Devuelve siempre 1 fila: es_nuevo (bool) + timestamp_creacion

    → es_nuevo = TRUE  → lead nuevo → INSERT exitoso → continúa a Capa 3

    → es_nuevo = FALSE → registro ya existe → evalúa timestamp_creacion

        → timestamp_creacion < 24 horas
            → duplicado real (doble click o reenvío accidental)
            → [Registrar evento: duplicado_detectado] → STOP

        → timestamp_creacion ≥ 24 horas
            → cliente legítimo que vuelve a contactar
            → [UPDATE leads: nuevo messaggio + timestamp_actualizacion]
            → [Registrar evento: lead_reactivado] → continúa a Capa 3
```

### Por qué CTE y no dos queries separadas

El ON CONFLICT DO NOTHING resuelve la race condition del doble click
de forma atómica — sin ventana de tiempo para concurrencia.

Sin CTE, si el INSERT devuelve 0 filas N8N recibe 0 items y el flujo
se detiene silenciosamente. La CTE garantiza que N8N siempre recibe
exactamente 1 fila con toda la información necesaria para rutear,
pase lo que pase en el INSERT.

La ventana de 24 horas resuelve el caso de negocio real — el cliente
que vuelve un mes después con el mismo email y servicio es un lead
nuevo legítimo, no un duplicado técnico.

Sin la ventana de tiempo, el UNIQUE CONSTRAINT bloquea silenciosamente
a ese cliente para siempre — Pablo no lo ve en Telegram, el cliente
no recibe respuesta. Se pierde un lead real.

### Política de deduplicación — decisión de negocio

| Caso | Tiempo | Acción | Notificación |
|------|--------|--------|--------------|
| Mismo email + servizio, < 24h | Duplicado real | Registrar y parar | Ninguna |
| Mismo email + servizio, ≥ 24h | Lead legítimo | Actualizar y procesar | Telegram normal |
| Mismo email + servizio diferente | Lead nuevo | INSERT nuevo registro | Telegram normal |

**Decisión documentada:** 24 horas como ventana cubre el doble click
y los reenvíos del mismo día. Cualquier contacto posterior se trata
como nuevo. Revisable cuando haya evidencia de que el mercado
necesita una ventana diferente.

### Momento del guardado

El lead se guarda con estado `pendiente` ANTES de llamar a Abstract,
antes de Telegram, antes de cualquier procesamiento externo.
Si todo lo demás falla, el lead ya existe en la base de datos.

### Manejo del resultado en N8N

```
[IF: es_nuevo === true]
    → TRUE  → [Registrar evento: lead_recibido] → continúa a Capa 3
    → FALSE → [Code node: calcula horas desde timestamp_creacion]
                  → < 24h  → [Registrar evento: duplicado_detectado] → STOP
                  → ≥ 24h  → [UPDATE leads: messaggio + timestamp_actualizacion]
                              → [Registrar evento: lead_reactivado] → continúa a Capa 3
```

**Estado actual:** ⚠️ Pendiente — se implementa en Bloque 5

---

## CAPA 3 — PROCESAMIENTO

**Propósito:** Formatear, enriquecer y clasificar el lead.

### Paso 1 — procesador-body
```
→ Normaliza email (lowercase, trim)
→ Valida campos obligatorios
→ Añade timestamp de procesamiento
```

### Paso 2 — HTTP Abstract (con resiliencia)
```
[HTTP Request a Abstract Email Reputation API]
    Configuración: On Error = Continue, Retry on Fail = 3 intentos con backoff
    
    → (respuesta válida)       → continúa con datos de Abstract
    → (error HTTP 500/timeout) → [Registrar evento: abstract_error] → pasa a Nivel 2
```

**Campos que usa el sistema de Abstract:**

| Campo Abstract | Tipo | ¿Es PII? | Uso |
|---------------|------|----------|-----|
| abstract_status | texto | ❌ NO | deliverable / undeliverable |
| abstract_smtp_valid | bool | ❌ NO | Validez SMTP |
| abstract_live_site | bool | ❌ NO | Dominio activo |
| esEmailLibre | bool | ❌ NO | Gmail, Hotmail, etc. |
| esDesechable | bool | ❌ NO | Mailinator, Guerrilla, etc. |
| nivelRiesgo | texto | ❌ NO | low / medium / high |

**Nota GDPR:** Campos con PII de Abstract (`first_name`, `last_name`, 
`email_domain`) se descartan — minimización aplicada. Solo se 
conservan propiedades técnicas del email, no datos de la persona.

**Credenciales involucradas:**
- Abstract API Key — vault de N8N

### Paso 3 — enriquecedor-clasificador

```
IF (abstract_status === 'deliverable' 
    AND abstract_smtp_valid === true 
    AND abstract_live_site !== null)

    → SÍ: NIVEL 1 — Abstract tiene confianza suficiente
    → NO: NIVEL 2 — Reglas propias del sistema
```

---

### NIVEL 1 — Switch por accion_recomendada

```
[Switch: accion_recomendada]

    → procesar
        Condición: mail corporativo, confianza alta
        Prioridad asignada: alta
        → Capa 4 (notificación ✅)
        → Capa 5 (registro evento)

    → revisar_manualmente
        Condición: mail personal válido
        Prioridad asignada: media
        → Capa 4 (notificación 🔎)
        → Capa 5 (registro evento)

    → descartar
        Condición: mail desechable o riesgo alto
        → NO notifica (sin Telegram)
        → Capa 5 (registro evento con razón)
```

---

### NIVEL 2 — IF por reglas propias

```
IF (esDesechable === true OR nivelRiesgo === 'high')
    → descartar
        → NO notifica
        → Capa 5 (registro evento con razón y nivel_resolucion: 'nivel2')

ELSE (resto de casos — Abstract no pudo verificar)
    → revisar_manualmente
        → Capa 4 (notificación ⚠️ nivel 2)
        → Capa 5 (registro evento con nivel_resolucion: 'nivel2')
```

**Principio de diseño:** El Nivel 2 nunca procesa automáticamente.
Un email que Abstract no puede verificar merece revisión humana.

---

## CAPA 4 — NOTIFICACIÓN

**Propósito:** Informar en tiempo real sobre leads que requieren atención.  
**Solo notifica:** procesar y revisar_manualmente. Los descartados NO generan notificación.

**Credenciales involucradas:**
- Telegram Bot Token — vault de N8N
- Telegram Chat ID — vault de N8N

### Mensajes por tipo

**Nivel 1 — procesar:**
```
✅ NUEVO LEAD — PRIORIDAD ALTA
Nome: {nome} {cognome}
Email: {email}
Azienda: {azienda}
Servizio: {servizio}
Messaggio: {messaggio}
Hora: {timestamp_roma}
---
Acción: procesar
Razón: mail corporativo verificado
Prioridad: alta
```

**Nivel 1 — revisar_manualmente:**
```
🔎 LEAD PARA REVISIÓN MANUAL
Nome: {nome} {cognome}
Email: {email}
Azienda: {azienda}
Servizio: {servizio}
Messaggio: {messaggio}
Hora: {timestamp_roma}
---
Acción: revisar manualmente
Razón: mail personal válido
Prioridad: media
```

**Nivel 2 — revisar_manualmente:**
```
⚠️ LEAD NIVEL 2 — REQUIERE REVISIÓN
Nome: {nome} {cognome}
Email: {email}
Azienda: {azienda}
Servizio: {servizio}
Messaggio: {messaggio}
Hora: {timestamp_roma}
---
Nivel de resolución: nivel 2
Razón de escalada: Abstract no pudo verificar
Acción sugerida: verificar por otros medios
```

**Lead reactivado (cliente que vuelve ≥ 24h):**
```
🔄 LEAD REACTIVADO
Nome: {nome} {cognome}
Email: {email}
Azienda: {azienda}
Servizio: {servizio}
Messaggio: {messaggio}
Hora: {timestamp_roma}
---
Nota: este contacto ya existía en el sistema.
Ha vuelto a contactar con el mismo servicio.
Primer contacto: {timestamp_creacion_original}
```

**Manejo de fallo de Telegram:**
```
→ (Telegram falla) → [Actualizar estado lead en PostgreSQL: 'notificacion_pendiente']
→ (Health check detecta pendientes) → reintento en siguiente ciclo
```

**Extensión futura — respuesta automática al lead:**
La Capa 4 es el punto de extensión natural. Añadir un nodo de 
email (SendGrid o SMTP) en paralelo al nodo de Telegram permite:
- Confirmar recepción al lead automáticamente
- Personalizar el mensaje según clasificación y servicio solicitado
- Todos los datos necesarios ya están disponibles en este punto

**Estado de la extensión:** 🔮 Planificada — Fase 2 o versión 2.0 del producto

---

## CAPA 5 — REGISTRO DE EVENTOS

**Propósito:** Trazabilidad completa. Saber exactamente qué hizo 
el sistema con cada lead, en qué momento y por qué.

**Eventos registrados en tabla eventos:**

| Evento | Cuándo se registra | Campos clave en detalle (JSONB) |
|--------|-------------------|--------------------------------|
| `lead_recibido` | Capa 2 — es_nuevo=true | `{"origen": "webhook", "es_nuevo": true}` |
| `duplicado_detectado` | Capa 2 — es_nuevo=false, < 24h | `{"email": "x", "servizio": "y", "horas_desde_creacion": 0.08}` |
| `lead_reactivado` | Capa 2 — es_nuevo=false, ≥ 24h | `{"timestamp_original": "2026-03-01T10:00:00Z", "dias_desde_creacion": 26}` |
| `abstract_consultado` | Capa 3 — respuesta de Abstract | `{"status": "deliverable", "smtp_valid": true, "live_site": true, "es_libre": false, "es_desechable": false, "nivel_riesgo": "low"}` |
| `abstract_error` | Capa 3 — Abstract falla | `{"codigo_error": "500", "mensaje": "timeout", "accion_tomada": "nivel2"}` |
| `clasificacion_realizada` | Capa 3 — decisión tomada | `{"nivel": "nivel1", "accion": "procesar", "razon": "mail corporativo verificado"}` |
| `notificacion_enviada` | Capa 4 — Telegram OK | `{"canal": "telegram", "tipo": "procesar"}` |
| `notificacion_pendiente` | Capa 4 — Telegram falla | `{"canal": "telegram", "error": "timeout", "reintento": true}` |
| `lead_descartado` | Capa 3 — rama descartar | `{"razon": "email desechable", "nivel": "nivel1"}` |

**Distinción duplicado_detectado vs lead_reactivado:**
Ambos casos ocurren cuando `es_nuevo = FALSE` — el registro ya existía
en la DB. El tipo de evento clasifica la intención del sistema.
El campo `horas_desde_creacion` en el JSONB da el contexto exacto:
un valor de `0.08` indica doble click accidental ocurrido hace minutos,
un valor de `600` indica un cliente que volvió semanas después.
Todo segundo intento queda registrado — ningún contacto se pierde
en el vacío, incluso los duplicados.

**Deuda técnica:** el nodo de descarte actual solo tiene un Edit Fields 
básico. Pendiente enriquecerlo con `razon` y `nivel_resolucion` 
antes de conectar PostgreSQL. — Bloque 5

---

## CAPA 6 — MANTENIMIENTO Y OBSERVABILIDAD

### Reporte semanal
```
[Schedule: lunes 8:00am hora Roma]
→ [Query PostgreSQL: resumen de la semana]
→ [Code node: formatea reporte]
→ [Telegram: entrega reporte a Pablo]
```

**Contenido del reporte:**
- Total leads recibidos en la semana
- Leads procesados (prioridad alta y media)
- Leads en revisión manual pendientes
- Leads descartados — razón más frecuente
- Leads escalados a Nivel 2
- Leads duplicados bloqueados
- Leads reactivados (clientes que volvieron)

**Métricas futuras (cuando haya volumen suficiente):**
- Porcentaje de leads por servicio solicitado
- Horarios pico de llegada
- Tasa de conversión por nivel de prioridad
- Tiempo promedio de respuesta a leads en revisión manual

---

### Limpieza automática (retención GDPR)
```
[Schedule: primer día de cada mes]
→ DELETE leads descartados con más de 60 días
→ DELETE leads en revisión con más de 6 meses
→ DELETE leads procesados con más de 12 meses
→ [Registrar: N registros eliminados en log del sistema]
```

**Política de retención documentada:**

| Estado | Retención | Justificación de negocio |
|--------|-----------|--------------------------|
| descartado | 60 días | Auditoría del clasificador |
| revisar_manualmente | 6 meses | Seguimiento comercial activo |
| procesado | 12 meses | Leads con posibilidad de conversión |

**Política de logs de N8N:**
Los execution logs contienen PII (nombre, email, mensaje del lead).
Retención máxima de logs: 30 días.
Configuración: variable de entorno `EXECUTIONS_DATA_MAX_AGE=30`
en el docker-compose del servidor de producción — se configura en Bloque 6.

---

### Health Check
```
[Schedule: cada 5 minutos]
→ [Verifica que N8N responde (endpoint /healthz)]
→ [Verifica que PostgreSQL responde (query SELECT 1)]
→ (todo OK)    → sin acción
→ (fallo N8N)  → [Telegram: alerta crítica — N8N no responde]
→ (fallo DB)   → [Telegram: alerta crítica — PostgreSQL no responde]
```

---

### Backup automático
```
[Schedule: diario a las 3:00am]
→ [pg_dump de PostgreSQL → archivo .sql.gz cifrado]
→ [Export de workflows de N8N vía API]
→ [Transferencia a almacenamiento externo (rsync o S3)]
→ [Verificar integridad del backup]
→ [Registrar resultado en log del sistema]
```

**Runbook de restauración:** documento separado `runbook.md`
que describe paso a paso cómo restaurar desde backup.
Sin runbook, el backup no sirve.

**Estado actual:** ⚠️ Pendiente — se implementa en Bloque 6

---

## TABLA DE DEUDA TÉCNICA

| Deuda | Bloque | Riesgo si no se resuelve |
|-------|--------|--------------------------|
| Webhook sin firma HMAC | 7 | Bots saturan el sistema y agotan créditos de Abstract |
| Sin deduplicación real | 5 | Leads duplicados en DB y dobles notificaciones |
| Sin ventana de tiempo en deduplicación | 5 | Clientes legítimos bloqueados permanentemente |
| Nodo descarte sin enriquecer | 5 | Sin trazabilidad de razón y nivel en descartados |
| Sin retención automática | 5 | Acumulación de PII — riesgo GDPR |
| Sin logs de N8N con límite | 6 | PII acumulada en logs indefinidamente |
| Sin health check | 8 | Fallos silenciosos sin alerta |
| Sin backup automático | 6 | Pérdida total de datos ante fallo del servidor |

---

## DECISIONES DE DISEÑO

**Abstract como fuente de datos, no tomador de decisiones:**
Abstract analiza propiedades del email y las devuelve. El sistema 
decide qué hacer con esas propiedades. Si Abstract se reemplaza 
por otra API, la lógica de negocio no cambia — solo el nodo HTTP.

**CTE atómica para la persistencia inicial:**
El ON CONFLICT DO NOTHING resuelve race conditions ante inserts
simultáneos. Implementado como CTE que siempre devuelve 1 fila —
`es_nuevo=TRUE` si el INSERT tuvo éxito, `es_nuevo=FALSE` si hubo
conflicto. N8N nunca recibe 0 items, nunca detiene el flujo
silenciosamente. Convierte una excepción técnica en un dato limpio.

**Ventana de tiempo de 24 horas para deduplicación:**
El doble click y el cliente que vuelve un mes después son casos
de negocio distintos aunque lleguen por el mismo canal técnico.
El UNIQUE CONSTRAINT resuelve la concurrencia instantánea.
La ventana de 24 horas resuelve el caso de negocio real.
Sin esta distinción, el sistema bloquea silenciosamente a clientes
legítimos sin que Pablo lo sepa ni el cliente reciba respuesta.
Identificado por Pablo — las tres IAs de la triangulación lo habían pasado por alto.

**Todo segundo intento queda registrado:**
Incluso los duplicados generan un evento `duplicado_detectado` en la
tabla eventos. El sistema nunca descarta silenciosamente. El campo
`horas_desde_creacion` en el JSONB permite distinguir en retrospectiva
un doble click accidental de un intento de recontacto legítimo.

**Guardado antes de procesar:**
La tabla leads recibe el registro con estado `pendiente` antes de 
cualquier llamada externa. Si Abstract falla, si Telegram falla, 
si hay un error de red — el lead ya existe y no se pierde.

**Nivel 2 nunca procesa automáticamente:**
Un email que Abstract no puede verificar merece revisión humana.
Automatizar sin confianza suficiente genera falsos positivos que 
dañan la relación con el cliente.

**Firestore como receptor, PostgreSQL como memoria:**
Firestore recibe el lead desde el formulario — primera red de seguridad.
PostgreSQL es la memoria del sistema — historial, deduplicación, 
reportes, retención. Son capas complementarias, no redundantes.

**Minimización GDPR en Abstract:**
Se descartan campos PII que Abstract devuelve (`first_name`, 
`last_name`) porque no tienen propósito en el flujo de clasificación.
Solo se conservan propiedades técnicas del email.

**La triangulación es fuerte en técnico, débil en negocio:**
Las IAs convergen rápido en soluciones técnicas correctas.
Las preguntas de negocio — cómo se comportan clientes reales —
tienen que venir del arquitecto. La experiencia de mercado
no se puede triangular entre modelos de lenguaje.

---

## DOCUMENTOS RELACIONADOS

| Documento | Contenido |
|-----------|-----------|
| `schema-postgresql-draft.md` | Tablas, columnas, tipos de dato, PII, retención, query CTE |
| `gdpr-data-mapping.md` | Inventario completo de PII del sistema |
| `adr/ADR-201.md` | Decisión Firebase → N8N trigger |
| `adr/ADR-004.md` | Checklist de 6 capas arquitectónicas |
| `adr/ADR-005.md` | Workflow Audit Template |
| `runbook.md` | Procedimientos de recuperación ante fallos (pendiente) |

---

**Documento creado**: 25 de marzo 2026  
**Última revisión**: 27 de marzo 2026  
**Autor**: Pablo Cuevas  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek — triangulación completa  
**Próxima revisión**: Al completar Bloque 5 (PostgreSQL implementado)
