# DAILY LOG — FASE 1 (ACTIVO)
## Constructor de Sistemas de Automatización

**Convención**: Una entrada por sesión significativa de trabajo.  
**Formato**: Fecha → qué hiciste → qué entendiste → qué quedó pendiente.  
**Archivo histórico**: `DAILY-LOG-FASE-1_historico.md` — Bloques 0-4 completos + PA1 Sesiones 1-2.5

---

## ESTADO AL INICIO DE ESTE LOG (10/04/2026)

### Bloques completados
| Bloque | Sesiones reales | Estado |
|--------|-----------------|--------|
| 0 — Preparación N8N | 3 | ✅ |
| 1 — Code Node + JS aplicado | 2 | ✅ |
| 2 — Triggers + Flujo real | 1 | ✅ |
| 3 — HTTP Request + APIs reales | 6 | ✅ |
| 4 — Lógica condicional + Routing | 3 | ✅ |

### Sistema de leads — estado actual
```
[Formulario pablocuevas.it]
    → Firebase Firestore (persistencia, primera red de seguridad)
    → N8N webhook (procesador-body → HTTP Abstract → enriquecedor-clasificador → Switch nivel1/nivel2 → Telegram)
```
Funciona en local con ngrok. Deuda técnica documentada: sin PostgreSQL, sin VPS, sin HMAC.

### PA1 — diseño completado, construcción pendiente
- Diagrama sistema v4.2 ✅
- Schema PostgreSQL v2.2 ✅
- GDPR data mapping v2.0 ✅
- **Próximo paso**: crear tablas en pgAdmin → conectar N8N → construir Capa 1 y Capa 2

### Decisiones de diseño críticas ya tomadas
- CTE atómica con `ON CONFLICT DO NOTHING` — devuelve `es_nuevo` (bool) + `timestamp_creacion`
- Ventana de 24h para deduplicación — distingue doble click de cliente legítimo que vuelve
- JSONB en tabla eventos — anti vendor lock-in, cero migración si cambia Abstract
- Guardado antes de procesar — lead en PostgreSQL con estado `pendiente` antes de cualquier API externa
- Nivel 2 nunca procesa automáticamente

### GeoLabor — piloto diseñado, pendiente de ejecutar
- Scope: granulometría + triángulo DIN 18123 = gratuito
- Flujo: JPEG escaneo → web app → N8N → API visión (Claude) → Excel
- Bloqueado hasta tener VPS y N8N en producción (Bloque 6)
- Archivos obtenidos: plantillas Excel + fotos minutas reales

### Stack activo
- N8N local via Docker, ngrok para exposición pública temporal
- PostgreSQL instalado, pgAdmin instalado — **nunca usado en práctica**
- Thunder Client (VS Code) para tests HTTP
- Repo: `pablocuevas-automation/architect-solutions-ia`

---

## ENTRADAS ACTIVAS

## 10/04 ✅ GeoLabor — Reconocimiento en laboratorio + diseño del piloto

Ver resumen en "Estado al inicio" arriba. Entrada completa en histórico.

**Pendiente al cierre:** retomar PA1 Sesión 3 — crear tablas en pgAdmin, construir Capa 1 y Capa 2 en N8N.

---

<!-- NUEVA ENTRADA AQUÍ -->