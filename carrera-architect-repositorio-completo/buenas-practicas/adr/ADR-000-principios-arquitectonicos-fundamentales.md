# ADR-000: Architectural Principles / Principios Arquitectónicos Fundamentales

**Date / Fecha:** 28 enero 2026  
**Status / Estado:** ✅ Accepted / Aceptada  
**Author / Autor:** Pablo  
**Type / Tipo:** Cross-cutting Methodology / Metodología Transversal  
**Scope / Ámbito:** All my projects / Todos mis proyectos

---

## Summary / Resumen

**EN:** These are my **non-negotiable principles** as a Solution Architect. They guide every technical decision I make.

**ES:** Estos son mis **principios no-negociables** como Solution Architect. Guían cada decisión técnica que tomo.

---

## Principles / Principios

### 1. Value Hierarchy: Decisions > Systems > Code / Jerarquía de Valor: Decisiones > Sistemas > Código

**EN - Meaning:**
- My value is NOT in lines of code I write
- My value is in decisions that avoid unnecessary code
- My value is in systems that work without me

**ES - Significado:**
- Mi valor NO está en líneas de código que escribo
- Mi valor está en decisiones que evitan código innecesario
- Mi valor está en sistemas que funcionan sin mí

**EN - Practical application:**
- Before building: "Should we build this?"
- During construction: "What happens if this fails?"
- After: "How does it work without my intervention?"

**ES - Aplicación práctica:**
- Antes de construir: "¿Debemos construir esto?"
- Durante construcción: "¿Qué pasa si esto falla?"
- Después: "¿Cómo funciona sin mi intervención?"

---

### 2. Technique as Infrastructure (Not as End) / Técnica como Infraestructura (No como Fin)

**EN - Meaning:**
- I learn technique up to the point it improves my judgment
- I don't seek to be "expert in X" - I seek to solve problems
- Tools are means, not objectives

**ES - Significado:**
- Aprendo técnica hasta donde mejora mi criterio
- No busco ser "experto en X" - busco resolver problemas
- Herramientas son medios, no objetivos

**EN - Practical application:**
- Python, JavaScript, SQL = tools
- Architecture, systems, trade-offs = real knowledge
- If a tool doesn't serve me → I discard it without remorse

**ES - Aplicación práctica:**
- Python, JavaScript, SQL = herramientas
- Arquitectura, sistemas, trade-offs = conocimiento real
- Si herramienta no me sirve → la descarto sin remordimiento

---

### 3. Data > Code / Datos > Código

**EN - Meaning:**
- SQL teaches how a company thinks
- Data structure reveals business logic
- Changing code is easy; migrating data is difficult

**ES - Significado:**
- SQL enseña cómo piensa empresa
- Estructura de datos revela lógica de negocio
- Cambiar código es fácil; migrar datos es difícil

**EN - Practical application:**
- Design data schema BEFORE code
- Thoughtful normalization, not by default
- Thoroughly tested migrations

**ES - Aplicación práctica:**
- Diseño schema de datos ANTES de código
- Normalización pensada, no por default
- Migrations testeadas exhaustivamente

---

### 4. Compliance = Power (European Differentiator) / Compliance = Poder (Diferenciador Europeo)

**EN - Meaning:**
- GDPR is not "overhead" - it's competitive advantage
- European market penalizes compliance ignorance
- Who understands compliance + AI = differentiated

**ES - Significado:**
- GDPR no es "overhead" - es ventaja competitiva
- Mercado europeo penaliza ignorantes de compliance
- Quien entiende compliance + IA = diferenciado

**EN - Practical application:**
- Compliance from day 1 (not retrofitted)
- COMPLIANCE.md in every project
- Reject projects that ignore this

**ES - Aplicación práctica:**
- Compliance desde día 1 (no retrofitted)
- COMPLIANCE.md en todo proyecto
- Rechazar proyectos que ignoren esto

---

### 5. Technical Sovereignty (Anti-Lock-in) / Soberanía Técnica (Anti-Lock-in)

**EN - Meaning:**
- My systems must be able to function if vendor X falls
- Dependency = fragility
- Control > Convenience

**ES - Significado:**
- Mis sistemas deben poder funcionar si vendor X cae
- Dependencia = fragilidad
- Control > Conveniencia

**EN - Practical application:**
- Self-hosted when possible
- Own APIs over closed platforms
- Documented exit strategy per vendor

**ES - Aplicación práctica:**
- Self-hosted cuando posible
- APIs propias sobre plataformas cerradas
- Exit strategy documentada por vendor

---

### 6. Progress = Systems in Production (Not Courses) / Progreso = Sistemas en Producción (No Cursos)

**EN - Meaning:**
- Knowledge without application is theater
- Measurable value: working code, satisfied clients
- Portfolio > certifications

**ES - Significado:**
- Conocimiento sin aplicación es teatro
- Valor medible: código funcionando, clientes satisfechos
- Portfolio > certificaciones

**EN - Practical application:**
- 1 finished project > 5 tutorials
- Real feedback > self-evaluation
- Iterate fast > perfectionism

**ES - Aplicación práctica:**
- 1 proyecto terminado > 5 tutoriales
- Feedback real > autoevaluación
- Iterar rápido > perfeccionismo

---

### 7. Architect ≠ Senior Programmer / Arquitecto ≠ Programador Senior

**EN - Meaning:**
- Developer asks: "How do I build it?"
- Architect asks: "Should we build it?"
- My role is to decide, not just execute

**ES - Significado:**
- Developer pregunta: "¿Cómo lo construyo?"
- Arquitecto pregunta: "¿Debemos construirlo?"
- Mi rol es decidir, no solo ejecutar

**EN - Practical application:**
- I question requirements
- I propose alternatives
- I document trade-offs
- I escalate complex decisions

**ES - Aplicación práctica:**
- Cuestiono requirements
- Propongo alternativas
- Documento trade-offs
- Escalo decisiones complejas

---

## Application in Projects / Aplicación en Proyectos

**EN:** Every project must:
1. Align with these principles (default)
2. If violating a principle → specific ADR justifying why

**ES:** Cada proyecto debe:
1. Alinearse con estos principios (default)
2. Si viola principio → ADR específico justificando por qué

**EN - Example:**
- Project X uses vendor lock-in (violates principle 5)
- ADR documents: "We chose AWS Lambda because [reasons] and accept lock-in because [justified trade-off]"

**ES - Ejemplo:**
- Proyecto X usa vendor lock-in (viola principio 5)
- ADR documenta: "Elegimos AWS Lambda porque [razones] y aceptamos lock-in porque [trade-off justificado]"

---

## Evolution / Evolución

**EN:** These principles evolve based on **real experience**, not theory.

**ES:** Estos principios evolucionan basado en **experiencia real**, no en teoría.

**EN - I add a principle when:**
- It saved me from serious error 2+ times
- I see repeated pattern in multiple projects
- It reflects deep learning

**ES - Añado principio cuando:**
- Me salvó de error grave 2+ veces
- Veo pattern repetido en múltiples proyectos
- Refleja aprendizaje profundo

**EN - I remove a principle when:**
- I discover it was dogma, not wisdom
- Context changed and it no longer applies

**ES - Elimino principio cuando:**
- Descubro que era dogma, no sabiduría
- Contexto cambió y ya no aplica

---

## References / Referencias

- **ADR-001:** Documentation via ADRs / Documentación vía ADRs (how I implement these principles / cómo implemento estos principios)
- **ADR-002:** GDPR Compliance / Compliance GDPR (specific application principle 4 / aplicación específica principio 4)
- **Roadmap:** From 0 to Solution Architect / De 0 a Solution Architect (origin of principles / origen de principios)

---

**Signed / Firmado:** Pablo  
**Next review / Revisión próxima:** Upon completing Phase 1 (month 6) / Al completar Fase 1 (mes 6)

*"Principles are compass, not map. They guide, not dictate."*  
*"Principios son brújula, no mapa. Guían, no dictan."*