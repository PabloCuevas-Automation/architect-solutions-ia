# ADR-208: Firebase como Buffer de Recepción — Rol, Duplicación PII y Deuda de Retención

**Fecha**: 10 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Arquitectónica + GDPR  
**Audiencia**: 🔒 Personal  
**Cuándo consultar**: Si se cuestiona por qué hay PII en Firebase + PostgreSQL, o al implementar eliminación de datos

---

## Contexto

El sistema tiene dos almacenamientos con PII:

- **Firebase Firestore**: recibe los datos del formulario web en el momento del submit
- **PostgreSQL**: recibe los mismos datos vía webhook N8N para persistencia operativa

Esta duplicación fue detectada como posible contradicción con el principio de minimización de datos (GDPR Art. 5.1.c). Este ADR documenta la decisión consciente sobre ese diseño y las deudas que genera.

---

## La Decisión

**Firebase actúa como buffer de recepción, no como fuente de verdad.**

El rol de cada almacenamiento es distinto y complementario:

| Almacenamiento | Rol | Fuente de verdad |
|---|---|---|
| Firebase Firestore | Primera red de seguridad — garantiza que el lead no se pierde si N8N falla | ❌ No |
| PostgreSQL | Memoria operativa del sistema — deduplicación, clasificación, retención, reportes | ✅ Sí |

Esta decisión ya estaba implícita en ADR-200 (conexión Firebase→N8N) y en el GDPR data mapping v2.0. Este ADR la formaliza explícitamente.

### Por qué mantener Firebase en el flujo

Sin Firebase, si N8N o el VPS no están disponibles en el momento del submit, el lead se pierde para siempre. Firebase actúa como amortiguador: el usuario recibe confirmación, el lead queda guardado, y N8N lo procesa cuando vuelve a estar disponible (o mediante recuperación manual).

La duplicación es el precio de la resiliencia en el punto de entrada.

---

## Deuda documentada: Retención y eliminación en Firebase

### Deuda 1 — Sin TTL en Firestore

**Estado**: ⚠️ Deuda conocida  
**Riesgo**: Los leads permanecen en Firebase indefinidamente después de ser procesados por PostgreSQL. Esto viola la política de retención definida en el GDPR data mapping (60 días / 6 meses / 12 meses según status).  
**Resolución**: Implementar TTL nativo de Firestore o un Cloud Function que elimine documentos procesados después del período de retención correspondiente.  
**Cuándo**: Bloque 6 — antes del primer lead real en producción.  
**Criterio de cierre**: Ningún documento en Firebase supera el período de retención de su status correspondiente en PostgreSQL.

### Deuda 2 — Sin mecanismo de borrado sincronizado

**Estado**: ⚠️ Deuda conocida  
**Riesgo**: Si un interesado ejerce su derecho de eliminación (GDPR Art. 17), el sistema elimina el registro de PostgreSQL pero no tiene un mecanismo documentado para eliminar el documento correspondiente en Firebase.  
**Resolución**: Documentar el procedimiento manual de borrado en Firebase como parte del runbook de derechos del interesado. Automatizar si el volumen lo justifica.  
**Cuándo**: Bloque 6.  
**Criterio de cierre**: El runbook de derechos del interesado incluye paso explícito para eliminación en Firebase.

---

## Deuda documentada: Abstract API y GDPR

### Deuda 3 — Abstract API sin DPA verificado

**Estado**: ⚠️ Deuda conocida  
**Riesgo**: El sistema envía el campo `email` (PII directo) a Abstract API para validación de reputación. El GDPR data mapping v2.0 documenta que *"Abstract no almacena PII según su política de privacidad"* — pero esto es una afirmación de la propia empresa, no un Data Processing Agreement firmado.  
**Impacto potencial**: Si Pablo ofrece GDPR-by-design como diferenciador de mercado, un cliente técnico o una auditoría puede cuestionar la transferencia de emails a un tercero sin DPA formal.  
**Resolución**: Verificar si Abstract API ofrece DPA. Si no lo ofrece, documentar la decisión de aceptar su política de privacidad como base suficiente para este caso de uso (volumen bajo, email como único campo, sin almacenamiento declarado). Si ofrece DPA, firmarlo.  
**Cuándo**: Antes de ofrecer el sistema como referencia a clientes.  
**Criterio de cierre**: Existe un documento (DPA firmado o decisión documentada con justificación legal) sobre el uso de Abstract API.

---

## Alternativas consideradas

**Eliminar Firebase del flujo:**  
Eliminaría la duplicación pero también la primera red de seguridad. Si N8N falla, el lead se pierde. Descartado — la resiliencia en el punto de entrada justifica la duplicación.

**Usar Firebase como única persistencia:**  
Eliminaría la duplicación pero eliminaría también las capacidades operativas de PostgreSQL (deduplicación con CTE atómica, ventana 24h, JSONB en eventos, retención por status). Descartado.

---

## Consecuencias

- La duplicación Firebase + PostgreSQL es una decisión consciente de resiliencia, no un error de diseño
- Genera tres deudas GDPR documentadas que deben resolverse en Bloque 6
- Este ADR convierte tres "huecos" identificados por auditoría en deuda técnica consciente con criterios de resolución claros

---

**Relacionado con**: ADR-200 (Firebase→N8N), ADR-204 (Write-Before-Processing), GDPR data mapping v2.0  
**Deudas activas**: 3 — ver sección "Deuda documentada"  
**Próxima revisión**: Al iniciar Bloque 6
