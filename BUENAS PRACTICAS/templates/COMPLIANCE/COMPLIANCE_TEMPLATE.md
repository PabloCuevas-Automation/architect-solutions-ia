# Compliance Checklist - [Nombre Proyecto]

**Última actualización:** [Fecha]  
**Responsable:** Pablo  
**Scope:** [Breve descripción del sistema]

---

## 1. Datos Guardados

### Datos que almacena este sistema:
- 

### Propósito de cada dato:
| Dato | Por qué lo necesitamos | Dónde se guarda |
|------|------------------------|-----------------|
|      |                        |                 |

---

## 2. PII Identificada

### Datos clasificados como PII:
- [ ] Nombres completos
- [ ] Emails
- [ ] Teléfonos
- [ ] Direcciones
- [ ] IPs
- [ ] Otros: _________

### Datos NO-PII:
- 

---

## 3. Retención

**Política de retención:**
- Datos operativos: [X días/meses/años]
- Logs de sistema: [X días]
- Backups: [X días]

**Justificación temporal:**
[Por qué guardamos X tiempo y no más]

**Proceso de eliminación automática:**
- [ ] Implementado (describe cómo)
- [ ] Pendiente (cuándo se hará)

---

## 4. Eliminación Bajo Petición

**Endpoint/Proceso:**
```
[Cómo un usuario puede solicitar eliminación]
```

**Tiempo de respuesta:** [X días según GDPR: máximo 30]

**Qué se elimina:**
- [ ] Datos personales
- [ ] Datos derivados (analytics, etc.)
- [ ] Backups

**Qué NO se elimina (y por qué):**
- 

---

## 5. Consentimiento

**Cómo se obtiene:**
- [ ] Explícito (checkbox, form)
- [ ] Implícito (términos de uso)
- [ ] N/A (justificar)

**Documentación de consentimiento:**
- [ ] Guardamos timestamp
- [ ] Guardamos versión de términos aceptados
- [ ] Guardamos IP de aceptación

---

## 6. Acceso y Seguridad

**Quién puede acceder a PII:**
- 

**Medidas de seguridad:**
- [ ] Encriptación en tránsito (HTTPS)
- [ ] Encriptación en reposo
- [ ] Access control (roles)
- [ ] Audit logs

---

## 7. Transferencias Internacionales

**¿Los datos salen de UE?**
- [ ] NO
- [ ] SÍ → ¿A dónde? _________ → ¿Mecanismo legal? _________

---

## 8. Incidentes / Breaches

**Plan en caso de breach:**
1. [Paso 1]
2. [Paso 2]
3. Notificar autoridad (72h según GDPR)
4. Notificar usuarios afectados

---

## 9. Auditoría

**Última auditoría:** [Fecha]  
**Próxima auditoría:** [Fecha]  
**Issues encontrados:** [Link a tickets/ADRs]

---

## 10. Referencias

- ADR relacionados: [Links]
- Políticas legales: [Links]
- Contact: [Quien responde preguntas compliance]