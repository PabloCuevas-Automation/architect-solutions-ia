# WORKFLOW AUDIT TEMPLATE
## Auditoría de Seguridad de Workflow N8N

**Referencia**: ADR-208 — Seguridad de aplicación en workflows N8N  
**Instrucción**: Completar una instancia por workflow antes de activar en producción.  
**Archivar**: adjuntar al proyecto como evidencia de due diligence GDPR.  
**Regla**: si alguna pregunta tiene respuesta NO sin mitigación documentada, el workflow no va a producción.

---

## IDENTIFICACIÓN

| Campo | Valor |
|-------|-------|
| **Nombre del workflow** | ___________________________________ |
| **Proyecto** | ☐ PA1 ☐ GeoLabor ☐ Suanfarma ☐ Otro: _______ |
| **Tipo** | ☐ Trigger/Webhook ☐ Scheduled ☐ Manual ☐ Sub-workflow |
| **Versión** | ___________________________________ |
| **Fecha de auditoría** | ___________________________________ |
| **Auditado por** | Pablo Cuevas |
| **Estado** | ☐ Aprobado para producción ☐ Rechazado — ver observaciones |

---

## PREGUNTA 1 — INPUTS: ¿Qué entra y está validado?

### 1.1 Inventario de campos externos

*Listar todos los campos que el workflow recibe del exterior (webhook, API, formulario, etc.)*

| Campo | Origen | Tipo esperado | ¿Se valida tipo? | ¿Se sanitiza? | ¿Se rechaza si falla? |
|-------|--------|--------------|-----------------|--------------|----------------------|
| | | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |

### 1.2 Campos de texto libre — riesgo XSS/injection

*Los campos de texto libre (mensajes, comentarios, nombres) pueden contener payloads maliciosos.*

| Campo de texto libre | ¿Se sanitiza antes de persistir en DB? | ¿Se sanitiza antes de renderizar en UI? | ¿Se usa en queries SQL? |
|---------------------|---------------------------------------|----------------------------------------|------------------------|
| | ☐ SÍ ☐ NO ☐ N/A | ☐ SÍ ☐ NO ☐ N/A | ☐ SÍ ☐ NO |
| | ☐ SÍ ☐ NO ☐ N/A | ☐ SÍ ☐ NO ☐ N/A | ☐ SÍ ☐ NO |

### 1.3 Validación de autenticación

| Pregunta | Respuesta |
|----------|-----------|
| ¿El workflow tiene HMAC u otro mecanismo de autenticación de origen? | ☐ SÍ ☐ NO ☐ N/A |
| Si tiene HMAC: ¿valida timestamp para prevenir replay attacks? | ☐ SÍ ☐ NO ☐ N/A |
| Si NO tiene autenticación: ¿está justificado? Razón: | ___________________________________ |

### 1.4 Veredicto Pregunta 1

☐ **APROBADO** — Todos los inputs están validados y sanitizados apropiadamente  
☐ **APROBADO CON DEUDA** — Existen gaps documentados como deuda técnica aceptada  
☐ **RECHAZADO** — Hay inputs sin validación en producción con datos reales  

**Observaciones**:
```




```

---

## PREGUNTA 2 — DATOS: ¿Tiene acceso mínimo necesario?

### 2.1 Acceso a bases de datos

*¿A qué tablas/campos tiene acceso este workflow? ¿Necesita todos?*

| Tabla / Campo | ¿El workflow lo usa? | ¿Lo necesita realmente? | Acción si no lo necesita |
|--------------|---------------------|------------------------|--------------------------|
| leads.email | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ Eliminar acceso ☐ N/A |
| leads.messaggio | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ Eliminar acceso ☐ N/A |
| leads.nome / cognome | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ Eliminar acceso ☐ N/A |
| eventos.* | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ Eliminar acceso ☐ N/A |
| Otra: _______ | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ Eliminar acceso ☐ N/A |

### 2.2 Credenciales y APIs externas

*¿A qué APIs y servicios tiene acceso este workflow?*

| API / Servicio | ¿Necesaria para este workflow? | ¿El scope de la credencial es mínimo? |
|---------------|-------------------------------|--------------------------------------|
| Abstract API | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO ☐ N/A |
| Telegram Bot | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO ☐ N/A |
| Otra: _______ | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO ☐ N/A |

### 2.3 Principio de mínimo privilegio

| Pregunta | Respuesta |
|----------|-----------|
| ¿El workflow accede a datos de más de un proyecto/cliente? | ☐ SÍ ☐ NO |
| Si SÍ: ¿está justificado arquitectónicamente? Razón: | ___________________________________ |
| ¿El workflow podría funcionar con acceso a menos tablas/campos? | ☐ SÍ — ajustar ☐ NO |

### 2.4 Veredicto Pregunta 2

☐ **APROBADO** — El workflow tiene acceso mínimo necesario  
☐ **APROBADO CON AJUSTE** — Se eliminarán accesos innecesarios antes de producción  
☐ **RECHAZADO** — El workflow tiene acceso a datos que no necesita, sin justificación  

**Observaciones**:
```




```

---

## PREGUNTA 3 — OUTPUTS: ¿Canales de salida auditados?

### 3.1 Inventario de canales de salida

*Todo nodo que envíe datos fuera del workflow es un canal de salida potencial.*

| Nodo | Canal | ¿Qué datos envía? | ¿Son mínimos necesarios? | ¿PII incluida? | ¿Justificada? |
|------|-------|------------------|-------------------------|---------------|---------------|
| | Telegram | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | Email | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | HTTP Request | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | PostgreSQL | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |
| | Otro: _______ | | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO |

### 3.2 Vector de exfiltración por canal legítimo

*Pregunta clave: ¿podría un atacante que modifique un nodo de salida usar ese canal para exfiltrar más datos de los previstos?*

| Canal de salida | ¿Podría usarse para exfiltración? | ¿Está mitigado? | Mitigación |
|----------------|----------------------------------|-----------------|------------|
| Telegram | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ___________________ |
| Email | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ___________________ |
| HTTP Request | ☐ SÍ ☐ NO | ☐ SÍ ☐ NO | ___________________ |

### 3.3 Conformidad GDPR de los outputs

| Pregunta | Respuesta |
|----------|-----------|
| ¿Algún canal de salida está fuera de la UE? (Telegram, APIs externas) | ☐ SÍ ☐ NO |
| Si SÍ: ¿viaja PII por ese canal? | ☐ SÍ — revisar ☐ NO ☐ N/A |
| ¿Los datos que salen son mínimos para el propósito del workflow? | ☐ SÍ ☐ NO |
| ¿El workflow tiene TTL de retención para los datos que procesa? | ☐ SÍ ☐ NO ☐ N/A |

### 3.4 Veredicto Pregunta 3

☐ **APROBADO** — Canales de salida auditados, datos mínimos, sin PII en canales externos  
☐ **APROBADO CON NOTA** — PII en canal externo justificada y documentada  
☐ **RECHAZADO** — PII viaja por canal externo sin justificación o datos no son mínimos  

**Observaciones**:
```




```

---

## VEREDICTO FINAL

| Pregunta | Resultado |
|----------|-----------|
| P1 — Inputs validados | ☐ Aprobado ☐ Aprobado con deuda ☐ Rechazado |
| P2 — Acceso mínimo | ☐ Aprobado ☐ Aprobado con ajuste ☐ Rechazado |
| P3 — Outputs auditados | ☐ Aprobado ☐ Aprobado con nota ☐ Rechazado |

### Decisión

☐ **APROBADO PARA PRODUCCIÓN**  
Todos los puntos aprobados o aprobados con deuda/ajuste documentado.

☐ **RECHAZADO — NO VA A PRODUCCIÓN**  
Uno o más puntos rechazados. Resolver antes de reauditar.

### Deuda técnica aceptada (si aplica)

| Deuda | Impacto | Resolver en |
|-------|---------|-------------|
| | | |
| | | |

### Notas adicionales
```




```

---

**Firma**: Pablo Cuevas  
**Fecha**: _______________  
**Workflow activado en producción**: ☐ SÍ ☐ NO (pendiente correcciones)

---

*Documento generado según ADR-208. Archivar en `/docs/audits/workflows/` del proyecto.*
