# Histórico: Fase 0 y Fase 0.5
## Pablo Cuevas — Enero / Febrero 2026

**Período**: 25 enero — 19 febrero 2026  
**Estado**: ✅ Completadas  
**Documento creado**: 25 de febrero 2026  
**Propósito**: Registro permanente de las fases fundacionales — de dónde viene el trabajo de Fase 1

---

## RESUMEN EJECUTIVO

En menos de 4 semanas, Pablo pasó de no tener experiencia técnica a tener:
- Un sitio web profesional en producción con 98/100 de performance
- JavaScript funcional validado por examen oficial
- Stack de desarrollo completo instalado y operativo
- Base de pensamiento arquitectónico documentada en ADRs
- Infraestructura de negocio real activa generando leads

El insight más importante de estas dos fases: **el aprendizaje por construcción de sistemas con propósito real es significativamente más efectivo que el estudio en abstracto**. Este patrón, descubierto en Fase 0.5, se convirtió en el principio rector de toda la carrera.

---

## FASE 0: REPROGRAMACIÓN MENTAL
**Duración**: 25-28 enero 2026 (4 días)  
**Objetivo**: Cambiar el marco mental antes de tocar código

### Qué se hizo

**Análisis de sistemas cotidianos**
Análisis de Gmail, banca online y Notion como sistemas. Para cada uno: flujo de datos, ubicación física de los datos, modelo de negocio, puntos de fallo. Primer ejercicio de pensar en sistemas, no en features.

**Inmersión en ADRs**
Lectura y análisis de 5 ADRs reales de proyectos open source (AWS, Spotify, IBM). Identificación de patrones: contexto, opciones consideradas, trade-offs aceptados. Comprensión de que la arquitectura es fundamentalmente sobre decisiones, no sobre código.

**Data Privacy y GDPR**
Fundamentos de GDPR. Identificación de PII en 10 servicios propios. Audit personal de compliance. Creación del template COMPLIANCE.md reutilizable. Primer ADR sobre manejo de PII en proyectos futuros.

**Decisión fundacional documentada**
Escritura del ADR-100: Pensamiento Sistémico Primero. La decisión más importante de toda la carrera: transitar como arquitecto (quien decide bien) y no como developer (quien ejecuta bien). Documento firmado con compromiso explícito.

### Decisiones tomadas en Fase 0

| Decisión | ADR | Resultado |
|----------|-----|-----------|
| Pensamiento sistémico antes que skills verticales | ADR-100 | ✅ Validado — guía toda la carrera |
| Usar ADRs como herramienta de documentación | ADR-000 serie | ✅ Activo en todos los proyectos |
| Naming conventions kebab-case | ADR-003 | ✅ En uso en todo el repositorio |

### Output de Fase 0
- ADR-100 firmado (compromiso personal documentado)
- Template COMPLIANCE.md creado
- Framework mental de pensamiento sistémico activo
- 3 sistemas cotidianos analizados como arquitecto

---

## FASE 0.5: PRERREQUISITOS TÉCNICOS
**Duración**: 28 enero — 19 febrero 2026 (~3 semanas)  
**Objetivo**: Stack técnico operativo + JavaScript funcional + proyecto real en producción

### Por qué existió esta fase

El roadmap original saltaba directamente a SQL y Python. Durante el análisis de prerrequisitos se descubrió que sin terminal, Git, Node.js y JavaScript básico, el 80% del roadmap habría sido bloqueante desde el primer día. Fase 0.5 no estaba en el plan original — fue una decisión arquitectónica de no construir sobre base inestable.

### Stack instalado y verificado

| Herramienta | Versión | Fecha | Uso |
|-------------|---------|-------|-----|
| Git | 2.52.0 | 28/01 | Control de versiones |
| Node.js | v24.13.0 | 28/01 | Ejecución JS local |
| PostgreSQL | 18.1 | 28/01 | Base de datos (preparación Fase 1) |
| Docker | 29.1.3 | 28/01 | Contenedores (N8N en Fase 1) |
| VS Code | — | 28/01 | Editor principal |

### JavaScript aprendido

**Conceptos dominados en contexto real:**
- Variables (let, const), tipos de datos, operadores
- Objetos y objetos anidados, acceso dinámico por corchetes
- Arrays y métodos críticos: map, filter, find, reduce, forEach
- Funciones, arrow functions, scope
- DOM, eventos, módulos ES6
- IntersectionObserver (lazy loading)
- async/await, fetch, manejo de errores con try/catch
- Template literals, optional chaining, spread operator
- Firebase SDK (módulos JS)
- GSAP 3.14.1 + ScrollTrigger

**Validación oficial**: Examen JavaScript del Corso Sviluppo Web (Trento) — ✅ Aprobado 16/02/2026

### El proyecto web — decisión estratégica crítica

El proyecto final del curso de Trento se convirtió en infraestructura de negocio real. Esta no fue la decisión original — fue una decisión arquitectónica tomada al reconocer que el mismo recurso (tiempo del curso) podía satisfacer dos necesidades simultáneamente: requisito académico + activo de marketing real.

**Documentada en ADR-102**: Proyecto Académico como Infraestructura de Negocio Real.

### pablocuevas.it — inventario técnico

**Stack implementado:**
- Frontend: HTML5 + CSS3 + JavaScript ES6 + Bootstrap 5
- Animaciones: GSAP 3.14.1 + ScrollTrigger
- Backend/DB: Firebase Firestore (colección `contatti`)
- Deploy: Vercel + GitHub CI/CD automático
- Dominio: pablocuevas.it (Register.it)

**Funcionalidades en producción:**
- Navbar con glass morphism + animación GSAP al scroll
- Hero con video loop en desktop + fallback 9:16 en mobile
- Sección Chi Sono con sistema multiidioma IT/ES/EN
- Sección Soluzioni con tabs 60/40 + 5 videos con lazy loading
- Sección Come Lavoro con 4 pasos + badge GDPR by design
- Formulario Contatti conectado a Firebase Firestore
- WhatsApp FAB flotante
- Responsive completo (breakpoints: 992px / 768px / 576px)

**Legal compliance:**
- Privacy Policy + Cookie Policy: iubenda (ID: 87386814)
- Termini e Condizioni: documento propio
- Terceros declarados: Firebase, Vercel, Google Fonts, WhatsApp
- Verificación: scan Register.it → 100% compliance

**Métricas de performance (PageSpeed Insights — 19/02/2026):**

| Métrica | Resultado |
|---------|-----------|
| Rendimiento | 98/100 |
| Accesibilidad | 97/100 |
| Recomendaciones | 100/100 |
| SEO | 100/100 |

### Decisiones tomadas en Fase 0.5

| Decisión | ADR | Impacto |
|----------|-----|---------|
| JavaScript > Python en Fase 1 | ADR-101 | Cambió el stack completo de Fase 1. N8N usa JS nativamente |
| Proyecto académico como infraestructura real | ADR-102 | pablocuevas.it en producción antes de terminar el plan |
| Perfil de aprendizaje: construcción sobre abstracción | ADR-103 | Principio rector de cómo se aprende cada tecnología nueva |

### Output de Fase 0.5

**Infraestructura activa:**
- pablocuevas.it live recibiendo leads reales en Firebase
- GitHub profesional con CI/CD configurado
- Stack completo instalado y operativo
- Email profesional: pablo.cuevas.automation@gmail.com

**Base técnica para Fase 1:**
- JavaScript sólido aplicado en contexto real
- Firebase integrado y funcionando
- Git/GitHub con flujo profesional
- Docker instalado (listo para N8N)
- Pensamiento arquitectónico activo (4 ADRs escritos)

---

## LO QUE ESTAS FASES REVELARON

### Patrón de aprendizaje descubierto (ADR-103)
Pablo no aprende linealmente por módulos. Aprende por sistemas con propósito real. En 9 días documentados pasó del 20% al 100% de un sistema complejo en producción, resolviendo bugs avanzados que muchos developers con meses de experiencia no manejan. Cuando el objetivo es abstracto, la energía baja. Cuando el objetivo es concreto y tiene propósito real, la energía sube y el aprendizaje se acelera.

**Implicación directa para el resto de la carrera**: cada tecnología nueva debe vincularse a un proyecto ancla con propósito real antes de empezar a aprenderla.

### Criterio de decisión bajo presión
El ADR-103 (Portfolio diferido) es evidencia de disciplina real. Se decidió NO implementar la sección de portfolio porque no había evidencia real para respaldarlo. La presión de "mostrar algo" para conseguir clientes era alta. La decisión correcta fue no forzarlo.

### Orquestación de IAs como competencia
Durante el proyecto web se orquestaron Claude, Gemini y Lima como herramientas distintas para propósitos distintos. Esa capacidad de orquestación es en sí misma una competencia vendible — pocos competidores locales saben hacer esto.

---

## DEUDA TÉCNICA HEREDADA A FASE 1

| Item | Estado | Solución |
|------|--------|---------|
| Notificación automática de leads Firebase | 🎯 Primera automatización N8N | Proyecto ancla Capa 1 |
| Email profesional pablo@pablocuevas.it | ⏳ Cuando primer ingreso | €3-5/mes |
| Upgrade iubenda (declarar Firebase formalmente) | ⏳ Cuando primer cliente | €27/mes |

---

## TIMELINE REAL

| Fecha | Hito |
|-------|------|
| 25/01 | ADR-100 firmado — decisión fundacional de carrera |
| 27/01 | GDPR fundamentos + audit personal completado |
| 28/01 | Stack completo instalado y verificado |
| 29/01 | Terminal + Git dominados + ADR-003 naming conventions |
| 02/02 | JavaScript variables, objetos, arrays completado |
| 10/02 | Inicio proyecto web (20% completado) |
| 11/02 | Chi Sono multiidioma + fix conflicto GSAP (40%) |
| 16/02 | ✅ Examen JavaScript aprobado + Soluzioni rediseñada (65%) |
| 18/02 | Firebase conectado + compliance legal implementado (80%) |
| 19/02 | ✅ pablocuevas.it 100% completado y en producción |
| 21/02 | Reestructuración completa del knowledge base + ADR-102, ADR-103 |

---

**Documento creado**: 25 de febrero 2026  
**Para uso de**: Pablo Cuevas  
**Siguiente fase**: Ver `fase-1-activa/fase-1-redefinida.md`
