# ADR-004: Production Infrastructure Template / Template Base de Infraestructura para Producción

**Date / Fecha**: 2026-03-01  
**Status / Estado**: Proposed / Propuesta  
**Type / Tipo**: Tactical / Táctica

---

## Context / Contexto

**EN**: When analyzing what an N8N automation system needs to be deliverable to a real client (not a local prototype), the workflow itself is only one piece of the complete system. A workflow without a server, without security, without backups, and without failure handling is not a product — it's a demo.

I need an architectural checklist that forces me to make conscious decisions about each layer of the system before I start building. This prevents the pattern of "I'll set up N8N nodes and figure out the rest later," which generates invisible technical debt and fragile systems.

This ADR emerges from the convergence between my Phase 1 roadmap (`fase-1-redefinida.md`, Layers 1-6) and the infrastructure layer analysis validated with two mentors (Claude and Gemini). Both independently arrived at the same fundamental structure.

**ES**: Al analizar qué necesita un sistema de automatización con N8N para ser entregable a un cliente real (no un prototipo local), el workflow en sí es solo una pieza del sistema completo. Un workflow sin servidor, sin seguridad, sin backups y sin manejo de fallos no es un producto — es una demo.

Necesito un checklist arquitectónico que me obligue a tomar decisiones conscientes sobre cada capa del sistema antes de empezar a construir. Esto evita el patrón de "pongo nodos en N8N y después veo lo demás", que genera deuda técnica invisible y sistemas frágiles.

Este ADR nace de la convergencia entre mi roadmap de Fase 1 (`fase-1-redefinida.md`, Capas 1-6) y el análisis de capas de infraestructura validado con dos mentores (Claude y Gemini). Ambos coinciden en la misma estructura fundamental.

---

## Alternatives / Alternativas

### Option A / Opción A: Build without checklist, learn as you go / Construir sin checklist, aprender sobre la marcha

- **Pros**:
  - **EN**: Maximum initial speed, less friction to start
  - **ES**: Velocidad inicial máxima, menos fricción al empezar
- **Cons / Contras**:
  - **EN**: Critical layers forgotten under delivery pressure, each project starts from zero, security and backups get postponed indefinitely
  - **ES**: Olvido capas críticas bajo presión de entrega, cada proyecto empieza de cero, la seguridad y los backups se posponen indefinidamente
- **Why NOT / Por qué NO**:
  - **EN**: Generates exactly the type of fragile systems I want to avoid — and it's what an automator does, not an architect.
  - **ES**: Genera exactamente el tipo de sistemas frágiles que quiero evitar — y es lo que hace un automatizador, no un arquitecto.

### Option B / Opción B: Use an external infrastructure framework (TOGAF, AWS Well-Architected) / Usar un framework de infraestructura externo

- **Pros**:
  - **EN**: Proven industry standards, exhaustive coverage, professional credibility
  - **ES**: Estándares de industria probados, cobertura exhaustiva, credibilidad profesional
- **Cons / Contras**:
  - **EN**: Excessive complexity for my current level, designed for teams and large companies, learning curve that blocks construction
  - **ES**: Complejidad excesiva para mi nivel actual, diseñados para equipos y empresas grandes, curva de aprendizaje que frena la construcción
- **Why NOT / Por qué NO**:
  - **EN**: Like using a surgeon's scalpel to cut bread — correct in theory, paralyzing in practice for Phase 1.
  - **ES**: Es como usar un bisturí de cirujano para cortar pan — correcto en teoría, paralizante en la práctica para Fase 1.

### Option C / Opción C: Own 6-layer template based on my roadmap / Template propio de 6 capas basado en mi roadmap - CHOSEN / ELEGIDA ⭐

- **Pros**:
  - **EN**: Aligned exactly with the 6 layers of `fase-1-redefinida.md`, simple enough to use on every project without friction, evolves with me — validated in production and refactored with real data
  - **ES**: Alineado exactamente con las 6 capas de `fase-1-redefinida.md`, lo suficientemente simple para usarlo en cada proyecto sin fricción, evoluciona conmigo — lo valido en producción y lo refactorizo con datos reales
- **Cons / Contras**:
  - **EN**: Doesn't cover enterprise scenarios (multi-region, horizontal scaling, microservice orchestration), may have blind spots only discovered in production
  - **ES**: No cubre escenarios enterprise (multi-región, escalado horizontal, orquestación de microservicios), puede tener puntos ciegos que solo descubriré en producción
- **Why YES / Por qué SÍ**:
  - **EN**: Solves today's real problem (delivering professional systems to clients in Rovereto/Trento) without adding accidental complexity. Blind spots are discovered empirically and documented as updates to this ADR.
  - **ES**: Resuelve el problema real de hoy (entregar sistemas profesionales a clientes en Rovereto/Trento) sin añadir complejidad accidental. Los puntos ciegos se descubren empíricamente y se documentan como actualizaciones de este ADR.

---

## Decision / Decisión

**EN**: Adopt a 6-layer checklist as mandatory verification before any system goes to production.

**ES**: Adoptar un checklist de 6 capas como verificación obligatoria antes de que cualquier sistema salga a producción.

**EN**: Each layer resolves a real failure category identified during Phase 1 design. The structure maps directly to the 6 synergistic layers of my roadmap (N8N Core, APIs, SQL, Deploy, Security, Resilience) and to the 5 infrastructure layers validated with external mentors. The 6 layers and their mandatory questions are:

**ES**: Cada capa resuelve una categoría de fallo real identificada durante el diseño de Fase 1. La estructura mapea directamente a las 6 capas sinérgicas de mi roadmap (N8N Core, APIs, SQL, Deploy, Seguridad, Resiliencia) y a las 5 capas de infraestructura validadas con mentores externos. Las 6 capas y sus preguntas obligatorias son:

### Layer 1 / Capa 1 — Application & Logic / Aplicación y Lógica (N8N Core + APIs)

**EN**: What business problem does this system solve? What workflows compose it? What external APIs does it consume and with what authentication type? Does the Code node handle inconsistent data with optional chaining and default values?

**ES**: ¿Qué problema de negocio resuelve este sistema? ¿Qué workflows lo componen? ¿Qué APIs externas consume y con qué tipo de autenticación? ¿El Code node maneja datos inconsistentes con optional chaining y valores por defecto?

### Layer 2 / Capa 2 — Persistence / Persistencia (PostgreSQL)

**EN**: Where do the system's data live? Is deduplication implemented? Is the schema documented with data types and relationships? Do queries use parameters (no SQL injection)?

**ES**: ¿Dónde viven los datos del sistema? ¿Hay deduplicación implementada? ¿El schema está documentado con tipos de dato y relaciones? ¿Las queries usan parámetros (sin SQL injection)?

### Layer 3 / Capa 3 — Hosting / Alojamiento (Docker + VPS)

**EN**: Where does the system live? Is it containerized with Docker Compose? Are environment variables in `.env`, never hardcoded? Is the server in the EU (GDPR)?

**ES**: ¿Dónde habita el sistema? ¿Está containerizado con Docker Compose? ¿Las variables de entorno están en `.env`, nunca hardcodeadas? ¿El servidor está en la UE (GDPR)?

### Layer 4 / Capa 4 — Perimeter & Security / Perímetro y Seguridad (Proxy + HTTPS + Hardening)

**EN**: Is there a reverse proxy (Nginx/Traefik) in front of N8N? HTTPS with active SSL certificate? Firewall configured with only necessary ports (22, 80, 443)? SSH with key, not password? PostgreSQL NOT exposed externally? Credentials in N8N's vault, never in the workflow?

**ES**: ¿Hay reverse proxy (Nginx/Traefik) delante de N8N? ¿HTTPS con certificado SSL activo? ¿Firewall configurado con solo puertos necesarios (22, 80, 443)? ¿SSH con clave, no contraseña? ¿PostgreSQL NO expuesto al exterior? ¿Credenciales en el vault de N8N, nunca en el workflow?

### Layer 5 / Capa 5 — Resilience / Resiliencia (Error handling + Fallback)

**EN**: What happens if an external API fails? (Retry with backoff) What happens if infrastructure goes down? (Dead Letter Queue / temporary storage) What happens if garbage data arrives? (Early discard with log) Is the system idempotent for main cases?

**ES**: ¿Qué pasa si una API externa falla? (Retry con backoff) ¿Qué pasa si la infraestructura cae? (Dead Letter Queue / almacenamiento temporal) ¿Qué pasa si llegan datos basura? (Descarte temprano con log) ¿El sistema es idempotente para los casos principales?

### Layer 6 / Capa 6 — Continuity / Continuidad (Backups + Observability / Observabilidad)

**EN**: Is there automated backup of PostgreSQL and workflows? Is the backup stored outside the server? Are there alerts if a critical workflow fails (Telegram/email)? Is there a health check that detects if N8N is down?

**ES**: ¿Hay backup automatizado de PostgreSQL y workflows? ¿El backup se almacena fuera del servidor? ¿Hay alertas si un workflow crítico falla (Telegram/email)? ¿Hay health check que detecte si N8N está caído?

---

### Usage Rule / Regla de Uso

**EN**: Before delivering any system, I review all 6 layers. If a layer is not implemented, I document why in the project's ADR (conscious technical debt). If there's no justification, the system doesn't go to production.

**ES**: Antes de entregar cualquier sistema, reviso las 6 capas. Si una capa no está implementada, documento por qué en el ADR del proyecto (deuda técnica consciente). Si no hay justificación, el sistema no sale a producción.

---

## Consequences / Consecuencias

### Positive / Positivas ✅
- **EN**: Every new project has a structured starting point, doesn't start from zero
- **ES**: Cada proyecto nuevo tiene un punto de partida estructurado, no empieza de cero
- **EN**: Eliminates the risk of forgetting critical layers under delivery pressure
- **ES**: Elimina el riesgo de olvidar capas críticas bajo presión de entrega
- **EN**: Differentiates my deliverable from an automator who only installs workflows: I deliver a documented system with security, backups, and failure handling
- **ES**: Diferencia mi entregable del de un automatizador que solo instala workflows: yo entrego un sistema documentado con seguridad, backups y manejo de fallos

### Negative / Negativas ⚠️
- **EN**: Adds review time to each project (estimated: 30 min checklist per system)
- **ES**: Añade tiempo de revisión a cada proyecto (estimado: 30 min de checklist por sistema)
- **EN**: The template may generate false security if I don't update it with what I learn in production
- **ES**: El template puede generar falsa seguridad si no lo actualizo con lo que aprendo en producción
- **EN**: Doesn't cover enterprise scenarios — accepted trade-off since that's not my market in Phase 1
- **ES**: No cubre escenarios enterprise — trade-off aceptado porque no es mi mercado en Fase 1

---

## Validation / Validación

**EN**: This decision will be successful if:
- ✅ Across the 3 Anchor Projects of Phase 1, the checklist forced me to think about at least one layer I would have forgotten without it
- ✅ When the first system is in production, I can identify if something is missing and update this ADR with real data (not assumptions)

**ES**: Esta decisión será exitosa si:
- ✅ En los 3 Proyectos Ancla de Fase 1, el checklist me obligó a pensar en al menos una capa que habría olvidado sin él
- ✅ Cuando el primer sistema esté en producción, puedo identificar si falta algo y actualizar este ADR con datos reales (no con suposiciones)

**Review / Revisar en**: Anchor Project 2 completion / Al completar Proyecto Ancla 2

---

## References / Referencias
- `fase-1-redefinida.md` — Synergistic Layers 1-6 / Capas 1-6 sinérgicas del roadmap
- Gemini conversation / Conversación con Gemini: 5-layer infrastructure model + risk analysis (medical clinic) / modelo de 5 capas + análisis de riesgo (clínica médica)
- Claude conversation / Conversación con Claude: roadmap coverage validation + corrections on CI/CD and DLQ / validación de cobertura del roadmap y correcciones sobre CI/CD y DLQ

---

**Created / Creado**: 2026-03-01  
**Last Updated / Última Actualización**: 2026-03-01
