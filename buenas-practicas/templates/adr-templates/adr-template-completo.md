# ADR-XXX: [Descriptive Title / Título Descriptivo]

**Date / Fecha**: YYYY-MM-DD  
**Status / Estado**: 🟡 Proposed / Propuesta | ✅ Accepted / Aceptada | ⚠️ Deprecated | 🔄 Superseded by ADR-YYY  
**Author / Autor**: [Your name / Tu nombre]  
**Deciders / Decisores**: [Who participated / Quién participó]  
**Type / Tipo**: Strategic / Estratégica | Tactical / Táctica | Organizational / Organizacional | Technical / Técnica

---

## Executive Summary (Optional) / Resumen Ejecutivo (Opcional)

**EN:** [1-2 sentences: Context → Decision → Main impact]

**ES:** [1-2 frases: Contexto → Decisión → Impacto principal]

**Example / Ejemplo:**
> "In the context of needing to automate workflows quickly, facing the choice between N8N and Make, I decided to use N8N self-hosted to achieve greater control and lower cost, accepting greater setup complexity."
>
> "En el contexto de necesitar automatizar workflows rápidamente, enfrentando la elección entre N8N y Make, decidí usar N8N self-hosted para lograr mayor control y menor costo, aceptando mayor complejidad de setup."

---

## Context / Contexto

### Current Situation / Situación Actual

**EN:**
- Current state of system/project
- Specific problem you face
- Known constraints (time, money, skills)

**ES:**
- Estado actual del sistema/proyecto
- Problema específico que enfrentas
- Restricciones conocidas (tiempo, dinero, habilidades)

### Why This Decision is Important / Por Qué Esta Decisión Es Importante

**EN:**
- What does it impact? (code, costs, team, timeline)
- How reversible is it?
- What happens if we DON'T decide?

**ES:**
- ¿Qué impacta? (código, costos, equipo, timeline)
- ¿Qué tan reversible es?
- ¿Qué pasa si NO decidimos?

### Key Factors / Factores Clave

**EN:**
- Factor 1 (e.g., Limited budget €X/month)
- Factor 2 (e.g., Need results in 6 weeks)
- Factor 3 (e.g., Only 10h/week available)

**ES:**
- Factor 1 (ej: Presupuesto limitado €X/mes)
- Factor 2 (ej: Necesito resultados en 6 semanas)
- Factor 3 (ej: Solo 10h/semana disponibles)

---

## Alternatives Considered / Alternativas Consideradas

### Option 1 / Opción 1: [Alternative name / Nombre alternativa]

**Brief description / Descripción breve:**

**EN:** [1-2 lines what this option implies]

**ES:** [1-2 líneas qué implica esta opción]

**Pros:**
- ✅ Advantage 1 / Ventaja 1
- ✅ Advantage 2 / Ventaja 2
- ✅ Advantage 3 / Ventaja 3

**Cons:**
- ❌ Disadvantage 1 / Desventaja 1
- ❌ Disadvantage 2 / Desventaja 2
- ❌ Disadvantage 3 / Desventaja 3

**Evaluation criteria / Criterios de evaluación:**

| Criterion / Criterio | Rating | Notes / Notas |
|---------------------|--------|---------------|
| Cost / Costo | X/5 | [Comment / Comentario] |
| Complexity / Complejidad | X/5 | [Comment / Comentario] |
| Implementation time / Tiempo implementación | X/5 | [Comment / Comentario] |
| Maintainability / Mantenibilidad | X/5 | [Comment / Comentario] |

---

### Option 2 / Opción 2: [Alternative name / Nombre alternativa]

[Repeat structure / Repetir estructura]

---

### Option 3 / Opción 3: [Alternative name / Nombre alternativa] - CHOSEN ⭐

[Repeat structure but mark as chosen / Repetir estructura pero marcar como elegida]

---

### Quickly Discarded Options / Opciones Descartadas Rápidamente

**EN:**
- **Option X**: Discarded because [brief reason]
- **Option Y**: Out of scope because [brief reason]

**ES:**
- **Opción X**: Descartada porque [razón breve]
- **Opción Y**: Fuera de alcance porque [razón breve]

---

## Decision / Decisión

### What We Decided / Qué Decidimos

**EN:** [Declare decision in 1-2 clear sentences]

**ES:** [Declara decisión en 1-2 frases claras]

**Example / Ejemplo:**
> "We chose to use N8N self-hosted as our main automation tool for the next 6 months of Phase 1."
>
> "Elegimos usar N8N self-hosted como nuestra herramienta principal de automatización para los próximos 6 meses de Fase 1."

### Why This Option / Por Qué Esta Opción

**EN - From technical perspective:**
[Why technically it's the best option for the problem]

**ES - Desde perspectiva técnica:**
[Por qué técnicamente es la mejor opción para el problema]

**EN - From business/objectives perspective:**
[How it aligns with time/money/impact goals]

**ES - Desde perspectiva de negocio/objetivos:**
[Cómo se alinea con metas de tiempo/dinero/impacto]

**EN - From team/skills perspective:**
[Why it's feasible with current skills]

**ES - Desde perspectiva de equipo/habilidades:**
[Por qué es factible con habilidades actuales]

**EN - Decisive factor:**
[What was THE factor that made this option win?]

**ES - Factor decisivo:**
[¿Cuál fue EL factor que hizo que esta opción ganara?]

---

## Consequences / Consecuencias

### Positive Impacts / Impactos Positivos ✅

**EN:**
1. **[Benefit 1]**: [Brief explanation how this benefit materializes]
2. **[Benefit 2]**: [Explanation]
3. **[Benefit 3]**: [Explanation]

**ES:**
1. **[Beneficio 1]**: [Explicación breve cómo se materializa]
2. **[Beneficio 2]**: [Explicación]
3. **[Beneficio 3]**: [Explicación]

### Negative Impacts / Impactos Negativos ⚠️

**EN:**
1. **[Cost/limitation 1]**: [Explanation + mitigation if exists]
2. **[Cost/limitation 2]**: [Explanation + mitigation]
3. **[Cost/limitation 3]**: [Explanation + mitigation]

**ES:**
1. **[Costo/limitación 1]**: [Explicación + mitigación si existe]
2. **[Costo/limitación 2]**: [Explicación + mitigación]
3. **[Costo/limitación 3]**: [Explicación + mitigación]

### Key Trade-offs / Trade-offs Clave

**EN:** [Summarize most important trade-offs in format "We sacrifice X to gain Y"]

**ES:** [Resume trade-offs más importantes en formato "Sacrificamos X para ganar Y"]

**Example / Ejemplo:**
> We sacrifice **setup simplicity** (Make is easier) to gain **infrastructure control and lower costs** (N8N self-hosted).
>
> Sacrificamos **simplicidad de setup** (Make es más fácil) para ganar **control sobre infraestructura y costos menores** (N8N self-hosted).

---

## Identified Risks / Riesgos Identificados

| Risk / Riesgo | Probability / Probabilidad | Impact / Impacto | Mitigation / Mitigación |
|--------------|---------------------------|-----------------|----------------------|
| [Risk 1 / Riesgo 1] | High/Medium/Low / Alta/Media/Baja | High/Medium/Low / Alto/Medio/Bajo | [How we mitigate / Cómo mitigamos] |
| [Risk 2 / Riesgo 2] | High/Medium/Low / Alta/Media/Baja | High/Medium/Low / Alto/Medio/Bajo | [How we mitigate / Cómo mitigamos] |

---

## Validation Criteria / Criterios de Validación

### How We'll Know If This Was a Good Decision / Cómo Sabremos Si Fue Buena Decisión

**EN - Short term (first [X] weeks):**
- ✅ Indicator 1: [Concrete metric]
- ✅ Indicator 2: [Concrete metric]

**ES - Corto plazo (primeras [X] semanas):**
- ✅ Indicador 1: [Métrica concreta]
- ✅ Indicador 2: [Métrica concreta]

**EN - Medium term ([X] months):**
- ✅ Indicator 3: [Concrete metric]

**ES - Medio plazo ([X] meses):**
- ✅ Indicador 3: [Métrica concreta]

### Warning Signs (when to reconsider) / Señales de Alerta (cuándo reconsiderar)

**EN - If we observe:**
- 🚨 [Warning sign 1]
- 🚨 [Warning sign 2]

Then we should **reconsider this decision**.

**ES - Si observamos:**
- 🚨 [Señal de alerta 1]
- 🚨 [Señal de alerta 2]

Entonces debemos **reconsiderar esta decisión**.

---

## Implementation (Optional) / Implementación (Opcional)

### Immediate Next Steps / Pasos Siguientes Inmediatos

**EN:**
1. [Action 1 - Responsible - Deadline]
2. [Action 2 - Responsible - Deadline]

**ES:**
1. [Acción 1 - Responsable - Deadline]
2. [Acción 2 - Responsable - Deadline]

---

## References / Referencias

### Related Documents / Documentos Relacionados

**EN:**
- [Link or path to related document 1]
- [Link or path to related document 2]

**ES:**
- [Link o path a documento relacionado 1]
- [Link o path a documento relacionado 2]

### Related ADRs / ADRs Relacionados

- ADR-XXX: [Title / Título] - [How it relates / Cómo se relaciona]
- Supersedes / Supersede a: ADR-YYY
- Superseded by / Supersedido por: ADR-ZZZ (when applicable / cuando aplique)

---

## Change History / Historial de Cambios

| Date / Fecha | Change / Cambio | Reason / Razón |
|-------------|----------------|---------------|
| YYYY-MM-DD | Created ADR in Proposed state / Creado ADR en estado Propuesta | [Reason / Razón] |
| YYYY-MM-DD | Changed to Accepted / Cambiado a Aceptada | [Consensus reached / Consenso alcanzado] |

---

## Usage Instructions / Instrucciones de Uso

### Mandatory Sections / Secciones Obligatorias

**EN:**
- ✅ Title, Date, Status
- ✅ Context
- ✅ Alternatives (minimum 2)
- ✅ Decision
- ✅ Consequences

**ES:**
- ✅ Título, Fecha, Estado
- ✅ Contexto
- ✅ Alternativas (mínimo 2)
- ✅ Decisión
- ✅ Consecuencias

### Optional Sections / Secciones Opcionales

**EN - Use according to need:**
- ⚪ Executive Summary (only large decisions)
- ⚪ Evaluation criteria table (only if comparing many options)
- ⚪ Identified risks (only if significant risks)
- ⚪ Implementation (only if decision requires action steps)

**ES - Usa según necesidad:**
- ⚪ Resumen Ejecutivo (solo decisiones grandes)
- ⚪ Criterios evaluación en tabla (solo si comparas muchas opciones)
- ⚪ Riesgos identificados (solo si hay riesgos significativos)
- ⚪ Implementación (solo si decisión requiere pasos acción)

### Target Length / Longitud Target

**EN:**
- **With all sections**: 3-4 pages
- **Only mandatory sections**: 1-2 pages

**ES:**
- **Con todas las secciones**: 3-4 páginas
- **Solo secciones obligatorias**: 1-2 páginas

### When to Use This Template / Cuándo Usar Este Template

**EN - Use for:**
- ✅ Strategic decisions affecting multiple months
- ✅ Decisions with multiple complex alternatives
- ✅ Decisions requiring formal justification
- ✅ First time using ADRs in a project

**ES - Usar para:**
- ✅ Decisiones estratégicas que afectan múltiples meses
- ✅ Decisiones con múltiples alternativas complejas
- ✅ Decisiones que requieren justificación formal
- ✅ Primera vez que usas ADRs en proyecto

---

**Signed / Firmado**: [Your name / Tu nombre]  
**Commitment date / Fecha de compromiso**: [Date / Fecha]

---

*Template version 1.0 - Created / Creado: 2026-01-28*
*Bilingual EN/ES - For professional and personal projects / Para proyectos profesionales y personales*