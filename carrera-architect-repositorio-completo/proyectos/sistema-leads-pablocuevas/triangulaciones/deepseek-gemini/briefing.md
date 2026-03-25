# BRIEFING — Sistema de Leads Pablo Cuevas
## Documento de contexto para sesión de triangulación IA

---

## Quién soy y qué estoy construyendo

Soy Pablo Cuevas, en transición profesional hacia AI Solutions Architect.
Ubicación: Rovereto, Trento, Italia.
Objetivo a 6 meses: €1.5-2.5k/mes con automatizaciones reales para clientes locales.

Estoy en **Fase 1 — Constructor de Sistemas de Automatización**, trabajando con N8N como núcleo de automatización. Mi diferenciador de mercado es GDPR by design + documentación arquitectónica como entregable.

---

## Estado actual del aprendizaje

Bloques completados: 0, 1, 2, 3, 4
Bloques pendientes: 5 (PostgreSQL), 6 (Deploy), 7 (Seguridad), 8 (Resiliencia)
Proyectos ancla: PA1 en curso (Sesiones 1 y 2 completadas), PA2 y PA3 pendientes

Referencia completa: ver `fase-1-redefinida.md`, `estimacion-sesiones-fase-1.md` y `DAILY-LOG-FASE-1.md`

---

## El proyecto: Sistema de Gestión de Leads

### Propósito
Automatizar la recepción, verificación, clasificación y notificación de leads que llegan desde el formulario de contacto de `pablocuevas.it`.

### Stack actual
- **Fuente**: Formulario web → Firebase Firestore + fetch al webhook de N8N simultáneamente
- **Motor**: N8N self-hosted local (Docker). Pendiente deploy en VPS en Bloque 6
- **Túnel temporal**: ngrok (deuda técnica documentada, se reemplaza en Bloque 6)
- **Verificación de email**: Abstract Email Reputation API
- **Notificaciones**: Telegram Bot
- **Persistencia**: PostgreSQL (pendiente — se implementa en Bloque 5)

### Workflow actual en N8N
```
Webhook → procesador-body → HTTP Abstract → enriquecedor-clasificador
    → IF (confianza Abstract)
        TRUE  → Switch → procesar / revisar_manualmente / descartar
        FALSE → nivel2-reglas-propias → IF → revisar_manualmente / descartar
```

---

## Lo que está diseñado pero no construido aún

### Deduplicación
Antes de procesar, consultar PostgreSQL por combinación `email + servicio`.
- Si existe → registrar duplicado y parar
- Si no existe → continuar flujo normal
- Razón: una persona puede pedir dos servicios distintos — no es duplicado real

### Persistencia en PostgreSQL
Dos tablas diseñadas:

**Tabla leads** — datos fijos del lead + decisión final del sistema:
`id, nome, cognome, email, azienda, servizio, messaggio, timestamp_roma, accion, razon, prioridad`

**Tabla eventos** — datos intermedios calculados por Abstract:
`lead_id, procesado, tipoCuenta, nivelRiesgo, esEmailLibre, esDesechable, abstract_status, abstract_smtp_valid, abstract_live_site`

Momento de guardado:
- Tabla leads: inmediatamente después de deduplicación, antes de procesar
- Tabla eventos: al final del flujo, cuando se conoce la decisión

Retención:
- Descartados: 60 días
- Revisión manual: 6 meses
- Procesados: 12 meses

### Reporte semanal
Schedule Trigger cada lunes 8:00am Roma → query PostgreSQL → Telegram
Contenido: total leads, procesados, en revisión, descartados, leads nivel 2

### Deuda técnica documentada
- Nodo de descarte actual solo tiene Edit Fields básico — pendiente enriquecerlo con `razon` y `nivel_resolucion` antes de conectar PostgreSQL
- URL de ngrok cambia al reiniciar — se reemplaza con VPS en Bloque 6
- Webhook sin validación de origen — se implementa en Bloque 7

---

## Lo que NO está en scope ahora

- Python, AWS, RAG, fine-tuning — Fase 2
- Deploy en producción — Bloque 6
- Seguridad avanzada — Bloque 7
- Resiliencia y monitoreo — Bloque 8

---

## Documentos de referencia adjuntos

| Documento | Contenido |
|-----------|-----------|
| `fase-1-redefinida.md` | Marco estratégico completo de Fase 1 |
| `estimacion-sesiones-fase-1.md` | Bloques, sesiones estimadas y reales |
| `DAILY-LOG-FASE-1.md` | Log detallado de cada sesión de trabajo |
| `diagrama-sistema.md` | Plano del sistema terminado (estado actual + pendiente) |
| `schema-postgresql-draft.md` | Tablas, columnas, PII y retención |
| `gdpr-data-mapping.md` | Inventario PII del sistema |
| `adr/` | Decisiones arquitectónicas documentadas |

---

**Documento creado**: 23 de marzo 2026
**Propósito**: Briefing para sesión de triangulación Claude + Gemini + DeepSeek
**Proyecto**: Sistema de Gestión de Leads — PA1 Fase 1