# SCHEMA POSTGRESQL — Sistema de Gestión de Leads
## pablocuevas.it — Proyecto Ancla 1

**Versión**: 2.2  
**Fecha**: 27 de marzo 2026  
**Estado**: ✅ Diseño validado — pendiente implementación Bloque 5  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Supersede**: schema-postgresql-draft.md v2.1

---

## Tabla: leads

Almacena los datos fijos del lead y la decisión final del sistema.
Una fila por combinación única de email + servizio.

| Campo | Tipo | ¿Es PII? | Notas |
|-------|------|----------|-------|
| `id` | entero autogenerado | NO | Identificador interno del sistema |
| `nome` | texto | ✅ SÍ | PII directo |
| `cognome` | texto | ✅ SÍ | PII directo |
| `email` | texto | ✅ SÍ | PII directo — parte de la clave única |
| `azienda` | texto | NO | Dato de empresa |
| `servizio` | texto | NO | Parte de la clave única |
| `messaggio` | texto | ✅ SÍ | Puede contener datos personales |
| `timestamp_roma` | fecha y hora | NO | Hora de llegada del lead (Roma) |
| `timestamp_creacion` | fecha y hora | NO | Timestamp de inserción en DB (UTC) — autogenerado |
| `timestamp_actualizacion` | fecha y hora | NO | Última modificación del registro — autogenerado |
| `status` | texto | NO | Estado operativo actual: pendiente / en_revision / procesado / descartado |
| `accion` | texto | NO | Decisión del sistema: procesar / revisar_manualmente / descartar |
| `razon` | texto | NO | Justificación de la decisión |
| `prioridad` | texto | NO | alta / media / baja |
| `nivel_resolucion` | texto | NO | nivel1 / nivel2 — qué nivel tomó la decisión |

**Constraint clave:**
```
UNIQUE (email, servizio)
```
Previene duplicados exactos. Es la base de la deduplicación atómica
con la CTE. Si el mismo email llega con el mismo servicio, el sistema
lo detecta sin generar error y sin detener el flujo de N8N.

**Distinción status vs accion:**
- `status` = estado operativo actual del lead en el sistema
- `accion` = decisión que tomó el sistema cuando clasificó el lead

Son momentos distintos — `accion` es el resultado de la clasificación,
`status` es el estado vigente que puede cambiar (ej. de `pendiente` a `procesado`).

---

## Tabla: eventos

Registra la línea de tiempo completa de lo que le pasó a cada lead.
Múltiples filas por lead — una por cada hito del flujo.

| Campo | Tipo | ¿Es PII? | Notas |
|-------|------|----------|-------|
| `id` | entero autogenerado | NO | Identificador único del evento |
| `lead_id` | entero | NO | FK → tabla leads (id) |
| `tipo_evento` | texto | NO | Tipo de evento registrado |
| `timestamp_evento` | fecha y hora (UTC) | NO | Cuándo ocurrió el evento — autogenerado |
| `detalle` | JSONB | NO | Datos específicos del evento — estructura variable |

**Tipos de evento posibles en `tipo_evento`:**

| Valor | Cuándo se registra | Ejemplo de `detalle` |
|-------|-------------------|----------------------|
| `lead_recibido` | Capa 2 — es_nuevo=true | `{"origen": "webhook", "es_nuevo": true}` |
| `duplicado_detectado` | Capa 2 — es_nuevo=false, < 24h | `{"email": "x@y.com", "servizio": "Automazione", "horas_desde_creacion": 0.08}` |
| `lead_reactivado` | Capa 2 — es_nuevo=false, ≥ 24h | `{"timestamp_original": "2026-03-01T10:00:00Z", "dias_desde_creacion": 26}` |
| `abstract_consultado` | Capa 3 — respuesta de Abstract | `{"status": "deliverable", "smtp_valid": true, "live_site": true, "es_libre": false, "es_desechable": false, "nivel_riesgo": "low"}` |
| `abstract_error` | Capa 3 — Abstract falla | `{"codigo_error": "500", "mensaje": "timeout", "accion_tomada": "nivel2"}` |
| `clasificacion_realizada` | Capa 3 — decisión tomada | `{"nivel": "nivel1", "accion": "procesar", "razon": "mail corporativo verificado"}` |
| `notificacion_enviada` | Capa 4 — Telegram OK | `{"canal": "telegram", "tipo": "procesar"}` |
| `notificacion_pendiente` | Capa 4 — Telegram falla | `{"canal": "telegram", "error": "timeout", "reintento": true}` |
| `lead_descartado` | Capa 3 — rama descartar | `{"razon": "email desechable", "nivel": "nivel1"}` |

**Por qué JSONB y no columnas fijas:**
El campo `detalle` guarda los datos específicos de cada evento como
un objeto JSON. Esto elimina el vendor lock-in — si Abstract se
reemplaza por otra API, los campos del JSON cambian pero el schema
de la tabla no. Cero migración de base de datos.

Con columnas fijas (`abstract_status`, `abstract_smtp_valid`) el
schema estaría acoplado al proveedor. Con JSONB, el proveedor
puede cambiar sin tocar la estructura de la DB.

**Nota GDPR:** ningún campo de eventos es PII en aislamiento,
pero al estar vinculados a un lead identificable via `lead_id`
se tratan con las mismas reglas de retención que la tabla leads.

---

## Relación entre tablas

La tabla leads almacena los datos que llegan fijos desde el formulario
más la decisión final del sistema. La tabla eventos almacena la línea
de tiempo de todo lo que ocurrió durante el procesamiento.

La conexión técnica: cada lead tiene un `id` único autogenerado.
La tabla eventos guarda ese `id` en `lead_id` para indicar a qué
lead pertenece cada evento.

Un lead → múltiples eventos. Esa es la relación uno a muchos.

---

## Política de retención

| Status del lead | Retención | Justificación de negocio |
|-----------------|-----------|--------------------------|
| `descartado` | 60 días | Auditoría del clasificador — detectar si es demasiado agresivo |
| `en_revision` | 6 meses | Seguimiento comercial activo |
| `procesado` | 12 meses | Leads con posibilidad de conversión activa |

La retención aplica a ambas tablas — cuando se elimina un lead,
se eliminan también todos sus eventos asociados.

La limpieza la ejecuta un workflow de N8N con Schedule mensual.

---

## Query de implementación — Capa 2 (Persistencia inicial)

La operación UPSERT debe devolver siempre 1 fila a N8N,
tanto si el INSERT tuvo éxito como si hubo conflicto.
Se implementa con una CTE:

```sql
WITH upsert AS (
  INSERT INTO leads (
    nome, cognome, email, azienda, servizio,
    messaggio, timestamp_roma, status
  )
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

**Parámetros en orden:** $1=nome, $2=cognome, $3=email, $4=azienda,
$5=servizio, $6=messaggio, $7=timestamp_roma.

**Resultado:** siempre 1 fila con `es_nuevo` (bool) y `timestamp_creacion`.
N8N rutea por `es_nuevo`. Si `TRUE` → lead nuevo → continúa a Capa 3.
Si `FALSE` → Code node calcula horas desde `timestamp_creacion`
para decidir si es duplicado real (< 24h) o lead reactivado (≥ 24h).

**Por qué la condición `(SELECT COUNT(*) FROM upsert) = 0`:**
Sin ella, el segundo SELECT del UNION ALL correría siempre — tanto
cuando el INSERT tuvo éxito como cuando hubo conflicto — y devolvería
2 filas en el caso de lead nuevo. La condición garantiza que el segundo
SELECT solo activa cuando el INSERT no insertó nada.

**Nota de implementación — type casting en N8N:**
PostgreSQL devuelve `timestamp_creacion` como string ISO, no como
objeto Date. En el Code node que calcula las horas desde la creación,
la conversión es obligatoria:

```javascript
const creacion = new Date(item.json.timestamp_creacion); // conversión necesaria
const ahora = new Date();
const horas = (ahora - creacion) / (1000 * 60 * 60);
```

Sin el `new Date()` envolviendo el string, la resta devuelve `NaN`
y el IF de 24h nunca funciona correctamente. Fallo silencioso.

---

## Deuda técnica

| Pendiente | Bloque | Notas |
|-----------|--------|-------|
| Implementar el schema en PostgreSQL real | 5 | Crear tablas con pgAdmin |
| Configurar UNIQUE CONSTRAINT | 5 | Parte del CREATE TABLE |
| Conectar N8N al nodo PostgreSQL | 5 | Credenciales en vault |
| Configurar EXECUTIONS_DATA_MAX_AGE=30 en .env | 6 | Variable de entorno en docker-compose producción |
| Usuario PostgreSQL con mínimos privilegios | 7 | No usar superuser para N8N |
| Puerto 5432 no expuesto al exterior | 7 | Solo conexión interna |

---

**Documento creado**: 25 de marzo 2026  
**Última revisión**: 27 de marzo 2026  
**Autor**: Pablo Cuevas  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Próxima revisión**: Al completar Bloque 5 (schema implementado en producción)
