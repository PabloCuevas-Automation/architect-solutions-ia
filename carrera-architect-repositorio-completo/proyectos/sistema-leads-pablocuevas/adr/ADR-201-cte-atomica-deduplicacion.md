# ADR-201: CTE Atómica + Ventana de 24h para Deduplicación

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — define el mecanismo central de persistencia y deduplicación  
**Audiencia**: 🔒 Personal  
**Proyecto**: PA1 — sistema-leads-pablocuevas

---

## Contexto

El sistema recibe leads desde el formulario de pablocuevas.it. Un lead se
identifica por la combinación de `email + servizio`. El sistema necesita:

1. Detectar si el lead ya existe antes de procesarlo
2. Distinguir un doble click accidental de un cliente legítimo que vuelve
3. Garantizar que ningún lead se pierde aunque falle cualquier paso posterior

El reto técnico: si dos peticiones del mismo lead llegan simultáneamente
(doble click rápido), un patrón SELECT → IF → INSERT tiene una ventana de
concurrencia donde ambas peticiones pasan el SELECT sin encontrar conflicto
y ambas insertan, generando duplicados.

---

## Alternativas consideradas

### Opción A: SELECT → IF existe → INSERT o ignorar (Read-Modify-Write)
- **Pros**: Lógica clara, fácil de leer
- **Contras**: Race condition documentada — dos peticiones simultáneas pueden
  pasar el SELECT en paralelo y ambas insertar. El sistema parece correcto
  pero falla bajo concurrencia real.
- **Por qué NO**: Falso positivo de seguridad. Funciona en tests manuales,
  falla en producción bajo carga.

### Opción B: UNIQUE CONSTRAINT sin lógica de tiempo
- **Pros**: Simple, el motor resuelve la concurrencia
- **Contras**: Bloquea permanentemente al cliente que vuelve con el mismo
  email y servicio meses después. Lead legítimo descartado silenciosamente
  sin notificación a Pablo ni respuesta al cliente.
- **Por qué NO**: Pérdida de leads reales de negocio. El cliente que vuelve
  después de 6 meses es exactamente el lead de mayor valor.

### Opción C: CTE atómica con ON CONFLICT DO NOTHING + ventana de 24h — ELEGIDA ⭐
- **Pros**: Resuelve la race condition a nivel de motor de base de datos.
  Distingue duplicado técnico (< 24h) de cliente legítimo (≥ 24h). Siempre
  devuelve exactamente 1 fila a N8N con toda la información necesaria para
  rutear.
- **Contras**: Query más compleja que una inserción simple. Requiere
  entender CTEs para mantenerla.
- **Por qué SÍ**: Es la única opción que resuelve correctamente tanto la
  concurrencia técnica como el caso de negocio real.

---

## Decisión

**Implementar una CTE atómica con ON CONFLICT DO NOTHING y ventana de 24 horas.**

### El mecanismo técnico

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
Si el INSERT tiene éxito, la CTE devuelve la fila insertada con `es_nuevo=TRUE`.
Si hay conflicto, el INSERT devuelve 0 filas. La condición
`(SELECT COUNT(*) FROM upsert) = 0` activa el segundo SELECT que recupera
el registro existente con `es_nuevo=FALSE`. Sin esta condición, el segundo
SELECT correría siempre y devolvería 2 filas en el caso de lead nuevo.
N8N siempre recibe exactamente 1 fila — nunca 0, nunca 2.

### La ventana de 24 horas

Cuando `es_nuevo = FALSE`, el sistema calcula las horas transcurridas desde
`timestamp_creacion`:

| Caso | Tiempo | Decisión | Justificación de negocio |
|------|--------|----------|--------------------------|
| Mismo email+servizio, < 24h | Duplicado real | Registrar y parar | Doble click o reenvío accidental |
| Mismo email+servizio, ≥ 24h | Lead reactivado | Actualizar y procesar | Cliente legítimo que vuelve |
| Mismo email, servizio diferente | Lead nuevo | INSERT nuevo registro | Interés en otro servicio |

**Por qué 24h y no otro umbral:**
24 horas cubre doble click, reenvíos del mismo día y cualquier reintento
dentro de la misma jornada laboral. Cualquier contacto posterior es
intención nueva. Umbral revisable cuando haya evidencia real de que el
mercado necesita algo diferente.

---

## Consecuencias

### Positivas ✅
- Race condition resuelta a nivel de motor — sin duplicados bajo concurrencia
- Clientes legítimos que vuelven después de 24h son procesados y notificados
- N8N siempre recibe 1 fila con `es_nuevo` y `timestamp_creacion` para rutear
- Todo segundo intento queda registrado en `eventos` — ningún contacto se
  pierde en el vacío

### Negativas ⚠️
- Query más compleja que un INSERT simple — requiere conocimiento de CTEs
  para mantenerla
- El umbral de 24h es arbitrario hasta tener datos reales de comportamiento
  de clientes

---

## Referencias

- `schema-postgresql-draft.md` v2.2 — query completa con parámetros
- ADR-204 — write-before-processing (principio relacionado)
- Triangulación 21/03/2026 — Claude identificó el problema de negocio,
  Gemini la solución técnica correcta, DeepSeek la síntesis

---

**Creado**: 25 de abril 2026  
**Autor**: Pablo Cuevas  
**Próxima revisión**: Al completar Bloque 6 (sistema en producción con datos reales)
