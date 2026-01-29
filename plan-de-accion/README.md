# Best Practices / Buenas Prácticas

**EN:** This folder contains core architectural best practices, decision templates, and compliance guidelines for the Architect Solutions IA project.

**ES:** Esta carpeta contiene buenas prácticas arquitectónicas fundamentales, templates de decisiones y guías de compliance para el proyecto Architect Solutions IA.

---

## Philosophy / Filosofía

**EN:**

This is not a collection of "generic best practices" copied from the internet. These are **decision frameworks and principles specifically designed for this learning journey** from semi-professional footballer to AI Solutions Architect.

**Core beliefs:**
1. **Decisions > Code** - Architecture is fundamentally about making good decisions, not writing perfect code
2. **Documentation = Thinking** - Writing forces clarity; undocumented decisions are lost decisions
3. **Context matters** - There are no universal "best practices," only good decisions for specific contexts
4. **Trade-offs are honest** - Every decision has costs; acknowledging them is professional maturity

**ES:**

Esto no es una colección de "buenas prácticas genéricas" copiadas de internet. Son **frameworks de decisión y principios diseñados específicamente para este viaje de aprendizaje** de futbolista semi-profesional a AI Solutions Architect.

**Creencias fundamentales:**
1. **Decisiones > Código** - Arquitectura es fundamentalmente sobre tomar buenas decisiones, no escribir código perfecto
2. **Documentación = Pensamiento** - Escribir fuerza claridad; decisiones no documentadas son decisiones perdidas
3. **Contexto importa** - No hay "buenas prácticas" universales, solo buenas decisiones para contextos específicos
4. **Trade-offs son honestos** - Toda decisión tiene costos; reconocerlos es madurez profesional

---

## Contents / Contenidos

### 📋 Core ADRs / ADRs Fundamentales

**EN:** Architecture Decision Records that establish foundational principles for this project.

**ES:** Architecture Decision Records que establecen principios fundamentales para este proyecto.

#### [ADR-000: Fundamental Architectural Principles](./ADR-000-principios-arquitectonicos-fundamentales.md)
**EN:** 7 core principles that guide all architectural decisions in this project.

**ES:** 7 principios fundamentales que guían todas las decisiones arquitectónicas en este proyecto.

**Key principles / Principios clave:**
- Context determines decisions / Contexto determina decisiones
- Sovereignty before convenience / Soberanía antes que conveniencia
- Simplicity before sophistication / Simplicidad antes que sofisticación
- Documentation is architecture / Documentación es arquitectura
- Progressive learning / Aprendizaje progresivo
- Failure is information / Fallo es información
- Value hierarchy: Decisions > Systems > Code / Jerarquía valor: Decisiones > Sistemas > Código

---

#### [ADR-001: Decision Documentation via ADRs](./ADR-001-documentacion-decisiones-via-adrs.md)
**EN:** Why and how to use Architecture Decision Records as primary decision documentation method.

**ES:** Por qué y cómo usar Architecture Decision Records como método principal de documentación de decisiones.

**Key decisions / Decisiones clave:**
- ADRs as mandatory for significant decisions / ADRs obligatorios para decisiones significativas
- Two templates: Complete (strategic) vs Minimal (tactical) / Dos templates: Completo (estratégico) vs Mínimo (táctico)
- Numbering system and status workflow / Sistema numeración y workflow de estados
- Review and evolution criteria / Criterios revisión y evolución

---

#### [ADR-002: PII Handling in Projects](./ADR-002-manejo-pii-proyectos.md)
**EN:** Mandatory policy for handling Personally Identifiable Information (PII) in all projects to ensure GDPR compliance.

**ES:** Política obligatoria para manejo de Información de Identificación Personal (PII) en todos los proyectos para asegurar compliance GDPR.

**Core requirements / Requisitos fundamentales:**
1. Data minimization / Minimización datos
2. Explicit consent / Consentimiento explícito
3. Deletion mechanisms / Mecanismos eliminación
4. Audit trail / Rastro auditoría
5. Automatic retention / Retención automática

---

### 📝 Templates

#### [Complete ADR Template](./adr-template-completo.md)
**EN:** Comprehensive template for strategic decisions with long-term impact.

**ES:** Template exhaustivo para decisiones estratégicas con impacto largo plazo.

**Use for / Usar para:**
- Strategic decisions (6+ months impact) / Decisiones estratégicas (impacto 6+ meses)
- High-cost reversibility / Reversibilidad alto costo
- Multiple complex alternatives / Múltiples alternativas complejas
- Stakeholder buy-in needed / Necesitas buy-in stakeholders

**Writing time / Tiempo escritura:** 1-3 hours / 1-3 horas

---

#### [Minimal ADR Template](./adr-template-minimo.md)
**EN:** Lightweight template for tactical decisions that need quick documentation.

**ES:** Template ligero para decisiones tácticas que necesitan documentación rápida.

**Use for / Usar para:**
- Tactical decisions (tool, library, pattern) / Decisiones tácticas (herramienta, library, patrón)
- Low-cost reversibility / Reversibilidad bajo costo
- 2-3 clear alternatives / 2-3 alternativas claras
- Need to document in 15-30 min / Necesitas documentar en 15-30 min

**Writing time / Tiempo escritura:** 15-30 minutes / 15-30 minutos

---

#### [Template Selection Guide](./guia-templates.md)
**EN:** Decision tree and guidance for choosing between Complete and Minimal ADR templates.

**ES:** Árbol de decisión y guía para elegir entre templates ADR Completo y Mínimo.

**Includes / Incluye:**
- Quick decision tree / Árbol decisión rápido
- Template comparison table / Tabla comparación templates
- Real examples for each template / Ejemplos reales cada template
- Common mistakes to avoid / Errores comunes a evitar
- Upgrade path (Minimal → Complete) / Camino actualización (Mínimo → Completo)

---

#### [Compliance Checklist Template](./compliance-template.md)
**EN:** Comprehensive GDPR and data privacy compliance checklist for projects handling user data.

**ES:** Checklist exhaustivo compliance GDPR y privacidad datos para proyectos que manejan datos usuario.

**Covers / Cubre:**
- Data inventory / Inventario datos
- Legal basis documentation / Documentación base legal
- User rights implementation / Implementación derechos usuario
- Consent management / Gestión consentimiento
- Security measures / Medidas seguridad
- Breach notification procedures / Procedimientos notificación brechas

---

## How to Use This Folder / Cómo Usar Esta Carpeta

### EN - For Daily Work:

**1. Before making a significant decision:**
- Read ADR-000 (Fundamental Principles) to align thinking
- Choose template: Strategic? → Complete. Tactical? → Minimal
- Use Template Selection Guide if unsure

**2. While writing ADR:**
- Follow template structure but adapt to context
- Focus on "why" not just "what"
- Document trade-offs honestly
- Include validation criteria

**3. After decision is implemented:**
- Review ADR quarterly or when situation changes
- Update status if decision is superseded
- Add lessons learned section

**4. For new projects:**
- Start with Compliance Checklist if handling user data
- Create ADR-000 equivalent for project-specific principles
- Establish ADR numbering system

### ES - Para Trabajo Diario:

**1. Antes de tomar decisión significativa:**
- Lee ADR-000 (Principios Fundamentales) para alinear pensamiento
- Elige template: ¿Estratégica? → Completo. ¿Táctica? → Mínimo
- Usa Guía Selección Templates si no estás seguro

**2. Mientras escribes ADR:**
- Sigue estructura template pero adapta a contexto
- Enfócate en "por qué" no solo "qué"
- Documenta trade-offs honestamente
- Incluye criterios validación

**3. Después de implementar decisión:**
- Revisa ADR trimestralmente o cuando situación cambie
- Actualiza estado si decisión es supersedida
- Añade sección lecciones aprendidas

**4. Para proyectos nuevos:**
- Empieza con Compliance Checklist si manejas datos usuario
- Crea equivalente ADR-000 para principios específicos proyecto
- Establece sistema numeración ADRs

---

## Evolution / Evolución

### EN - This folder will evolve:

**Current state (Phase 0-1):**
- Core architectural principles established
- ADR methodology defined
- Basic templates available
- PII handling policy mandatory

**Future additions (Phase 2-3):**
- System design patterns
- API design guidelines
- Testing strategies
- Performance optimization frameworks
- MLOps best practices
- Cost optimization playbooks

**Not included (intentionally):**
- Generic "coding best practices" (context-dependent)
- Framework-specific guidelines (too narrow)
- Opinions without trade-off analysis (not helpful)

### ES - Esta carpeta evolucionará:

**Estado actual (Fase 0-1):**
- Principios arquitectónicos fundamentales establecidos
- Metodología ADR definida
- Templates básicos disponibles
- Política manejo PII obligatoria

**Adiciones futuras (Fase 2-3):**
- Patrones diseño sistemas
- Guías diseño APIs
- Estrategias testing
- Frameworks optimización performance
- Best practices MLOps
- Playbooks optimización costos

**No incluido (intencionalmente):**
- "Buenas prácticas coding" genéricas (depende contexto)
- Guías específicas frameworks (muy estrechas)
- Opiniones sin análisis trade-offs (no ayudan)

---

## Key Concepts / Conceptos Clave

### EN:

**Architecture Decision Record (ADR):**
A document that captures an important architectural decision, including context, alternatives considered, the decision made, and its consequences.

**PII (Personally Identifiable Information):**
Any data that can be used to identify an individual (name, email, IP address, location, etc.). Requires special handling under GDPR.

**GDPR (General Data Protection Regulation):**
EU regulation protecting personal data and privacy. Mandatory compliance for projects serving EU users.

**Trade-off:**
A situation where you sacrifice one quality to gain another. All decisions involve trade-offs; good architecture acknowledges them explicitly.

**Context:**
The specific circumstances (constraints, requirements, goals) that influence a decision. Same technical solution may be right in one context, wrong in another.

### ES:

**Architecture Decision Record (ADR):**
Documento que captura decisión arquitectónica importante, incluyendo contexto, alternativas consideradas, decisión tomada y sus consecuencias.

**PII (Información Identificación Personal):**
Cualquier dato que puede usarse para identificar individuo (nombre, email, dirección IP, ubicación, etc.). Requiere manejo especial bajo GDPR.

**GDPR (Reglamento General Protección Datos):**
Regulación UE protegiendo datos personales y privacidad. Compliance obligatorio para proyectos sirviendo usuarios UE.

**Trade-off:**
Situación donde sacrificas una cualidad para ganar otra. Todas decisiones involucran trade-offs; buena arquitectura los reconoce explícitamente.

**Contexto:**
Circunstancias específicas (restricciones, requisitos, objetivos) que influyen decisión. Misma solución técnica puede ser correcta en un contexto, incorrecta en otro.

---

## Frequently Asked Questions / Preguntas Frecuentes

### EN:

**Q: Do I need to write an ADR for every decision?**
A: No. Only for decisions that are significant, hard to reverse, or set precedent. Use judgment.

**Q: Can I modify the templates?**
A: Absolutely. Templates are starting points. Adapt to your needs.

**Q: What if I made a wrong decision?**
A: Update the ADR status to "Superseded" and create new ADR with lessons learned. Mistakes documented are knowledge gained.

**Q: Should I write ADRs in English or Spanish?**
A: This project uses bilingual format for templates (reusable). For project-specific ADRs, choose based on audience. See ADR-003 for internationalization strategy.

**Q: How many ADRs is too many?**
A: There's no limit. Better too many than important decisions undocumented.

### ES:

**P: ¿Necesito escribir ADR para cada decisión?**
R: No. Solo para decisiones significativas, difíciles revertir, o que sientan precedente. Usa juicio.

**P: ¿Puedo modificar los templates?**
R: Absolutamente. Templates son puntos partida. Adapta a tus necesidades.

**P: ¿Qué pasa si tomé decisión incorrecta?**
R: Actualiza estado ADR a "Supersedido" y crea nuevo ADR con lecciones aprendidas. Errores documentados son conocimiento ganado.

**P: ¿Debo escribir ADRs en inglés o español?**
R: Este proyecto usa formato bilingüe para templates (reutilizables). Para ADRs específicos proyecto, elige según audiencia. Ver ADR-003 para estrategia internacionalización.

**P: ¿Cuántos ADRs son demasiados?**
R: No hay límite. Mejor demasiados que decisiones importantes sin documentar.

---

## Contributing / Contribuir

### EN:

This is a personal learning project, but the principles and templates can be useful for others.

**If you find these helpful:**
- Fork and adapt to your context
- Share improvements (optional)
- Credit original source (appreciated)

**What makes a good addition:**
- Solves real problem you encountered
- Includes trade-off analysis
- Provides concrete examples
- Is context-aware (not "always do X")

### ES:

Este es proyecto aprendizaje personal, pero principios y templates pueden ser útiles para otros.

**Si encuentras esto útil:**
- Fork y adapta a tu contexto
- Comparte mejoras (opcional)
- Acredita fuente original (apreciado)

**Qué hace buena adición:**
- Resuelve problema real que encontraste
- Incluye análisis trade-offs
- Proporciona ejemplos concretos
- Es consciente contexto (no "siempre haz X")

---

## License / Licencia

**EN:** These templates and practices are freely available for personal and commercial use. Attribution appreciated but not required.

**ES:** Estos templates y prácticas están disponibles libremente para uso personal y comercial. Atribución apreciada pero no requerida.

---

## Contact / Contacto

**Project / Proyecto:** Architect Solutions IA  
**Author / Autor:** Pablo  
**Start date / Fecha inicio:** January 2026 / Enero 2026  
**Current phase / Fase actual:** Phase 0-1 (Mental Reprogramming + Flow Integrator)

---

**Last updated / Última actualización:** 2026-01-28

---

*"Good architecture is not about perfect decisions. It's about documented decisions that can be learned from."*

*"Buena arquitectura no es sobre decisiones perfectas. Es sobre decisiones documentadas de las que se puede aprender."*