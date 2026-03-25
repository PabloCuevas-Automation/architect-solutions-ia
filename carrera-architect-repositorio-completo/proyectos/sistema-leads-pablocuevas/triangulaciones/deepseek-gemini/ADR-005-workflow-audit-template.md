# ADR-005: Workflow Audit Template / Template de Audit de Workflows

**Date / Fecha**: 2026-03-17  
**Status / Estado**: Active / Activo  
**Type / Tipo**: Universal Principle / Principio Universal (000-series)

---

## Context / Contexto

**EN**: A workflow that works is not the same as a workflow that is deliverable. Without a systematic review process, critical gaps in security, resilience, GDPR compliance, and maintainability go undetected until they become production incidents. ADR-004 covers system-level infrastructure (server, HTTPS, backups). This ADR covers internal workflow quality — a distinct and complementary layer applied during and after construction, not just before deployment.

**ES**: Un workflow que funciona no es lo mismo que un workflow entregable. Sin un proceso de revisión sistemático, las brechas críticas en seguridad, resiliencia, cumplimiento GDPR y mantenibilidad pasan desapercibidas hasta que se convierten en incidentes en producción. El ADR-004 cubre la infraestructura a nivel de sistema (servidor, HTTPS, backups). Este ADR cubre la calidad interna del workflow — una capa distinta y complementaria que se aplica durante y después de la construcción, no solo antes del deploy.

---

## Decision / Decisión

**EN**: Apply a 5-layer workflow audit to every workflow before considering it complete. The audit is not optional — if a layer fails, it is documented as conscious technical debt with a resolution date.

**ES**: Aplicar un audit de 5 capas a cada workflow antes de considerarlo completo. El audit no es opcional — si una capa falla, se documenta como deuda técnica consciente con fecha de resolución.

---

## Audit Template / Template de Audit

```markdown
# WORKFLOW AUDIT

**Workflow**: 
**Fecha**:
**Bloque/Proyecto**:

## 1. Seguridad
- [ ] Credenciales en vault — ninguna key visible en el JSON exportado
- [ ] Webhook con validación de origen o documentado por qué no aplica
- [ ] Sin PII logueado innecesariamente

## 2. Resiliencia
- [ ] On Error configurado conscientemente en cada nodo HTTP
- [ ] Fallback definido si una API externa falla
- [ ] Idempotente — ejecutar dos veces no genera duplicados

## 3. Observabilidad
- [ ] Todos los nodos tienen nombres descriptivos
- [ ] Execution log permite reconstruir qué pasó sin adivinar
- [ ] Sticky note o documentación mínima en el canvas

## 4. GDPR
- [ ] Campos PII identificados
- [ ] Cada campo PII tiene propósito justificado
- [ ] Sin campos extraídos "por si acaso"

## 5. Mantenibilidad
- [ ] Alguien ajeno puede entender el workflow en 5 minutos
- [ ] Naming de nodos refleja qué hace cada uno, no cómo

## Deuda técnica identificada
(Qué falta y por qué es aceptable ahora)

## Próxima revisión
```

---

## Relation to ADR-004 / Relación con ADR-004

| | ADR-004 | ADR-005 |
|---|---|---|
| **Qué cubre** | Infraestructura del sistema | Calidad interna del workflow |
| **Cuándo** | Antes de entregar al cliente | Durante y después de construir |
| **Alcance** | Servidor, HTTPS, backups, firewall | Credenciales, errores, GDPR, naming |

---

## Consequences / Consecuencias

**Positivas ✅**
- Cada workflow tiene un estándar mínimo de calidad antes de cerrarse
- El audit fuerza decisiones conscientes sobre GDPR y resiliencia en cada proyecto
- Diferencia el entregable de un automatizador del de un arquitecto

**Negativas ⚠️**
- Añade 15-20 minutos de revisión por workflow
- Puede generar falsa seguridad si se completa mecánicamente sin criterio real

---

## Validation / Validación

**EN**: This decision is successful if across the 3 Anchor Projects of Phase 1, the audit catches at least one issue per project that would have reached production undetected.

**ES**: Esta decisión es exitosa si en los 3 Proyectos Ancla de Fase 1, el audit detecta al menos un problema por proyecto que habría llegado a producción sin detectarse.

---

**Created / Creado**: 2026-03-17  
**Review / Revisión**: Al completar Proyecto Ancla 1