# DECISIONES ARQUITECTÓNICAS — PROYECTO PA1
## sistema-leads-pablocuevas.it

**Versión**: 1.1  
**Fecha**: 01 de mayo 2026  
**Estado**: ✅ Activo — documento único de referencia del proyecto  
**Consolidó**: ADR-200 a ADR-205  
**Supersede**: ADR-CONSOLIDADAS-PA1_25-04-2026.md v1.0  
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
- Un **timestamp** sin comillas no es JSON válido dentro del array
- En modo `fx`, N8N envía el array entero como un solo parámetro `$1`

### Decisión

**Usar expresiones `{{ $json.campo }}` directamente en el SQL.
Query Parameters siempre vacío.**

```sql
-- ✅ CORRECTO
INSERT INTO leads (nome, email, messaggio)
VALUES (
  '{{ $json.body.nome }}',
  '{{ $json.body.email }}',
  '{{ $json.body.messaggio }}'
)
```

**Regla operativa:**
> En nodos PostgreSQL de N8N: expresiones `{{ $json.campo }}` directamente
> en el SQL. Query Parameters siempre vacío. Sin excepciones.

Para strings: envolver en comillas simples `'{{ $json.campo }}'`  
Para números: sin comillas `{{ $json.campo }}`

### Consecuencias

**✅ Positivas**: Elimina la clase completa de errores de parsing. Funciona
con cualquier contenido — comas, comillas, caracteres especiales, JSON
anidado. El SQL es legible con los valores donde se usan.

**⚠️ Negativas**: Es interpolación de strings, no queries parametrizadas.
N8N muestra advertencia de SQL injection — válida en principio pero el
campo Query Parameters está roto en la práctica para valores complejos.

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

## REFERENCIAS CRUZADAS

### Cómo navegar este documento

Para entender la decisión de trigger: ADR-200.  
Para entender la lógica de persistencia y deduplicación: ADR-201.  
Para crear cualquier nodo PostgreSQL en N8N: ADR-202 (obligatorio).  
Para entender el schema de eventos: ADR-203.  
Para entender el orden del flujo: ADR-204.  
Para entender los límites de la clasificación automática: ADR-205.

### ADRs de series superiores que aplican a PA1

| ADR | Cómo aplica en PA1 |
|-----|--------------------|
| ADR-000 | Decisiones > Código. La CTE es más compleja que un INSERT simple pero correcta |
| ADR-002 | PII Management — data mapping GDPR en `gdpr-data-mapping.md` |
| ADR-004 | 6-Layer Checklist — obligatorio antes de deploy en Bloque 6 |
| ADR-005 | 5-Layer Workflow Audit — obligatorio antes de dar el workflow por completo |
| ADR-006 | Credenciales en .env — implementado desde Sesión 3 |

### Estado operativo (01 de mayo 2026)

| ADR | Estado | Implementado | Pendiente |
|-----|--------|-------------|-----------|
| ADR-200 | ✅ | Trigger activo | URL ngrok → URL VPS en Bloque 6 |
| ADR-201 | ✅ | Capa 2 completa | Validar con datos reales en producción |
| ADR-202 | ✅ | Todos los nodos PostgreSQL actualizados | Verificar en nodos futuros Bloque 5 |
| ADR-203 | ✅ | Schema implementado — Capa 3 enmendada S7 | Añadir eventos Bloque 5 cuando se implementen |
| ADR-204 | ✅ | INSERT antes de Abstract | — |
| ADR-205 | ✅ | Nivel 2 sin rama procesar | — |

### Deuda técnica documentada

| ADR | Deuda | Resolver en |
|-----|-------|-------------|
| ADR-200 | URL ngrok temporal en formulario | Bloque 6 |
| ADR-202 | Advertencia de SQL injection en N8N — evaluar sanitización | Bloque 7 |

---

**Documento consolidado por**: Pablo Cuevas + Claude (Anthropic)  
**Fecha consolidación**: 25 de abril 2026  
**Última revisión**: 01 de mayo 2026  
**Próxima revisión**: Al completar Bloque 5 (eventos notificacion y lead_descartado)  
**No mezclar con**: ADR consolidadas serie 000-100 ni con decisiones de GeoLabor (serie 300)
