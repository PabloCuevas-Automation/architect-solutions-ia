## Bloque 1 — Entrada

**¿Qué dispara el flujo?**

 El flujo lo dispara a traves del webhook, un envio de leads mediante un formulario.

**¿Qué datos exactos llegan al webhook?**

LLega, nombre, apellido, empresa, servicios que necesitan, mensaje, timestamp.

**¿Qué pasa si llega el mismo email por segunda vez? (deduplicación)**

 El sistema consulta PostgreSQL verificando si existe la 
combinación email + servicio. Si ya existe, registra el 
duplicado y para. Si el mismo email llega con un servicio 
diferente, se procesa como lead nuevo — una persona puede 
necesitar más de un servicio.

## Bloque 2 — Procesamiento

**¿Qué hace el sistema con esos datos paso a paso?** 

1. Llega el lead al webhook
2. procesador-body formatea y limpia los datos
3. Abstract verifica el email
4. IF — ¿Abstract tiene confianza suficiente?
   
   SÍ → Nivel 1
        Switch según accion_recomendada:
        - procesar → Telegram + PostgreSQL
        - revisar_manualmente → Telegram + PostgreSQL
        - descartar → registro PostgreSQL
   
   NO → Nivel 2
        Reglas propias del sistema
        IF según propiedades del lead:
        - cumple reglas → Telegram (mención nivel 2) + PostgreSQL
        - no cumple → registro PostgreSQL


**¿Qué decide Abstract y qué decide el sistema propio?**

 En el sistema Abstract no decide, si no que "analiza" el mail y dependiendo su base de datos, da las propiedades de ese mail, y yo luego uso los datos que me aporta Abstract y los evaluo y uso como mi sistema lo necesite. 

**¿Qué rama toma cada tipo de lead?**

- Mail corporativo, Abstract confianza alta → Nivel 1 → Switch 
  → procesar → Telegram (prioridad alta) + guardar PostgreSQL

- Mail personal válido, Abstract confianza alta → Nivel 1 → Switch 
  → revisar_manualmente → Telegram (revisión manual) + guardar PostgreSQL

- Mail desechable o riesgo alto → Nivel 1 → Switch 
  → descartar → registrar PostgreSQL sin notificar

- Abstract no puede verificar → Nivel 2 → reglas propias → IF
  → cumple reglas → Telegram (mención nivel 2, revisión manual) + guardar PostgreSQL
  → no cumple reglas → registrar PostgreSQL sin notificar

## Bloque 3 — Salida

**¿Qué se guarda en PostgreSQL y en qué momento?**

- Tabla leads: se guarda inmediatamente después de la deduplicación
  y antes de cualquier procesamiento. Si algo falla después, 
  el lead ya está registrado.

- Tabla eventos: se guarda al final del flujo, cuando ya se sabe
  qué decidió el sistema y qué nivel lo resolvió.

Retención según acción:
- descartado → 60 días
- revisar_manualmente → 6 meses  
- procesado → 12 meses

**¿Qué se notifica por Telegram y con qué información?**

Mediante telegram se notifica la informacion necesaria para que se sepa que se ha hecho con el leads, la informacion es Nombre, apellido, empresa, mensaje, mail, servicio y hora, esto es en la notificacion en ambos niveles, luego en cada nivel ,viene enriquecido con los procesos antes hablados,  en nivel 1: accion sugerida, prioridad y razon , en nivel 2: razon de escalada y accion sugerida

**¿Qué pasa con los descartados?**

No se notifica por Telegram. Se registra en PostgreSQL con:
- razon del descarte
- nivel donde fue descartado (nivel 1 o nivel 2)
- timestamp
- retención: 60 días con borrado automático

Deuda técnica actual: el nodo de descarte solo tiene un 
Edit Fields básico. Pendiente enriquecerlo con razon y 
nivel_resolucion antes de guardar en PostgreSQL.


## Bloque 4 — Reporte semanal

**¿Qué dispara el reporte?**

Un Schedule Trigger cada lunes a las 8:00am hora de Roma.

**¿Qué información contiene?**

- Total de leads recibidos en la semana
- Leads procesados (prioridad alta y media)
- Leads en revisión manual pendientes
- Leads descartados con razón más frecuente
- Leads de nivel 2 escalados

**¿Cómo llega a ti?**

Notificación por Telegram con el resumen formateado.

**Métricas futuras (cuando haya volumen suficiente):**

- Porcentaje de leads por servicio solicitado
- Horarios pico de llegada de leads
- Tasa de conversión por nivel de prioridad
- Tiempo promedio de respuesta a leads en revisión manual