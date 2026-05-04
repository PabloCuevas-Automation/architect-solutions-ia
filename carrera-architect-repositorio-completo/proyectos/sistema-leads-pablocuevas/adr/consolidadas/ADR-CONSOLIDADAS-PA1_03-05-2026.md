# DECISIONES ARQUITECTÓNICAS — PROYECTO PA1
## sistema-leads-pablocuevas.it

**Versión**: 1.3  
**Fecha**: 03 de mayo 2026  
**Estado**: ✅ Activo — documento único de referencia del proyecto  
**Consolidó**: ADR-200 a ADR-207  
**Supersede**: ADR-CONSOLIDADAS-PA1_02-05-2026.md v1.2  
**Audiencia**: Todas personales — ver campo de audiencia en cada ADR  
**Formato**: Searchable, cross-referenced, no redundancias

---

## ÍNDICE RÁPIDO

| ID | Título | Audiencia | Estado | Cuándo consultar |
|----|--------|-----------|--------|-----------------|
| ADR-200 | Firebase→N8N via fetch() en submit handler | 🔒 Personal | ✅ Activo | Cambiar el trigger del sistema |
| ADR-201 | CTE Atómica + Ventana de 24h para Deduplicación | 🔒 Personal | ✅ Activo | Tocar la lógica de persistencia o deduplicación |
| ADR-202 | Patrón de Queries PostgreSQL en N8N — Expresiones Inline | 🔒 Personal | ✅ Activo | Crear cualquier nodo PostgreSQL en N8N |
| ADR-203 | JSONB en Campo `detalle` de Tabla Eventos | 🔒 Personal | ✅ Activo | Añadir nuevos tipos de evento |
| ADR-204 | Write-Before-Processing — Guardado Antes de Procesar | 🔒 Personal | ✅ Activo | Cambiar el orden del flujo de procesamiento |
| ADR-205 | Nivel 2 Nunca Procesa Automáticamente | 🔒 Personal | ✅ Activo | Modificar la lógica de clasificación |
| ADR-206 | Minimización PII en Notificaciones Telegram | 🔒 Personal | ✅ Activo | Diseñar o modificar Capa 4 (notificaciones) |
| ADR-207 | Reconstrucción de Capa 4 Antes de Producción | 🔒 Personal | ✅ Activo | **Obligatorio antes del deploy en VPS** |

---

---

# ADR-200: Conexión Firebase → N8N via Formulario Web

**Fecha**: 4 de marzo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Técnica  
**Audiencia**: 🔒 Personal

### Contexto

El sistema necesita que N8N sea notificado cuando llega un nuevo contacto
desde el formulario web. Firestore ya recibe y persiste los datos — la
pregunta es cómo y cuándo N8N se entera de esa llegada.

### Alternativas

**Opción A — Schedule Trigger + consulta a Firestore:**
Simple pero con latencia de hasta X minutos. Un lead caliente enfría rápido.
Descartada por el trade-off de latencia.

**Opción B — Firebase Cloud Functions:**
Tiempo real pero requiere escribir y deployar código fuera de N8N. Nueva
capa de infraestructura sin proyecto ancla que la justifique. Descartada.

**Opción C — fetch() en el submit handler del formulario ⭐ ELEGIDA:**
Tiempo real, cero infraestructura nueva, código que ya se controla.
El fetch se ejecuta sin await con su propio .catch — si N8N falla, el
usuario no se entera porque su lead ya llegó a Firestore.

### Decisión

Añadir un `fetch()` al submit handler del formulario que notifica al
webhook de N8N inmediatamente después de guardar en Firestore.

Firestore es la fuente de verdad. N8N es el procesador. Un fallo del
procesador no bloquea la confirmación al usuario.

La URL del webhook es temporal (`localhost:5678`) marcada con TODO.
Se reemplaza en Bloque 6 cuando N8N esté en VPS con URL pública.

### Consecuencias

**✅ Positivas**: Lead procesado en segundos. Sin nueva infraestructura.
Fallo de N8N no impacta la experiencia del usuario.

**⚠️ Negativas**: URL del webhook acoplada al formulario — cambiar la URL
de N8N requiere actualizar y redesployar el formulario.

### Referencias
- `db.js` — submit handler del formulario
- ADR-004 — checklist de infraestructura (TODO pendiente: URL pública)

**Creado**: 4 de marzo 2026

---

---

# ADR-201: CTE Atómica + Ventana de 24h para Deduplicación

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — define el mecanismo central de persistencia y deduplicación  
**Audiencia**: 🔒 Personal

### Contexto

El sistema recibe leads desde el formulario de pablocuevas.it. Un lead se
identifica por la combinación de `email + servizio`. El sistema necesita:

1. Detectar si el lead ya existe antes de procesarlo
2. Distinguir un doble click accidental de un cliente legítimo que vuelve
3. Garantizar que ningún lead se pierde aunque falle cualquier paso posterior

El reto técnico: si dos peticiones del mismo lead llegan simultáneamente
(doble click rápido), un patrón SELECT → IF → INSERT tiene una ventana de
concurrencia donde ambas peticiones pasan el SELECT sin encontrar conflicto
y ambas insertan, generando duplicados.

### Alternativas

**Opción A — SELECT → IF existe → INSERT o ignorar (Read-Modify-Write):**
Lógica clara pero race condition documentada — dos peticiones simultáneas
pueden pasar el SELECT en paralelo y ambas insertar. Funciona en tests
manuales, falla bajo concurrencia real. Descartada.

**Opción B — UNIQUE CONSTRAINT sin lógica de tiempo:**
Simple, el motor resuelve la concurrencia, pero bloquea permanentemente
al cliente que vuelve con el mismo email y servicio meses después. Lead
legítimo descartado silenciosamente sin notificación. Descartada.

**Opción C — CTE atómica con ON CONFLICT DO NOTHING + ventana de 24h ⭐ ELEGIDA:**
Resuelve la race condition a nivel de motor. Distingue duplicado técnico
(< 24h) de cliente legítimo (≥ 24h). Siempre devuelve exactamente 1 fila
a N8N con toda la información necesaria para rutear.

### Decisión

**Implementar una CTE atómica con ON CONFLICT DO NOTHING y ventana de 24 horas.**

```sql
WITH upsert AS (
  INSERT INTO leads (nome, cognome, email, azienda, servizio,
                     messaggio, timestamp_roma, status)
  VALUES ($1, $2, $3, $4, $5, $6, $7, 'pendiente')
  ON CONFLICT (email, servizio) DO NOTHING
  RETURNING id, timestamp_creacion, TRUE AS es_nuevo
)
SELECT id, timestamp_creacion, es_nuevo FROM upsert
UNION ALL
SELECT id, timestamp_creacion, FALSE AS es_nuevo
FROM leads
WHERE email = $3 AND servizio = $5
  AND (SELECT COUNT(*) FROM upsert) = 0
```

**Por qué ON CONFLICT DO NOTHING resuelve la race condition:**
PostgreSQL gestiona el conflicto a nivel de motor con bloqueos internos.
No hay ventana de tiempo entre el check y el insert — es una operación
atómica. Dos peticiones simultáneas: una inserta, la otra recibe DO NOTHING.

**Por qué la CTE siempre devuelve 1 fila:**
Si el INSERT tiene éxito, devuelve la fila insertada con `es_nuevo=TRUE`.
Si hay conflicto, el INSERT devuelve 0 filas. La condición
`(SELECT COUNT(*) FROM upsert) = 0` activa el segundo SELECT que recupera
el registro existente con `es_nuevo=FALSE`. Sin esta condición, el segundo
SELECT correría siempre y devolvería 2 filas en el caso de lead nuevo.

**La ventana de 24 horas:**

| Caso | Tiempo | Decisión |
|------|--------|----------|
| Mismo email+servizio, < 24h | Duplicado real | Registrar y parar |
| Mismo email+servizio, ≥ 24h | Lead reactivado | Actualizar y procesar |
| Mismo email, servizio diferente | Lead nuevo | INSERT nuevo registro |

24 horas cubre doble click, reenvíos del mismo día y cualquier reintento
dentro de la misma jornada laboral. Umbral revisable con datos reales.

### Consecuencias

**✅ Positivas**: Race condition resuelta a nivel de motor. Clientes legítimos
que vuelven después de 24h son procesados. N8N siempre recibe 1 fila para
rutear. Todo segundo intento queda registrado — ningún contacto se pierde.

**⚠️ Negativas**: Query más compleja que un INSERT simple. El umbral de 24h
es arbitrario hasta tener datos reales de comportamiento de clientes.

### Referencias

- `schema-postgresql-draft.md` v2.2 — query completa con parámetros
- ADR-204 — write-before-processing (principio relacionado)
- Triangulación 21/03/2026 — Claude identificó el problema de negocio,
  Gemini la solución técnica correcta, DeepSeek la síntesis

**Creado**: 25 de abril 2026  
**Próxima revisión**: Al completar Bloque 6 con datos reales de producción

---

---

# ADR-202: Patrón de Queries PostgreSQL en N8N — Expresiones Inline

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Táctica — patrón de implementación obligatorio  
**Audiencia**: 🔒 Personal

### Contexto

N8N ofrece dos formas de pasar valores a queries PostgreSQL: Query Parameters
(`[$1, $2, $3]`) y expresiones inline (`{{ $json.campo }}` en el SQL).

Durante la construcción de PA1, el campo Query Parameters produjo errores
sistemáticos que consumieron múltiples iteraciones de debugging:
`there is no parameter $3`, `invalid input syntax for type json`,
`Token "$" is invalid`.

### El problema con Query Parameters

El campo parsea el contenido como texto antes de enviarlo a PostgreSQL.
Cualquier carácter especial dentro de un valor rompe ese parsing:

- Una **coma** dentro de un string se interpreta como separador del array
- **Comillas** dentro de un JSON serializado rompen la delimitación de strings

### Decisión

**Usar expresiones inline `{{ $json.campo }}` directamente en el SQL.**

```sql
INSERT INTO eventos (lead_id, tipo_evento, detalle)
VALUES (
  {{ $json.lead_id }},
  'lead_recibido',
  '{"origen": "web", "es_nuevo": true}'
)
```

### Consecuencias

**✅ Positivas**: Elimina la clase completa de errores de parsing. Funciona
con cualquier contenido — comas, comillas, caracteres especiales, JSON
anidado. El SQL es legible con los valores donde se usan.

**⚠️ Negativas**: Es interpolación de strings, no queries parametrizadas.
N8N muestra advertencia de SQL injection — válida en principio pero el
campo Query Parameters está roto en la práctica para valores complejos.
Riesgo mitigado cuando HMAC esté implementado en Capa 1 (ADR-207).

### Referencias

- PA1 Sesión 6 (25/04/2026) — descubierto durante construcción de nodos
  de registro de eventos en tabla `eventos`

**Creado**: 25 de abril 2026  
**Próxima revisión**: Si N8N lanza una versión que corrija Query Parameters

---

---

# ADR-203: JSONB en Campo `detalle` de Tabla Eventos

**Fecha**: 25 de abril 2026  
**Última revisión**: 01 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Táctica — decisión de schema  
**Audiencia**: 🔒 Personal

### Contexto

La tabla `eventos` registra la línea de tiempo de cada lead. Cada tipo de
evento tiene datos específicos distintos. La pregunta: ¿cómo almacenar
esos datos variables por tipo de evento?

### Alternativas

**Columnas fijas por campo**: schema acoplado a los proveedores actuales.
Si Abstract se reemplaza, hay que migrar el schema. Viola el principio
de anti vendor lock-in (ADR-000). Descartada.

**JSONB en campo `detalle` ⭐ ELEGIDA**: schema estable independientemente
del proveedor. Cada evento almacena exactamente lo que necesita.
Consultable con operadores JSONB de PostgreSQL.

### Decisión

**Campo `detalle` de tipo JSONB en la tabla `eventos`.**

| Evento | Campos en `detalle` | Estado |
|--------|---------------------|--------|
| `lead_recibido` | `origen`, `es_nuevo` | ✅ Implementado S6 |
| `duplicado_detectado` | `horas_desde_creacion` | ✅ Implementado S6 |
| `lead_reactivado` | `timestamp_original`, `dias_desde_creacion` | ✅ Implementado S6 |
| `abstract_consultado` | `deliverability_status`, `quality_score`, `is_live_site`, `address_risk` | ✅ Implementado S7 |
| `abstract_error` | `error_message` | ✅ Implementado S7 |
| `clasificacion_realizada` | `accion_recomendada`, `razon`, `prioridad`, `nivel_riesgo` | ✅ Implementado S7 |
| `notificacion_enviada` | `canal`, `tipo` | ⏳ Pendiente Bloque 5 |
| `lead_descartado` | `razon`, `accion_recomendada` | ⏳ Pendiente Bloque 5 |

### Consecuencias

**✅ Positivas**: Anti vendor lock-in. Schema estable. Cada evento lleva
exactamente los datos relevantes.

**⚠️ Negativas**: Sin validación de schema a nivel de base de datos para
el contenido del JSONB.

**Enmienda 01/05/2026** — Campos de Capa 3 actualizados para reflejar
implementación real (Sesión 7). Minimización GDPR aplicada: de ~30 campos
disponibles en Abstract se guardan únicamente los 4 con propósito operativo
demostrable. Campo `abstract_error` simplificado a `error_message` — cuando
la API falla por completo, es el único dato disponible.

**Creado**: 25 de abril 2026

---

---

# ADR-204: Write-Before-Processing — Guardado Antes de Procesar

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — principio de resiliencia del sistema  
**Audiencia**: 🔒 Personal

### Contexto

El sistema llama a servicios externos (Abstract API, Telegram) después de
recibir un lead. Cualquiera puede fallar. Si el lead no está guardado antes
de esas llamadas, un fallo externo significa pérdida del lead.

### Decisión

**El lead se guarda en PostgreSQL con estado `pendiente` antes de cualquier
llamada a servicios externos.**

```
Webhook → PostgreSQL INSERT (pendiente) → Abstract API → Telegram
```

Si Abstract falla, el lead ya existe en la DB. Si Telegram falla, el lead
ya está clasificado. Un lead que falla en cualquier capa posterior nunca
se pierde — está en la DB con estado `pendiente` esperando resolución.

### Consecuencias

**✅ Positivas**: Ningún lead se pierde por fallos externos. El estado
`pendiente` actúa como cola de trabajo visible.

**⚠️ Negativas**: La DB puede acumular leads en `pendiente` si hay fallos
sistemáticos — requiere monitoreo en Bloque 8.

**Creado**: 25 de abril 2026

---

---

# ADR-205: Nivel 2 Nunca Procesa Automáticamente

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — decisión de negocio sobre clasificación  
**Audiencia**: 🔒 Personal

### Contexto

Abstract API puede fallar o no tener confianza suficiente para verificar
un email. En esos casos el sistema pasa a Nivel 2. La pregunta: ¿el Nivel 2
puede decidir procesar automáticamente un lead?

### Decisión

**El Nivel 2 nunca procesa automáticamente. Solo puede descartar o
escalar a revisión manual.**

```
Nivel 1 (Abstract con confianza): procesar / revisar_manualmente / descartar
Nivel 2 (Abstract falló):         revisar_manualmente / descartar — NUNCA procesar
```

**Por qué:**
Si `abstract_live_site === null`, Abstract no pudo verificar el dominio.
Procesar automáticamente un lead con información incompleta genera falsos
positivos. Un email que el sistema no puede verificar merece revisión humana.

La coherencia arquitectónica: si el Nivel 2 procesara automáticamente,
la condición `abstract_live_site === null` nunca podría cumplirse para
activar el procesamiento — el clasificador sería inalcanzable. Nivel 2
con procesamiento automático es arquitectónicamente incoherente.

### Consecuencias

**✅ Positivas**: Sin falsos positivos por leads no verificados. Pablo
mantiene control sobre cualquier lead que el sistema no puede clasificar.

**⚠️ Negativas**: Más carga manual cuando Abstract falla sistemáticamente.
Leads legítimos con dominios poco conocidos van a revisión manual.

**Creado**: 25 de abril 2026

---

---

# ADR-206: Minimización PII en Notificaciones Telegram

**Fecha**: 2 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Seguridad / GDPR / UX  
**Audiencia**: 🔒 Personal

### Contexto

El sistema notifica la llegada de nuevos leads via Telegram (`PabloLeadsBot`).
La notificación debe permitir actuar rápido sin comprometer la base legal GDPR.

Telegram no tiene DPA firmado con el operador del sistema, sus servidores
están fuera de la UE, y no puede considerarse un procesador GDPR-compliant.
Cualquier dato personal (nombre, email, mensaje del lead) que viaje por
Telegram sale de la infraestructura controlada sin garantías legales.

Al mismo tiempo, una notificación que solo dice "llegó un lead" sin contexto
operativo no es accionable. El arquitecto necesita ver suficiente información
para decidir si actuar, sin exponer PII fuera del sistema.

### Alternativas evaluadas

**Opción A — PII completo en Telegram (nombre + email + mensaje):**
UX máxima. Sin fricción. Incompatible con GDPR — los datos del lead viajan
a servidores de terceros fuera de la UE sin base legal. Descartada.

**Opción B — Solo ID interno en Telegram:**
GDPR perfecto. UX inaceptable. Requiere abrir pgAdmin, filtrar por ID,
navegar tablas. No es operativo como flujo de trabajo diario. Descartada.

**Opción C — Identificadores + metadatos operativos + link a interfaz propia ⭐ ELEGIDA:**
La notificación de Telegram contiene solo datos no-PII de valor operativo:
ID del lead, tipo de servicio, acción recomendada, score de riesgo.
Un link apunta a la interfaz de administración en el propio VPS, bajo HTTPS
y autenticación básica. Los PII se consultan ahí — nunca salen del sistema.

### Decisión

Las notificaciones de Telegram contienen **identificadores y metadatos
operativos únicamente**. Los datos personales del lead se consultan en
la interfaz de administración alojada en infraestructura propia.

**Contenido de la notificación (Capa 4):**

```
🔔 Nuevo lead #47
Servicio: Automatización de procesos
Acción: Procesar
Riesgo: Bajo | Score: 0.91

→ Ver lead: https://admin.pablocuevas.it/leads/47
```

**Qué viaja por Telegram:**
- `id` del lead (entero — sin valor fuera del sistema)
- `servizio` (tipo de consulta — no identifica a la persona)
- `accion_recomendada` + `nivel_riesgo` + `quality_score` (metadatos operativos)
- URL a interfaz propia

**Qué no viaja por Telegram:**
- `nome` — PII directo
- `email` — PII directo
- `messaggio` — puede contener PII adicional

### La interfaz de administración

Una página mínima en el VPS propio (mismo servidor que N8N + PostgreSQL).
Autenticación básica HTTP o token de acceso. Muestra los datos completos
del lead al acceder con el link de la notificación.

**No es un proyecto nuevo**: es una extensión natural del VPS (Bloque 6).
Se implementa en Bloque 5 una vez el VPS esté operativo.

### Consecuencias

**✅ Positivas**:
- Notificación accionable sin exponer PII fuera del sistema
- Base legal GDPR mantenida — Telegram es solo el canal de alerta
- Diferenciador de mercado: documentable y demostrable a clientes
- Interfaz de consulta reutilizable para futuros proyectos

**⚠️ Negativas**:
- Requiere construir la interfaz de administración (Bloque 5, post-VPS)
- Un paso adicional para ver PII vs. tenerlo directo en Telegram
- La URL del VPS debe ser estable — cambio de dominio rompe links históricos

### Referencias

- ADR-007 — Principio de mínimo privilegio (mismo razonamiento aplicado a canales externos)
- ADR-204 — Write-Before-Processing (datos en PostgreSQL antes de notificar)
- ADR-207 — Reconstrucción de Capa 4 (implementación técnica de esta decisión)
- Bloque 5 — construcción de interfaz de administración (post Bloque 6)
- Bloque 6 — VPS, prerequisito para URL estable del link

**Creado**: 2 de mayo 2026

---

---

# ADR-207: Reconstrucción de Capa 4 Antes de Producción

**Fecha**: 3 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Técnica — corrección de implementación existente  
**Audiencia**: 🔒 Personal  
**Supersede**: implementación actual de Capa 4 (notificaciones con PII completa)

### Contexto

La Capa 4 del sistema PA1 está implementada y funcional. Los nodos de
notificación envían actualmente a Telegram:

```
Nome: {nome} {cognome}
Email: {email}
Azienda: {azienda}
Servizio: {servizio}
Messaggio: {messaggio}
```

El 2 de mayo 2026 se aprobó ADR-206, que establece que ningún dato personal
puede viajar por Telegram. La implementación actual viola ese ADR de forma directa.

Esta contradicción no es deuda técnica futura — es una violación activa de
una decisión arquitectónica ya tomada. Debe resolverse antes de que cualquier
dato real de cliente entre al sistema.

### La contradicción exacta

| Campo actual en Telegram | ¿Es PII? | ¿Permitido por ADR-206? |
|--------------------------|----------|------------------------|
| `nome` + `cognome` | ✅ SÍ | ❌ NO |
| `email` | ✅ SÍ | ❌ NO |
| `azienda` | ❌ NO | ✅ SÍ |
| `servizio` | ❌ NO | ✅ SÍ |
| `messaggio` | ✅ SÍ | ❌ NO |
| `timestamp_roma` | ❌ NO | ✅ SÍ |

### Decisión

**Reconstruir todos los nodos de notificación de Capa 4 antes de
desplegar en el VPS de producción.**

**Estructura de notificación nueva — por tipo:**

Nivel 1 — procesar:
```
✅ Lead #{id} — PRIORIDAD ALTA
Servicio: {servizio}
Acción: Procesar
Riesgo: Bajo | Score: {quality_score}

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

Nivel 1 — revisar_manualmente:
```
🔎 Lead #{id} — REVISIÓN MANUAL
Servicio: {servizio}
Razón: {razon}

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

Nivel 2 — revisar_manualmente:
```
⚠️ Lead #{id} — NIVEL 2
Servicio: {servizio}
Abstract no pudo verificar el dominio

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

Lead reactivado:
```
🔄 Lead #{id} — REACTIVADO
Servicio: {servizio}
Días desde primer contacto: {dias_desde_creacion}

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

### Sanitización de messaggio — trabajo adicional en procesador-body1

Aunque `messaggio` no viajará por Telegram, sí se almacena en PostgreSQL
y se mostrará en la interfaz de administración dentro de un navegador.

**Riesgo**: XSS persistente si `messaggio` contiene HTML o JavaScript.

**Resolución**: añadir en `procesador-body1` antes del INSERT:

```javascript
const sanitize = (str) => {
  if (typeof str !== 'string') return '';
  return str
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#x27;');
};
items[0].json.messaggio = sanitize(items[0].json.messaggio);
```

### Prerequisito: interfaz de administración

El link en la notificación apunta a `https://admin.pablocuevas.it/leads/{id}`.
Esta interfaz debe existir antes de que la Capa 4 reconstruida entre en
producción. Se construye en Bloque 5 (post-VPS).

**Secuencia de implementación**:
1. VPS operativo (Runbook-VPS-001)
2. Interfaz de administración básica desplegada
3. DNS `admin.pablocuevas.it` apuntando al VPS
4. Capa 4 reconstruida apuntando a esa URL
5. Verificación completa con Thunder Client

### Consecuencias

**✅ Positivas**:
- Capa 4 queda alineada con ADR-206 antes del primer dato real
- Telegram queda como canal de alerta, no como canal de datos
- Sanitización de messaggio elimina el vector XSS persistente
- Sistema GDPR-compliant en la capa de notificaciones desde el primer lead

**⚠️ Negativas**:
- Requiere reconstruir los nodos de notificación (una sesión de trabajo)
- Requiere construir la interfaz de administración (prerequisito del link)
- Un paso adicional para ver el detalle completo del lead

### Referencias

- ADR-206 — Minimización PII en Notificaciones Telegram (decisión que origina este ADR)
- ADR-010 — Secuencia de deploy: seguridad antes que producción
- ADR-009 — Seguridad de workflows N8N (vector XSS en messaggio)
- Runbook-VPS-001 — VPS prerequisito para URL estable

**Creado**: 3 de mayo 2026

---

---

## REFERENCIAS CRUZADAS

### Cómo navegar este documento

Para entender la decisión de trigger: ADR-200.  
Para entender la lógica de persistencia y deduplicación: ADR-201.  
Para crear cualquier nodo PostgreSQL en N8N: ADR-202 (obligatorio).  
Para entender el schema de eventos: ADR-203.  
Para entender el orden del flujo: ADR-204.  
Para entender los límites de la clasificación automática: ADR-205.  
Para diseñar o modificar Capa 4 (notificaciones): ADR-206 (obligatorio).  
Para implementar Capa 4 antes del deploy: ADR-207 (obligatorio — bloquea VPS).

### ADRs de series superiores que aplican a PA1

| ADR | Cómo aplica en PA1 |
|-----|--------------------|
| ADR-000 | Decisiones > Código. La CTE es más compleja que un INSERT simple pero correcta |
| ADR-002 | PII Management — data mapping GDPR en `gdpr-data-mapping.md` |
| ADR-004 | 6-Layer Checklist — obligatorio antes de deploy en Bloque 6 |
| ADR-005 | 5-Layer Workflow Audit — obligatorio antes de dar el workflow por completo |
| ADR-006 | Credenciales en .env — implementado desde Sesión 3 |
| ADR-007 | Mínimo privilegio — aplica a canales externos (Telegram) via ADR-206 |
| ADR-008 | Arquitectura de seguridad VPS — define el entorno donde PA1 se desplegará |
| ADR-009 | Seguridad de workflows — 3 preguntas de auditoría antes de activar en producción |
| ADR-010 | Secuencia de deploy — seguridad antes que producción. Bloquea el VPS hasta resolver ADR-207 |

### Estado operativo (03 de mayo 2026)

| ADR | Estado | Implementado | Pendiente |
|-----|--------|-------------|-----------|
| ADR-200 | ✅ | Trigger activo | URL ngrok → URL VPS en Bloque 6 |
| ADR-201 | ✅ | Capa 2 completa | Validar con datos reales en producción |
| ADR-202 | ✅ | Todos los nodos PostgreSQL actualizados | Evaluar sanitización adicional en Bloque 7 |
| ADR-203 | ✅ | Schema implementado — Capa 3 enmendada S7 | Añadir eventos Bloque 5 cuando se implementen |
| ADR-204 | ✅ | INSERT antes de Abstract | — |
| ADR-205 | ✅ | Nivel 2 sin rama procesar | — |
| ADR-206 | ✅ | Decisión tomada | Implementar — prerequisito del deploy (ver ADR-207) |
| ADR-207 | ⚠️ | Decisión tomada — **aún no implementado** | **Bloquea deploy a VPS** |

### Deuda técnica documentada

| ADR | Deuda | Riesgo | Resolver en |
|-----|-------|--------|-------------|
| ADR-200 | URL ngrok temporal en formulario | Webhook deja de funcionar si ngrok reinicia | Bloque 6 |
| ADR-202 | SQL injection en expresiones inline | Mitigado cuando HMAC esté activo | Bloque 7 |
| ADR-206 | Interfaz de administración no construida | Sin ella el link de Telegram no resuelve | Bloque 5 (post VPS) |
| ADR-207 | Capa 4 envía PII a Telegram actualmente | 🔴 Violación activa ADR-206 — bloquea deploy | **Antes de Bloque 6** |
| ADR-207 | messaggio sin sanitizar en procesador-body1 | XSS persistente en interfaz de administración | **Antes de Bloque 6** |
| — | HMAC como placeholder en Capa 1 | Cualquier petición HTTP es procesada | **Antes de Bloque 6** |
| — | Replay attack sin timestamp en HMAC | GDPR Art. 9 si messaggio tiene datos especiales | **Antes de Bloque 6** |
| — | eventos notificacion_enviada / lead_descartado | Trazabilidad incompleta en Capa 4 | Bloque 5 |
| — | UPDATE leads en rama reactivación | messaggio desactualizado en DB | Bloque 5 |

---

**Documento consolidado por**: Pablo Cuevas + Claude (Anthropic)  
**Fecha consolidación**: 03 de mayo 2026  
**Versión**: 1.3  
**Supersede**: ADR-CONSOLIDADAS-PA1_02-05-2026.md v1.2  
**Próxima revisión**: Al completar implementación de ADR-207 y resolver deudas pre-deploy  
**No mezclar con**: ADR consolidadas serie 000-100 ni con decisiones de GeoLabor (serie 300)
