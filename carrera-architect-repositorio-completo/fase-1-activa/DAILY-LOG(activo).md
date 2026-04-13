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

## 13/04 ✅ PA1 Sesión 3 — PostgreSQL operativo + tablas creadas

### Qué hice
- Reorganicé el daily log: creé `DAILY-LOG-FASE-1_activo.md` + estructura para histórico
- Implementé seguridad de credenciales desde cero: `.gitignore` en raíz del repo + en proyecto leads, `.env` + `.env.example` en carpeta `docker`
- Creé `docker-compose.yml` con variables `${VARIABLE}` — sin credenciales hardcodeadas
- Levanté PostgreSQL + pgAdmin como contenedores Docker por primera vez
- Conecté pgAdmin al contenedor de PostgreSQL (`host: postgres`, no `localhost`)
- Creé las tablas `leads` y `eventos` con el schema v2.2
- Validé el schema con INSERT de prueba + SELECT + DELETE

### Qué entendí
- Docker aísla programas en contenedores sin instalar nada en el sistema operativo
- El host para conectar contenedores entre sí es el nombre del servicio en el compose, no `localhost` — porque cada contenedor tiene su propio localhost
- `SERIAL PRIMARY KEY`, `DEFAULT NOW()`, `DEFAULT 'pendiente'` — PostgreSQL rellena automáticamente sin que N8N lo haga
- `CONSTRAINT UNIQUE (email, servizio)` — la base de datos rechaza duplicados a nivel de motor, no solo a nivel de aplicación
- `REFERENCES leads(id)` — integridad referencial: no puedes crear un evento para un lead inexistente
- Defensa en profundidad: `.gitignore` en raíz del repo + en cada proyecto — si falla una capa, la otra protege

### Decisiones tomadas
- Carpeta `docker` dentro del proyecto (no separada) — infraestructura vive con el proyecto
- `.gitignore` doble — raíz del repo + proyecto leads
- ADR-006 creado: gestión de credenciales como principio universal

### Archivos creados hoy
- `docker/.env` — credenciales locales reales
- `docker/.env.example` — plantilla versionada
- `docker/docker-compose.yml` — PostgreSQL + pgAdmin con variables
- `docker/README.md` — cómo levantar y conectar
- `adr/ADR-006-gestion-credenciales.md` — principio universal
- `.gitignore` en raíz del repo

### Pendiente para próxima sesión
- Conectar N8N al nodo PostgreSQL con credenciales del vault
- Construir Capa 1 (placeholder HMAC) y Capa 2 (UPSERT con CTE) en N8N
- Hacer commit de todo lo de hoy con mensaje descriptivo

<!-- NUEVA ENTRADA AQUÍ -->