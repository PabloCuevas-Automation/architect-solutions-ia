# DIAGRAMA DE SISTEMA — Sistema de Gestión de Leads
## pablocuevas.it — Proyecto Ancla 1

**Versión**: 4.3  
**Fecha**: 28 de abril 2026  
**Estado**: 🔄 En construcción — Capas 1-3 implementadas, Capas 4-6 pendientes  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Supersede**: pa1-diagrama-sistema.md v4.2

---

## VISIÓN GENERAL DEL SISTEMA

```
[Formulario pablocuevas.it]
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 1 — SEGURIDAD                                 │
│  Validación de firma HMAC del webhook               │
│  Estado: ⚠️ Placeholder — se implementa en Bloque 7 │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 2 — PERSISTENCIA INICIAL                      │
│  Deduplicación con ventana de tiempo (24h)          │
│  CTE atómica — devuelve es_nuevo + timestamp_creacion│
│  Estado: ✅ Implementado — Sesiones 3-6             │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 3 — PROCESAMIENTO + REGISTRO DE EVENTOS       │
│  Abstract API → Clasificación Nivel 1 / Nivel 2     │
│  Registro inline de eventos a lo largo del flujo    │
│  Estado: ✅ Implementado — Sesiones 4-7             │
└─────────────────────────────────────────────────────┘
         ↓
┌─────────────────────────────────────────────────────┐
│  CAPA 4 — NOTIFICACIÓN                              │
│  Telegram (Pablo) — extensible a email al lead      │
│  Estado: ✅ Implementado — Sesiones 4-5             │
└─────────────────────────────────────────────────────┘
┌─────────────────────────────────────────────────────┐
│  CAPA 5 — MANTENIMIENTO Y OBSERVABILIDAD            │
│  Reportes + Retención GDPR + Health Check + Backup  │
│  Estado: ⚠️ Pendiente — se implementa en Bloque 6+  │
└─────────────────────────────────────────────────────┘
```

**Principio rector:** Un lead que falla en cualquier capa queda
registrado. Nunca se pierde en el vacío.

---

## FLUJO REAL DEL WORKFLOW EN N8N (estado al 28/04/2026)

```
Webhook1
  → capa1-validacion-hmac           (placeholder HMAC — Bloque 7)
  → capa2-persistencia-upsert       (CTE atómica → tabla leads)
  → capa2-merge-datos               (fusiona webhook + resultado PostgreSQL)
  → capa2-if-es-nuevo
      → TRUE  → capa2-registrar-lead-recibido        (PostgreSQL → eventos)
                → capa2-restaurar-datos-recibido      ($('capa2-merge-datos').first())
                → procesador-body1                    (normaliza email, añade timestamp)
                → HTTP Abstract (email)
                    → Success
                        → capa3-registrar-abstract-consultado  (PostgreSQL → eventos)
                        → capa3-restaurar-datos-abstract       ($('HTTP Abstract (email)').first())
                        → enriquecedor-clasificador            (Code node — lógica nivel1/nivel2)
                        → capa3-registrar-clasificacion        (PostgreSQL → eventos)
                        → capa3-restaurar-datos-clasificacion  ($('enriquecedor-clasificador').first())
                        → IF (nivel1 vs nivel2)
                            → nivel1 → Switch (accion_recomendada)
                                          → procesar          → notificador-procesar-nivel1 → Respond to Webhook1
                                          → revisar           → notificador-revisar-nivel1
                                          → descartar         → descartado (manual)
                            → nivel2 → nivel2-reglas-propias (Code node)
                                     → IF1
                                          → descartar         → descartado1 (manual)
                                          → revisar           → notificador-revisar-nivel2
                    → Error
                        → capa3-registrar-abstract-error       (PostgreSQL → eventos) → fin

      → FALSE → capa2-logica-duplicado              (calcula horas_desde_creacion + dias_desde_creacion)
              → capa2-if-reactivado
                  → TRUE  → capa2-registrar-lead-reactivado    (PostgreSQL → eventos)
                             → capa2-restaurar-datos-reactivado ($('capa2-logica-duplicado').first())
                             → procesador-body1                 (mismo flujo desde HTTP Abstract)
                  → FALSE → capa2-registrar-duplicado          (PostgreSQL → eventos) → fin
```

**Nota — UPDATE leads en reactivación:** el diagrama de diseño contempla
un `UPDATE leads` (nuevo messaggio + timestamp_actualizacion) antes del
registro del evento `lead_reactivado`. Este nodo no está construido aún.
Es deuda técnica documentada — se implementa en Bloque 5.

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

**Datos que llegan al webhook:**

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

**Estado actual:** ✅ Implementado — Sesiones 3-6

### Flujo de deduplicación

```
[PostgreSQL — CTE atómica]
    INSERT INTO leads (...) ON CONFLICT (email, servizio) DO NOTHING
    Devuelve siempre 1 fila: es_nuevo (bool) + timestamp_creacion

    → es_nuevo = TRUE  → lead nuevo → continúa
                         [Evento: lead_recibido]

    → es_nuevo = FALSE → registro ya existe → evalúa timestamp_creacion

        → timestamp_creacion < 24 horas
            → duplicado real (doble click o reenvío accidental)
            → [Evento: duplicado_detectado] → STOP

        → timestamp_creacion ≥ 24 horas
            → cliente legítimo que vuelve a contactar
            → ⚠️ [UPDATE leads: messaggio + timestamp_actualizacion] — PENDIENTE Bloque 5
            → [Evento: lead_reactivado] → continúa
```

### Por qué CTE y no dos queries separadas

El `ON CONFLICT DO NOTHING` resuelve la race condition del doble click
de forma atómica. Sin CTE, si el INSERT devuelve 0 filas N8N recibe
0 items y el flujo se detiene silenciosamente. La CTE garantiza que
N8N siempre recibe exactamente 1 fila con toda la información necesaria
para rutear, pase lo que pase en el INSERT.

### Por qué el Code node de restauración es necesario

Los nodos PostgreSQL con INSERT sin RETURNING devuelven `{"success": true}`
y reemplazan el item del flujo. Todo lo que viene después recibe solo ese
objeto. El patrón `return [$('nombre-nodo').first()]` recupera el item
completo de cualquier nodo anterior — decisión arquitectónica consciente
de qué datos salen de cada punto del flujo.

### Política de deduplicación

| Caso | Tiempo | Acción | Notificación |
|------|--------|--------|--------------|
| Mismo email + servizio, < 24h | Duplicado real | Registrar y parar | Ninguna |
| Mismo email + servizio, ≥ 24h | Lead legítimo | Actualizar y procesar | Telegram normal |
| Mismo email + servizio diferente | Lead nuevo | INSERT nuevo registro | Telegram normal |

### Momento del guardado

El lead se guarda con estado `pendiente` ANTES de llamar a Abstract,
antes de Telegram, antes de cualquier procesamiento externo.
Si todo lo demás falla, el lead ya existe en la base de datos.
Este es el principio **write-before-processing** — resiliencia por diseño.

---

## CAPA 3 — PROCESAMIENTO

**Propósito:** Formatear, enriquecer y clasificar el lead.

**Estado actual:** ✅ Implementado — Sesiones 4-7

### Paso 1 — procesador-body1

```
→ Normaliza email (lowercase, trim)
→ Valida campos obligatorios
→ Añade timestamp de procesamiento
```

### Paso 2 — HTTP Abstract (email)

```
[HTTP Request a Abstract Email Reputation API]
Configuración: On Error = Continue (using error output)

    → (Success) → continúa con datos de Abstract
    → (Error)   → [Evento: abstract_error] → fin
```

**Campos que extrae el sistema de Abstract (minimización GDPR aplicada):**

| Campo Abstract original | Campo usado internamente | ¿Es PII? | Propósito |
|------------------------|--------------------------|----------|-----------|
| `email_deliverability.status` | `abstract_status` | ❌ NO | deliverable / undeliverable |
| `email_deliverability.is_smtp_valid` | `abstract_smtp_valid` | ❌ NO | Validez SMTP |
| `email_domain.is_live_site` | `abstract_live_site` | ❌ NO | Dominio activo — decide nivel1/nivel2 |
| `email_quality.is_free_email` | `esEmailLibre` | ❌ NO | Gmail, Hotmail, etc. |
| `email_quality.is_disposable` | `esDesechable` | ❌ NO | Mailinator, Guerrilla, etc. |
| `email_risk.address_risk_status` | `nivelRiesgo` | ❌ NO | low / medium / high |

**Campos descartados por minimización GDPR:**
`first_name`, `last_name`, `organization_name` y todos los campos de
`email_domain` sin propósito operativo (registrar, fechas, etc.).

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

### NIVEL 1 — Switch por accion_recomendada

```
[Switch: accion_recomendada]

    → procesar
        Condición: mail corporativo, confianza alta
        Prioridad asignada: alta
        → notificador-procesar-nivel1 (Telegram ✅)

    → revisar_manualmente
        Condición: mail personal válido
        Prioridad asignada: media
        → notificador-revisar-nivel1 (Telegram 🔎)

    → descartar
        Condición: mail desechable o riesgo alto
        → descartado (sin Telegram)
```

### NIVEL 2 — IF por reglas propias

```
[nivel2-reglas-propias — Code node]

IF (esDesechable === true OR nivelRiesgo === 'high')
    → descartar
        → descartado1 (sin Telegram)

ELSE
    → revisar_manualmente
        → notificador-revisar-nivel2 (Telegram ⚠️)
```

**Principio de diseño:** El Nivel 2 nunca procesa automáticamente.
Un email que Abstract no puede verificar merece revisión humana.
Si `abstract_live_site === null` el lead llega a Nivel 2, y ningún
lead de Nivel 2 puede cumplir la condición `procesar` — hacer
procesamiento automático en Nivel 2 sería arquitectónicamente incoherente.

---

## CAPA 4 — NOTIFICACIÓN

**Propósito:** Informar en tiempo real sobre leads que requieren atención.
**Solo notifica:** `procesar` y `revisar_manualmente`. Los descartados NO generan notificación.

**Estado actual:** ✅ Implementado — Sesiones 4-5

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

**Deuda técnica — footer promocional de N8N:**
Los mensajes de Telegram incluyen un footer de N8N (plan gratuito).
Se elimina al pasar a VPS con licencia propia en Bloque 6.

**Extensión futura — respuesta automática al lead:**
Añadir un nodo de email (SendGrid o SMTP) en paralelo al nodo de
Telegram. Todos los datos necesarios ya están disponibles en este punto.
Estado: 🔮 Planificada — Fase 2

---

## REGISTRO DE EVENTOS (tabla eventos en PostgreSQL)

**Propósito:** Trazabilidad completa. Saber exactamente qué hizo
el sistema con cada lead, en qué momento y por qué.

**Nota arquitectónica:** Los eventos no se registran en una capa
separada al final del flujo — se registran inline en el momento
exacto en que ocurre cada hecho. Capa 2 registra lo que pasa en
la deduplicación. Capa 3 registra lo que pasa en el procesamiento.

**Eventos implementados (estado al 28/04/2026):**

| Evento | Cuándo | Campos JSONB reales |
|--------|--------|---------------------|
| `lead_recibido` | Capa 2 — es_nuevo=true | `{"origen": "webhook", "es_nuevo": true}` |
| `duplicado_detectado` | Capa 2 — es_nuevo=false, < 24h | `{"horas_desde_creacion": 0.08}` |
| `lead_reactivado` | Capa 2 — es_nuevo=false, ≥ 24h | `{"timestamp_original": "...", "dias_desde_creacion": 2}` |
| `abstract_consultado` | Capa 3 — Abstract OK | `{"deliverability_status": "...", "quality_score": 0, "is_live_site": null, "address_risk": "high"}` |
| `abstract_error` | Capa 3 — Abstract falla | `{"error_message": "..."}` |
| `clasificacion_realizada` | Capa 3 — decisión tomada | `{"accion_recomendada": "descartar", "razon": "...", "prioridad": "media", "nivel_riesgo": "high"}` |

**Eventos pendientes de implementar:**

| Evento | Cuándo | Bloque |
|--------|--------|--------|
| `notificacion_enviada` | Capa 4 — Telegram OK | Bloque 5 |
| `notificacion_pendiente` | Capa 4 — Telegram falla | Bloque 5 |
| `lead_descartado` | Capa 3 — rama descartar | Bloque 5 |

**Por qué JSONB y no columnas fijas:**
Cada tipo de evento tiene un conjunto de datos diferente. JSONB permite
registrar exactamente lo relevante para cada caso sin forzar un schema
único. Si Abstract cambia sus nombres de campo, solo cambia el nodo
de registro — la tabla `eventos` no requiere migración.

---

## CAPA 5 — MANTENIMIENTO Y OBSERVABILIDAD

**Estado actual:** ⚠️ Pendiente — se implementa en Bloque 6+

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

---

### Limpieza automática (retención GDPR)

```
[Schedule: primer día de cada mes]
→ DELETE leads descartados con más de 60 días
→ DELETE leads en revisión con más de 6 meses
→ DELETE leads procesados con más de 12 meses
→ [Registrar: N registros eliminados en log del sistema]
```

**Política de retención:**

| Estado | Retención | Justificación |
|--------|-----------|---------------|
| descartado | 60 días | Auditoría del clasificador |
| revisar_manualmente | 6 meses | Seguimiento comercial activo |
| procesado | 12 meses | Leads con posibilidad de conversión |

**Política de logs de N8N:**
Los execution logs contienen PII. Retención máxima: 30 días.
Configuración: `EXECUTIONS_DATA_MAX_AGE=30` en docker-compose del
servidor de producción — se configura en Bloque 6.

---

### Health Check

```
[Schedule: cada 5 minutos]
→ [Verifica que N8N responde (endpoint /healthz)]
→ [Verifica que PostgreSQL responde (query SELECT 1)]
→ (todo OK)    → sin acción
→ (fallo N8N)  → [Telegram: alerta crítica]
→ (fallo DB)   → [Telegram: alerta crítica]
```

---

### Backup automático

```
[Schedule: diario a las 3:00am]
→ [pg_dump de PostgreSQL → archivo .sql.gz cifrado]
→ [Export de workflows de N8N vía API]
→ [Transferencia a almacenamiento externo]
→ [Verificar integridad del backup]
→ [Registrar resultado en log del sistema]
```

Sin runbook de restauración, el backup no sirve.
Documento separado: `runbook.md` — pendiente de crear en Bloque 6.

---

## TABLA DE DEUDA TÉCNICA

| Deuda | Bloque | Riesgo si no se resuelve |
|-------|--------|--------------------------|
| Webhook sin firma HMAC | 7 | Bots saturan el sistema y agotan créditos de Abstract |
| UPDATE leads en reactivación no implementado | 5 | messaggio desactualizado en la DB para leads reactivados |
| Eventos notificacion y lead_descartado sin implementar | 5 | Trazabilidad incompleta en Capa 4 |
| SQL injection en expresiones inline PostgreSQL | 7 | Riesgo si un atacante controla el formulario |
| URL ngrok temporal en formulario | 6 | Webhook deja de funcionar si ngrok reinicia |
| Footer promocional N8N en Telegram | 6 | Aspecto poco profesional en notificaciones |
| Sin retención automática | 6 | Acumulación de PII — riesgo GDPR |
| Sin logs de N8N con límite | 6 | PII acumulada en logs indefinidamente |
| Sin health check | 6 | Fallos silenciosos sin alerta |
| Sin backup automático | 6 | Pérdida total de datos ante fallo del servidor |

---

## DECISIONES DE DISEÑO

**Abstract como fuente de datos, no tomador de decisiones:**
Abstract analiza propiedades del email y las devuelve. El sistema
decide qué hacer. Si Abstract se reemplaza por otra API, la lógica
de negocio no cambia — solo el nodo HTTP.

**CTE atómica para la persistencia inicial:**
`ON CONFLICT DO NOTHING` resuelve race conditions ante inserts
simultáneos. La CTE garantiza que N8N siempre recibe 1 fila —
`es_nuevo=TRUE` si el INSERT tuvo éxito, `es_nuevo=FALSE` si hubo
conflicto. Convierte una excepción técnica en un dato limpio.

**Ventana de tiempo de 24 horas para deduplicación:**
El doble click y el cliente que vuelve un mes después son casos
de negocio distintos aunque lleguen por el mismo canal técnico.
El UNIQUE CONSTRAINT resuelve la concurrencia instantánea.
La ventana de 24 horas resuelve el caso de negocio real.
Identificado por Pablo — las tres IAs de la triangulación lo habían
pasado por alto.

**Write-before-processing:**
La tabla leads recibe el registro con estado `pendiente` antes de
cualquier llamada externa. Si Abstract falla, si Telegram falla,
si hay un error de red — el lead ya existe y no se pierde.

**Nivel 2 nunca procesa automáticamente:**
Si `abstract_live_site === null` el lead llega a Nivel 2, y ningún
lead de Nivel 2 puede cumplir la condición `procesar`. Automatizar
sin confianza suficiente genera falsos positivos que dañan la
relación con el cliente.

**JSONB en tabla eventos — anti vendor lock-in:**
Si Abstract cambia su estructura de respuesta, solo cambia el nodo
de registro del evento. El schema de PostgreSQL no requiere migración.
La elección de los campos a guardar aplica minimización GDPR:
solo los datos con propósito operativo demostrable.

**Code node de restauración como punto de control explícito:**
En lugar de dejar que cada nodo pase lo que quiere al siguiente,
el patrón `return [$('nodo-origen').first()]` es una decisión
consciente de qué datos continúan el flujo. Soberanía sobre el
flujo de datos — no magia implícita.

**Firestore como receptor, PostgreSQL como memoria:**
Firestore recibe el lead desde el formulario — primera red de
seguridad. PostgreSQL es la memoria del sistema — historial,
deduplicación, reportes, retención. Capas complementarias,
no redundantes.

**La triangulación es fuerte en técnico, débil en negocio:**
Las IAs convergen rápido en soluciones técnicas correctas.
Las preguntas de negocio — cómo se comportan clientes reales —
tienen que venir del arquitecto. La experiencia de mercado
no se puede triangular entre modelos de lenguaje.

---

## DOCUMENTOS RELACIONADOS

| Documento | Contenido |
|-----------|-----------|
| `pa-1schema-postgresql-draft.md` | Tablas, columnas, tipos de dato, PII, retención, query CTE |
| `pa1-gdpr-data-mapping.md` | Inventario completo de PII del sistema |
| `ADR-CONSOLIDADAS-PA1_25-04-2026.md` | ADRs serie 200 — decisiones de PA1 |
| `ADR-CONSOLIDADAS-pablo-cuevas.md` | ADRs serie 000-100 — principios universales |
| `adr/ADR-006.md` | Gestión de credenciales — .env + .gitignore |
| `adr/ADR-007.md` | Principio de mínimo privilegio para acceso IA |
| `runbook.md` | Procedimientos de recuperación ante fallos (pendiente) |

---

**Documento creado**: 25 de marzo 2026  
**Última revisión**: 28 de abril 2026  
**Autor**: Pablo Cuevas  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek — triangulación completa  
**Próxima revisión**: Al completar Bloque 5 (deuda técnica de Capa 2-4)
