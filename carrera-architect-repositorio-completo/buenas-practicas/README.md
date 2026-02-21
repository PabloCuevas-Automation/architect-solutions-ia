# Buenas Prácticas - Metodología de Trabajo / Best Practices - Work Methodology

**Autor / Author:** Pablo  
**Última actualización / Last update:** 28 enero 2026 / January 28, 2026  
**Propósito / Purpose:** Principios transversales que aplico en TODOS mis proyectos / Transversal principles I apply in ALL my projects

---

## ¿Qué es esta carpeta? / What is this folder?

**[ES]** Esta carpeta contiene mi **metodología de trabajo** como Solution Architect:
- Principios arquitectónicos fundamentales
- Políticas de compliance
- Templates reutilizables
- Decisiones que NO son específicas de un proyecto

**NO contiene:**
- ADRs de decisiones específicas de proyectos
- ADRs de mi roadmap de aprendizaje

**[EN]** This folder contains my **work methodology** as a Solution Architect:
- Fundamental architectural principles
- Compliance policies
- Reusable templates
- Decisions that are NOT project-specific

**DOES NOT contain:**
- ADRs for project-specific decisions
- ADRs for my learning roadmap

---

## ¿Cuándo usar esto? / When to use this?

**[ES]** **Al empezar proyecto nuevo:**
1. Copia carpeta `/BUENAS_PRACTICAS/` a proyecto
2. Lee todos los ADRs (son pocos, fundamentales)
3. Aplica principios desde día 1
4. Crea ADRs específicos del proyecto en su propia carpeta

**Al tomar decisión arquitectónica:**
1. ¿Contradice principios de BUENAS_PRACTICAS? → Justifica con ADR
2. ¿Alineado con principios? → Documenta aplicación

**[EN]** **When starting a new project:**
1. Copy `/BUENAS_PRACTICAS/` folder to project
2. Read all ADRs (they are few, fundamental)
3. Apply principles from day 1
4. Create project-specific ADRs in their own folder

**When making an architectural decision:**
1. Does it contradict BUENAS_PRACTICAS principles? → Justify with ADR
2. Aligned with principles? → Document application

---

## Contenido Actual / Current Content

### ADRs (Principios / Principles)
- **ADR-000:** Principios Arquitectónicos Fundamentales / Fundamental Architectural Principles
- **ADR-001:** Documentación de Decisiones vía ADRs / Decision Documentation via ADRs
- **ADR-002:** Compliance GDPR Obligatorio / Mandatory GDPR Compliance

### Templates
- `COMPLIANCE_TEMPLATE.md` - Checklist compliance por proyecto / Compliance checklist per project
- `ADR_TEMPLATE_DETALLADO.md` - Para decisiones estratégicas / For strategic decisions
- `ADR_TEMPLATE_LIGERO.md` - Para decisiones tácticas / For tactical decisions

---

## Filosofía / Philosophy

**[ES]**
> "Estos principios no son reglas rígidas.  
> Son guardrails que me protegen de malas decisiones.  
> Si los violo, lo documento con ADR explicando por qué."

**Ejemplo:**
- Principio: "Evitar vendor lock-in"
- Proyecto X: "Elegí vendor lock-in con AWS porque [razones]" → ADR documenta trade-off

**[EN]**
> "These principles are not rigid rules.  
> They are guardrails that protect me from bad decisions.  
> If I violate them, I document it with an ADR explaining why."

**Example:**
- Principle: "Avoid vendor lock-in"
- Project X: "I chose vendor lock-in with AWS because [reasons]" → ADR documents trade-off

---

## Evolución / Evolution

**[ES]** Esta carpeta crece lentamente. No añado principios por capricho.

**Criterio para añadir nuevo ADR aquí:**
- ✅ Aplica a 80%+ de mis proyectos
- ✅ Refleja aprendizaje profundo (no moda pasajera)
- ✅ Me ha salvado de error real
- ❌ NO: decisiones específicas de tecnología ("Usar PostgreSQL")
- ❌ NO: preferencias personales ("Prefiero tabs vs spaces")

**[EN]** This folder grows slowly. I don't add principles on a whim.

**Criteria for adding a new ADR here:**
- ✅ Applies to 80%+ of my projects
- ✅ Reflects deep learning (not a passing fad)
- ✅ Has saved me from a real error
- ❌ NO: technology-specific decisions ("Use PostgreSQL")
- ❌ NO: personal preferences ("I prefer tabs vs spaces")