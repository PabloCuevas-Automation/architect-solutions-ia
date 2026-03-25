# GDPR DATA MAPPING — sistema-leads-pablocuevas

**Sistema**: sistema-leads-pablocuevas  
**Versión**: 2.0  
**Fecha**: 25 de marzo 2026  
**Responsable**: Pablo Cuevas  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Supersede**: gdpr-data-mapping.md v1.0  
**Próxima revisión**: Al completar Bloque 5

---

## 1. Inventario de datos personales (PII)

| Campo | Tipo PII | Fuente | Dónde se almacena | Retención | Propósito |
|-------|----------|--------|-------------------|-----------|-----------|
| `nome` | Directo | Formulario web | Firebase + PostgreSQL (tabla leads) + N8N logs (30d) + Telegram | Según status del lead | Personalización comunicación |
| `cognome` | Directo | Formulario web | Firebase + PostgreSQL (tabla leads) + N8N logs (30d) + Telegram | Según status del lead | Personalización comunicación |
| `email` | Directo | Formulario web | Firebase + PostgreSQL (tabla leads) + N8N logs (30d) + Telegram | Según status del lead | Canal de contacto + deduplicación |
| `azienda` | Indirecto | Formulario web | Firebase + PostgreSQL (tabla leads) + N8N logs (30d) + Telegram | Según status del lead | Clasificación del lead |
| `servizio` | Indirecto | Formulario web | Firebase + PostgreSQL (tabla leads) + N8N logs (30d) + Telegram | Según status del lead | Segmentación por interés |
| `messaggio` | Potencialmente sensible | Formulario web | Firebase + PostgreSQL (tabla leads) + N8N logs (30d) + Telegram | Según status del lead | Contexto para propuesta |

**Política de retención por status:**

| Status | Retención | Justificación |
|--------|-----------|---------------|
| `descartado` | 60 días | Auditoría del clasificador |
| `en_revision` | 6 meses | Seguimiento comercial activo |
| `procesado` | 12 meses | Leads con posibilidad de conversión |

---

## 2. Flujo de datos

```
Usuario (formulario pablocuevas.it)
    ↓
Firebase Firestore (receptor inicial — primera red de seguridad)
    ↓ (fetch paralelo al submit)
N8N Webhook (procesamiento)
    ↓
PostgreSQL — tabla leads (persistencia antes de procesar)
    ↓
Abstract Email Reputation API (validación — recibe solo email)
    ↓
PostgreSQL — tabla eventos (registro de cada paso del flujo)
    ↓
Telegram (notificación — PII visible en chat del bot)
```

**Rol de cada almacenamiento:**
- **Firebase**: receptor inicial desde el formulario. Primera red de seguridad. No es la fuente de verdad del sistema.
- **PostgreSQL**: memoria del sistema. Historial completo, deduplicación, reportes, retención. Es la fuente de verdad operativa.
- **N8N logs**: logs de ejecución con PII — retención máxima 30 días (configurado via variable de entorno `EXECUTIONS_DATA_MAX_AGE=30`).
- **Telegram**: notificaciones con PII — historial en la plataforma de terceros sin control de retención propio. Riesgo identificado.

**Datos que salen del sistema:**
- Abstract recibe solo el `email` para validación. No almacena PII según su política de privacidad.
- Telegram recibe `nome`, `cognome`, `email`, `azienda`, `servizio`, `messaggio`. Queda en el historial del chat del bot.

**Minimización aplicada:**
Abstract devuelve campos PII adicionales (`first_name`, `last_name`) que el sistema descarta — no tienen propósito en el flujo de clasificación. Solo se conservan propiedades técnicas del email.

---

## 3. Base legal para el procesamiento

**Base legal**: Interés legítimo (Art. 6.1.f GDPR) — el usuario contacta activamente solicitando información comercial.

**Justificación del interés legítimo:**
- El usuario inicia el contacto de forma voluntaria
- Los datos se usan exclusivamente para responder a su solicitud
- El procesamiento es proporcional al propósito

**Alternativa considerada**: Consentimiento explícito (Art. 6.1.a) — más robusto pero requiere checkbox adicional en el formulario. Pendiente de evaluar en Proyecto Ancla 2.

---

## 4. Derechos del interesado

| Derecho | Estado | Mecanismo actual | Plan |
|---------|--------|-----------------|------|
| Acceso (Art. 15) | ⚠️ Manual | Consulta directa a PostgreSQL | Automatizar con query en Bloque 5 |
| Rectificación (Art. 16) | ⚠️ Manual | Edición directa en PostgreSQL | Manual indefinidamente — bajo volumen |
| Supresión (Art. 17) | ⚠️ Manual | DELETE en PostgreSQL + Firebase | Automatizar con workflow en Bloque 5 |
| Portabilidad (Art. 20) | ⚠️ Manual | Export manual desde PostgreSQL | Query de export en Bloque 5 |
| Oposición (Art. 21) | ⚠️ Manual | Sin mecanismo automatizado | Workflow de supresión en Bloque 5 |

---

## 5. Medidas de seguridad

### Activas
- [x] N8N self-hosted — datos no salen a servidores de terceros (excepto Abstract y Telegram)
- [x] Credenciales en vault encriptado de N8N
- [x] Firebase con reglas de acceso configuradas
- [x] Minimización GDPR — campos PII de Abstract descartados

### Pendientes

| Medida | Bloque | Descripción |
|--------|--------|-------------|
| HTTPS en producción | 6 | VPS + Certbot — datos en tránsito cifrados |
| Servidor en UE | 6 | Hetzner Frankfurt — cumplimiento transferencias |
| Retención automática de datos | 5 | Job mensual de limpieza en PostgreSQL |
| Retención de logs N8N 30 días | 6 | Variable `EXECUTIONS_DATA_MAX_AGE=30` en docker-compose |
| Usuario PostgreSQL mínimos privilegios | 7 | Usuario `n8n_user` solo con permisos necesarios |
| Puerto 5432 no expuesto | 7 | Solo conexión interna entre contenedores |
| Backup automático cifrado | 6 | pg_dump diario + almacenamiento externo |
| Validación de origen webhook (HMAC) | 7 | Header `X-Webhook-Signature` |

---

## 6. Deuda técnica GDPR

| Deuda | Riesgo | Bloque de resolución |
|-------|--------|----------------------|
| Sin retención automática de datos | PII acumulada indefinidamente en PostgreSQL | 5 |
| Derechos del interesado manuales | Lento e inconsistente ante solicitudes | 5 |
| N8N logs sin límite de retención | PII acumulada en logs del servidor | 6 |
| Sin HTTPS en producción | Datos en tránsito sin cifrar | 6 |
| Sin backup automático | Pérdida total de datos ante fallo | 6 |
| Telegram retiene historial | PII en plataforma de terceros sin control | Proyecto Ancla 2 |
| Sin validación de origen webhook | Peticiones falsas posibles | 7 |
| Sin usuario PostgreSQL de mínimos privilegios | Superficie de ataque innecesaria | 7 |

---

**Documento creado**: 25 de marzo 2026  
**Autor**: Pablo Cuevas  
**Validado con**: Claude (Anthropic) + Gemini + DeepSeek  
**Próxima revisión**: Al completar Bloque 5 (PostgreSQL implementado)