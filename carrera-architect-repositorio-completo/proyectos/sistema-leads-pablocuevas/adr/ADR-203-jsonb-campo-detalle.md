# ADR-203: JSONB en Campo `detalle` de Tabla Eventos

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Táctica — decisión de schema  
**Audiencia**: 🔒 Personal  
**Proyecto**: PA1 — sistema-leads-pablocuevas

---

## Contexto

La tabla `eventos` registra la línea de tiempo de cada lead. Cada tipo de
evento tiene datos específicos distintos — un `abstract_consultado` tiene
campos de reputación de email, un `duplicado_detectado` tiene
`horas_desde_creacion`, un `notificacion_enviada` tiene el canal usado.

La pregunta de diseño: ¿cómo almacenar esos datos variables por tipo de evento?

---

## Alternativas

### Opción A: Columnas fijas por campo
Añadir columnas como `abstract_status`, `abstract_smtp_valid`,
`horas_desde_creacion`, `canal_notificacion`, etc.

- **Contras**: El schema queda acoplado a los proveedores actuales. Si Abstract
  se reemplaza por otra API, hay que migrar el schema. La mayoría de columnas
  estarían en NULL para la mayoría de eventos — tabla dispersa.
- **Por qué NO**: Viola el principio de anti vendor lock-in (ADR-000).

### Opción B: JSONB en campo `detalle` — ELEGIDA ⭐
Un campo JSONB que almacena los datos específicos de cada evento como objeto.

- **Pros**: Schema estable independientemente del proveedor. Cada evento
  almacena exactamente lo que necesita. Consultable con operadores JSONB
  de PostgreSQL. Cero migración si cambia Abstract.
- **Contras**: Menos validación a nivel de schema — el contenido del JSONB
  no está tipado por la base de datos.
- **Por qué SÍ**: El proveedor puede cambiar sin tocar la estructura de la DB.

---

## Decisión

**Campo `detalle` de tipo JSONB en la tabla `eventos`.**

Cada tipo de evento define su propio contrato de campos en el JSONB:

| Evento | Campos en `detalle` |
|--------|---------------------|
| `lead_recibido` | `origen`, `es_nuevo` |
| `duplicado_detectado` | `horas_desde_creacion` |
| `lead_reactivado` | `timestamp_original`, `dias_desde_creacion` |
| `abstract_consultado` | `status`, `smtp_valid`, `live_site`, `es_libre`, `es_desechable`, `nivel_riesgo` |
| `abstract_error` | `codigo_error`, `mensaje`, `accion_tomada` |
| `clasificacion_realizada` | `nivel`, `accion`, `razon` |
| `notificacion_enviada` | `canal`, `tipo` |

---

## Consecuencias

### Positivas ✅
- Anti vendor lock-in: cambiar de Abstract a otra API no requiere migración
- Schema estable a lo largo del tiempo
- Cada evento lleva exactamente los datos relevantes

### Negativas ⚠️
- Sin validación de schema a nivel de base de datos para el contenido del JSONB
- El contrato de campos por tipo de evento vive en el código, no en la DB

---

**Creado**: 25 de abril 2026  
**Autor**: Pablo Cuevas
