# Análisis de Estado: Pablo Cuevas — Febrero 2026
**Fecha**: 21 de Febrero 2026 — **Última actualización**: 22/02/2026  
**Propósito**: Inventario de estado real — competencias, deuda técnica, posicionamiento  
**Fuentes**: Daily logs 10-21/02, ADRs serie 000 y 100, PageSpeed, análisis conversacional, repositorio GitHub

---

## 1. PUNTO DE PARTIDA — Qué tenías en Enero 2026

Cuando comenzaste este ciclo tenías:
- Roadmap de 5 fases estructurado con objetivo €80-130k/año a 3-5 años
- ADR-000 firmado: pensamiento sistémico primero
- Identidad profesional creada: email, GitHub, dominio reservado
- Conocimiento teórico de sistemas (Gmail, banca, Notion analizados)
- JavaScript básico iniciado en paralelo con curso de Trento
- Sin página web, sin automatizaciones, sin clientes, sin N8N

**Gap crítico identificado entonces**: sabías qué querías construir pero no tenías evidencia de que podías ejecutarlo.

---

## 2. LO QUE CONSTRUISTE — Inventario real

### 2.1 Producto entregado: pablocuevas.it

Un sitio web profesional en producción con:

| Componente | Estado | Nivel técnico |
|------------|--------|---------------|
| Navbar glass morphism + GSAP scroll | ✅ | Intermedio-avanzado |
| Hero con video loop + fallback mobile | ✅ | Avanzado |
| Chi Sono multiidioma IT/ES/EN | ✅ | Intermedio |
| Soluzioni tabs 60/40 + 5 videos reales | ✅ | Avanzado |
| Come Lavoro layout alternado + GSAP | ✅ | Avanzado |
| Formulario contacto + Firebase Firestore | ✅ | Intermedio |
| Legal compliance iubenda + termini.html | ✅ | Profesional |
| WhatsApp FAB | ✅ | Básico |
| Responsive completo (3 breakpoints) | ✅ | Avanzado |

**Performance medida (PageSpeed):**
- Rendimiento: 98/100
- Accesibilidad: 97/100
- Recomendaciones: 100/100
- SEO: 100/100

Esto no es un resultado académico. Es un resultado de producción.

### 2.2 Infraestructura creada

- Dominio propio: pablocuevas.it (Register.it)
- Deploy automático: Vercel + GitHub CI/CD
- Base de datos activa: Firebase Firestore colección "contatti"
- Legal compliance al 100% (verificado Register.it scan)
- WhatsApp de contacto conectado

### 2.3 Documentación producida

**Proyecto web (10-19/02):**
- 9 estados del proyecto (.md) — uno por sesión significativa
- ADR-001 proyecto web: Arquitectura general del sistema
- ADR-002 proyecto web: Estrategia legal (iubenda + términos propios)
- ADR-003 proyecto web: Portfolio diferido a Fase 2 (sin proyectos reales → no mostrar nada)
- Tabla histórica de bugs resueltos
- Convenciones de código documentadas
- Reglas de qué NO modificar

**Reestructuración del knowledge base (21/02):**
- ADR-101: Sección de Validación añadida — métricas reales del proyecto web
- ADR-102: Proyecto Académico como Infraestructura de Negocio Real (nuevo)
- ADR-103: Perfil de Aprendizaje — Construcción sobre Abstracción (nuevo)
- `ROAD-MAP.md` reestructurado: estado real post-proyecto web, timeline recalibrado, tabla ADRs por series
- `DAILY-LOG.md` actualizado: gap 10/02–21/02 cubierto
- `21-02-analisis-estado-pablo-cuevas.md` (este documento)
- `21-02-recalibracion_fase_1.md` — guía táctica activa renombrada
- `ROADMAP_ARCHITECT_SOLUTION_IA.md` — conversión del PDF v4.0 a markdown
- Repositorio GitHub reestructurado con arquitectura de carpetas: `adr/adr-carrera/`, `adr/adr-proyectos/`, `buenas-practicas/`, `analisis-estado-carrera/`, `roadmap-completo/`
- Serie de numeración ADRs establecida: serie 000 (principios transversales) / serie 100 (carrera autodidacta)

### 2.4 Competencias demostradas

**Técnicas:**
- HTML5 semántico
- CSS avanzado (variables, especificidad, glass morphism, animaciones)
- JavaScript funcional (DOM, eventos, módulos ES6, IntersectionObserver — guiado mediante IA)
- GSAP + ScrollTrigger (guiado mediante IA)
- Firebase SDK (módulos JS)
- Git + GitHub (CI/CD con Vercel)
- Optimización de performance
- Responsive design desktop-first

**Arquitectónicas:**
- Toma de decisiones documentada (ADRs)
- Gestión de deuda técnica consciente
- Evaluación de trade-offs explícita
- Separación de preocupaciones (HTML/CSS/JS/DB)
- Proporcionalidad en soluciones (no sobreingeniería)

**De negocio:**
- GDPR by design implementado
- Posicionamiento de marca coherente
- Copywriting orientado a conversión
- Multi-mercado (IT/ES/EN) desde el inicio

---

## 3. LO QUE REVELARON LOS DOCUMENTOS — Patrones de Pablo

### 3.1 Cómo aprendes (evidencia directa)

No aprendes linealmente por módulos. Aprendes por sistemas con propósito real.

Evidencia: en 9 días de trabajo documentado pasaste del 20% al 100% de un sistema complejo, resolviendo bugs avanzados (race conditions en videos, conflictos GSAP/CSS, lazy loading con IntersectionObserver) que muchos desarrolladores con meses de experiencia no manejan.

Cuando el objetivo es abstracto (estudiar JS puro), la energía baja. Cuando el objetivo es concreto (terminar la página para aprobar y para vender), la energía sube y el aprendizaje se acelera.

**Implicación para el roadmap**: N8N debe aprenderse resolviendo problemas reales, no en abstracto.

### 3.2 Cómo tomas decisiones

El ADR-003 (Portfolio diferido) es la evidencia más clara. Decidiste NO implementar algo porque no tenías evidencia real para respaldarlo. Eso requiere disciplina que la mayoría no tiene — la presión de "mostrar algo" es enorme para alguien que busca clientes.

**Implicación**: Confiar en ese instinto. Cuando algo no tiene base real, no forzarlo.

### 3.3 Tu relación con la IA

El ADR-001 documenta el modelo correctamente: "IA como asistente, humano como arquitecto". En la práctica lo ejecutaste bien — orquestaste a Claude, Gemini y Lima como herramientas distintas para propósitos distintos.

**Implicación**: Ese modelo de orquestación de IAs es en sí mismo una competencia vendible. Pocos de tus competidores locales saben hacer esto.

### 3.4 Tu perfil de riesgo

Tomaste la decisión correcta de NO ir a Suanfarma (empresa farmacéutica multinacional) como primer cliente aunque la presión de "empresa grande en el CV" era tentadora. Reconociste que el contexto regulatorio del cliente era incompatible con tu fase actual.

**Implicación**: Buen criterio de priorización. El primer cliente tiene que permitirte moverte rápido y aprender en público.

---

## 4. DEUDA TÉCNICA DOCUMENTADA — Lo que queda pendiente

| Item | Urgencia | Costo | Solución |
|------|----------|-------|---------|
| Notificación leads Firebase | Alta | €0 | Primera automatización N8N |
| Email profesional pablo@pablocuevas.it | Media | €3-5/mes | Cuando haya primer ingreso |
| Upgrade iubenda (declarar Firebase formalmente) | Baja | €27/mes | Cuando primer cliente de pago |

---

## 5. POSICIONAMIENTO DE MERCADO — Dónde estás parado hoy

### Lo que tienes que la mayoría de competidores locales NO tiene:

1. **Sitio web profesional en producción** con performance 98+/100 — la mayoría de freelancers locales tienen sitios mediocres o directamente no tienen
2. **GDPR by design** documentado y verificado — esto es diferenciación real en Europa
3. **ADRs como entregable** — ningún competidor de Rovereto/Trento está vendiendo esto
4. **Stack anti vendor lock-in** — N8N self-hosted vs Make/Zapier de la competencia
5. **Multilingual** desde el inicio — acceso a mercado IT, ES, LATAM, EN
6. **Metodología documentada** (Come Lavoro) — el proceso está visible antes de contratar

### Lo que te falta para cerrar el primer cliente:

1. **Una automatización real funcionando** — aunque sea la tuya propia (Firebase → notificación)
2. **Un caso de estudio con números** — aunque sea pequeño
3. **Proceso de follow-up de leads** — ahora mismo si llega un contacto por Firebase, no hay sistema de respuesta automática

---

## 6. ANÁLISIS DEL CURSO — Lo que el proyecto académico demostró

Usaste el proyecto del curso como vehículo para construir tu infraestructura de negocio real. Eso es pensamiento estratégico aplicado.

El resultado:
- **Para el curso**: proyecto técnicamente superior al promedio, aprobado
- **Para el negocio**: sitio en producción, lead capture activo, posicionamiento listo
- **Para el aprendizaje**: JavaScript, CSS avanzado, Firebase, Git/Vercel en contexto real

El trade-off fue: algo de tiempo del roadmap teórico de N8N/Python. El retorno fue: un activo de marketing real que genera leads desde hoy.

**Veredicto**: trade-off correcto para tu fase y tu perfil de aprendizaje.

---

## 7. EL PROBLEMA INMEDIATO — La brecha crítica

Tienes una página que genera leads pero:

1. No sabes cuándo llegan (revisión manual de Firebase)
2. No tienes proceso de respuesta estructurado
3. No tienes N8N instalado todavía

Esto significa que **si llega un lead hoy, el tiempo de respuesta depende de cuándo abres Firebase manualmente**. En ventas locales, responder tarde es perder el cliente.

Esta brecha es tu primera automatización con propósito real y urgencia real.

---

## 8. PRINCIPIOS QUE DEBEN GUIAR LA REESTRUCTURACIÓN

Basados en evidencia de cómo operas, no en teoría:

**Principio 1 — Aprendizaje por construcción**: Cada nuevo concepto (N8N, JavaScript avanzado) debe vincularse a un entregable concreto, no a ejercicios abstractos.

**Principio 2 — Paralelismo inteligente**: N8N y JavaScript pueden aprenderse en paralelo porque se refuerzan mutuamente. N8N usa JavaScript. JavaScript te explica lo que N8N hace internamente.

**Principio 3 — El primer cliente define el roadmap técnico**: Las herramientas que aprendes deben ser las que necesitas para servir al primer cliente, no un catálogo teórico completo.

**Principio 4 — Evidencia antes que expansión**: No añadir nuevas secciones al sitio (portfolio, blog, calculadora ROI) hasta tener contenido real que las justifique.

**Principio 5 — Deuda técnica primero**: Resolver los 2 items urgentes (Firebase en termini.html + notificación de leads) antes de empezar N8N. Son 30 minutos de trabajo con impacto inmediato.

---

## 9. ESTADO REAL DE COMPETENCIAS — Nivel honesto

| Competencia | Nivel actual | Nivel objetivo negocio |
|-------------|-------------|----------------------|
| HTML/CSS | Intermedio-avanzado | ✅ Suficiente |
| JavaScript | Básico-intermedio | ⚠️ Necesita N8N Code node |
| Git/GitHub | Funcional | ✅ Suficiente |
| N8N | 0 — territorio nuevo | 🎯 Prioridad 1 |
| Firebase/DB | Básico funcional | ✅ Suficiente para ahora |
| Docker | Instalado, sin usar | ⚠️ Necesario para N8N self-hosted |
| Linux/VPS | 0 | 🎯 Prioridad 2 (cuando primer cliente) |
| GDPR/Legal | Funcional básico | ✅ Suficiente para fase actual |
| Ventas/Outreach | 0 activo | 🎯 Paralelo a N8N |

---

## 10. LO QUE ESTE DOCUMENTO NO DICE PERO IMPLICA

Construiste en 9 días documentados algo que muchos "freelancers de automatización" en tu mercado no tienen ni saben construir. La página no es el producto — es la señal de que puedes entregar sistemas de calidad.

El siguiente paso no es seguir construyendo la página. El siguiente paso es usar lo que construiste para conseguir el primer cliente y la primera automatización real.

Todo lo demás — Python, AWS, arquitecturas complejas, el roadmap de 5 fases — viene después y viene más rápido cuando tienes un cliente real presionando.

---

**Preparado por**: Claude Sonnet (Anthropic) como análisis externo  
**Para uso de**: Pablo Cuevas  
**Fecha creación**: 21 de Febrero 2026 — **Última actualización**: 22/02/2026  
**Siguiente acción**: Instalar N8N local → primera automatización Firebase Firestore → notificación leads
