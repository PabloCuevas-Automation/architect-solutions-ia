# SCHEMA POSTGRESQL — Sistema de Gestión de Leads
## pablocuevas.it — Proyecto Ancla 1
 
**Versión**: 2.0  
**Fecha**: 25 de marzo 2026  
**Estado**: ✅ Diseño validado — pendiente implementación Bloque 5  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Supersede**: schema-postgresql-draft.md v1.0
 
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
| `timestamp_creacion` | fecha y hora | NO | Timestamp de inserción en DB (UTC) |
| `timestamp_actualizacion` | fecha y hora | NO | Última modificación del registro |
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
con ON CONFLICT DO NOTHING. Si el mismo email llega con el mismo
servicio, el sistema lo detecta sin generar error.
 
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
| `timestamp_evento` | fecha y hora (UTC) | NO | Cuándo ocurrió el evento |
| `detalle` | JSONB | NO | Datos específicos del evento — estructura variable |
 
**Tipos de evento posibles en `tipo_evento`:**
 
| Valor | Cuándo se registra | Ejemplo de `detalle` |
|-------|-------------------|----------------------|
| `lead_recibido` | Capa 2 — INSERT exitoso | `{"origen": "webhook", "rowCount": 1}` |
| `duplicado_detectado` | Capa 2 — rowCount = 0 | `{"email": "x@y.com", "servizio": "Automazione"}` |
| `lead_reactivado` | Capa 2 — rowCount = 0, > 24h | `{"timestamp_original": "2026-03-01T10:00:00Z", "dias_desde_creacion": 24}` |
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
 
## Deuda técnica
 
| Pendiente | Bloque | Notas |
|-----------|--------|-------|
| Implementar el schema en PostgreSQL real | 5 | Crear tablas con pgAdmin |
| Configurar UNIQUE CONSTRAINT | 5 | Parte del CREATE TABLE |
| Conectar N8N al nodo PostgreSQL | 5 | Credenciales en vault |
| Usuario PostgreSQL con mínimos privilegios | 7 | No usar superuser para N8N |
| Puerto 5432 no expuesto al exterior | 7 | Solo conexión interna |
 
---
 
**Documento creado**: 25 de marzo 2026  
**Autor**: Pablo Cuevas  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Próxima revisión**: Al completar Bloque 5 (schema implementado en producción)