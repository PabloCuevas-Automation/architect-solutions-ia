# DAILY LOG — FASE 1
## Constructor de Sistemas de Automatización

**Convención**: Una entrada por sesión significativa de trabajo.  
**Formato**: Fecha → qué hiciste → qué entendiste → qué quedó pendiente.  
**Propósito**: Contexto activo para sesiones de trabajo con mentor. No es un diario — es una herramienta.

---

## 25/01 ✅ Fase 0 — ADRs
Analizados 5 ADRs reales (AWS, Spotify, IBM). Creados ADR-000 (pensamiento sistémico primero) y ADR-001 (JavaScript > Python Fase 1). Templates completos.

---

## 27/01 ✅ Fase 0 — Data Privacy
GDPR fundamentos. Identificación PII en 10 servicios. Análisis sistemas (Gmail, Banco, Notion). Audit personal compliance. 3h.

---

## 28/01 ✅ Fase 0.5 — Setup Técnico
Instalados: Git 2.52.0, Node.js v24.13.0, PostgreSQL 18.1, Docker 29.1.3. Email profesional + GitHub creados. Repo versionado (53 archivos). Timeline corregido: examen JS 16/02. 5h.

---

## 29/01 ✅ Fase 0.5 — Terminal, Git y Estándares
Dominio de navegación PowerShell: `pwd`, `ls`, `mkdir`, `cd`, `New-Item`. ADR-003 (Naming Conventions): reorganización completa del repositorio a kebab-case. Refuerzo Git: estados de archivos, flujo de seguridad antes del `git add .`. Activos bilingües EN/ES para ADRs y templates.

---

## 02/02 ✅ Fase 0.5 — Variables, tipos de datos y objetos JS
Variables: LET y CONST, tipos de datos, creación de objetos. Bases de objetos JS. Objetos anidados, acceso dinámico por corchetes, operadores para decisiones.

---

## 10/02 — 19/02 ✅ PROYECTO WEB — pablocuevas.it
**Decisión estratégica**: proyecto académico del Corso Sviluppo Web (Trento) usado como infraestructura de negocio real. Orquestación de Claude, Gemini y Lima.

**Stack**: HTML5 + CSS3 + JS + Bootstrap 5 + GSAP 3.14.1 + Firebase Firestore + Vercel CI/CD

**Hitos:**
- 10/02: Navbar glass morphism + Hero con video. ~20%
- 11/02: Chi Sono multiidioma IT/ES/EN + fix GSAP. ~40%
- 16/02: ✅ Examen JavaScript aprobado. Soluzioni rediseñada con lazy loading. ~65%
- 18/02: Formulario → Firebase Firestore. WhatsApp FAB. Legal compliance (iubenda). ~80%
- 19/02: ✅ 100% completado y en producción. Come Lavoro + badge GDPR by design.

**PageSpeed:** Rendimiento 98 / Accesibilidad 97 / Recomendaciones 100 / SEO 100

**Infraestructura activa:** pablocuevas.it live, Firebase recibiendo leads reales, Vercel CI/CD.

**Aprendizaje clave:** aprendizaje por construcción > estudio abstracto. 9 sesiones → sistema completo en producción. Documentado en ADR-103.

---

## 21/02 ✅ Reestructuración knowledge base + ADRs nuevos
Sesión de revisión estratégica. Punto de inflexión entre cierre del proyecto web e inicio de Fase 1.

**Limpieza:**
- Eliminados documentos obsoletos (recalibración v1.0, planes basados en Python-first)
- ADR-101 actualizado con sección de validación y métricas reales
- DAILY-LOG gap cubierto (10/02 - 21/02)

**ADRs nuevos:**
- ADR-102: Proyecto Académico como Infraestructura de Negocio Real
- ADR-103: Perfil de Aprendizaje — Construcción sobre Abstracción

**Repositorio:** Reestructurado y pusheado a GitHub vía VS Code GUI.

**Próximo hito identificado:** Primera automatización N8N — Firebase → detectar lead → notificación.

---

## 23-24/02 ✅ Mini-Workflows en JS orientados a N8N
Primera ejecución de JS puro via Node.js en terminal.

**Conceptos:**
- Modelo mental N8N: `[Trigger] → [Nodo] → [Output]`
- Items vs arrays de items — cómo viajan entre nodos
- Spread operator para enriquecer sin perder datos originales
- `map` vs `forEach` — transformar vs iterar

**Workflows construidos:**
- W1: Firebase lead → filter → formatear → simular envío (guiado)
- W2: Clasificador de intención con Switch y respuestas dinámicas (guiado)
- W3: Clasificador por horario laboral (independiente)
- Extra: Resumen diario — 5 items → 1 output con totales agrupados

**Estado:** Flujo de datos entre nodos entendido. Transformación sin pérdida clara. Sintaxis con consulta — conceptos de fondo sólidos.

---

## 25/02 ✅ Reorganización completa del sistema de documentación
Sesión estratégica de arquitectura de conocimiento. La más importante desde el 21/02.

**Problema identificado y resuelto:**
El roadmap original había mutado múltiples veces (Python → JavaScript, SQL pospuesto, Cloud redefinido) sin que los documentos de validación reflejaran esa realidad. Generaba desorientación y pérdida de confianza en el sistema propio.

**Decisiones tomadas:**

*Redefinición de Fase 1:*
- Fase 1 no es "aprender N8N" — es "construir sistemas de automatización dignos de un arquitecto"
- SQL, Cloud y deploy son parte de Fase 1, no de Fase 2
- Razón: Pablo tendrá clientes reales durante Fase 1, no después
- Los clientes pueden necesitar N8N 24/7 (Cloud), bases de datos (SQL) y sistemas seguros (deploy)
- Documentado en `fase-1-redefinida.md`

*Separación de capas de documentación:*
- `fase-1-activa/` — trabajo activo (se actualiza frecuentemente)
- `roadmap-largo-plazo/` — brújula de 3-5 años (se toca raramente)
- `historico/` — registro permanente de fases completadas
- `_backup/` — documentos obsoletos que no se eliminan
- `adr/` — fuente única de verdad, sin duplicados

*Convención DAILY-LOG por fase:*
- Un DAILY-LOG por fase activa (este documento)
- Al completar Fase 1 → se mueve a `historico/`
- Fase 2 nace con DAILY-LOG limpio

**Documentos generados hoy:**
- `fase-1-redefinida.md` — marco estratégico completo con 6 capas sinérgicas
- `plan-tactico-fase-1.md` — ejercicios y condiciones por bloque
- `estimacion-sesiones-fase-1.md` — 9 bloques + 3 proyectos ancla, ~63 sesiones estimadas
- `fase-0-fase-05-historico.md` — registro completo de fases anteriores
- `README.md` — índice del repositorio

**Reorganización del repositorio:**
Estructura antigua con carpetas duplicadas y ADRs en dos lugares → estructura limpia con responsabilidades claras. Ejecutado en PowerShell paso a paso con verificación.

**Sistema de trabajo establecido:**
- Project de Claude: `fase-1-redefinida.md` + `estimacion-sesiones-fase-1.md` + `DAILY-LOG-FASE-1.md`
- Cada sesión: pegar el bloque activo → trabajar → actualizar DAILY-LOG
- Al completar bloque: actualizar tabla de progreso en `estimacion-sesiones-fase-1.md`
- Al completar Fase 1: DAILY-LOG se archiva, nace Fase 2 con sistema idéntico

## 26/02 ✅ Bloque 0 — Sesión 1: Instalación y exploración N8N

Instalé N8N con Docker y accedí a `localhost:5678`. Creé cuenta local, exploré canvas, panel de nodos y execution log. Construí el primer workflow `[Manual Trigger] → [Edit Fields]` y observé el flujo de datos real.

**Entendido:** Un item en N8N tiene siempre la estructura `{ "json": { ... } }`. Los datos de negocio viven dentro de `.json`, por lo que la ruta correcta desde un Code node es `item.json.nombre`. El Manual Trigger produce un array con un item vacío `[{}]` — su trabajo es arrancar el flujo, no inyectar datos. Los datos reales los define el trigger específico.

**Pregunta abierta:** ¿N8N tiene nodo nativo de Firebase o necesito webhook/schedule para conectarlo? Lo resuelvo en Bloque 2, Sesión 3.

## 27/02 ✅ Bloque 0 — Sesión 2: Primer workflow con datos reales y disección del execution log

Construí el workflow `[Manual Trigger] → [Edit Fields] → [Edit Fields]` con datos reales y experimenté con el flujo de datos entre nodos.

**Experimentos realizados:**

Configuré el primer Edit Fields con tres campos (`nombre: Pablo`, `fase: 1`, `bloque: 0`) y el segundo con un campo nuevo (`ciudad: Rovereto`). Experimenté con la opción "Include Other Input Fields" activada y desactivada, y luego con la sobreescritura de un campo existente (`nombre`) en el segundo nodo.

**Entendido:**

Un item en N8N es un contenedor de datos que viaja como array de objetos a lo largo del flujo. Dentro de cada item existen tres compartimentos: `.json` (los datos de negocio que vienen externamente y con los cuales trabajamos), `binary` (para archivos adjuntos) y `pairedItem` (referencias internas que N8N usa para su propio rastreo). La UI de N8N muestra únicamente el contenido de `.json` porque es lo que manipulamos en la práctica.

`$json` es la llave de acceso al compartimento `.json` desde el Code node — un atajo que evita escribir el camino completo `items[0].json`. Lo que se ve en el execution log es exactamente lo que `$json` contiene en código: son la misma cosa, dos representaciones del mismo dato.

El nodo Edit Fields tiene dos modos de comportamiento según "Include Other Input Fields": con la opción activada, el nodo enriquece el item sumando los campos nuevos a los existentes (patrón de enriquecimiento); con la opción desactivada, produce un item limpio con solo los campos definidos explícitamente (patrón de transformación). La elección entre uno y otro no es técnica — es de diseño: ¿estoy enriqueciendo o estoy transformando?

Sobre la sobreescritura: el último nodo que toca un campo gana, igual que el spread operator en JavaScript. El peligro real no es que el workflow falle — es que no falla. Los datos incorrectos viajan silenciosamente hasta el final del flujo generando una cadena de resultados incorrectos difícil de rastrear, porque el síntoma visible aparece lejos de la causa real. Intentar acceder a `item.nombre` sin pasar por `.json` devuelve `undefined` sin lanzar error — el mismo tipo de fallo silencioso.

**Pregunta abierta:** Sigue abierta la del Bloque 2 — ¿N8N tiene nodo nativo de Firebase o necesito webhook/schedule?
## 28/02 ✅ Bloque 0 — Sesión 3: Consolidación conceptual y cierre del Bloque 0

Lectura de la documentación oficial del Code node y consolidación de modelos mentales.

**Entendido:**

El naming de variables es libre — `item`, `lead`, `carlitos` son equivalentes. Lo fijo es la estructura interna que entrega N8N: `.json`, `.binary`, `.pairedItem`. El `$` es la señal visual de que se accede al entorno de N8N, no a JavaScript puro.

El Code node es el único nodo donde se escribe lógica arbitraria. El resto va de cerrado a semi-editable. Consecuencia de diseño: preferir nodos predeterminados cuando pueden hacer el trabajo — son más legibles.

Distinción crítica internalizada: `filter` decide cuántos items salen, `map` decide qué forma tiene cada item que sale. No son intercambiables.

**Diagrama de flujo:** `[Manual Trigger]` → item vacío → `[Edit Fields]` añade campos → `[Code node]` transforma con `map` para quedarse solo con campos numéricos → output con `fase: 1` y `bloque: 0`.

**Señal de desbloqueo superada:** item, workflow y execution log definidos con palabras propias sin mirar apuntes.

**Estado del Bloque 0:** ✅ Completado.

## 01/03 ✅ Bloque 1 — Sesiones 1 y 2: Code Node + JavaScript aplicado en N8N

**Construido:** workflow `[Manual Trigger] → [Code A: genera leads] → [Code B: transforma]` con datos incompletos intencionalmente para simular condiciones reales.

**Entendido:**

`console.log` en N8N aparece en la consola del navegador (DevTools → Console), no en el panel de output del nodo. Son canales distintos: el output panel muestra lo que el nodo devuelve al flujo, la consola muestra diagnóstico del desarrollador.

La distinción `!== ""` vs `!== null` es crítica. Un campo vacío es una cadena vacía, no ausencia de valor. Comparar contra `null` cuando el dato es `""` produce un falso positivo silencioso — el item pasa el filtro, el workflow no falla, y los datos incorrectos viajan hasta el final sin advertencia.

El `map` construye un objeto nuevo en cada iteración — sin spread, los campos originales desaparecen. `...item.json` vuelca los campos existentes en el objeto nuevo antes de agregar los nuevos. Validado experimentalmente con y sin spread para observar el contraste directo.

`item.json?.nombre || "Cliente"` son dos capas de protección distintas: `?.` protege contra propiedad inexistente, `||` maneja valor falsy. La robustez del Code node no debe depender de promesas de calidad de fuentes externas que no controlamos.

**ADR-004 generado:** checklist de 6 capas como verificación obligatoria antes de producción (Aplicación, Persistencia, Alojamiento, Seguridad, Resiliencia, Continuidad). Descartados: construir sin checklist por deuda técnica invisible, y frameworks externos (TOGAF, AWS Well-Architected) por complejidad excesiva para el mercado actual. Convergencia independiente con Claude y Gemini en la misma estructura fundamental. Revisión programada al completar Proyecto Ancla 2.

# DAILY LOG — 02/03/2026

## Bloque 1 completo — Code Node + JavaScript aplicado

---

## Sesiones 1 y 2 (registradas anteriormente)

Workflow `[Manual Trigger] → [Code A: genera leads] → [Code B: transforma]` con datos incompletos intencionalmente. Conceptos clave: `console.log` en DevTools, `!== ""` vs `!== null`, spread operator, optional chaining.

---

## Sesión 3  — Filtrado y comportamiento de array vacío

Filtrado de leads por empresa no vacía. Experimento con condición imposible para observar comportamiento de N8N con array vacío.

**Entendido:** N8N no lanza error cuando el filter devuelve array vacío — detiene la ejecución silenciosamente. Los nodos siguientes no corren. Fallo silencioso sin aviso.

**Cómo manejar este caso en un sistema real:**
- **Audit trail**: el workflow registra siempre cuántos items entraron, cuántos pasaron el filter y cuántos fueron descartados — independientemente del resultado.
- **Alerting** (Telegram/email): notificación de estado en cada ejecución. Reactivo.
- **Observabilidad** (PostgreSQL): historial persistente consultable. Permite reconstruir qué pasó y hasta qué punto funcionó correctamente cuando algo falla.

Sin alerting el sistema es ciego. Sin observabilidad es amnésico.

---

## Sesiones adicionales previas a sesión 4 — Ejercicios map + filter

Antes de la sesión 4 se completaron tres ejercicios de consolidación:

**Ejercicio 1 — map puro:** calcular `precioConIVA` con spread operator. Validado que sin spread los campos originales desaparecen.

**Ejercicio 2 — filter puro:** filtrar tareas por estado y prioridad con condición doble `&&`. Detectado y corregido el bug crítico de `=` (asignación) vs `===` (comparación) — ambas ejecutan sin error pero dan resultados opuestos.

**Ejercicio 3 — map + filter encadenados:** clasificar leads con `esClientePotencial` y luego filtrar. Pablo razonó correctamente el orden: map primero porque el filter depende de un campo que el map crea. También identificó de forma independiente el valor de dos nodos separados para trazabilidad — "con dos nodos, el execution log te muestra el estado intermedio".

---

## Sesión 4  — Clasificación por lógica de negocio

Clasificación de leads por dominio de email usando `split`, `includes` y ternario.

**Construido:** workflow que extrae el dominio del email, verifica si está en lista de dominios gratuitos y asigna prioridad `"alta"` o `"baja"`.

**Entendido:**
- `split("@")[1]` extrae la parte después del `@`
- `includes` verifica pertenencia a una lista sin necesitar `===`
- El ternario `condicion ? "valor1" : "valor2"` reemplaza el if/else dentro del map
- `const` declarado fuera del map no puede acceder a `item` — tiene que estar dentro de la función

**Demostrado:** Pablo razonó correctamente el orden map → filter antes de escribir código, y justificó que el orden depende del problema, no de una regla fija.

---

## Sesión 5  — Error handling en Code node

Dataset con campos faltantes intencionales: leads sin email, sin nombre, sin presupuesto, y un item completamente vacío `{}`.

**Construido:** Code node robusto que no explota con datos incompletos.

**Entendido:**
- `?.` (optional chaining) protege contra propiedad inexistente — devuelve `undefined` en lugar de TypeError
- `||` maneja valor falsy como fallback
- Ternario anidado para tres casos: sin dominio → `"no especificada"`, dominio gratuito → `"baja"`, dominio corporativo → `"alta"`
- Distinción entre item vacío `{}` (descartar) y lead incompleto (guardar con estado) — no son el mismo caso

**Demostrado:** Pablo identificó por sí mismo que Marco Rossi (lead con nombre pero sin email) merece tratamiento diferente al item completamente vacío — razonamiento de negocio aplicado al diseño técnico.

---

## Referencia JS creada 

Generado documento `js-metodos-referencia-n8n.md` con todos los métodos relevantes para N8N:
- Métodos de Array: `map`, `filter`, `includes`, `find`, `some`, `every`, `reduce`
- Métodos de String: `split`, `includes`, `replace`, `trim`, `toLowerCase`, `toUpperCase`
- Métodos de Object: `Object.keys`, `Object.values`, `Object.entries`
- Operadores: spread `...`, optional chaining `?.`, nullish coalescing `??`, OR `||`, ternario `? :`
- Sección de combinaciones — patrones que se repiten en Code nodes reales

Decisión tomada: la sintaxis se resuelve con IA. El criterio — saber cuál método usar y por qué — es el conocimiento real a internalizar.

---

## Sesión 6  — Workflow integrador

Dataset sucio de 6 items con campos faltantes variados + item vacío. Un solo Code node que hace todo.

**Construido:**
```javascript
const items = $input.all();
items.filter(item => Object.keys(item.json).length > 0);

const dominiosGratuitos = ["gmail.com", "hotmail.com", "yahoo.com", "outlook.com"];

return items.map(item => {
  const dominio = item.json.email?.split("@")[1].toLowerCase().trim() || null;
  return {
    json: {
      ...item.json,
      nombre: item.json.nombre || "Cliente sin identificar",
      prioridad: !dominio ? "no especificada" : dominiosGratuitos.includes(dominio) ? "baja" : "alta",
      emailNormalizado: item.json.email?.toLowerCase().trim() || null,
    }
  };
});
```

**Entendido:**
- `Object.keys(item.json).length > 0` detecta y descarta items vacíos
- Encadenamiento de múltiples métodos sobre una cadena: `?.split("@")[1].toLowerCase().trim()`
- `const` dentro del map no lleva `;` al final cuando está dentro de un objeto — rompe la expresión
- `emailNormalizado` se asigna directamente dentro del objeto, no como `const` separado

**Debugging demostrado sin guía:**
Pablo debuggeó de forma autónoma usando el editor de N8N — leyó el error en rojo, identificó que `items` no estaba definida antes de ser llamada, entendió que `const` dentro de un objeto no funciona, y detectó que el `;` rompía la expresión. No usó el execution log — usó el editor con ejecución paso a paso. Eso es exactamente cómo se trabaja en producción.

**Comando estándar N8N local (con persistencia):**
`docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n n8nio/n8n`
Sin el volumen `-v`, los workflows desaparecen al cerrar Docker. Con volumen, persisten en la máquina.
Docker empaqueta un programa con todo lo que necesita para correr en una caja portable llamada contenedor. Sin volumen, los datos viven dentro de la caja y desaparecen al apagarla. Con volumen, los datos viven en tu máquina y persisten.

---

## Estado del Bloque 1

**Sesiones completadas:** 6/6  
**Estado:** ✅ Completado  
**Señal de desbloqueo superada:** Code node que transforma, filtra y enriquece items con datos incompletos. Debugging autónomo. Razonamiento de orden de operaciones por criterio, no por intuición.

## 04/03  Bloque 2 — Triggers + Flujo Real (Sesiones 1-4)

---

### Sesión 1 — Schedule Trigger

Workflow construido: `[Schedule Trigger] → [Code: captura timestamp] → [Code: formatea mensaje]`

**Conceptos internalizados:**
- `new Date().toISOString()` genera timestamp UTC universal — para máquinas y bases de datos
- `new Date().toLocaleString("it-IT", { timeZone: "Europe/Rome" })` genera hora legible local — para humanos y notificaciones
- Un sistema real necesita ambos porque tiene dos audiencias: la máquina que procesa y el humano que lee
- UTC es la fuente de verdad. Guardar hora local en DB genera inconsistencias históricas cuando cambia el horario estacional
- En N8N 2.x el toggle "Active" pasó a llamarse "Publish" — mismo concepto, nuevo nombre

**Workflow guardado en `_snippets`** con nodos renombrados: `capturador-timestamp` y `orario-UTC-EURoma`.

---

### Sesión 2 — Webhook Trigger

Workflow construido: `[Webhook] → [procesador-body] → [Respond to Webhook]`

**Herramienta nueva: Thunder Client** — extensión de VS Code para enviar requests HTTP con interfaz visual. Reemplaza curl en PowerShell, que en Windows es un alias de `Invoke-WebRequest` y no se comporta igual.

**Conceptos internalizados:**
- El Webhook no ejecuta solo — queda escuchando activamente hasta que llega un POST
- El webhook de test solo acepta una llamada por cada "Execute workflow" — es comportamiento intencional de N8N
- `Respond to Webhook` es necesario porque hay un emisor esperando respuesta. Sin él, el emisor recibe timeout aunque N8N procesó correctamente
- `Respond to Webhook` siempre va al final — solo puedes responder cuando terminaste de procesar
- El Schedule no necesita responder porque nadie está esperando al otro lado

**Pruebas realizadas con Thunder Client:**
- POST con datos completos → respuesta correcta con todos los campos
- POST sin `nombre` → `nombre: "desconocido"` por fallback `||`
- POST con body vacío → todos los campos con fallback, `procesado: true`

**Decisión de diseño documentada:** body vacío devuelve `procesado: true` — en un sistema real habría que decidir conscientemente si un body vacío es caso válido o error.

**Workflow guardado en `_snippets`** con nodos renombrados: `receptor-leads` (Webhook) y `procesador-body` (Code node).

---

### Sesión 3 — Firebase → N8N

**Decisión arquitectónica tomada:** cómo conectar Firebase con N8N. Tres opciones evaluadas:

| Opción | Mecanismo | Tiempo real | Complejidad |
|--------|-----------|-------------|-------------|
| A — Schedule + Firestore | Polling cada X minutos | ❌ Latencia | Baja |
| B — Firebase Cloud Functions | Cloud Function dispara POST | ✅ | Alta |
| C — Modificar formulario web | `fetch` en submit handler | ✅ | Baja |

**Elegida Opción C** por criterio arquitectónico: tiempo real + cero infraestructura nueva + código que ya se controla. La Opción B se aplaza para cuando un proyecto la necesite de verdad, no como ejercicio.

**Polling vs Webhook internalizado:** polling busca datos en intervalos fijos aunque no haya nada nuevo. Webhook reacciona solo cuando llega el dato — no trabaja en vacío. Para leads, la latencia importa.

**Modificación en `db.js`:**
- Añadido `fetch` al webhook de N8N justo después del `addDoc` a Firestore
- `fetch` sin `await` con `.catch` propio — si N8N falla, el usuario no se entera porque su lead ya llegó a Firestore. Son fallos independientes
- Recuperado el bloque `finally` que se había perdido — sin él el botón queda deshabilitado si hay error
- URL marcada con TODO pendiente para Bloque 6

```javascript
// Notificar a N8N — fallo silencioso, no bloquea al usuario
// TODO: reemplazar URL cuando N8N esté en producción (Bloque 6)
fetch("TU_URL_TUNEL_AQUI", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ nome, cognome, email, azienda, servizio, messaggio })
}).catch(err => console.error("N8N notification failed:", err));
```

**Verificado con Thunder Client:** workflow actualizado con campos reales del formulario. POST con datos de lead italiano → 200 OK, todos los campos procesados y timestamp correcto.

**Estado actual del sistema:**
```
Formulario pablocuevas.it → Firestore (persistencia) + N8N webhook (procesamiento)
```

Lo que falta para Proyecto Ancla 1: clasificación por prioridad, PostgreSQL, notificación Telegram/email, deduplicación, reporte semanal.

---

### Sesión 4 — ADR de la decisión

Creado **ADR-201** — primer ADR de la serie 200 (decisiones de proyectos Fase 1).

Convención de series establecida:
- **000-series**: principios arquitectónicos universales
- **100-series**: decisiones de carrera y aprendizaje
- **200-series**: decisiones de proyectos Fase 1

ADR documenta las tres opciones evaluadas, el razonamiento de la elección, las consecuencias aceptadas y los criterios de validación — incluyendo la deuda técnica consciente de la URL temporal.

---

**Estado del Bloque 2:** ✅ Completado  

## 05/03 + 06/03 ✅ Sesión Demo — Tunnel + Telegram + Demo con profesor

---

### Lo que se construyó (05/03)

**Infraestructura temporal:**
- Instalado ngrok via Microsoft Store, authtoken configurado permanentemente
- Tunnel activo: `ngrok http 5678` expone `localhost:5678` con URL pública HTTPS
- Deuda técnica consciente documentada: URL cambia al reiniciar ngrok → actualizar `db.js` antes de cada demo, se reemplaza en Bloque 6 con VPS

**Actualización `db.js`:**
- Reemplazada URL temporal del tunnel en el fetch a N8N
- Commit + push → Vercel deploy automático
- El fetch sigue siendo sin `await` con `.catch` silencioso — Firestore es la fuente de verdad, N8N es secundario

**Nodo Telegram añadido al workflow:**
- Bot creado via @BotFather, Chat ID obtenido via @userinfobot
- Credenciales guardadas en vault de N8N
- Nodo renombrado: `notificador-telegram`
- Mensaje formateado con campos del lead + prioridad + timestamp italiano

**Workflow final:**
```
[Webhook] → [procesador-body] → [notificador-telegram] → [Respond to Webhook]
```

**Test end-to-end verificado:**
- Formulario `pablocuevas.it` → Firebase ✅
- Formulario `pablocuevas.it` → N8N webhook ✅
- Clasificación por dominio de email ✅
- Notificación Telegram en tiempo real ✅

---

### La demo con el profesor (06/03)

Primera prueba falló (sin internet en el laptop). Segunda prueba funcionó. El profesor pudo ver el flujo completo en vivo: formulario → notificación Telegram en segundos.

**Lo que valoró:** la claridad de N8N como interfaz y la honestidad técnica sobre capacidades actuales y lo que falta aprender.

**Conversación honesta mantenida:** se le explicó que puedo construir el núcleo funcional pero que la arquitectura completa — seguridad, hosting, manejo de datos, resiliencia — es lo que estoy aprendiendo ahora. Lo entendió y lo respetó.

---

### Oportunidades que se abrieron

**Suanfarma SPA:**
Contacto interno interesado en automatizar flujos de su grupo de trabajo con IA. Contexto: proceso pequeño dentro de una multinacional farmacéutica — riesgo manejable si el scope está bien definido. Análisis en profundidad cuando llegue el momento. Compliance farmacéutico (GDPR, posible ISO 27001) hace valioso el respaldo del estudio si se formaliza.

**Propuesta del profesor (Dettaglio Studio):**
De manera informal, ofreció respaldo de su estudio cuando tenga sistemas más completos y el proyecto Suanfarma avanzado. Estructura: ellos aportan marca, cartera y marketing — yo ejecuto las automatizaciones. División económica discutida a modo de ejemplo: proyecto que yo vendería en €3-4k, con el estudio se vende en €7-8k, con distribución entre backend developer, estudio y yo.

**Criterios claros antes de formalizar cualquier cosa:**
- División económica por escrito
- Claridad sobre quién es el cliente en cada proyecto
- Scope de Suanfarma bien definido antes de acercarse

---

### Entendido en las conversaciones

La diferencia entre un automatizador y un arquitecto no es técnica — es la capacidad de entregar un sistema completo con todas sus capas: infra, seguridad, persistencia, resiliencia, compliance y documentación.

Un VPS reemplaza el laptop encendido: servidor always-on, URL fija, HTTPS real, PostgreSQL persistente, backups automáticos. Eso convierte una demo en un producto entregable.

Las oportunidades de hoy se concretan en 4-6 semanas — exactamente cuando estarán terminados los Proyectos Ancla. El trabajo de las próximas semanas es lo que hace posible lo que se abrió hoy.

---
## 07/03 — 13/03 ✅ Consolidación teórica Bloques 0, 1 y 2

Pausa deliberada antes de iniciar Bloque 3. Al enfrentar el primer ejercicio de HTTP Request (restcountries.com → extraer campos de JSON anidado), identifiqué que podía leer la estructura pero no tenía confianza para escribir el código de extracción. Decisión: consolidar los tres bloques antes de avanzar.

---

### Documento generado

`consolidacion-bloques-0-1-2.md` — revisión teórica completa de los tres bloques con el porqué arquitectónico de cada concepto + 16 preguntas de validación en cuatro niveles (por bloque + integración final).

---

### Preguntas de validación trabajadas (con Claude + Gemini)

**Bloque 0 — Modelo mental de N8N:**
- Ruta de acceso a datos anidados: distinguir `{{$json.campo}}` (expresiones en nodos visuales) vs `item.json.campo` (Code node en JS puro). Son dos contextos con dos sintaxis.
- Edit Fields para GDPR: modo transformación como política de lista blanca — defines qué **sí** pasa, no qué eliminas. Más resiliente que un Code node para este caso porque un error de código podría dejar pasar el campo sensible.
- Sobreescritura silenciosa: prevención > detección. Naming descriptivo (`status_recepcion`, `status_procesamiento`) elimina colisiones sin necesidad de monitoreo extra.
- Array como estructura universal: homogeneidad elimina la verificación "¿es objeto o array?" en cada nodo. Mismo principio que APIs bien diseñadas.

**Bloque 1 — Code Node + JS aplicado:**
- Orden filter→map vs map→filter: no hay regla fija. Depende de si el campo por el que filtras ya existe. Si necesitas crear el campo primero, map va antes.
- Programación defensiva en tres niveles: Code node (fallbacks), formulario (campos obligatorios), y comunicación con el cliente (calidad de datos = calidad de decisiones).
- Diagnóstico de fallos silenciosos en producción: tres hipótesis siempre — filter devuelve vacío por cambio de formato, URL de tunnel caída, nodo final falla pero workflow marca verde. Cada una se verifica en un lugar diferente del execution log.
- JavaScript no conserva campos automáticamente porque no hay un "antes" — `map` crea un objeto nuevo literal. El spread no preserva, construye.
- Clasificación multifactor antes de pagar APIs: el formulario ya pide `azienda`. Email corporativo OR empresa declarada = prioridad alta. Costo cero.

**Bloque 2 — Triggers + Flujo real:**
- Si Firebase tuviera webhooks nativos: eliminaría el `fetch` de `db.js`, frontend no sabría que N8N existe. Mejor separación de responsabilidades, pero menor control directo. No justifica el cambio ahora.
- Sistema híbrido webhook + polling de conciliación: webhook para velocidad, polling nocturno como red de seguridad que verifica leads no procesados. Necesita campo `procesado` en DB — se implementa en Bloque 5 con PostgreSQL.
- Type 1 vs Type 2: Firebase (datos) = Type 1. Telegram (notificación) = Type 2. N8N como motor de automatización = Type 1 también (workflows, lógica, credenciales viven dentro).
- Escala de 100 leads en 5 minutos: N8N procesa webhooks en secuencia, no en paralelo. No explota, hace cola. Cuello de botella real: si ngrok cae durante el pico, `.catch` silencioso atrapa todo pero N8N recibe cero. Firestore los tiene todos.

---

### Criterio consolidado: Code node vs Edit Fields

No es "simple vs complejo". Es: ¿la herramienta visual puede expresar esta lógica sin volverse más confusa que el código?

Code node cuando:
1. Necesitas operar sobre el array completo (promedios, duplicados, agrupaciones)
2. La lógica condicional es demasiado compleja para nodos visuales encadenados
3. Necesitas encadenar métodos sobre un dato (`email?.split("@")[1]?.toLowerCase().trim()`)

Edit Fields / nodos visuales cuando: la operación es estándar y el nodo visual la expresa de forma autodocumentada.

---

### Conceptos complementarios trabajados con Gemini

- Declarativo (Edit Fields: dices QUÉ) vs Imperativo (Code node: dices CÓMO)
- Operaciones verticales (transformar cada item independiente) vs horizontales (cruzar datos entre items)
- Edit Fields más resiliente para compliance — error de código puede dejar pasar campo sensible; error en nodo visual es visible

---

## 14/03 ✅ Bloque 3 — Sesiones 1, 2 y 3: HTTP Request + APIs Reales
 
Tres sesiones completadas en dos sesiones efectivas de trabajo real.
 
---
 
### Sesión 1 — HTTP Request sin autenticación (restcountries.com)
 
Workflow construido: `[Manual Trigger] → [HTTP Request: GET restcountries.com] → [Code: extrae campos]`
 
**Conceptos internalizados:**
 
Una API no "le da internet" a un workflow — es un contrato de comunicación. Define qué podés pedir, en qué formato, y qué formato tiene la respuesta. El servidor interno puede cambiar completamente sin que el cliente lo note, porque la interfaz acordada no cambia. Esa separación entre implementación interna y contrato externo es el valor real de una API bien diseñada.
 
El JSON que devuelven las APIs reales tiene dos tipos de anidación. La anidación estructural refleja jerarquías reales del dominio — `name.common`, `name.official` — y sus claves son fijas y conocidas de antemano. La anidación con claves dinámicas es más difícil: campos como `currencies` o `languages` usan el código ISO como clave (`"EUR"`, `"ITA"`), que varía según el país consultado. Para este segundo caso, `Object.keys(objeto)[0]` extrae la clave sin importar cuál sea, y después se usa esa clave para acceder al valor.
 
La anidación no existe para evitar sobreescrituras — existe para reflejar relaciones jerárquicas reales. Las sobreescrituras son un problema de colisión de nombres en el mismo nivel, que se resuelve con spread operator y naming descriptivo, no con anidación.
 
**Output obtenido:**
```json
[{
  "nombre": "Italy",
  "capital": "Rome",
  "moneda": "euro (€)",
  "poblacion": 58927633,
  "idioma": "Italian",
  "region": "Europe"
}]
```
 
**Proceso correcto para navegar JSON desconocido:** ejecutar primero y observar el output crudo, identificar si el root es objeto o array, mapear la jerarquía visualmente hasta el dato necesario, identificar si hay claves dinámicas que requieren `Object.keys()`, y recién entonces escribir el código de extracción.
 
---
 
### Sesión 2 — Credentials en N8N (OpenWeatherMap)
 
Workflow construido: `[Manual Trigger] → [HTTP Request: OpenWeatherMap autenticado] → [Code: extrae campos clima]`
 
**Conceptos internalizados:**
 
La autenticación con API key resuelve tres problemas simultáneamente: identidad (saber quién sos), autorización (controlar qué podés hacer) y rate limiting (limitar cuánto podés pedir). Sin autenticación, cualquiera podría saturar los servidores del proveedor o acceder a datos que no le corresponden.
 
El vault de credenciales de N8N encripta las API keys y las inyecta automáticamente en cada request sin que aparezcan en ningún lugar visible del workflow. Si exportás el workflow como JSON, solo ves una referencia al ID de la credencial — nunca la key en texto plano. Es el equivalente a un gestor de passwords: el login funciona sin que la contraseña sea visible en ningún momento.
 
El flujo correcto para cualquier API nueva es: verificar que la conexión funciona con la key en la URL, confirmar que la respuesta es correcta, mover la key al vault, actualizar el nodo para usar la credencial, y verificar que sigue funcionando. Desarrollo y producción deben tener el mismo nivel de seguridad desde el primer día — no es algo que se aplica solo al momento del deploy.
 
Lección importante sobre campos opcionales: `data.rain` solo aparece en la respuesta de OpenWeatherMap cuando hay precipitación. En un día soleado el campo no existe y `data.rain["1h"]` lanza TypeError. El patrón correcto es siempre `data.rain?.["1h"] || 0` — el `?.` antes del corchete funciona exactamente igual que con notación de punto.
 
**Output obtenido:**
```json
[{
  "ciudad": "Rovereto",
  "temperatura": "9.17°C",
  "sensacion": "8.47°C",
  "humedad": "93%",
  "condicion": "light rain",
  "viento": "1.73 m/s",
  "lluvia": "0.55 mm"
}]
```
 
**Snippet guardado:** `openweathermap-clima-actual` — HTTP Request autenticado con Query Auth. URL limpia sin key, credencial inyectada por N8N desde vault. Campos opcionales (`rain`, `snow`) solo presentes con precipitación — `?.` obligatorio. Patrón: `data.weather[0]` para condición, `data.main` para métricas, `data.rain?.["1h"]` para lluvia.
 
---
 
### Sesión 3 — Manejo de errores de API
 
Workflow construido: `[Manual Trigger] → [HTTP Request: On Error = Continue] → [Code: clasifica error] → [Switch: rutea por tipo] → [Edit Fields x4: rama por tipo]`
 
**Conceptos internalizados:**
 
No todos los errores HTTP tienen la misma causa ni la misma solución, y un sistema robusto los trata de forma diferente. Un 401 indica problema de autenticación — reintentar no sirve porque el problema no es de disponibilidad sino de credenciales inválidas; la respuesta correcta es detener y alertar a un humano. Un 429 indica rate limit alcanzado — reintentar inmediatamente empeora el problema; la respuesta correcta es esperar con backoff. Un 500 indica error del lado del servidor externo — puede ser transitorio, tiene sentido reintentar con límite, pero si persiste hay que escalar.
 
La opción **On Error** del nodo HTTP Request controla qué hace N8N cuando el request falla. "Stop Workflow" detiene todo inmediatamente — útil como defecto seguro, pero no permite manejo personalizado. "Continue" hace que el error viaje como dato al nodo siguiente dentro de `item.json.error`, lo que permite clasificarlo con un Code node y rutear de forma diferente según el tipo. Esta es la distinción crítica: `item.error` no existe — el error llega en `item.json.error`.
 
La distinción **texto plano vs expresión** en nodos de N8N es fundamental. Los campos en modo texto plano interpretan el contenido literalmente — útil para valores fijos como el string `"exito"`. Los campos en modo expresión (ícono `fx`, fondo verde) evalúan `{{ $json.campo }}` como una referencia dinámica al JSON del item actual — obligatorio para comparar contra datos que cambian según el request. Activar el modo expresión se hace con el ícono `fx` o arrastrando el campo desde el panel de input.
 
El Switch no es el fallback — es el router que decide qué fallback ejecutar. La resiliencia real está en lo que vive al final de cada rama: notificaciones, lógica de reintento, escalada a humano. El Switch hace visible en el canvas el diseño de manejo de errores, lo cual es una ventaja arquitectónica concreta: cualquiera puede abrir el workflow y ver exactamente qué pasa cuando cada cosa falla.
 
**Validación de diseño trabajada:** cuando una API devuelve 429 consistentemente durante un pico de 50 leads, el sistema correcto no descarta ni bloquea los leads pendientes — los encola en almacenamiento persistente (PostgreSQL) con estado `pendiente` y un workflow de Schedule los procesa en orden FIFO cuando el rate limit se libera. La cola con prioridad es una evolución válida para cuando haya evidencia de que el valor diferencial de los leads justifica la complejidad adicional, pero introduce el riesgo de starvation para items de baja prioridad. Para Fase 1, FIFO con PostgreSQL es el diseño correcto.
 
**Snippet guardado:** `error-handling-http-request` — On Error en modo Continue, clasificación por `item.json.error` (no `item.error`), Switch con expresiones `{{ $json.tipo }}` en modo fx. Patrones: 401/403 = auth_error (no reintentar), 429 = rate_limit (esperar), 500+ = server_error (reintentar con límite).
 
---
## 17/03 - 18/03 ✅ Bloque 3 — Sesiones 4, 5 y 6: APIs reales, enriquecimiento y audit

---

### Sesión 4 — Segunda y tercera API (17/03)

**Objetivo real:** completar el mapa de autenticación — no integrar APIs por completar el checklist.

**API A — Abstract Email Reputation**
- Auth: Query Auth (proveedor no soporta header — trade-off aceptado y documentado)
- Endpoint: `GET https://emailreputation.abstractapi.com/v1/`
- Campos extraídos con criterio GDPR: `email_address`, `email_deliverability.status`, `email_quality.is_free_email`, `email_quality.is_disposable`, `email_risk.address_risk_status`, `email_sender.organization_type`
- Campos descartados con justificación: `first_name`, `last_name` (PII sin propósito), `email_domain.domain` (redundante), `email_breaches` (no relevante para clasificación)
- Lógica de `accion_recomendada` diseñada antes de escribir código: descartar → revisar_manualmente → procesar

**API B — GitHub API**
- Auth: Bearer Token via Header Auth (`Authorization: Bearer <token>`)
- Fine-grained PAT con Contents: Read-only + repo específico — principio de mínimos privilegios aplicado
- Endpoint: `GET https://api.github.com/repos/{owner}/{repo}`
- Criterio `es_portfolio_relevante` definido por Pablo: público + stars >= 1 + tópico relevante + descripción != null + actualizado < 6 meses
- JSON crudo tiene 80+ campos — se extraen solo los útiles (minimización aplicada)

**Mapa de autenticación completo:**

| API | Auth | Patrón |
|-----|------|--------|
| restcountries.com | Sin auth | URL pública |
| OpenWeatherMap | Query Auth | `?appid=KEY` |
| Abstract | Query Auth | `?api_key=KEY` (forzado por proveedor) |
| GitHub | Header Auth | `Authorization: Bearer TOKEN` |

**Lección clave:** Bearer Token es Header Auth con formato estándar de industria. El patrón es idéntico en OpenAI, Anthropic, Stripe — aprendido una vez, aplica en todas.

---

### Sesión 5 — Enriquecimiento de leads (17/03)

**Sistema de leads actualizado:**
```
[Webhook] → [procesador-body] → [HTTP Abstract] → [enriquecedor-clasificador] → [notificador-telegram] → [Respond to Webhook]
```

**Problema resuelto:** cuando HTTP Abstract ejecuta, los datos originales del lead quedan atrás en `procesador-body`. Solución: `$('procesador-body1').item.json` accede a cualquier nodo anterior por nombre — no solo al input inmediato.

**Bug detectado y corregido:** `organization_name: "Gmail"` para cuentas personales de Google → `tipoCuenta: "comercial"` incorrecto. Causa: Abstract considera Gmail una organización comercial. Corrección: usar `esEmailLibre` directamente en lugar de depender de `tipoCuenta`. Lección: los datos de APIs externas no siempre son lo que esperás — diseño robusto usa múltiples señales, no un campo único.

**Fallback implementado:** si Abstract falla, `enriquecedor-clasificador` detecta `!abstract.email_deliverability` y asigna `revisar_manualmente` con razón explícita. El lead nunca se pierde.

**On Error configurado:** HTTP Abstract en modo Continue — si la API falla, el workflow sigue con datos básicos del lead.

---

### Sesión 6 — Consolidación, audit y organización (18/03)

**Workflow renombrado:** `sistema-leads-enriquecido`

**Audit completado** con el template ADR-005 (5 capas):

| Capa | Estado | Deuda |
|------|--------|-------|
| Seguridad | ⚠️ | Webhook sin validación de origen → Bloque 7 |
| Resiliencia | ⚠️ | Sin idempotencia → Bloque 5 |
| Observabilidad | ✅ | Sticky note general pendiente (menor) |
| GDPR | ⚠️ | Sin política de retención → Bloque 5 |
| Mantenibilidad | ✅ | — |

**Veredicto:** entregable con deuda documentada — las tres deudas tienen fecha de resolución y no representan riesgo grave sin PostgreSQL.

**Estructura de repositorio definida:**
```
proyectos/
├── _templates/
│   ├── workflow-audit-template.md
│   └── proyecto-inicio-checklist.md
└── sistema-leads-pablocuevas/
    ├── README.md
    ├── adr/
    │   └── ADR-201-firebase-n8n-trigger.md
    ├── workflows/
    │   └── sistema-leads-enriquecido-audit.md
    └── gdpr-data-mapping.md
```

**ADR-005 creado:** Workflow Audit Template — principio universal, serie 000. Complementa ADR-004 (infraestructura) cubriendo calidad interna del workflow.

**Documentos generados:**
- `workflow-audit-template.md` — template vacío para futuros workflows
- `proyecto-inicio-checklist.md` — template pre-construcción (versión Bloque 3, evoluciona con cada bloque)
- `sistema-leads-enriquecido-audit.md` — audit completado del workflow actual
- `gdpr-data-mapping.md` — inventario PII del sistema de leads

**Convención ADRs de proyecto establecida:**
- Serie 200 → Proyecto Ancla 1
- Serie 300 → Proyecto Ancla 2
- ADR-200 corregido a ADR-201 en encabezado interno

**Deuda técnica anotada:** escritura autónoma de Code nodes — conceptos sólidos, construcción sin guía pendiente de consolidar en Bloques 4-5.

---

**Estado del Bloque 3:** ✅ Completado — 6 sesiones reales


## 18/03 ✅ Bloque 4 — Sesiones 1 y 2: Lógica condicional + Routing

---

### Sesión 1 — IF node

Workflow extendido: `[enriquecedor-clasificador] → [IF: prioridad === 'alta'] → TRUE: notificador-telegram / FALSE: marcador-cola`

**Bug detectado y corregido antes de construir el IF:**

El `enriquecedor-clasificador` no tenía el campo `prioridad` en su output — lo que existía era `accion_recomendada`, que es un campo diferente. El IF buscaba `$json.prioridad` y encontraba `undefined`, por lo que siempre evaluaba FALSE silenciosamente sin lanzar error.

Segunda causa del bug: `prioridad` se calculaba usando `abstract.accion_recomendada`, que no existe en el objeto de Abstract — ese campo lo construye el propio nodo más abajo como `accion`. La corrección fue mover el cálculo de `prioridad` después del bloque `if/else` que calcula `accion`, y referenciar `accion` en lugar de `abstract.accion_recomendada`.

**Concepto internalizado — modo expresión en el IF node:**

El campo izquierdo (`{{ $json.prioridad }}`) requiere `fx` obligatoriamente. Sin él, N8N compara el string literal `"{{ $json.prioridad }}"` contra `"alta"` — nunca matchea.

El campo derecho (`alta`) es un string fijo. En este caso concreto, `fx` activado o desactivado da el mismo resultado. El `fx` en el campo derecho importa solo cuando el valor de comparación es dinámico (`{{ $json.otro_campo }}`).

**Concepto internalizado — pinned data:**

El nodo de Telegram mostraba en OUTPUT un item de una ejecución anterior en lugar del dato real recién llegado. Causa: el banner "This data is pinned for test executions" indica que N8N usa un resultado guardado como referencia para testear nodos downstream sin ejecutar todo el flujo. Solución: "Unpin" en el banner. Sin hacer Unpin, el output visual miente — el workflow funciona correctamente pero lo que se ve no corresponde a la ejecución actual.

**Concepto internalizado — Publish vs ejecución manual:**

El toggle Publish controla únicamente si el workflow responde a triggers externos automáticos (webhook desde formulario, Schedule programado). Ejecutar desde el editor no requiere Publish — el propio usuario actúa como trigger. Thunder Client sí requiere Publish porque simula un trigger externo real.

**Validación superada:** lead con `@rossi.com` → rama TRUE → Telegram. Lead con `@gmail.com` → rama FALSE → `marcador-cola`. Execution log muestra nodos grises en la rama que no corrió.

---

### Sesión 2 — Switch node

**Problema identificado en el diseño IF:**

La rama FALSE mezclaba dos semánticas completamente distintas: leads que necesitan revisión humana (`revisar_manualmente`) y leads en cola de procesamiento. Sin distinción, un lead urgente que necesita atención humana espera indefinidamente en la misma fila que un lead ordinario. El IF no puede expresar esta diferencia — solo tiene dos salidas.

**Switch node construido:**

```
[enriquecedor-clasificador] → [Switch: accion_recomendada]
        ↓ procesar                ↓ revisar_manualmente        ↓ descartar
[notificador-telegram]      [alerta-revision-humana]      [log-descartado]
```

Tres ramas con criterio propio. Cada salida tiene semántica específica — no son variantes de "sí" y "no", son rutas de negocio distintas.

**Concepto internalizado — IF vs Switch:**

IF es binario: dos caminos. Switch es un router: N caminos, cada uno con criterio propio. La elección no es de complejidad — es de semántica. Cuando las ramas representan acciones cualitativamente diferentes, Switch hace visible en el canvas el diseño de negocio.

**Decisión de diseño documentada — rama descartar:**

Campos mínimos a registrar para un lead descartado:
- `email` — identificación del registro
- `razon` — auditoría operativa (¿el clasificador es demasiado agresivo? ¿cuántos descartes por tipo?)
- `timestamp` — base para calcular cuándo borrar
- `accion: "descartado"` — estado del sistema

Nombre y apellido son PII sin propósito real en el contexto de descarte — minimización aplicada. Retención máxima: 2 meses con borrado automático. Sin `razon`, no hay posibilidad de reconstruir qué pasó si un prospecto real se queja de no haber recibido respuesta.

**Validación superada:** tres requests con Thunder Client — `@rossi.com` → `procesar` → Telegram, `@gmail.com` → `revisar_manualmente` → `alerta-revision-humana`, email sin `@` → `descartar` → `log-descartado`. Las tres ramas funcionaron correctamente.

---
## 22/03 ✅ Bloque 4 — Sesiones 3 y 4: Patrón de Fallback + Consolidación

---

### Sesión 3 — Patrón de fallback de 3 niveles

**Sistema construido:**
```
Webhook → procesador-body → HTTP Abstract → enriquecedor-clasificador
    → IF (confianza Abstract)
        TRUE  → Switch nivel1 → procesar / revisar_manualmente / descartar
        FALSE → nivel2-reglas-propias → IF nivel2 → revisar_manualmente / descartar
```

**Conceptos internalizados:**

Distinción crítica establecida: "Abstract respondió" ≠ "Abstract respondió con confianza". El IF no detecta errores HTTP — detecta calidad insuficiente de respuesta. Tres condiciones en AND: `abstract_status === "deliverable"` + `abstract_smtp_valid === true` + `abstract_live_site !== null`. El caso `rossi.com` lo demostró: score 0.99 con todo `email_domain` en null — contradicción que el umbral captura correctamente.

Decisión de diseño — campos planos vs objetos anidados: en lugar de pasar `email_deliverability` completo, se extraen solo los tres campos necesarios (`abstract_status`, `abstract_smtp_valid`, `abstract_live_site`). Minimización GDPR aplicada — `email_sender` con `first_name`/`last_name` de Abstract descartado, PII duplicada sin propósito.

Nivel 2 con reglas propias independientes de Abstract:
- DESCARTAR: `esDesechable === true` OR `nivelRiesgo === "high"`
- REVISAR_MANUALMENTE: todo lo demás

Decisión arquitectónica: el Nivel 2 nunca procesa automáticamente. Un dominio que Abstract no puede verificar merece ojos humanos — Posición B elegida y defendida. `abstract_live_site !== null` como condición adicional para `procesar` en Nivel 2 eliminada — simplifica el sistema sin perder criterio.

`nivel_resolucion` y `razon_nivel2` — propósito real: trazabilidad operativa, no solo debugging. Permiten métricas (`WHERE nivel_resolucion = 'nivel2'`), auditoría de negocio y visibilidad continua del comportamiento del sistema.

Tres mensajes Telegram diferenciados:
- ✅ Nivel 1 procesar — confianza alta, acción automática
- 🔎 Nivel 1 revisar_manualmente — Abstract dudó
- ⚠️ Nivel 2 revisar_manualmente — Abstract no pudo verificar

**Validación con 5 casos reales:**
- `test@anthropic.com` → Nivel 1 procesar ✅
- `pablo.cuevas.automation@gmail.com` → Nivel 1 revisar_manualmente ✅
- `test@mailinator.com` → Nivel 1 descartar ✅
- `paolo.rossi@rossi.com` → Nivel 2 revisar_manualmente ✅
- `test@guerrillamail.com` → Nivel 1 descartar ✅ (Abstract lo conoce — correcto)

---

### Sesión 4 — Consolidación

**IF vs Switch:** IF para dos caminos excluyentes. Switch cuando las ramas representan acciones de negocio cualitativamente distintas — no solo "más de dos caminos".

**Code node en Nivel 2:** lógica combinada compleja (`AND`, `OR`, condiciones encadenadas) expresada en un solo lugar. Encadenar IFs de N8N sería más frágil y menos legible.

**Nodo más crítico del sistema:** `procesador-body` — punto de entrada único de datos reales. Abstract puede fallar y hay fallback. `procesador-body` no tiene fallback — un error ahí viaja silenciosamente por todo el flujo.

---
## 24/03 - 25/03 ✅ PA1 — Sesiones 1 y 2 + Triangulación completa

---

### Sesión 1 — Schema PostgreSQL (diseño conceptual)

Primera vez trabajando con conceptos de bases de datos relacionales
construidos desde el sistema propio, no desde un tutorial abstracto.

**Conceptos internalizados:**
- Tabla = hoja de cálculo que no se rompe con escala
- Columna = encabezado con tipo de dato fijo
- Fila = un registro concreto
- Dos tablas porque un lead tiene datos fijos (tabla leads) y una
  línea de tiempo de eventos (tabla eventos) — relación uno a muchos
- PII pseudoanonimizado: los campos de eventos no son PII solos,
  pero vinculados a un lead identificable se tratan igual

**Decisiones tomadas:**
- Deduplicación por `email + servizio` — misma persona puede pedir
  dos servicios distintos, eso no es duplicado
- Política de retención: descartados 60d / revisión 6m / procesados 12m
  — cada período tiene justificación de negocio documentada

---

### Sesión 2 — Diagrama de sistema v1.0

Creado `diagrama-sistema.md` con los 4 bloques del sistema completo:
entrada, procesamiento, salida y reporte semanal.

**Decisiones clave:**
- Guardado en PostgreSQL ANTES de cualquier procesamiento — si todo
  falla después, el lead ya está registrado
- Reporte semanal: Schedule lunes 8am → query PostgreSQL → Telegram
- Deuda técnica del nodo de descarte identificada y documentada

---

### Triangulación — primera triangulación formal del proyecto

Generado `briefing-triangulacion.md` y compartido con Gemini y DeepSeek.

**Rol de cada IA en la triangulación:**
- Claude: estructurar, documentar, coherencia de diseño a largo plazo
- Gemini: cuestionar trade-offs, resiliencia, valor de negocio
- DeepSeek: análisis técnico crudo, síntesis entre posturas

El patrón correcto: diseñar con criterio propio → triangular para
detectar puntos ciegos → sintetizar lo mejor de cada perspectiva.

---

### Diagrama v2.0 → v3.0 — evolución tras triangulación

Gemini y DeepSeek identificaron que el diagrama v1.0 era un
"Happy Path" — buen diseño de negocio pero sin caminos de error.

**Race Condition identificada por Gemini:**
Doble click → dos inserts simultáneos → ambos pasan deduplicación
porque PostgreSQL aún no tiene el registro. Solución: UNIQUE
CONSTRAINT sobre (email, servizio) — el guardián vive en la DB,
no en N8N.

**Debate sobre deduplicación:**
- Claude propuso: SELECT → IF en N8N → INSERT/UPDATE (Read-Modify-Write)
- Gemini detectó: ese patrón reintroduce la race condition
- DeepSeek sintetizó: ON CONFLICT DO NOTHING — atómico, sin error
  23505, rowCount como señal de negocio

**Decisión final:** ON CONFLICT DO NOTHING.
rowCount = 1 → lead nuevo. rowCount = 0 → duplicado, registrar y parar.

**Aprendizaje de arquitecto:** Claude tuvo la pregunta de negocio
correcta pero el patrón técnico frágil. Gemini tuvo la solución
técnica correcta. DeepSeek sintetizó ambos. Sin triangulación,
habría quedado un sistema con race condition documentada.

**Diagrama v3.0 — 6 capas con responsabilidad única:**

1. **Seguridad** — firma HMAC, rechaza requests inválidos
2. **Persistencia inicial** — UPSERT atómico, deduplicación, estado pendiente
3. **Procesamiento** — Abstract + clasificación Nivel 1 / Nivel 2
4. **Notificación** — Telegram, extensible a email automático al lead
5. **Registro de eventos** — trazabilidad completa con JSONB
6. **Mantenimiento** — reporte semanal, retención GDPR, health check, backup

**Principio rector:** "Un lead que falla en cualquier capa queda
registrado. Nunca se pierde en el vacío."

---

### Schema v2.0 — rediseño tras triangulación

**Problema identificado en schema v1.0:**
La tabla eventos tenía columnas fijas (`abstract_status`,
`abstract_smtp_valid`) — vendor lock-in real. Si Abstract cambia,
migración de DB obligatoria.

**Solución — JSONB:**
Tabla eventos rediseñada con 5 columnas fijas:
`id, lead_id, tipo_evento, timestamp_evento, detalle (JSONB)`

El campo `detalle` guarda los datos variables de cada evento como
objeto JSON. Si Abstract se reemplaza, el JSON cambia pero el
schema no. Cero migración de base de datos.

**GDPR v2.0 actualizado:**
- PostgreSQL como fuente de verdad operativa (no Firebase)
- Firebase como receptor inicial — primera red de seguridad
- Logs de N8N con retención máxima 30 días
- Todas las deudas con bloque de resolución asignado

---

### Decisión de negocio — ventana de tiempo en deduplicación

**Gap identificado por Pablo — las tres IAs lo habían pasado por alto:**

Con el diseño anterior — UNIQUE CONSTRAINT permanente — el cliente
que vuelve un mes después con el mismo email y servicio queda
bloqueado silenciosamente. Pablo no lo ve en Telegram, el cliente
no recibe respuesta. Se pierde un lead real.

**Solución — ventana de tiempo de 24 horas:**
```
rowCount = 0 → consulta timestamp_creacion
    → < 24h → duplicado real (doble click) → registrar y parar
    → > 24h → cliente legítimo que vuelve → actualizar y procesar
```

24 horas cubre el doble click y reenvíos accidentales del mismo día.
Cualquier contacto posterior se trata como lead nuevo legítimo.

**Nuevo tipo de evento:** `lead_reactivado` — cuando un cliente
vuelve después de 24 horas con el mismo email y servicio.

**Nuevo mensaje Telegram:** notificación específica para leads
reactivados con referencia al primer contacto original.

**Diagrama v4.0** actualizado con esta decisión.

---

### Aprendizaje sistémico — límites de la triangulación

**La triangulación es fuerte en técnico, débil en negocio.**

Las IAs convergemos rápido en soluciones técnicas correctas —
race conditions, vendor lock-in, JSONB, GDPR. Tenemos formación
técnica sólida y los patrones son conocidos.

Somos malos para detectar gaps de negocio porque no tenemos
clientes reales. La pregunta "¿qué pasa con el cliente que vuelve
un mes después?" no viene de conocimiento técnico — viene de
pensar en el cliente real.

**Patrón documentado:**
- Triangulación → efectiva para: arquitectura técnica, compliance,
  patrones de base de datos, seguridad
- Triangulación → débil para: comportamiento real de clientes,
  casos de uso que solo aparecen con experiencia de mercado

**Las preguntas de negocio tienen que venir del arquitecto.**
Las IAs no las van a encontrar solas.

---

### Decisiones pendientes de formalizar

- **ADR-104**: método de triangulación con múltiples IAs como
  proceso estándar de validación de diseño
- **Template de diagrama**: base reutilizable para futuros proyectos
- **6 documentos por proyecto**: ADR + diagrama + audit + checklist
  + README + runbook — estándar de entrega profesional

---

**Estado al cierre:**
- `diagrama-sistema-v4.md` ✅
- `schema-postgresql-draft.md` v2.0 + `lead_reactivado` ✅
- `gdpr-data-mapping.md` v2.0 ✅
- `briefing-triangulacion.md` ✅
- Push a GitHub ✅

**Siguiente sesión:** PA1 Sesión 3 — construcción real en N8N.
El diseño está validado. No hay más diseño previo pendiente.

*Documento: DAILY-LOG-FASE-1.md*  
*Fase activa: Fase 1 — Constructor de Sistemas de Automatización*  
*Iniciado: 25 enero 2026*  
*Bloque activo: 0 — Preparación N8N*
