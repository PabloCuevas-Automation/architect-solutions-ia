# WORKFLOW AUDIT TEMPLATE

**Workflow**: [nombre del workflow]
**Fecha**:
**Bloque/Proyecto**:
**Auditado por**: Pablo Cuevas

---

## 1. Seguridad
- [ ] Credenciales en vault — ninguna key visible en el JSON exportado
- [ ] Webhook con validación de origen, o documentado por qué no aplica
- [ ] Sin PII logueado innecesariamente en el execution log

**Notas:**

---

## 2. Resiliencia
- [ ] On Error configurado conscientemente en cada nodo HTTP (no por defecto)
- [ ] Fallback definido si una API externa falla
- [ ] Idempotente — ejecutar dos veces con los mismos datos no genera duplicados

**Notas:**

---

## 3. Observabilidad
- [ ] Todos los nodos tienen nombres descriptivos (no "Code node 3")
- [ ] Execution log permite reconstruir qué pasó sin adivinar
- [ ] Hay sticky note o documentación mínima en el canvas

**Notas:**

---

## 4. GDPR
- [ ] Campos PII identificados explícitamente
- [ ] Cada campo PII tiene propósito justificado (purpose limitation)
- [ ] Sin campos extraídos "por si acaso"

**Campos PII en este workflow:**
| Campo | Propósito |
|-------|-----------|
| | |

**Notas:**

---

## 5. Mantenibilidad
- [ ] Alguien ajeno puede entender el workflow en 5 minutos
- [ ] Naming de nodos refleja qué hace cada uno, no cómo lo hace

**Notas:**

---

## Resultado

| Capa | Estado | Observación |
|------|--------|-------------|
| 1. Seguridad | ✅ / ⚠️ / ❌ | |
| 2. Resiliencia | ✅ / ⚠️ / ❌ | |
| 3. Observabilidad | ✅ / ⚠️ / ❌ | |
| 4. GDPR | ✅ / ⚠️ / ❌ | |
| 5. Mantenibilidad | ✅ / ⚠️ / ❌ | |

**Veredicto**: Entregable / No entregable / Entregable con deuda documentada

---

## Deuda técnica identificada

| Deuda | Impacto | Resolución programada |
|-------|---------|----------------------|
| | | |

---

## Próxima revisión