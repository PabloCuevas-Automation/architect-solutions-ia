# ADR-002: PII Management in Projects / Manejo de PII en Proyectos

**Date / Fecha:** 28 enero 2026  
**Status / Estado:** ✅ Accepted / Aceptada  
**Author / Autor:** Pablo  
**Type / Tipo:** Mandatory Compliance Policy / Política de Compliance Obligatoria  
**Scope / Ámbito:** ALL projects without exception / TODOS los proyectos sin excepción

---

## Summary / Resumen

**EN:** In the context of working in the European market (GDPR) and developing AI/automation systems that inevitably handle personal data, I decided to adopt a **mandatory compliance-first policy** for handling Personally Identifiable Information (PII), accepting 15-20% development overhead to gain legal protection, client trust, and professional differentiation in a market where most developers ignore these requirements.

**ES:** En el contexto de trabajar en mercado europeo (GDPR) y desarrollar sistemas IA/automatización que inevitablemente manejan datos personales, decidí adoptar **política obligatoria compliance-first** para manejo de Personally Identifiable Information (PII), aceptando overhead de desarrollo 15-20% para ganar protección legal, confianza de clientes y diferenciación profesional en mercado donde mayoría de developers ignoran estos requisitos.

---

## Context / Contexto

### The GDPR Reality / La Realidad GDPR

**EN - Legal Framework:**
- GDPR = General Data Protection Regulation (EU law since 2018)
- Fines: up to €20M or 4% of global revenue (whichever is higher)
- Applies to: ANY company processing EU citizens' data (even if based outside EU)
- Enforcement: Active and increasing (thousands of fines already issued)

**ES - Marco Legal:**
- GDPR = General Data Protection Regulation (ley UE desde 2018)
- Multas: hasta €20M o 4% revenue global (lo que sea mayor)
- Aplica a: CUALQUIER empresa procesando datos de ciudadanos UE (incluso fuera UE)
- Enforcement: Activo y creciente (miles de multas ya emitidas)

### What is PII / Qué es PII

**EN - Personal Identifiable Information includes:**
- **Direct identifiers:** Name, email, phone, address, ID numbers, passport
- **Online identifiers:** IP address, cookies, device IDs, user IDs
- **Location data:** GPS coordinates, home address, frequent locations
- **Biometric data:** Fingerprints, facial recognition, voice
- **Financial data:** Bank accounts, credit cards, income
- **Health data:** Medical records, prescriptions, genetic data
- **Special categories:** Race, religion, political views, sexual orientation

**ES - Personal Identifiable Information incluye:**
- **Identificadores directos:** Nombre, email, teléfono, dirección, números ID, pasaporte
- **Identificadores online:** IP address, cookies, device IDs, user IDs
- **Datos de ubicación:** Coordenadas GPS, domicilio, ubicaciones frecuentes
- **Datos biométricos:** Huellas digitales, reconocimiento facial, voz
- **Datos financieros:** Cuentas bancarias, tarjetas crédito, ingresos
- **Datos salud:** Historiales médicos, recetas, datos genéticos
- **Categorías especiales:** Raza, religión, opiniones políticas, orientación sexual

---

## Decision / Decisión

### Core Requirements (Non-Negotiable) / Requisitos Core (No-Negociables)

**EN - ALL my projects MUST:**

1. **Data Minimization**
   - Collect ONLY data absolutely necessary
   - Challenge every data field: "Do we really need this?"
   - Prefer aggregated/anonymized over individual data

2. **Explicit Consent**
   - NEVER assume consent
   - Clear opt-in (not pre-checked boxes)
   - Granular consent per purpose
   - Easy to withdraw

3. **Deletion Mechanisms**
   - Right to be forgotten (GDPR Article 17)
   - Automated deletion upon request
   - Cascade deletion across systems
   - Documented retention policies

4. **Audit Trail**
   - Log all PII access (who, when, what, why)
   - Immutable logs
   - Retention: 2 years minimum

5. **Automatic Retention**
   - Data deleted after retention period
   - Automated (not manual process)
   - Configurable per data type

**ES - TODOS mis proyectos DEBEN:**

1. **Minimización de Datos**
   - Recolectar SOLO datos absolutamente necesarios
   - Cuestionar cada campo: "¿Realmente necesitamos esto?"
   - Preferir datos agregados/anonimizados sobre individuales

2. **Consentimiento Explícito**
   - NUNCA asumir consentimiento
   - Opt-in claro (no checkboxes pre-marcados)
   - Consentimiento granular por propósito
   - Fácil de retirar

3. **Mecanismos de Eliminación**
   - Derecho al olvido (GDPR Artículo 17)
   - Eliminación automática bajo petición
   - Cascade deletion across sistemas
   - Políticas de retención documentadas

4. **Audit Trail**
   - Log de todos los accesos a PII (quién, cuándo, qué, por qué)
   - Logs inmutables
   - Retención: 2 años mínimo

5. **Retención Automática**
   - Datos eliminados después de período retención
   - Automatizado (no proceso manual)
   - Configurable por tipo de dato

---

## Implementation / Implementación

### Every Project Must Have / Cada Proyecto Debe Tener

**EN:**

1. **COMPLIANCE.md file** (project root)
   - Data inventory: what PII we store
   - Retention policy: how long
   - Deletion process: how users can delete
   - Consent mechanism: how we obtain it

2. **PII Detection in Code Reviews**
   - Before merging: check for new PII fields
   - Question: Is this necessary? Alternatives?

3. **Database Design**
   - PII in separate tables (easier to delete)
   - Encrypted at rest
   - Access controls (RBAC)

4. **Testing**
   - Test deletion flows
   - Test consent withdrawal
   - Test data export

**ES:**

1. **Archivo COMPLIANCE.md** (raíz proyecto)
   - Inventario de datos: qué PII guardamos
   - Política retención: cuánto tiempo
   - Proceso eliminación: cómo usuarios pueden eliminar
   - Mecanismo consentimiento: cómo lo obtenemos

2. **Detección PII en Code Reviews**
   - Antes de merge: check por nuevos campos PII
   - Pregunta: ¿Es necesario? ¿Alternativas?

3. **Diseño Database**
   - PII en tablas separadas (más fácil eliminar)
   - Encrypted at rest
   - Access controls (RBAC)

4. **Testing**
   - Testear flows de eliminación
   - Testear retiro de consentimiento
   - Testear export de datos

---

## Alternatives Considered / Alternativas Consideradas

### Alternative A: Ignore Compliance / Alternativa A: Ignorar Compliance

**EN - Pros:**
- ✅ Faster development (15-20% less time)
- ✅ Simpler architecture
- ✅ No overhead

**Cons:**
- ❌ **LEGAL RISK:** Fines up to €20M or 4% revenue
- ❌ **REPUTATIONAL DAMAGE:** Data breach = trust destroyed
- ❌ **COMPETITIVE DISADVANTAGE:** Serious clients check compliance
- ❌ **TECHNICAL DEBT:** Retrofitting compliance is 10x harder

**Why NOT:** The risk is unacceptable. One breach or complaint = career/business destroyed.

**ES - Pros:**
- ✅ Desarrollo más rápido (15-20% menos tiempo)
- ✅ Arquitectura más simple
- ✅ Sin overhead

**Contras:**
- ❌ **RIESGO LEGAL:** Multas hasta €20M o 4% revenue
- ❌ **DAÑO REPUTACIONAL:** Data breach = confianza destruida
- ❌ **DESVENTAJA COMPETITIVA:** Clientes serios verifican compliance
- ❌ **DEUDA TÉCNICA:** Retrofit compliance es 10x más difícil

**Por qué NO:** El riesgo es inaceptable. Un breach o queja = carrera/negocio destruido.

---

### Alternative B: Maximum Paranoia / Alternativa B: Paranoia Máxima

**EN - Approach:**
- Never store ANY PII
- Encrypt everything
- Complex consent flows
- External audit every 6 months

**Pros:**
- ✅ Maximum legal protection
- ✅ Minimal risk

**Cons:**
- ❌ **50-70% DEVELOPMENT OVERHEAD**
- ❌ Impossible for simple projects
- ❌ Over-engineering
- ❌ Poor UX (too many consent prompts)

**Why NOT:** Not proportional. We're not a bank or hospital.

**ES - Enfoque:**
- Nunca guardar NADA de PII
- Encriptar todo
- Flujos consent complejos
- Audit externo cada 6 meses

**Pros:**
- ✅ Máxima protección legal
- ✅ Riesgo mínimo

**Contras:**
- ❌ **50-70% OVERHEAD DESARROLLO**
- ❌ Imposible para proyectos simples
- ❌ Over-engineering
- ❌ UX pobre (demasiados prompts consent)

**Por qué NO:** No proporcional. No somos banco ni hospital.

---

### Alternative C: Pragmatic Compliance (CHOSEN) / Alternativa C: Compliance Pragmático (ELEGIDA) ⭐

**EN - Approach:**
- Identify PII in EVERY project
- Implement 5 core requirements (non-negotiable)
- Document everything in COMPLIANCE.md
- Test critical flows
- Accept 15-20% overhead

**Pros:**
- ✅ **LEGAL PROTECTION:** Compliant with GDPR
- ✅ **PROPORTIONAL:** Not over-engineering
- ✅ **COMPETITIVE ADVANTAGE:** Most developers ignore this
- ✅ **CLIENT TRUST:** Professional approach
- ✅ **FUTURE-PROOF:** Easy to upgrade if needed

**Cons:**
- ❌ 15-20% development overhead
- ❌ Requires discipline
- ❌ Ongoing maintenance

**Why YES:** Balance between risk and practicality. Good enough for 95% of projects.

**ES - Enfoque:**
- Identificar PII en CADA proyecto
- Implementar 5 requisitos core (no-negociables)
- Documentar todo en COMPLIANCE.md
- Testear flows críticos
- Aceptar 15-20% overhead

**Pros:**
- ✅ **PROTECCIÓN LEGAL:** Compliant con GDPR
- ✅ **PROPORCIONAL:** No over-engineering
- ✅ **VENTAJA COMPETITIVA:** Mayoría developers ignoran esto
- ✅ **CONFIANZA CLIENTE:** Enfoque profesional
- ✅ **FUTURE-PROOF:** Fácil upgrade si necesario

**Contras:**
- ❌ 15-20% overhead desarrollo
- ❌ Requiere disciplina
- ❌ Mantenimiento ongoing

**Por qué SÍ:** Balance entre riesgo y practicidad. Suficiente para 95% proyectos.

---

## Consequences / Consecuencias

### Positive / Positivas ✅

**EN:**
1. **Legal Protection** - Defensible position if complaint
2. **Professional Differentiation** - Serious approach shows maturity
3. **Client Trust** - Enterprise clients REQUIRE this
4. **Better Architecture** - Thinking about data = better design
5. **Future-Proof** - Easy to upgrade compliance if regulations tighten

**ES:**
1. **Protección Legal** - Posición defendible si hay queja
2. **Diferenciación Profesional** - Enfoque serio muestra madurez
3. **Confianza Cliente** - Clientes enterprise REQUIEREN esto
4. **Mejor Arquitectura** - Pensar en datos = mejor diseño
5. **Future-Proof** - Fácil upgrade compliance si regulaciones se endurecen

### Negative / Negativas ⚠️

**EN:**
1. **15-20% Development Overhead** - More time per feature
2. **Ongoing Maintenance** - Policies must be updated
3. **Requires Discipline** - Easy to forget in rapid development

**ES:**
1. **15-20% Overhead Desarrollo** - Más tiempo por feature
2. **Mantenimiento Ongoing** - Políticas deben actualizarse
3. **Requiere Disciplina** - Fácil olvidar en desarrollo rápido

---

## Success Metrics / Métricas de Éxito

**EN:**
- ✅ Every project has COMPLIANCE.md
- ✅ Every project has tested deletion flow
- ✅ Zero PII breaches
- ✅ Client feedback: "You take this seriously"

**ES:**
- ✅ Cada proyecto tiene COMPLIANCE.md
- ✅ Cada proyecto tiene flow eliminación testeado
- ✅ Cero PII breaches
- ✅ Feedback cliente: "Tomas esto en serio"

---

## References / Referencias

- **GDPR Official:** https://gdpr.eu
- **ADR-000:** Principle 4 (Compliance = Power)
- **Template:** compliance-template.md

---

**Signed / Firmado:** Pablo  
**Next review / Revisión próxima:** Any GDPR update / Cualquier actualización GDPR

*"Compliance is not overhead. It's professionalism."*  
*"Compliance no es overhead. Es profesionalismo."*