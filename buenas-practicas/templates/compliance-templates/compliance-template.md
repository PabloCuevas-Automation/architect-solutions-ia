# Compliance Checklist / Checklist de Compliance

**Project / Proyecto:** [Project name / Nombre proyecto]  
**Last review / Última revisión:** [Date / Fecha]  
**Reviewer / Revisor:** [Your name / Tu nombre]  
**Status / Estado:** 🟢 Compliant / Conforme | 🟡 In Progress / En progreso | 🔴 Non-compliant / No conforme

---

## Purpose / Propósito

**EN:** This document tracks GDPR and data privacy compliance for this project. Review quarterly or when adding new data handling features.

**ES:** Este documento rastrea compliance GDPR y privacidad de datos para este proyecto. Revisar trimestralmente o al añadir nuevas features de manejo de datos.

---

## 1. Data Inventory / Inventario de Datos

### 1.1 Personal Data Collected / Datos Personales Recopilados

**EN - List all data fields collected:**

| Field / Campo | Type / Tipo | Purpose / Propósito | Retention / Retención | PII? |
|--------------|-------------|---------------------|---------------------|------|
| [Example: Email] | String | User authentication / Autenticación usuario | 2 years / 2 años | ✅ Yes / Sí |
| [Example: IP Address] | String | Security logging / Logging seguridad | 90 days / 90 días | ✅ Yes / Sí |
| [Example: Name] | String | User profile / Perfil usuario | Until deletion / Hasta eliminación | ✅ Yes / Sí |
| [Example: User ID] | UUID | Internal reference / Referencia interna | Permanent / Permanente | ❌ No |

**ES - Listar todos los campos de datos recopilados:**

[Same table structure / Misma estructura tabla]

### 1.2 PII Classification / Clasificación PII

**EN - Personally Identifiable Information categories:**

- ✅ **Identity Data**: Name, email, phone, address
- ✅ **Technical Data**: IP address, device info, cookies
- ⬜ **Financial Data**: Credit card, bank account (NOT collected)
- ⬜ **Health Data**: Medical information (NOT collected)
- ⬜ **Sensitive Categories**: Race, religion, sexual orientation (NOT collected)

**ES - Categorías de Información de Identificación Personal:**

- ✅ **Datos Identidad**: Nombre, email, teléfono, dirección
- ✅ **Datos Técnicos**: IP, info dispositivo, cookies
- ⬜ **Datos Financieros**: Tarjeta crédito, cuenta banco (NO recopilados)
- ⬜ **Datos Salud**: Información médica (NO recopilados)
- ⬜ **Categorías Sensibles**: Raza, religión, orientación sexual (NO recopilados)

---

## 2. Legal Basis / Base Legal

**EN - For each data type, identify legal basis under GDPR:**

| Data / Datos | Legal Basis / Base Legal | Notes / Notas |
|-------------|--------------------------|---------------|
| Email | Consent / Consentimiento | Explicit opt-in / Opt-in explícito |
| IP Address | Legitimate Interest / Interés legítimo | Security / Seguridad |
| Usage Analytics | Consent / Consentimiento | Cookie banner / Banner cookies |

**ES - Para cada tipo de datos, identificar base legal bajo GDPR:**

[Same table / Misma tabla]

**EN - GDPR Legal Bases:**
1. Consent / Consentimiento
2. Contract / Contrato
3. Legal Obligation / Obligación legal
4. Vital Interests / Intereses vitales
5. Public Task / Tarea pública
6. Legitimate Interests / Intereses legítimos

**ES - Bases Legales GDPR:**
[Same list / Misma lista]

---

## 3. Data Flow / Flujo de Datos

### 3.1 Data Storage / Almacenamiento de Datos

**EN:**
- **Database / Base de datos:** [PostgreSQL, MySQL, MongoDB, etc.]
- **Location / Ubicación:** [EU, US, specific country / UE, US, país específico]
- **Provider / Proveedor:** [AWS, Google Cloud, self-hosted / auto-alojado]
- **Encryption at rest / Cifrado en reposo:** ✅ Yes / Sí | ❌ No
- **Encryption in transit / Cifrado en tránsito:** ✅ Yes / Sí (TLS/SSL) | ❌ No

**ES:**
[Same structure / Misma estructura]

### 3.2 Third-Party Services / Servicios de Terceros

**EN - List all services that process user data:**

| Service / Servicio | Purpose / Propósito | Data Shared / Datos compartidos | DPA Signed / DPA firmado? |
|-------------------|---------------------|--------------------------------|-------------------------|
| [Example: SendGrid] | Email delivery / Entrega email | Email address / Dirección email | ✅ Yes / Sí |
| [Example: Stripe] | Payment processing / Procesamiento pagos | Payment info / Info pago | ✅ Yes / Sí |
| [Example: Google Analytics] | Usage analytics / Analítica uso | IP (anonymized) / IP (anonimizado) | ✅ Yes / Sí |

**ES - Listar todos los servicios que procesan datos usuario:**

[Same table / Misma tabla]

**DPA = Data Processing Agreement / Acuerdo Procesamiento de Datos**

### 3.3 Data Transfers / Transferencias de Datos

**EN:**
- **Do you transfer data outside EU? / ¿Transfieres datos fuera UE?:** ✅ Yes / Sí | ❌ No
- **If yes, to which countries? / Si sí, ¿a qué países?:** [List / Lista]
- **Safeguards in place / Salvaguardas implementadas:**
  - ⬜ Standard Contractual Clauses (SCCs)
  - ⬜ Binding Corporate Rules (BCRs)
  - ⬜ Adequacy Decision
  - ⬜ Consent from user / Consentimiento usuario

**ES:**
[Same structure / Misma estructura]

---

## 4. User Rights / Derechos del Usuario

### 4.1 Rights Implementation / Implementación de Derechos

**EN - GDPR grants users these rights. Check implementation:**

| Right / Derecho | Implemented / Implementado | How / Cómo |
|----------------|---------------------------|-----------|
| **Right to Access** / Derecho acceso | ✅ Yes / Sí | Export feature / Feature exportar |
| **Right to Rectification** / Derecho rectificación | ✅ Yes / Sí | Edit profile / Editar perfil |
| **Right to Erasure** / Derecho supresión | ✅ Yes / Sí | Delete account / Eliminar cuenta |
| **Right to Restrict Processing** / Derecho restringir procesamiento | ⬜ Partial / Parcial | [Describe / Describir] |
| **Right to Data Portability** / Derecho portabilidad | ✅ Yes / Sí | Export JSON/CSV |
| **Right to Object** / Derecho oposición | ✅ Yes / Sí | Opt-out marketing / Opt-out marketing |

**ES - GDPR otorga estos derechos a usuarios. Verificar implementación:**

[Same table / Misma tabla]

### 4.2 User Request Process / Proceso Solicitudes Usuario

**EN:**
1. **How users can submit requests / Cómo usuarios pueden enviar solicitudes:**
   - Email: [compliance@yourproject.com]
   - In-app form / Formulario en app: [Yes/No]
   - Support ticket / Ticket soporte: [Yes/No]

2. **Response timeline / Tiempo respuesta:**
   - Target / Objetivo: [e.g., 15 days / ej: 15 días]
   - GDPR requirement / Requisito GDPR: 30 days maximum / 30 días máximo

3. **Verification process / Proceso verificación:**
   - [How you verify user identity / Cómo verificas identidad usuario]

**ES:**
[Same structure / Misma estructura]

---

## 5. Consent Management / Gestión de Consentimiento

### 5.1 Consent Collection / Recopilación de Consentimiento

**EN:**
- ⬜ **Cookie banner implemented** / Banner cookies implementado
- ⬜ **Clear, plain language** / Lenguaje claro y simple
- ⬜ **Granular consent options** / Opciones consentimiento granulares
- ⬜ **Pre-ticked boxes avoided** / Casillas pre-marcadas evitadas
- ⬜ **Easy to withdraw consent** / Fácil retirar consentimiento

**ES:**
[Same checklist / Mismo checklist]

### 5.2 Consent Records / Registros de Consentimiento

**EN:**
- **Do you log consent events? / ¿Registras eventos consentimiento?:** ✅ Yes / Sí | ❌ No
- **What is logged? / ¿Qué se registra?:**
  - User ID
  - Timestamp / Marca tiempo
  - Consent type / Tipo consentimiento
  - IP address (optional) / Dirección IP (opcional)
  - Version of privacy policy / Versión política privacidad

**ES:**
[Same structure / Misma estructura]

---

## 6. Data Retention / Retención de Datos

### 6.1 Retention Policy / Política de Retención

**EN - Define retention periods for each data type:**

| Data Type / Tipo Datos | Retention Period / Período Retención | Justification / Justificación |
|------------------------|--------------------------------------|------------------------------|
| User accounts / Cuentas usuario | Until deletion request / Hasta solicitud eliminación | Business need / Necesidad negocio |
| Logs / Registros | 90 days / 90 días | Security / Seguridad |
| Analytics / Analítica | 2 years / 2 años | Business intelligence |
| Backups | 30 days / 30 días | Disaster recovery / Recuperación desastres |

**ES - Definir períodos retención para cada tipo datos:**

[Same table / Misma tabla]

### 6.2 Deletion Process / Proceso de Eliminación

**EN:**
- ⬜ **Automated deletion implemented** / Eliminación automatizada implementada
- ⬜ **Manual deletion process documented** / Proceso eliminación manual documentado
- ⬜ **Backups include deletion** / Backups incluyen eliminación
- ⬜ **Third-party deletion verified** / Eliminación terceros verificada

**ES:**
[Same checklist / Mismo checklist]

---

## 7. Security Measures / Medidas de Seguridad

### 7.1 Technical Measures / Medidas Técnicas

**EN:**
- ✅ **Encryption at rest** / Cifrado en reposo
- ✅ **Encryption in transit (TLS/SSL)** / Cifrado en tránsito
- ✅ **Access control (authentication)** / Control acceso (autenticación)
- ✅ **Access control (authorization/RBAC)** / Control acceso (autorización/RBAC)
- ⬜ **Two-factor authentication** / Autenticación dos factores
- ✅ **Regular backups** / Backups regulares
- ⬜ **Intrusion detection** / Detección intrusiones
- ✅ **Firewall protection** / Protección firewall
- ⬜ **DDoS protection** / Protección DDoS

**ES:**
[Same checklist / Mismo checklist]

### 7.2 Organizational Measures / Medidas Organizacionales

**EN:**
- ⬜ **Staff training on data protection** / Capacitación personal protección datos
- ⬜ **Access limited to need-to-know** / Acceso limitado a need-to-know
- ⬜ **Confidentiality agreements signed** / Acuerdos confidencialidad firmados
- ⬜ **Incident response plan documented** / Plan respuesta incidentes documentado
- ⬜ **Regular security audits** / Auditorías seguridad regulares

**ES:**
[Same checklist / Mismo checklist]

---

## 8. Breach Notification / Notificación de Brechas

### 8.1 Breach Response Plan / Plan Respuesta Brechas

**EN:**
1. **Detection / Detección:** [How you detect breaches / Cómo detectas brechas]
2. **Assessment / Evaluación:** [Within / Dentro de] 24 hours
3. **Containment / Contención:** [Immediate / Inmediata]
4. **Notification to authority / Notificación autoridad:** Within 72 hours (GDPR requirement / requisito GDPR)
5. **User notification / Notificación usuarios:** If high risk / Si alto riesgo

**ES:**
[Same structure / Misma estructura]

### 8.2 Contact Information / Información de Contacto

**EN:**
- **Data Protection Authority / Autoridad Protección Datos:** [Relevant DPA / DPA relevante]
- **Internal DPO (if applicable) / DPO interno (si aplica):** [Name/Email]
- **Incident response email / Email respuesta incidentes:** [security@yourproject.com]

**ES:**
[Same structure / Misma estructura]

---

## 9. Privacy Documentation / Documentación Privacidad

### 9.1 Required Documents / Documentos Requeridos

**EN:**
- ✅ **Privacy Policy published** / Política Privacidad publicada
- ✅ **Last updated / Última actualización:** [Date / Fecha]
- ✅ **Cookie Policy (if applicable)** / Política Cookies (si aplica)
- ⬜ **Data Processing Agreement with sub-processors** / Acuerdo Procesamiento Datos con sub-procesadores
- ⬜ **Records of Processing Activities (ROPA)** / Registro Actividades Procesamiento

**ES:**
[Same checklist / Mismo checklist]

### 9.2 Documentation Location / Ubicación Documentación

**EN:**
- **Privacy Policy URL:** [URL]
- **Cookie Policy URL:** [URL]
- **Terms of Service URL:** [URL]
- **Internal compliance docs:** [Path to docs / Ruta a docs]

**ES:**
[Same structure / Misma estructura]

---

## 10. Compliance Status / Estado de Compliance

### 10.1 Overall Assessment / Evaluación General

**EN - Rate compliance status:**

| Area / Área | Status / Estado | Priority / Prioridad | Due Date / Fecha límite |
|------------|----------------|---------------------|----------------------|
| Data Inventory / Inventario Datos | 🟢 Complete / Completo | - | - |
| User Rights / Derechos Usuario | 🟡 In Progress / En progreso | High / Alta | [Date] |
| Consent Management / Gestión Consentimiento | 🔴 Not Started / No iniciado | High / Alta | [Date] |
| Security Measures / Medidas Seguridad | 🟢 Complete / Completo | - | - |

**ES - Calificar estado compliance:**

[Same table / Misma tabla]

### 10.2 Action Items / Elementos de Acción

**EN - List outstanding compliance tasks:**

1. [ ] **Task / Tarea:** Implement cookie banner / Implementar banner cookies  
   **Priority / Prioridad:** High / Alta  
   **Owner / Responsable:** [Name / Nombre]  
   **Due / Fecha límite:** [Date / Fecha]

2. [ ] **Task:** Document breach response plan / Documentar plan respuesta brechas  
   **Priority:** Medium / Media  
   **Owner:** [Name]  
   **Due:** [Date]

**ES - Listar tareas compliance pendientes:**

[Same structure / Misma estructura]

---

## 11. Review History / Historial de Revisiones

| Date / Fecha | Reviewer / Revisor | Changes / Cambios | Next Review / Próxima Revisión |
|-------------|-------------------|-------------------|-------------------------------|
| 2026-01-28 | [Name] | Initial review / Revisión inicial | 2026-04-28 (3 months) |
| | | | |

---

## Notes / Notas

**EN - Additional compliance considerations:**

[Add any specific notes relevant to your project / Añadir notas específicas relevantes a tu proyecto]

**ES - Consideraciones adicionales compliance:**

[Same space for notes / Mismo espacio para notas]

---

## Resources / Recursos

**EN:**
- GDPR official text: https://gdpr.eu/
- GDPR checklist: https://gdpr.eu/checklist/
- Data Protection Authorities: https://edpb.europa.eu/about-edpb/about-edpb/members_en

**ES:**
- Texto oficial GDPR: https://gdpr.eu/
- Checklist GDPR: https://gdpr.eu/checklist/
- Autoridades Protección Datos: https://edpb.europa.eu/about-edpb/about-edpb/members_es

---

**Reviewed by / Revisado por:** [Your name / Tu nombre]  
**Date / Fecha:** [Today / Hoy]  
**Signature / Firma:** _______________________

---

*Compliance Checklist v1.0 - Created / Creado: 2026-01-28*  
*Bilingual EN/ES - Stay compliant, protect users / Mantente conforme, protege usuarios*

**⚠️ Disclaimer / Descargo de responsabilidad:**

**EN:** This checklist is for guidance only and does not constitute legal advice. Consult with a qualified data protection professional or lawyer for your specific situation.

**ES:** Este checklist es solo para orientación y no constituye asesoramiento legal. Consulta con profesional cualificado de protección de datos o abogado para tu situación específica.