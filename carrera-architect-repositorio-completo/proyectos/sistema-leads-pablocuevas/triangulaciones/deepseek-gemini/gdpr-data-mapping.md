# GDPR DATA MAPPING — sistema-leads-pablocuevas

**Sistema**: sistema-leads-pablocuevas
**Fecha**: 2026-03-17
**Responsable**: Pablo Cuevas
**Revisión programada**: Al completar Bloque 5

---

## 1. Inventario de datos personales (PII)

| Campo | Tipo PII | Fuente | Dónde se almacena | Retención | Propósito |
|-------|----------|--------|-------------------|-----------|-----------|
| `nome` | Directo | Formulario web | Firebase + N8N execution log + Telegram | Sin definir* | Personalización comunicación |
| `cognome` | Directo | Formulario web | Firebase + N8N execution log + Telegram | Sin definir* | Personalización comunicación |
| `email` | Directo | Formulario web | Firebase + N8N execution log + Telegram | Sin definir* | Canal de contacto + validación |
| `azienda` | Indirecto | Formulario web | Firebase + N8N execution log + Telegram | Sin definir* | Clasificación del lead |
| `servizio` | Indirecto | Formulario web | Firebase + N8N execution log + Telegram | Sin definir* | Segmentación por interés |
| `messaggio` | Potencialmente sensible | Formulario web | Firebase + N8N execution log + Telegram | Sin definir* | Contexto para propuesta |

*Retención sin definir — pendiente de implementar política formal en Bloque 5.

---

## 2. Flujo de datos

```
Usuario (formulario pablocuevas.it)
    ↓
Firebase Firestore (persistencia primaria — fuente de verdad)
    ↓
N8N Webhook (procesamiento)
    ↓
Abstract Email Reputation API (validación — no se almacena PII)
    ↓
Telegram (notificación — PII visible en chat del bot)
```

**Datos que salen del sistema:**
- Abstract recibe solo el `email` para validación. No almacena datos según su política de privacidad.
- Telegram recibe `nome`, `cognome`, `email`, `azienda`, `servizio`, `messaggio`. Queda en el historial del chat del bot.

---

## 3. Base legal para el procesamiento

**Base legal**: Interés legítimo (Art. 6.1.f GDPR) — el usuario contacta activamente solicitando información comercial.

**Alternativa considerada**: Consentimiento explícito (Art. 6.1.a) — más robusto pero requiere checkbox adicional en el formulario. Pendiente de evaluar en Proyecto Ancla 2.

---

## 4. Derechos del interesado

| Derecho | Estado | Mecanismo |
|---------|--------|-----------|
| Acceso (Art. 15) | ⚠️ Manual | Consulta directa a Firebase |
| Rectificación (Art. 16) | ⚠️ Manual | Edición directa en Firebase |
| Supresión (Art. 17) | ⚠️ Manual | Eliminación manual en Firebase + Telegram |
| Portabilidad (Art. 20) | ⚠️ Manual | Export manual desde Firebase |
| Oposición (Art. 21) | ⚠️ Manual | Sin mecanismo automatizado |

*Todos manuales en Fase 1 — automatización programada en Bloque 5 con PostgreSQL.*

---

## 5. Medidas de seguridad activas

- [x] N8N self-hosted — datos no salen a servidores de terceros (excepto Abstract y Telegram)
- [x] Credenciales en vault encriptado de N8N
- [x] Firebase con reglas de acceso configuradas
- [ ] HTTPS en producción — pendiente Bloque 6 (VPS + Certbot)
- [ ] Servidor en UE — pendiente Bloque 6 (Hetzner, Frankfurt)
- [ ] Retención automática — pendiente Bloque 5

---

## 6. Deuda técnica GDPR

| Deuda | Riesgo | Resolución |
|-------|--------|------------|
| Sin política de retención formal | Datos acumulados indefinidamente | Bloque 5 — job de eliminación automática |
| Derechos del interesado manuales | Lento e inconsistente | Bloque 5 — automatizar con PostgreSQL |
| Telegram retiene historial | PII en plataforma de terceros sin control de retención | Evaluar en Proyecto Ancla 2 |
| Sin HTTPS en producción | Datos en tránsito sin cifrar | Bloque 6 — Certbot |

---

**Creado**: 2026-03-17
**Próxima revisión**: Al completar Bloque 5