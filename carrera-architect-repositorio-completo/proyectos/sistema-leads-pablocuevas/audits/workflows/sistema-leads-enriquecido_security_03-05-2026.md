# AUDITORÍA DE SEGURIDAD DE WORKFLOW N8N
## sistema-leads-pablocuevas.it

**Referencia**: ADR-009 — Seguridad de Aplicación en Workflows N8N  
**Basado en**: WORKFLOW-AUDIT-TEMPLATE.md  
**Fecha**: 03 de mayo 2026  
**Auditado por**: Pablo Cuevas + Claude (revisión sobre JSON exportado)

---

## IDENTIFICACIÓN

| Campo | Valor |
|-------|-------|
| **Nombre del workflow** | sistema-leads-pablocuevas.it |
| **Proyecto** | ✅ PA1 |
| **Tipo** | ✅ Trigger/Webhook |
| **Versión** | v2.0 (post-Bloque 5 — con Capas 2 y 3) |
| **Fecha de auditoría** | 03 de mayo 2026 |
| **Auditado por** | Pablo Cuevas |
| **Estado** | ❌ Rechazado — ver bloqueos en sección de veredicto |

---

## PREGUNTA 1 — INPUTS: ¿Qué entra y está validado?

### 1.1 Inventario de campos externos

| Campo | Origen | Tipo esperado | ¿Se valida tipo? | ¿Se sanitiza? | ¿Se rechaza si falla? |
|-------|--------|--------------|-----------------|--------------|----------------------|
| `nome` | Webhook body | String | ❌ NO (fallback a "sconosciuto") | ❌ NO | ❌ NO |
| `cognome` | Webhook body | String | ❌ NO (fallback a "") | ❌ NO | ❌ NO |
| `email` | Webhook body | String/email | ✅ Parcial (toLowerCase + trim) | ❌ NO | ❌ NO |
| `azienda` | Webhook body | String | ❌ NO (fallback a "non specificata") | ❌ NO | ❌ NO |
| `servizio` | Webhook body | String | ❌ NO (fallback a "non specificato") | ❌ NO | ❌ NO |
| `messaggio` | Webhook body | String | ❌ NO (fallback a "") | ❌ NO | ❌ NO |
| Abstract API response | HTTP externo | JSON | ✅ Parcial (abstractFallo guard) | N/A | ❌ NO |

**Nota sobre la validación actual**: El nodo `procesador-body1` hace optional chaining (`body.email?.toLowerCase()`) y valores por defecto, pero esto es manejo de nulls — no es validación de tipo ni de contenido. Un campo con `email = "no-es-un-email"` pasa sin problema.

### 1.2 Campos de texto libre — riesgo XSS/injection

| Campo de texto libre | ¿Se sanitiza antes de persistir en DB? | ¿Se sanitiza antes de renderizar en UI? | ¿Se usa en queries SQL? |
|---------------------|---------------------------------------|----------------------------------------|------------------------|
| `messaggio` | ❌ NO | ❌ NO (UI futura) | ✅ SÍ — vía string interpolation |
| `nome` | ❌ NO | ❌ NO (UI futura) | ✅ SÍ — vía string interpolation |
| `cognome` | ❌ NO | ❌ NO (UI futura) | ✅ SÍ — vía string interpolation |
| `azienda` | ❌ NO | ❌ NO (UI futura) | ✅ SÍ — vía string interpolation |

**Descripción del riesgo**:

Todos los campos de texto libre se insertan en PostgreSQL mediante string interpolation directa:

```sql
-- Patrón actual en capa2-persistencia-upsert:
VALUES (
  '{{ $json.body.nome }}',
  '{{ $json.body.cognome }}',
  '{{ $json.body.email }}',
  ...
)
```

Si cualquiera de estos campos contiene una comilla simple `'`, la query falla con error de sintaxis PostgreSQL. Si el contenido es específicamente construido, puede manipular la estructura de la query.

Afecta también a los nodos de eventos:
```sql
-- capa2-registrar-lead-reactivado:
json_build_object('timestamp_original', '{{ $json.timestamp_creacion }}')

-- capa3-registrar-abstract-error:
json_build_object('error_message', '{{ $json.error.message }}')
```

**Solución**: Usar el campo "Query Parameters" del nodo PostgreSQL en N8N, que permite `$1`, `$2` etc. con binding de parámetros — el driver gestiona el escaping correctamente y el riesgo desaparece.

### 1.3 Validación de autenticación

| Pregunta | Respuesta |
|----------|-----------|
| ¿El workflow tiene HMAC u otro mecanismo de autenticación de origen? | ❌ NO — placeholder activo |
| Si tiene HMAC: ¿valida timestamp para prevenir replay attacks? | N/A |
| Si NO tiene autenticación: ¿está justificado? | ⚠️ Deuda técnica consciente — Bloque 7 |

**Detalle**: El nodo `capa1-validacion-hmac` tiene el código preparado con el TODO documentado, pero pasa todas las peticiones sin validar. Cualquier actor que conozca la URL del webhook puede enviar leads falsos.

**Mitigante en producción futura**: La URL del webhook no es trivialmente enumerable (UUID como path), pero seguridad por oscuridad no es seguridad. HMAC obligatorio antes de activar en VPS con datos reales.

### 1.4 Veredicto Pregunta 1

❌ **RECHAZADO** — Inputs sin validación real de tipo ni contenido. Texto libre en queries SQL sin parametrizar. HMAC no implementado.

**Observaciones**:
```
El sistema tiene fallbacks para nulls (optional chaining) pero no tiene
validación real. La diferencia es importante:
- Fallback: "si email es null, usa null" → el sistema no falla
- Validación: "si email no tiene formato válido, rechaza la petición" → el sistema rechaza activamente entradas inválidas

En producción con datos reales, la ausencia de parametrización SQL
es el riesgo más inmediato a resolver. No requiere rediseño —
es un cambio puntual en cada nodo PostgreSQL.
```

---

## PREGUNTA 2 — DATOS: ¿Tiene acceso mínimo necesario?

### 2.1 Acceso a bases de datos

| Tabla / Campo | ¿El workflow lo usa? | ¿Lo necesita realmente? | Acción si no lo necesita |
|--------------|---------------------|------------------------|--------------------------|
| `leads.email` | ✅ SÍ | ✅ SÍ (deduplicación + enriquecimiento) | N/A |
| `leads.messaggio` | ✅ SÍ | ⚠️ Parcialmente — se persiste y notifica, pero el propósito de negocio es discutible | Revisar si necesita persistencia o solo notificación temporal |
| `leads.nome` / `cognome` | ✅ SÍ | ✅ SÍ (identificación en notificación) | N/A |
| `leads.azienda` | ✅ SÍ | ✅ SÍ (clasificación Nivel 2: `tieneEmpresa`) | N/A |
| `leads.servizio` | ✅ SÍ | ✅ SÍ (constraint de deduplicación email+servicio) | N/A |
| `eventos.*` | ✅ SÍ | ✅ SÍ (trazabilidad) | N/A |

**Nota sobre `messaggio`**: El campo `messaggio` es PII de texto libre. Se persiste indefinidamente (sin retención implementada) y se envía completo a Telegram. La pregunta de negocio es si el sistema necesita persistir el mensaje o solo notificarlo. Si la notificación es el propósito, `messaggio` podría no persistirse en DB, reduciendo el riesgo GDPR. Decisión pendiente de documentar como ADR.

### 2.2 Credenciales y APIs externas

| API / Servicio | ¿Necesaria para este workflow? | ¿El scope de la credencial es mínimo? |
|---------------|-------------------------------|--------------------------------------|
| Abstract Email Reputation API | ✅ SÍ | ✅ SÍ — solo lectura, solo email reputation |
| Telegram Bot API | ✅ SÍ | ⚠️ El bot tiene permisos de envío a cualquier chat que conozca su token. Scope no es restringible por diseño de Telegram. |
| PostgreSQL local | ✅ SÍ | ⚠️ El usuario de PostgreSQL (`n8n_user`) tiene acceso a todas las tablas del proyecto. En producción: configurar permisos mínimos por tabla (INSERT en `leads`, INSERT en `eventos`, SELECT en `leads`). |

### 2.3 Principio de mínimo privilegio

| Pregunta | Respuesta |
|----------|-----------|
| ¿El workflow accede a datos de más de un proyecto/cliente? | ❌ NO — solo base de datos PA1 |
| ¿El workflow podría funcionar con acceso a menos tablas/campos? | ⚠️ Ver nota sobre `messaggio` arriba |

### 2.4 Veredicto Pregunta 2

⚠️ **APROBADO CON AJUSTE** — Acceso generalmente justificado. Pendiente: (1) decidir si `messaggio` debe persistirse, (2) configurar permisos PostgreSQL mínimos por tabla antes de VPS.

**Observaciones**:
```
El usuario de PostgreSQL en desarrollo local tiene acceso completo
porque es desarrollo. En producción el principio es:
- n8n_leads_user: INSERT en leads, INSERT en eventos, SELECT en leads
- Sin acceso a otras bases de datos del servidor
Esto se configura en el runbook de VPS antes del deploy.
```

---

## PREGUNTA 3 — OUTPUTS: ¿Canales de salida auditados?

### 3.1 Inventario de canales de salida

| Nodo | Canal | ¿Qué datos envía? | ¿Son mínimos necesarios? | ¿PII incluida? | ¿Justificada? |
|------|-------|------------------|-------------------------|---------------|---------------|
| `notificador-procesar-nivel1` | Telegram | nome, cognome, email, azienda, servizio, messaggio, prioridad, razon, timestamp | ❌ NO | ✅ SÍ | ❌ NO — messaggio no es necesario en notificación |
| `notificador-revisar-nivel1` | Telegram | nome, cognome, email, azienda, servizio, messaggio, razon, prioridad, timestamp | ❌ NO | ✅ SÍ | ❌ NO — messaggio no es necesario |
| `notificador-revisar-nivel2` | Telegram | nome, cognome, email, azienda, servizio, messaggio, razon_nivel2, accion_nivel2, timestamp | ❌ NO | ✅ SÍ | ❌ NO — messaggio no es necesario |
| `capa2-persistencia-upsert` | PostgreSQL (local) | nome, cognome, email, azienda, servizio, messaggio, timestamp, status | ✅ SÍ (propósito de negocio) | ✅ SÍ | ✅ SÍ — con retención pendiente |
| `capa2/3-registrar-*` | PostgreSQL (local) | lead_id, tipo_evento, detalle JSONB | ✅ SÍ | ⚠️ Mínima (solo IDs y metadata) | ✅ SÍ |
| `HTTP Abstract (email)` | API externa (EU) | Solo `email` | ✅ SÍ | ✅ SÍ (email) | ✅ SÍ — minimización correcta |
| `Respond to Webhook1` | HTTP response | Todos los items procesados | ⚠️ Más de lo necesario | ✅ SÍ | ⚠️ Responde con datos completos incluyendo PII |

### 3.2 Vector de exfiltración por canal legítimo

| Canal de salida | ¿Podría usarse para exfiltración? | ¿Está mitigado? | Mitigación |
|----------------|----------------------------------|-----------------|------------|
| Telegram | ✅ SÍ — si el bot token es comprometido, todos los leads históricos en mensajes son accesibles | ❌ NO actualmente | ADR-207: reconstruir Capa 4 para no enviar datos por Telegram |
| HTTP Request (Abstract) | ✅ SÍ — pero scope mínimo (solo email) | ✅ SÍ — minimización aplicada | Solo se envía `email`, no PII adicional |
| PostgreSQL | ✅ SÍ — si credencial DB es comprometida | ⚠️ Parcial | En producción: usuario con mínimos permisos + red interna Docker |
| Respond to Webhook | ✅ SÍ — responde con datos completos del lead | ❌ NO | Considerar responder solo con `{status: "ok", id: lead_id}` |

### 3.3 Conformidad GDPR de los outputs

| Pregunta | Respuesta |
|----------|-----------|
| ¿Algún canal de salida está fuera de la UE? | ✅ SÍ — Telegram (servidores UAE/global) y Abstract API |
| Si SÍ: ¿viaja PII por ese canal? | ✅ SÍ — Telegram recibe nombre, email, mensaje completo. Abstract recibe email. |
| ¿Los datos que salen son mínimos para el propósito del workflow? | ❌ NO — Telegram recibe más datos de los necesarios para el propósito de notificación |
| ¿El workflow tiene TTL de retención para los datos que procesa? | ❌ NO — pendiente Bloque 6 |

### 3.4 Veredicto Pregunta 3

❌ **RECHAZADO** — PII completa (incluyendo messaggio) viaja a Telegram sin justificación arquitectónica para datos tan amplios. El propósito de la notificación es alertar a Pablo — no requiere enviar el mensaje completo del lead por un canal de mensajería externo.

**Observaciones**:
```
La reconstrucción de Capa 4 (ADR-207) resuelve esto completamente:
- Telegram recibe: "Nuevo lead - ver detalles en admin.pablocuevas.it/leads/{id}"
- La interfaz de administración (detrás de autenticación, en el VPS) muestra los datos completos
- PII no sale del ecosistema controlado

Abstract API envía solo email — esto es correcto y no requiere cambio.
```

---

## VEREDICTO FINAL

| Pregunta | Resultado |
|----------|-----------|
| P1 — Inputs validados | ❌ Rechazado — SQL injection + sin HMAC |
| P2 — Acceso mínimo | ⚠️ Aprobado con ajuste — permisos DB por tabla pendientes |
| P3 — Outputs auditados | ❌ Rechazado — PII completa a Telegram sin justificación |

### Decisión

❌ **RECHAZADO — NO VA A PRODUCCIÓN**

El workflow puede operar en desarrollo local con datos sintéticos. No puede activarse en un VPS con datos reales de clientes hasta resolver los puntos bloqueantes.

### Deuda técnica aceptada

| Deuda | Impacto en dev local | Resolver en |
|-------|---------------------|-------------|
| HMAC placeholder | Bajo — entorno local, no expuesto | Bloque 7 |
| SQL string interpolation | Bajo — datos de test, no maliciosos | Antes del deploy VPS |
| Telegram con PII completa | Bajo — alertas solo van a Pablo | ADR-207 — Bloque 5 post-VPS |
| Sin retención automática | Bajo — volumen mínimo en dev | Bloque 6 |
| Permisos PostgreSQL no mínimos | Bajo — entorno local controlado | Antes del deploy VPS |
| `messaggio` sin sanitizar | Bajo — sin UI de administración activa | Antes de construir UI admin |

### Secuencia de resolución pre-producción

1. **Ahora (si se trabaja en el workflow)**: Parametrizar queries PostgreSQL — cambio puntual en cada nodo, no requiere rediseño
2. **Bloque 5**: Reconstruir Capa 4 (ADR-207) — Telegram sin PII
3. **Bloque 6**: Retención automática + permisos PostgreSQL mínimos + configurar TTL execution logs
4. **Bloque 7**: HMAC real con validación de timestamp

### Notas adicionales
```
Este audit identifica 2 hallazgos que no estaban en documentación previa:

1. BUG: Ruta de error de Abstract es un dead end — el nodo
   capa3-registrar-abstract-error no conecta a enriquecedor-clasificador.
   La lógica de fallback abstractFallo=true nunca se ejecuta.
   Resolución: añadir nodo restaurador después del registro del error.

2. DEAD CODE: Switch1 huérfano — no está conectado al flujo.
   Debe eliminarse del canvas para evitar confusión.

Ambos son correcciones puntuales, no rediseños.
```

---

**Firma**: Pablo Cuevas  
**Fecha**: 03 de mayo 2026  
**Workflow activado en producción**: ❌ NO — pendiente correcciones

---

*Documento generado según ADR-009. Archivar en `sistema-leads-pablocuevas/audits/workflows/`*
