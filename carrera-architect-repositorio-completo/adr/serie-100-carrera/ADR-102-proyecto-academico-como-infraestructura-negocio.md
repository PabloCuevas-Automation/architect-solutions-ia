# ADR-102: Proyecto Académico como Infraestructura de Negocio Real

**Fecha**: 10 de febrero 2026  
**Estado**: ✅ Aceptada y VALIDADA  
**Autor**: Pablo  
**Tipo**: Estratégica — afecta posicionamiento de mercado y velocidad de entrada a Fase 1  
**Contexto**: Curso de Desarrollo Web (Trento) — entrega final obligatoria  
**Validación**: 21 de febrero 2026

---

## Resumen Ejecutivo

En el contexto del curso de desarrollo web patrocinado por el gobierno de Trento, enfrentando la necesidad de construir un proyecto final académico, **decidí que el proyecto no sería un ejercicio de práctica sino infraestructura de negocio real**: mi sitio web profesional de servicios de automatización IA, desplegado en producción con dominio propio, base de datos activa, compliance GDPR verificado y performance medida.

Esta decisión aceptó mayor complejidad y ambición en el entregable académico a cambio de eliminar el tiempo que habría requerido construir esa infraestructura después del curso.

---

## Contexto

### La bifurcación

Ante un proyecto académico obligatorio, hay exactamente dos caminos:

**Camino A**: Construir lo mínimo que aprueba el curso. Un sitio funcional, quizás con un template, suficiente para la nota. Tiempo: 3-5 días.

**Camino B**: Construir el sitio que necesito para el negocio, usando el curso como estructura de soporte (profesor disponible, feedback incluido, deadline que genera urgencia). Tiempo: misma ventana, mayor densidad de trabajo.

La pregunta arquitectónica real no era técnica — era de optimización de recursos. El curso ofrecía un recurso escaso: tiempo de un profesor con conocimiento del stack, en un período en el que de todas formas tenía que construir algo.

### Por qué el momento importaba

El roadmap ya tenía previsto construir un sitio web profesional para servicios de automatización. Sin el curso, ese trabajo habría llegado en algún momento de la Fase 1, sin soporte externo, sin deadline que genere urgencia, y consumiendo tiempo que podría ir a N8N.

Usar el curso para construirlo significa que llego a Fase 1 con infraestructura de marketing ya operativa, no pendiente.

---

## Alternativas Consideradas

### Opción A: Mínimo viable académico
Sitio de práctica con template, funcional, suficiente para aprobar. Sin dominio propio, sin backend real, sin compliance.

**Por qué NO**: Desperdicia el recurso del profesor. Genera deuda de trabajo que hay que pagar después, fuera del contexto de apoyo del curso. El costo de oportunidad supera ampliamente el ahorro de complejidad.

---

### Opción B: Sitio profesional completo usando el curso como estructura ⭐ ELEGIDA
Construir pablocuevas.it — sitio de servicios de automatización IA — como entregable del curso. Con dominio real, Firebase, compliance GDPR, performance optimizada, multiidioma.

**Por qué SÍ**: Un recurso (el curso) satisface dos necesidades simultáneamente. El tiempo invertido produce valor académico y valor de negocio. El profesor proporciona soporte en exactamente el momento en que se necesita.

---

## Decisión

**Usar el proyecto final del curso de Trento para construir pablocuevas.it como infraestructura de negocio real**, no como ejercicio académico. Esto significa:

- Estándares de producción, no de práctica
- Decisiones reales con trade-offs documentados
- Compliance real, no simulado
- Performance medida, no asumida
- Base de datos activa recibiendo datos reales

---

## Lo que se construyó — Inventario técnico

### Stack implementado
- **Frontend**: HTML5 + CSS3 + JavaScript ES6 + Bootstrap 5
- **Animaciones**: GSAP 3.14.1 + ScrollTrigger
- **Backend/DB**: Firebase Firestore (colección `contatti`)
- **Deploy**: Vercel + GitHub CI/CD automático
- **Dominio**: pablocuevas.it (Register.it)

### Funcionalidades en producción
- Navbar con glass morphism + animación GSAP al scroll
- Hero con video loop en desktop + fallback 9:16 en mobile
- Sección Chi Sono con sistema multiidioma IT/ES/EN (banderas)
- Sección Soluzioni con tabs 60/40 + 5 videos reales con lazy loading (IntersectionObserver)
- Sección Come Lavoro con 4 pasos en layout alternado + badge GDPR by design
- Formulario Contatti conectado a Firebase Firestore
- WhatsApp FAB flotante
- Responsive completo (breakpoints: 992px / 768px / 576px)

### Legal compliance
- Privacy Policy + Cookie Policy: iubenda (ID: 87386814, plan gratuito)
- Termini e Condizioni: documento propio (termini.html)
- Terceros declarados: Firebase, Vercel, Google Fonts, WhatsApp
- Verificación independiente: scan Register.it → 100% compliance

### Decisiones arquitectónicas documentadas
- ADR-001 proyecto web: Arquitectura general del sistema
- ADR-002 proyecto web: Estrategia legal (iubenda + términos propios)
- ADR-003 proyecto web: Portfolio diferido a Fase 2 (sin proyectos reales → no mostrar nada)

---

## ✅ VALIDACIÓN — 21 de febrero 2026

### Métricas objetivas (PageSpeed Insights — fecha: 19/02/2026)

| Métrica | Resultado |
|---------|-----------|
| Rendimiento | **98/100** |
| Accesibilidad | **97/100** |
| Recomendaciones | **100/100** |
| SEO | **100/100** |

### Estado de infraestructura

| Componente | Estado |
|------------|--------|
| Dominio pablocuevas.it | ✅ Live |
| Firebase Firestore | ✅ Activo — recibiendo leads |
| Vercel CI/CD | ✅ Deploy automático en cada push |
| Legal compliance | ✅ Verificado 100% (scan Register.it) |
| WhatsApp integrado | ✅ Funcionando |
| Performance mobile | ✅ Video 9:16 con fallback |

### Lo que la decisión logró

**Eliminó trabajo futuro**: El sitio que habría requerido 2-4 semanas de Fase 1 ya está hecho. Ese tiempo se libera para N8N.

**Generó evidencia técnica real**: 98/100 en PageSpeed no es un número de práctica — es evidencia de que se sabe construir sistemas con estándares de producción. Eso se puede mostrar a un cliente hoy.

**Validó el patrón de aprendizaje**: El ADR-103 (Perfil de Aprendizaje) nació directamente de la observación de cómo avanzó este proyecto. La construcción con propósito real genera aprendizaje más profundo que el estudio abstracto. Esta es la evidencia empírica.

**Creó el primer activo de captación**: El formulario conectado a Firebase está recibiendo datos ahora mismo. Cualquier lead que llegue antes de que exista la automatización de notificación existe ya en la base de datos.

### Deuda técnica generada (consciente)

| Item | Estado | Próximo paso |
|------|--------|-------------|
| Automatización notificación leads | ⚠️ Pendiente | Primera automatización N8N |
| Email profesional pablo@pablocuevas.it | ⏳ Diferido | Cuando primer ingreso |

La deuda es conocida, documentada y acotada. No es sorpresa — es trade-off aceptado conscientemente.

### Veredicto

**Decisión correcta con retorno superior al esperado.** El objetivo era tener un sitio profesional operativo. El resultado fue un sistema completo con métricas de producción, compliance verificado y pipeline de leads activo.

El costo fue mayor complejidad durante 9 sesiones de trabajo intensivo. El beneficio fue llegar a Fase 1 con infraestructura de negocio funcional en lugar de pendiente.

---

**Autor**: Pablo Cuevas  
**Fecha decisión**: 10 de febrero 2026  
**Fecha validación**: 21 de febrero 2026
