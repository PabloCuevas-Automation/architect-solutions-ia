# ADR-001: Decision Documentation via ADRs / Documentación de Decisiones vía ADRs

**Date / Fecha:** 28 enero 2026  
**Status / Estado:** ✅ Accepted / Aceptada  
**Author / Autor:** Pablo  
**Type / Tipo:** Cross-cutting Methodology / Metodología Transversal  
**Scope / Ámbito:** All my projects / Todos mis proyectos

---

## Summary / Resumen

**EN:** In the context of being a Solution Architect where my main value is **judgment** (not execution speed), I decided to document all significant architectural decisions using Architecture Decision Records (ADRs), accepting writing overhead (~15 min per ADR) to gain traceability, learning, and professional differentiation.

**ES:** En el contexto de ser Solution Architect donde valor principal es **criterio** (no velocidad de ejecución), decidí documentar todas las decisiones arquitectónicas significativas usando Architecture Decision Records (ADRs), aceptando overhead de escritura (~15 min por ADR) para ganar trazabilidad, aprendizaje y diferenciación profesional.

---

## Context / Contexto

### Why Document Decisions / Por Qué Documentar Decisiones

**EN - Problem without ADRs:**
```
6 months later...
"Why did we choose PostgreSQL and not MongoDB?"
"Why self-hosted and not managed?"
"Who decided this?"
```
Result: Decisions are lost, errors are repeated, no learning.

**ES - Problema sin ADRs:**
```
6 meses después...
"¿Por qué elegimos PostgreSQL y no MongoDB?"
"¿Por qué self-hosted y no managed?"
"¿Quién decidió esto?"
```
Resultado: Decisiones se pierden, se repiten errores, no hay aprendizaje.

---

## Decision / Decisión

**EN - I will use ADRs to document:**

1. **Significant Architectural Decisions**
   - Affects multiple components
   - Difficult or costly to reverse (Type 1 decision)
   - Has important trade-offs
   - Someone will ask "why?" in 6 months

2. **Cross-cutting Policies**
   - Compliance policies
   - Security standards
   - Testing strategies
   - Deployment practices

**NOT using ADRs for:**
- ❌ Trivial reversible decisions
- ❌ Personal preferences without impact
- ❌ Obvious choices without alternatives
- ❌ Code implementation details

**ES - Usaré ADRs para documentar:**

1. **Decisiones Arquitectónicas Significativas**
   - Afecta múltiples componentes
   - Difícil o costosa de revertir (Type 1 decision)
   - Tiene trade-offs importantes
   - Alguien preguntará "¿por qué?" en 6 meses

2. **Políticas Transversales**
   - Compliance policies
   - Security standards
   - Testing strategies
   - Deployment practices

**NO uso ADRs para:**
- ❌ Decisiones triviales reversibles
- ❌ Preferencias personales sin impacto
- ❌ Elecciones obvias sin alternativas
- ❌ Detalles de implementación de código

---

## ADR Structure / Estructura de ADRs

### Detailed Template (Strategic Decisions) / Template Detallado (Decisiones Estratégicas)

**EN - Use for:** Type 1 decisions, high impact, multiple alternatives

**Mandatory sections:**
1. Executive Summary
2. Context
3. Alternatives Considered (minimum 2)
4. Decision
5. Consequences (positive + negative)
6. Trade-offs

**ES - Uso para:** Decisiones Type 1, alto impacto, múltiples alternativas

**Secciones obligatorias:**
1. Resumen Ejecutivo
2. Contexto
3. Alternativas Consideradas (mínimo 2)
4. Decisión
5. Consecuencias (positivas + negativas)
6. Trade-offs

### Light Template (Tactical Decisions) / Template Ligero (Decisiones Tácticas)

**EN - Use for:** Type 2 decisions, reversible, clear context

**Mandatory sections:**
1. Context (brief)
2. Decision
3. Why
4. Trade-offs (if relevant)

**ES - Uso para:** Decisiones Type 2, reversibles, contexto claro

**Secciones obligatorias:**
1. Contexto (breve)
2. Decisión
3. Por Qué
4. Trade-offs (si relevante)

---

## Alternatives Considered / Alternativas Consideradas

### Alternative A: Don't Document (Code Only) / Alternativa A: No Documentar (Solo Código)

**EN - Pros:**
- ✅ Faster short-term
- ✅ No overhead

**Cons:**
- ❌ Knowledge is lost
- ❌ Errors are repeated
- ❌ Difficult onboarding
- ❌ No systematized learning

**Why NOT:** My value as architect IS judgment. Without documentation, that value disappears.

**ES - Pros:**
- ✅ Más rápido short-term
- ✅ Sin overhead

**Contras:**
- ❌ Conocimiento se pierde
- ❌ Errores se repiten
- ❌ Onboarding difícil
- ❌ No hay aprendizaje sistematizado

**Por qué NO:** Mi valor como arquitecto ES el criterio. Sin documentation, ese valor desaparece.

### Alternative B: Wiki/Confluence / Alternativa B: Wiki/Confluence

**EN - Pros:**
- ✅ Friendly interface
- ✅ Easy search

**Cons:**
- ❌ Separated from code
- ❌ Easily outdated
- ❌ Requires login/permissions
- ❌ Not version-controlled

**Why NOT:** ADRs next to code = single source of truth

**ES - Pros:**
- ✅ Interfaz amigable
- ✅ Buscar fácil

**Contras:**
- ❌ Separado del código
- ❌ Desactualizado fácilmente
- ❌ Requiere login/permissions
- ❌ No version-controlled

**Por qué NO:** ADRs junto al código = single source of truth

### Alternative C: ADRs (CHOSEN) / Alternativa C: ADRs (ELEGIDA) ⭐

**EN - Pros:**
- ✅ Version-controlled (Git)
- ✅ Next to code
- ✅ Simple Markdown
- ✅ Portable
- ✅ Industry standard

**Cons:**
- ❌ Overhead ~15 min per ADR
- ❌ Requires discipline

**ES - Pros:**
- ✅ Version-controlled (Git)
- ✅ Junto al código
- ✅ Markdown simple
- ✅ Portable
- ✅ Estándar industry

**Contras:**
- ❌ Overhead ~15 min por ADR
- ❌ Requiere disciplina

---

## Consequences / Consecuencias

### Positive / Positivas ✅

**EN:**
1. **Systematized Learning** - Each documented decision is a learned lesson
2. **Traceability** - 6 months later I know why I decided X
3. **Professional Differentiation** - Portfolio of architectural judgment
4. **Fast Onboarding** - New dev reads ADRs → understands context

**ES:**
1. **Aprendizaje Sistematizado** - Cada decisión documentada es lección aprendida
2. **Trazabilidad** - 6 meses después sé por qué decidí X
3. **Diferenciación Profesional** - Portfolio de criterio arquitectónico
4. **Onboarding Rápido** - Nuevo dev lee ADRs → entiende contexto

### Negative / Negativas ⚠️

**EN:**
1. **Time Overhead** - ~15-30 min per significant ADR
2. **Temptation to Over-document** - Risk: ADR for every trivial thing

**ES:**
1. **Time Overhead** - ~15-30 min por ADR significativo
2. **Tentación de Sobre-documentar** - Riesgo: ADR por cada cosa trivial

---

## Success Metrics / Métricas de Éxito

**EN - Short term (3 months):**
- ✅ 5+ ADRs written
- ✅ No project without ADRs
- ✅ ADRs help in debugging

**ES - Corto plazo (3 meses):**
- ✅ 5+ ADRs escritos
- ✅ Ningún proyecto sin ADRs
- ✅ ADRs ayudan en debugging

---

**Signed / Firmado:** Pablo  
**Next review / Revisión próxima:** Upon completing 10 ADRs / Al completar 10 ADRs

*"Undocumented decisions = Lost decisions"*  
*"Decisiones no documentadas = Decisiones perdidas"*