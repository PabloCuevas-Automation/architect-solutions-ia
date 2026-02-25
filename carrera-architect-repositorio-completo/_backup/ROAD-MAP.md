# ROADMAP ACTUAL — FEBRERO 2026

**Fecha última actualización:** 22/02/2026  
**Estado:** 🔄 Transición activa — Post-proyecto web, Pre-N8N productivo

---

## Estado actual — dónde estoy hoy

### ✅ Completado
- **Fase 0**: Pensamiento sistémico, ADRs, GDPR fundamentos, setup técnico
- **Fase 0.5**: Terminal, Git, GitHub, Node.js, PostgreSQL, Docker instalado
- **JavaScript (Fase 1)**: Aprobado examen curso Trento (16/02). Variables, objetos, arrays, funciones, DOM, ES6 módulos, fetch, async/await básico
- **Proyecto web**: pablocuevas.it en producción — 98/100 PageSpeed, Firebase activo, GDPR compliant, CI/CD funcionando

### 🎯 Inmediato (próximas 2 semanas)
1. Instalar N8N local (Docker ya instalado — paso natural)
2. Primera automatización: Firebase Firestore → N8N → notificación leads

### ⏳ Fase 1 en curso (próximos 3-4 meses)
- N8N productivo: workflows reales, Code node con JS, integraciones API
- Primer cliente: proceso definido, outreach activo en mercado Rovereto/Trento
- Primer caso de estudio documentado con métricas reales

---

## Documentos vigentes

### Guía táctica activa
- ✅ `21-02-recalibracion_fase_1.md` — plan Fase 1 activo (días 1-25 ejecutados, días 26-42 en curso)

### Visión de largo plazo (NO plan táctico)
- ✅ `ROADMAP_ARCHITECT_SOLUTION_IA.md` — Fases 2-4 intactas como brújula

### Análisis de estado
- ✅ `21-02-analisis-estado-pablo-cuevas.md` — inventario real de competencias, deuda técnica y posicionamiento

---

## ADRs vigentes

### Serie 000 — Principios y metodología transversal

| ID | Decisión | Estado |
|----|----------|--------|
| ADR-000 | Principios arquitectónicos fundamentales | ✅ Activo |
| ADR-001 | Documentación de decisiones vía ADRs | ✅ Activo |
| ADR-002 | Manejo de PII — compliance-first obligatorio | ✅ Activo |
| ADR-003 | Naming conventions kebab-case | ✅ Activo |

### Serie 100 — Decisiones de la carrera autodidacta

| ID | Decisión | Estado | Validación |
|----|----------|--------|------------|
| ADR-100 | Pensamiento sistémico antes que skills verticales | ✅ Activo | Validado: proyecto web construido como sistema, no como código |
| ADR-101 | Priorizar JavaScript sobre Python en Fase 1 | ✅ Validado | Examen aprobado 16/02, pablocuevas.it en producción, Firebase integrado |
| ADR-102 | Proyecto académico como infraestructura de negocio real | ✅ Validado | 98/100 PageSpeed, pipeline de leads activo, compliance verificado |
| ADR-103 | Perfil de aprendizaje: construcción sobre abstracción | ✅ Activo | Documento de autoconocimiento permanente — sin fecha de revisión |

---

## Timeline recalibrado

| Hito | Fecha original | Estado real |
|------|---------------|-------------|
| Examen JavaScript | 16/02/2026 | ✅ Aprobado |
| Proyecto web en producción | 15-20/02/2026 | ✅ Live desde 19/02 |
| N8N instalado local | 10/03/2026 (plan original) | 🎯 Objetivo: antes de 01/03 |
| Primera automatización propia | — | 🎯 Objetivo: antes de 10/03 |
| Primer cliente contactado | — | 🎯 Objetivo: antes de 31/03 |
| €1.5k/mes | 31/03/2026 | ⚠️ Recalibrar: más realista Q2 2026 |

---

## Principios operativos vigentes (no cambiar)

1. **Aprendizaje por construcción** — N8N se aprende resolviendo el problema real del formulario, no en abstracto
2. **Proyecto ancla** — cada nueva tecnología debe vincularse a un entregable concreto
3. **Evidencia antes que expansión** — no añadir secciones al sitio sin contenido real
4. **Deuda técnica consciente documentada** — resolver Firebase en termini.html antes de empezar N8N
5. **Anti vendor lock-in** — N8N self-hosted sobre cloud, datos propios

---

## Regla de actualización de este documento

Actualizar cuando:
- ✅ Se completa un hito
- ✅ Se toma una decisión que cambia el timeline
- ✅ Se añade un ADR nuevo

NO actualizar:
- ❌ Por "mantener perfecto"
- ❌ Por cambios menores que no afectan dirección
