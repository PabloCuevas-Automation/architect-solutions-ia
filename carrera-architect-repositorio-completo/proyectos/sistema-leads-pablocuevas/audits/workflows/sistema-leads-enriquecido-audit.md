# WORKFLOW AUDIT — sistema-leads-enriquecido

**Workflow**: sistema-leads-enriquecido
**Fecha**: 2026-03-17
**Bloque/Proyecto**: Bloque 3 / Proyecto Ancla 1
**Auditado por**: Pablo Cuevas

---

## 1. Seguridad
- [x] Credenciales en vault — Abstract (Query Auth) y Telegram en vault, ninguna key visible en JSON exportado
- [ ] Webhook con validación de origen — **NO implementado** (deuda documentada)
- [x] Sin PII logueado innecesariamente — PII viaja entre nodos por necesidad funcional, no se loguea en canales externos

**Notas:** Chat ID de Telegram visible en el nodo — no es dato sensible, no da acceso sin el bot token.

---

## 2. Resiliencia
- [x] On Error configurado conscientemente — HTTP Abstract en modo Continue
- [x] Fallback definido — `enriquecedor-clasificador` detecta si Abstract falló (`!abstract.email_deliverability`) y asigna `revisar_manualmente` con razón explícita
- [ ] Idempotente — **NO implementado** (deuda documentada, requiere PostgreSQL)

**Notas:** Si el mismo lead llega dos veces, se generan dos notificaciones Telegram. Sin consecuencias graves sin DB.

---

## 3. Observabilidad
- [x] Todos los nodos tienen nombres descriptivos — `procesador-body1`, `HTTP Abstract (email)`, `enriquecedor-clasificador`, `notificador-telegram`
- [x] Execution log permite reconstruir qué pasó — flujo corto y bien nombrado
- [x] Sticky notes en canvas — 3 sticky notes: descripción general, documentación Abstract, deuda técnica

**Notas:** Añadir sticky note con propósito de negocio del sistema completo (pendiente menor).

---

## 4. GDPR
- [x] Campos PII identificados explícitamente
- [x] Cada campo PII tiene propósito justificado
- [x] Sin campos extraídos "por si acaso" — `first_name`, `last_name`, `email_domain`, `email_breaches` descartados conscientemente

**Campos PII en este workflow:**
| Campo | Propósito |
|-------|-----------|
| `nome` + `cognome` | Personalización de comunicación comercial |
| `email` | Canal de contacto principal + validación |
| `azienda` | Clasificación del lead por tipo de cliente |
| `servizio` | Segmentación por tipo de servicio |
| `messaggio` | Contexto de la necesidad para preparar propuesta |

**Notas:** Sin política de retención formal todavía — los datos viven en el execution log de N8N (self-hosted) y en Telegram. Resolución en Bloque 5 con PostgreSQL.

---

## 5. Mantenibilidad
- [x] Alguien ajeno puede entender el workflow en 5 minutos con nociones básicas de N8N
- [x] Naming de nodos refleja qué hace cada uno

**Notas:** El profesor validó que el workflow es legible sin explicación verbal.

---

## Resultado

| Capa | Estado | Observación |
|------|--------|-------------|
| 1. Seguridad | ⚠️ | Webhook sin validación de origen |
| 2. Resiliencia | ⚠️ | Sin idempotencia |
| 3. Observabilidad | ✅ | Sticky note general pendiente (menor) |
| 4. GDPR | ⚠️ | Sin política de retención formal |
| 5. Mantenibilidad | ✅ | |

**Veredicto**: Entregable con deuda documentada — las tres deudas tienen fecha de resolución y no representan riesgo grave en fase local sin PostgreSQL.

---

## Deuda técnica identificada

| Deuda | Impacto | Resolución programada |
|-------|---------|----------------------|
| Webhook sin validación de origen | Cualquier actor puede enviar POSTs falsos | Bloque 7 — Seguridad |
| Sin idempotencia | Mismo lead procesado dos veces genera notificación duplicada | Bloque 5 — PostgreSQL |
| Sin política de retención PII | `messaggio` y otros PII sin tiempo de vida definido | Bloque 5 — PostgreSQL + job de eliminación |

---

## Próxima revisión
Al completar Bloque 5 — cuando PostgreSQL esté integrado, re-auditar capas 1, 2 y 4.