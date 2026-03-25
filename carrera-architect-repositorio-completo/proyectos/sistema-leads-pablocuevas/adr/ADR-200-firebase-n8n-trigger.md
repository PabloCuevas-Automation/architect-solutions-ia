# ADR-005: Conexión Firebase → N8N via Formulario Web

**Fecha**: 2026-03-04  
**Estado**: Aceptada  
**Tipo**: Técnica

---

## Contexto

El sistema de leads de pablocuevas.it necesita que N8N sea notificado cuando llega un nuevo contacto desde el formulario web. Firestore ya recibe y persiste los datos — la pregunta es cómo y cuándo N8N se entera de esa llegada.

La decisión afecta directamente la latencia del sistema y su complejidad de mantenimiento. Un lead caliente enfría rápido — la notificación en tiempo real tiene valor de negocio real, no solo técnico.

---

## Alternativas

### Opción A: Schedule Trigger + consulta a Firestore
- **Pros**: Simple de implementar, no requiere tocar el formulario
- **Contras**: Latencia de hasta X minutos según el intervalo, consultas innecesarias cuando no hay leads
- **Por qué NO**: El trade-off de simplicidad no compensa la latencia. Un lead puede esperar minutos antes de ser procesado.

### Opción B: Firebase Cloud Functions
- **Pros**: Tiempo real, completamente desacoplado del formulario
- **Contras**: Requiere escribir y deployar código fuera de N8N, nueva capa de infraestructura a mantener, coste adicional
- **Por qué NO**: Complejidad sin proyecto ancla que la justifique. Se aplaza para cuando un cliente lo requiera explícitamente.

### Opción C: fetch() en el submit handler del formulario — ELEGIDA ⭐
- **Pros**: Tiempo real, cero infraestructura nueva, código que ya se controla, fallo silencioso sin impacto al usuario
- **Contras**: URL del webhook hardcodeada temporalmente con TODO pendiente, acoplamiento leve entre formulario y N8N
- **Por qué SÍ**: Máximo resultado con mínima complejidad. El formulario ya tiene el momento exacto del evento — aprovecharlo es más simple y más directo que cualquier alternativa.

---

## Decisión

**Decidí**: Añadir un `fetch()` al submit handler del formulario que notifica al webhook de N8N inmediatamente después de guardar en Firestore.

**Porque**: La Opción C da tiempo real sin añadir infraestructura. El `fetch` se ejecuta sin `await` con su propio `.catch` — si N8N falla, el usuario no se entera porque su lead ya llegó a Firestore. Son fallos independientes con consecuencias independientes. Firestore es la fuente de verdad; N8N es el procesador. Un fallo del procesador no debe bloquear la confirmación al usuario.

La URL del webhook es temporal (`localhost:5678`) y está marcada con TODO explícito en el código. Se reemplaza en Bloque 6 cuando N8N esté deployado en VPS con URL pública.

---

## Consecuencias

### Positivas ✅
- Lead procesado en N8N en segundos desde el submit del formulario
- Sin nueva infraestructura que mantener
- Fallo de N8N no impacta la experiencia del usuario
- Código en un solo lugar, fácil de encontrar y modificar

### Negativas ⚠️
- URL del webhook acoplada al formulario — cambiar la URL de N8N requiere actualizar y redesployar el formulario
- Solución temporal hasta Bloque 6 — `localhost:5678` no funciona en producción
- Si el formulario no se envía (usuario abandona), N8N no se entera — pero tampoco Firestore, así que es comportamiento correcto

---

## Validación

Esta decisión será exitosa si:
- ✅ N8N recibe el lead en menos de 5 segundos desde el submit del formulario (verificado con Thunder Client)
- ✅ Un fallo de N8N no muestra error al usuario ni bloquea el guardado en Firestore
- ✅ La URL se reemplaza por la URL del VPS al completar Bloque 6

Revisar en: Bloque 6 — Deploy en producción

---

## Referencias
- `db.js` — submit handler del formulario, función `form.addEventListener("submit")`
- ADR-004 — checklist de infraestructura antes de producción (TODO pendiente: URL pública)

---

**Creado**: 2026-03-04  
**Última actualización**: 2026-03-04
