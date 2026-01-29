 # Template Selection Guide / Guía de Selección de Templates

**Last update / Última actualización:** 2026-01-28  
**Purpose / Propósito:** Help you choose the right ADR template / Ayudarte a elegir el template ADR correcto

---

## Quick Decision Tree / Árbol de Decisión Rápido

```
EN:
┌─ Is this a strategic decision affecting multiple months?
│  └─ YES → Use Complete Template
│  └─ NO  → Continue ↓
│
├─ Do you have 3+ complex alternatives to compare?
│  └─ YES → Use Complete Template
│  └─ NO  → Continue ↓
│
├─ Is this easily reversible (low cost)?
│  └─ YES → Use Minimal Template
│  └─ NO  → Use Complete Template
│
└─ Do you need to write it in under 30 minutes?
   └─ YES → Use Minimal Template
   └─ NO  → Use Complete Template

ES:
┌─ ¿Es decisión estratégica que afecta múltiples meses?
│  └─ SÍ → Usa Template Completo
│  └─ NO → Continúa ↓
│
├─ ¿Tienes 3+ alternativas complejas a comparar?
│  └─ SÍ → Usa Template Completo
│  └─ NO → Continúa ↓
│
├─ ¿Es fácilmente reversible (bajo costo)?
│  └─ SÍ → Usa Template Mínimo
│  └─ NO → Usa Template Completo
│
└─ ¿Necesitas escribirlo en menos de 30 minutos?
   └─ SÍ → Usa Template Mínimo
   └─ NO → Usa Template Completo
```

---

## Template Comparison / Comparación de Templates

| Criterion / Criterio | Minimal / Mínimo | Complete / Completo |
|---------------------|------------------|-------------------|
| **EN: Writing time** / **ES: Tiempo escritura** | 15-30 min | 1-3 hours |
| **EN: Length** / **ES: Longitud** | 1 page / 1 página | 3-4 pages / 3-4 páginas |
| **EN: Reading time** / **ES: Tiempo lectura** | 3-5 min | 10-15 min |
| **EN: Alternatives** / **ES: Alternativas** | 2-3 | 3-5+ |
| **EN: Detail level** / **ES: Nivel detalle** | Essential / Esencial | Exhaustive / Exhaustivo |
| **EN: Best for** / **ES: Mejor para** | Tactical / Tácticas | Strategic / Estratégicas |

---

## Use Minimal Template When / Usa Template Mínimo Cuando

### EN - Situations:

✅ **Tactical decisions:**
- Choosing a library (Axios vs Fetch)
- Selecting a code pattern (Singleton vs Factory)
- Tool selection (VSCode vs WebStorm)

✅ **Low-cost reversibility:**
- Can change in 1-2 days if wrong
- Minimal technical debt
- Doesn't affect other teams

✅ **Clear alternatives:**
- 2-3 obvious options
- Simple pros/cons
- Quick evaluation

✅ **Time-sensitive:**
- Need to decide and document today
- Part of active sprint
- Blocking immediate work

### ES - Situaciones:

✅ **Decisiones tácticas:**
- Elegir library (Axios vs Fetch)
- Seleccionar patrón código (Singleton vs Factory)
- Selección herramienta (VSCode vs WebStorm)

✅ **Reversibilidad bajo costo:**
- Puedes cambiar en 1-2 días si es incorrecto
- Deuda técnica mínima
- No afecta otros equipos

✅ **Alternativas claras:**
- 2-3 opciones obvias
- Pros/contras simples
- Evaluación rápida

✅ **Tiempo-sensible:**
- Necesitas decidir y documentar hoy
- Parte de sprint activo
- Bloquea trabajo inmediato

---

## Use Complete Template When / Usa Template Completo Cuando

### EN - Situations:

✅ **Strategic decisions:**
- Architecture overhaul (monolith → microservices)
- Cloud provider selection (AWS vs Azure vs GCP)
- Major framework choice (React vs Vue vs Angular)
- Data storage strategy (SQL vs NoSQL vs hybrid)

✅ **High-cost reversibility:**
- Would take weeks/months to change
- Significant technical debt if wrong
- Affects multiple teams/systems

✅ **Complex alternatives:**
- 3-5+ viable options
- Many evaluation criteria
- Need formal comparison

✅ **Long-term impact:**
- Decision affects 6+ months
- Foundation for future work
- Sets precedent for similar decisions

✅ **Stakeholder buy-in needed:**
- Multiple teams affected
- Budget implications
- Need executive approval

### ES - Situaciones:

✅ **Decisiones estratégicas:**
- Cambio arquitectura (monolith → microservices)
- Selección cloud provider (AWS vs Azure vs GCP)
- Elección framework mayor (React vs Vue vs Angular)
- Estrategia almacenamiento datos (SQL vs NoSQL vs híbrido)

✅ **Reversibilidad alto costo:**
- Tomaría semanas/meses cambiar
- Deuda técnica significativa si es incorrecto
- Afecta múltiples equipos/sistemas

✅ **Alternativas complejas:**
- 3-5+ opciones viables
- Muchos criterios evaluación
- Necesitas comparación formal

✅ **Impacto largo plazo:**
- Decisión afecta 6+ meses
- Fundación para trabajo futuro
- Sienta precedente para decisiones similares

✅ **Necesitas buy-in stakeholders:**
- Múltiples equipos afectados
- Implicaciones presupuesto
- Necesitas aprobación ejecutiva

---

## Real Examples / Ejemplos Reales

### EN - Minimal Template Examples:

**Example 1: Testing Library**
```
Decision: Use Jest instead of Mocha for unit testing
Why: Jest has better defaults and less configuration
Time to decide: 20 minutes
ADR: 1 page
```

**Example 2: Logging Format**
```
Decision: Use structured JSON logs instead of plain text
Why: Better for parsing and monitoring tools
Time to decide: 30 minutes
ADR: 1 page
```

**Example 3: HTTP Client**
```
Decision: Use Axios instead of native Fetch
Why: Better error handling and interceptors
Time to decide: 15 minutes
ADR: 1 page
```

### ES - Ejemplos Template Mínimo:

**Ejemplo 1: Testing Library**
```
Decisión: Usar Jest en lugar de Mocha para unit testing
Por qué: Jest tiene mejores defaults y menos configuración
Tiempo decidir: 20 minutos
ADR: 1 página
```

**Ejemplo 2: Formato de Logging**
```
Decisión: Usar logs JSON estructurados en lugar de texto plano
Por qué: Mejor para parsing y herramientas monitoring
Tiempo decidir: 30 minutos
ADR: 1 página
```

**Ejemplo 3: Cliente HTTP**
```
Decisión: Usar Axios en lugar de Fetch nativo
Por qué: Mejor manejo errores e interceptores
Tiempo decidir: 15 minutos
ADR: 1 página
```

---

### EN - Complete Template Examples:

**Example 1: Database Selection**
```
Decision: PostgreSQL as primary database
Alternatives: MySQL, MongoDB, Aurora
Evaluation: Performance, scaling, cost, team expertise
Impact: Foundation for next 2 years
ADR: 4 pages with detailed comparison
```

**Example 2: Automation Platform**
```
Decision: N8N self-hosted over Make.com
Alternatives: Make, Zapier, custom Python
Evaluation: Cost, control, scalability, learning curve
Impact: Core tool for next 6 months
ADR: 3 pages with cost analysis
```

**Example 3: Authentication Strategy**
```
Decision: JWT + OAuth2 hybrid approach
Alternatives: Sessions, JWT-only, OAuth2-only
Evaluation: Security, UX, compliance (GDPR)
Impact: Affects all user-facing features
ADR: 4 pages with security analysis
```

### ES - Ejemplos Template Completo:

**Ejemplo 1: Selección Base de Datos**
```
Decisión: PostgreSQL como base de datos principal
Alternativas: MySQL, MongoDB, Aurora
Evaluación: Performance, escalado, costo, expertise equipo
Impacto: Fundación para próximos 2 años
ADR: 4 páginas con comparación detallada
```

**Ejemplo 2: Plataforma Automatización**
```
Decisión: N8N self-hosted sobre Make.com
Alternativas: Make, Zapier, Python custom
Evaluación: Costo, control, escalabilidad, curva aprendizaje
Impacto: Herramienta core próximos 6 meses
ADR: 3 páginas con análisis costos
```

**Ejemplo 3: Estrategia Autenticación**
```
Decisión: Enfoque híbrido JWT + OAuth2
Alternativas: Sessions, JWT-solo, OAuth2-solo
Evaluación: Seguridad, UX, compliance (GDPR)
Impacto: Afecta todas features usuario
ADR: 4 páginas con análisis seguridad
```

---

## Common Mistakes / Errores Comunes

### EN - Avoid:

❌ **Using Complete Template for everything**
- Wastes time on trivial decisions
- Creates documentation debt
- Slows down development

❌ **Using Minimal Template for strategic decisions**
- Lacks justification for important choices
- Missing context for future reviewers
- Inadequate for stakeholder buy-in

❌ **Not using ADRs at all**
- Lost institutional knowledge
- Repeated debates on same topics
- No audit trail for decisions

### ES - Evitar:

❌ **Usar Template Completo para todo**
- Desperdicias tiempo en decisiones triviales
- Crea deuda documentación
- Ralentiza desarrollo

❌ **Usar Template Mínimo para decisiones estratégicas**
- Falta justificación para elecciones importantes
- Falta contexto para revisores futuros
- Inadecuado para buy-in stakeholders

❌ **No usar ADRs en absoluto**
- Conocimiento institucional perdido
- Debates repetidos sobre mismos temas
- Sin audit trail para decisiones

---

## Upgrade Path / Camino de Actualización

### EN - When to Upgrade from Minimal to Complete:

**Situation / Situación:**
You wrote a Minimal ADR but realized the decision is more complex than expected.

**Action / Acción:**
1. Keep the Minimal ADR (preserve history)
2. Create new Complete ADR (supersedes Minimal)
3. Reference original in new ADR
4. Mark old ADR as "Superseded by ADR-XXX"

**Example / Ejemplo:**
```
ADR-042 (Minimal): "Use Redis for caching"
→ Discovered we need distributed caching strategy
→ ADR-055 (Complete): "Distributed caching architecture with Redis Cluster"
```

### ES - Cuándo Actualizar de Mínimo a Completo:

**Situación:**
Escribiste ADR Mínimo pero descubriste que decisión es más compleja.

**Acción:**
1. Mantén ADR Mínimo (preserva historia)
2. Crea nuevo ADR Completo (supersede Mínimo)
3. Referencia original en nuevo ADR
4. Marca ADR viejo como "Supersedido por ADR-XXX"

**Ejemplo:**
```
ADR-042 (Mínimo): "Usar Redis para caching"
→ Descubrimos necesitamos estrategia distributed caching
→ ADR-055 (Completo): "Arquitectura caching distribuido con Redis Cluster"
```

---

## Quick Start / Inicio Rápido

### EN - First Time Using ADRs?

**Start with Minimal Template:**
1. Choose a recent tactical decision
2. Set timer for 30 minutes
3. Fill out Minimal Template
4. Review with team (5 minutes)
5. Adjust and commit

**Why:**
- Lower barrier to entry
- Builds habit
- Quick wins
- Learn by doing

### ES - ¿Primera Vez Usando ADRs?

**Empieza con Template Mínimo:**
1. Elige decisión táctica reciente
2. Pon timer 30 minutos
3. Completa Template Mínimo
4. Revisa con equipo (5 minutos)
5. Ajusta y commitea

**Por qué:**
- Menor barrera entrada
- Construye hábito
- Victorias rápidas
- Aprende haciendo

---

## FAQ / Preguntas Frecuentes

### EN:

**Q: Can I modify the templates?**
A: Yes! Templates are starting points. Adapt to your context.

**Q: Do I need both sections in bilingual format?**
A: Use what fits your audience. Remove EN if Spanish-only project, vice versa.

**Q: What if I'm unsure which template to use?**
A: Start with Minimal. Easier to upgrade than downgrade.

**Q: Should I write ADRs for past decisions?**
A: Yes, especially important ones. Helps document "why we are here."

**Q: How many ADRs is too many?**
A: No limit. If decision is worth documenting, write ADR.

### ES:

**P: ¿Puedo modificar los templates?**
R: ¡Sí! Templates son puntos de partida. Adapta a tu contexto.

**P: ¿Necesito ambas secciones en formato bilingüe?**
R: Usa lo que encaje con tu audiencia. Elimina EN si proyecto solo español, viceversa.

**P: ¿Qué pasa si no estoy seguro qué template usar?**
R: Empieza con Mínimo. Más fácil actualizar que reducir.

**P: ¿Debo escribir ADRs para decisiones pasadas?**
R: Sí, especialmente importantes. Ayuda documentar "por qué estamos aquí."

**P: ¿Cuántos ADRs son demasiados?**
R: Sin límite. Si decisión vale documentar, escribe ADR.

---

## Summary / Resumen

### EN:

| If decision is... | Use template... | Writing time... |
|------------------|-----------------|----------------|
| Tactical, reversible | Minimal | 15-30 min |
| Strategic, long-term | Complete | 1-3 hours |
| Unsure? | Start Minimal | Can upgrade later |

**Remember:** The best ADR is the one that gets written. Don't let perfect be enemy of good.

### ES:

| Si decisión es... | Usa template... | Tiempo escritura... |
|------------------|-----------------|---------------------|
| Táctica, reversible | Mínimo | 15-30 min |
| Estratégica, largo plazo | Completo | 1-3 horas |
| ¿No seguro? | Empieza Mínimo | Puedes actualizar después |

**Recuerda:** El mejor ADR es el que se escribe. No dejes que perfecto sea enemigo de bueno.

---

*Guide v1.0 - Created / Creado: 2026-01-28*
*Bilingual EN/ES - Choose wisely, document well / Elige sabiamente, documenta bien*