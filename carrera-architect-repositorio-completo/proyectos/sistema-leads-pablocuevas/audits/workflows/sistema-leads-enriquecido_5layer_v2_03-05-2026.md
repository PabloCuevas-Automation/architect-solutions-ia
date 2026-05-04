# AUDIT 5-LAYER — sistema-leads-pablocuevas.it
## Workflow: sistema-leads-enriquecido

**Referencia**: ADR-005 — 5-Layer Workflow Audit Template  
**Versión**: v2.0  
**Fecha**: 03 de mayo 2026  
**Auditado por**: Pablo Cuevas + Claude (revisión sobre JSON exportado)  
**Supersede**: v1.0 (marzo 2026 — pre-PostgreSQL, pre-Capas 2 y 3)  
**Estado**: ⚠️ APROBADO CON DEUDAS — no apto para producción sin resolver bloqueos marcados 🔴

---

## QUÉ CAMBIÓ DESDE v1.0

La v1.0 auditó el sistema en estado pre-persistencia: solo Webhook → procesador → Abstract → clasificador → Telegram. Sin base de datos.

**Añadido desde entonces:**
- Capa 1: `capa1-validacion-hmac` (placeholder)
- Capa 2: `capa2-persistencia-upsert` + `capa2-merge-datos` + `capa2-if-es-nuevo` + `capa2-logica-duplicado` + `capa2-if-reactivado` + tres nodos de registro de eventos + dos nodos de restauración
- Capa 3: Registro de eventos `abstract_consultado`, `abstract_error`, `clasificacion_realizada` con nodos de restauración

**Deudas v1.0 resueltas:**
- ✅ Idempotencia: deduplicación implementada con CTE atómica (ADR-201)
- ✅ Persistencia: write-before-processing implementado (ADR-204)
- ✅ Trazabilidad parcial: tabla `eventos` con 3 tipos registrados en Capa 2 y Capa 3

**Deudas v1.0 que siguen abiertas:**
- ⚠️ Sin retención de datos PII (política pendiente Bloque 6)
- ⚠️ HMAC sin implementar (Bloque 7)
- ⚠️ Capa 4 con PII completa en Telegram (ADR-207 — bloquea producción)

---

## CAPA 1 — SECURITY

### Hallazgos

**🔴 BLOQUEANTE — SQL Injection via string interpolation**

Todos los nodos PostgreSQL usan interpolación de strings directa en lugar de queries parametrizadas:

```sql
-- ACTUAL (riesgo):
VALUES ('{{ $json.body.email }}', '{{ $json.body.messaggio }}', ...)

-- En json_build_object también:
json_build_object('timestamp_original', '{{ $json.timestamp_creacion }}')
```

Si un campo contiene una comilla simple (`'`), la query falla con error de sintaxis. Con un payload construido, podría corromper o manipular datos. No es SQL injection clásico en un INSERT, pero es un vector real de corrupción de datos y fallo de workflow.

N8N permite queries parametrizadas en el nodo PostgreSQL via "Query Parameters" — esto elimina el riesgo completamente.

**⚠️ DEUDA CONOCIDA — HMAC placeholder**

`capa1-validacion-hmac` deja pasar todas las peticiones sin validar firma. Cualquier POST al endpoint procesa un lead. Aceptable en desarrollo local; **inaceptable en producción**.

Referencia: ADR-010 — Seguridad Antes que Producción.

**⚠️ DEUDA CONOCIDA — messaggio sin sanitización**

El campo `messaggio` persiste en DB sin escapar HTML. Vector XSS para cualquier interfaz de administración futura.

Referencia: ADR-207 — precondición para Capa 4.

**ℹ️ MENOR — chatId hardcodeado**

`chatId: "8043204939"` aparece en claro en los tres nodos Telegram. No es un secreto en sentido estricto (es un identificador de chat, no una credencial), pero es mejor moverlo a variable de entorno en Bloque 6 junto con el resto de la configuración de producción.

**ℹ️ MENOR — Abstract API key como query param**

Limitación documentada del proveedor. La key no viaja en header por diseño de Abstract. Riesgo mitigado: el VPS usará HTTPS (Nginx + Certbot en Bloque 6), así el query param viaja cifrado en tránsito.

### Veredicto Capa 1

⚠️ **APROBADO CON DEUDAS** para desarrollo local.  
🔴 **BLOQUEANTE para producción**: SQL injection + HMAC deben resolverse antes del deploy.

---

## CAPA 2 — RESILIENCE

### Hallazgos

**🔴 BUG CRÍTICO — Ruta de error de Abstract es un dead end**

Cuando Abstract falla, el flujo va:
```
HTTP Abstract (error) → capa3-registrar-abstract-error → FIN
```

El evento se registra en la tabla `eventos`, pero el lead **nunca llega a `enriquecedor-clasificador`**. El nodo `enriquecedor-clasificador` tiene lógica de fallback para `abstractFallo === true`, pero esa lógica **nunca se ejecuta** porque el flujo se detiene antes.

Consecuencia real: cuando Abstract falla, el sistema hace `write-before-processing` correctamente (el lead está en DB), registra el error, y luego... silencio. No hay clasificación, no hay notificación a Pablo, y el webhook no responde nunca → timeout en el formulario.

**Fix necesario**: Añadir un nodo de restauración después de `capa3-registrar-abstract-error` que devuelva el contexto del lead y lo conecte a `enriquecedor-clasificador`. La lógica de fallback ya está escrita — solo falta que se alcance.

**⚠️ ARCHITECTURAL GAP — Webhook no responde en la mayoría de rutas**

El nodo `Respond to Webhook1` solo se conecta desde `notificador-procesar-nivel1`. Todas las demás rutas (revisar Nivel 1, revisar Nivel 2, descartar, duplicado) nunca responden al webhook.

El formulario usa `responseMode: "responseNode"`, lo que significa que espera una respuesta. Las rutas sin respuesta dejan la petición colgada hasta timeout.

Mitigante actual: el formulario web escribe primero a Firebase (fire-and-forget antes del webhook N8N), por lo que el lead nunca se pierde. Pero el timeout afecta la experiencia si el JS espera la respuesta del webhook para confirmar al usuario.

Decisión requerida antes de Bloque 6: ¿conectar `Respond to Webhook1` a todas las rutas, o confirmar que el frontend no depende de esa respuesta?

**⚠️ DEUDA — Switch1 huérfano**

El nodo `Switch1` existe en el canvas pero no está conectado a ningún flujo. Es dead code. Origen probable: iteración anterior de la lógica de Nivel 2. Debe eliminarse para evitar confusión futura.

**⚠️ DEUDA — Capa 2 duplicado no responde al webhook**

`capa2-registrar-duplicado` termina el flujo sin responder al webhook. Comportamiento probablemente intencional (el duplicado real no necesita notificación), pero el webhook queda sin respuesta. Mismo problema que el punto anterior.

### Veredicto Capa 2

🔴 **BUG a corregir**: ruta de error de Abstract desconectada de la lógica de clasificación.  
⚠️ **Decisión pendiente**: política de respuesta del webhook para rutas no-procesar.

---

## CAPA 3 — OBSERVABILITY

### Hallazgos

**Estado de la tabla eventos (implementado vs. pendiente):**

| Evento | Estado | Nodo |
|--------|--------|------|
| `lead_recibido` | ✅ Implementado | `capa2-registrar-lead-recibido` |
| `duplicado_detectado` | ✅ Implementado | `capa2-registrar-duplicado` |
| `lead_reactivado` | ✅ Implementado | `capa2-registrar-lead-reactivado` |
| `abstract_consultado` | ✅ Implementado | `capa3-registrar-abstract-consultado` |
| `abstract_error` | ✅ Implementado | `capa3-registrar-abstract-error` |
| `clasificacion_realizada` | ✅ Implementado | `capa3-registrar-clasificacion` |
| `notificacion_enviada` | ⏳ Pendiente | Bloque 5 |
| `notificacion_pendiente` | ⏳ Pendiente | Bloque 5 |
| `lead_descartado` | ⏳ Pendiente | Bloque 5 |

**⚠️ GAP — abstract_error no genera clasificacion_realizada**

Cuando Abstract falla, se registra `abstract_error` pero nunca `clasificacion_realizada`. Si en algún momento el sistema necesita reconstruir qué pasó con ese lead mirando solo la tabla `eventos`, no hay registro de la decisión final. Para un auditor GDPR esto es un gap de trazabilidad.

Relacionado con el bug de Capa 2 — se resuelve con el mismo fix (conectar la ruta de error a `enriquecedor-clasificador`).

**⚠️ PENDIENTE — Execution logs contienen PII**

Los logs de ejecución de N8N contienen los datos completos del webhook (incluyendo email, nome, messaggio). En producción estos logs deben tener retención máxima de 30 días (`EXECUTIONS_DATA_MAX_AGE=30` en docker-compose del VPS).

### Veredicto Capa 3

⚠️ **APROBADO CON DEUDAS** — Cobertura buena para Capa 2 y Capa 3. Capa 4 pendiente. Gap de trazabilidad en ruta abstract_error (relacionado con bug de Capa 2).

---

## CAPA 4 — GDPR

### Hallazgos

**🔴 BLOQUEANTE — Capa 4 envía PII completa a Telegram**

Los tres nodos de notificación (`notificador-procesar-nivel1`, `notificador-revisar-nivel1`, `notificador-revisar-nivel2`) incluyen en el mensaje de Telegram:
- `nome` + `cognome` — nombre completo
- `email` — dato de contacto directo
- `messaggio` — contenido libre, puede incluir cualquier información personal
- `azienda` — empresa

Telegram no es un canal GDPR-compliant para datos personales completos. ADR-207 documenta que Capa 4 debe reconstruirse antes de producción: Telegram envía solo un link a una interfaz de administración, los datos no viajan por Telegram.

**Estado**: Documentado como bloqueante en ADR-207. No se puede activar en producción sin resolver.

**🔴 BLOQUEANTE — Sin política de retención implementada**

`messaggio` y el resto de PII están en la tabla `leads` sin job de eliminación automática. Política definida en el diagrama del sistema:

| Estado | Retención | Acción |
|--------|-----------|--------|
| descartado | 60 días | DELETE automático |
| revisar_manualmente | 6 meses | DELETE automático |
| procesado | 12 meses | DELETE automático |

Implementación: Scheduled workflow + query DELETE. Pendiente Bloque 6.

**⚠️ DEUDA — messaggio sin sanitización**

`messaggio` se persiste directamente desde el webhook sin escapar HTML/SQL. Si hay una interfaz de administración futura que renderice este campo, es un vector XSS.

**✅ CORRECTO — Minimización de datos de Abstract**

El nodo `enriquecedor-clasificador` descarta correctamente los campos PII de Abstract (`email_sender.first_name`, `email_sender.last_name`) y extrae solo los campos operacionales necesarios. Alineado con ADR-002 y la decisión de minimización documentada.

**✅ CORRECTO — Credenciales en vault**

Ninguna credential aparece en claro en el JSON exportado. Vault de N8N correctamente configurado.

### Veredicto Capa 4

🔴 **NO APTO PARA PRODUCCIÓN** — Dos bloqueos: Capa 4 con PII en Telegram (ADR-207) y sin retención automática de datos.

---

## CAPA 5 — MAINTAINABILITY

### Hallazgos

**✅ Naming consistente y descriptivo**

La convención kebab-case se aplica correctamente en todos los nodos nuevos:
`capa2-persistencia-upsert`, `capa2-merge-datos`, `capa2-if-es-nuevo`, etc. El nombre del nodo es documentación del flujo — esto cumple.

**⚠️ Nodos con nombres genéricos**

- `If` (Capa 3, routing Nivel 1 vs Nivel 2) — debería ser `capa3-if-nivel1-o-nivel2` o similar
- `If1` (Nivel 2, routing descartar vs revisar) — debería ser `nivel2-if-descartar`
- `Switch` (Capa 3, routing accion_recomendada) — debería ser `capa3-switch-accion`
- `Switch1` — huérfano, debe eliminarse

Impacto: bajo en desarrollo local. Impacto real cuando se añadan nodos nuevos y el canvas sea más grande.

**⚠️ Inconsistencia de valores en nivel2-reglas-propias**

El nodo `nivel2-reglas-propias` produce:
```javascript
accion = "Procesar"      // capital P
accion = "Revisar manualmente"  // capital R, con espacio (no underscore)
accion = "descartar"     // lowercase
```

Los valores no son consistentes entre sí. `Switch1` (huérfano) esperaría `"revisar_manualmente"` y `"descartar"`. Aunque `Switch1` no está conectado y por tanto no causa bug activo, la inconsistencia desorientaría a cualquiera que revisara el código. Normalizar a snake_case lowercase antes de Bloque 6.

**✅ Deuda técnica documentada en sticky notes**

Las dos sticky notes de deuda técnica (idempotencia, retención PII) siguen siendo válidas y precisas. El flujo de Capas 2 y 3 no tiene sticky notes propias — considerar añadir una nota de arquitectura en el canvas que describa el patrón de restauración (por qué existen los nodos `capa2-restaurar-datos-*`).

**✅ Estructura de restauración consistente**

El patrón `registrar-evento → restaurar-datos → continuar-flujo` se aplica consistentemente en Capa 2 y Capa 3. Esto es correcto y documentable como patrón reutilizable.

### Veredicto Capa 5

⚠️ **APROBADO CON AJUSTES MENORES** — Naming general bueno, inconsistencias puntuales a corregir antes de producción.

---

## RESUMEN EJECUTIVO

| Capa | Estado | Acción requerida |
|------|--------|-----------------|
| 1 — Security | ⚠️ Con deudas | 🔴 SQL injection (resolver antes de producción) · HMAC (Bloque 7) |
| 2 — Resilience | 🔴 Bug activo | 🔴 Conectar ruta error Abstract a enriquecedor-clasificador |
| 3 — Observability | ⚠️ Con deudas | Eventos Capa 4 pendientes · Execution log TTL en VPS |
| 4 — GDPR | 🔴 Bloqueante | 🔴 Capa 4 reconstrucción (ADR-207) · Retención automática (Bloque 6) |
| 5 — Maintainability | ⚠️ Menor | Normalizar nombres de nodos genéricos · Eliminar Switch1 huérfano |

### Veredicto global

**APROBADO PARA DESARROLLO LOCAL — NO APTO PARA PRODUCCIÓN**

Bloqueos para ir a producción (ordenados por prioridad):
1. 🔴 Fix bug ruta error Abstract → clasificación (Capa 2)
2. 🔴 Queries parametrizadas en PostgreSQL (Capa 1)
3. 🔴 Reconstrucción Capa 4 — Telegram sin PII (ADR-207)
4. 🔴 Job de retención automática de PII (Bloque 6)
5. ⚠️ HMAC real — deuda aceptada hasta Bloque 7

---

*Guardar en: `sistema-leads-pablocuevas/audits/workflows/`*  
*Próximo audit: al completar Bloque 5 (Capa 4 reconstruida) o antes del deploy en VPS*
