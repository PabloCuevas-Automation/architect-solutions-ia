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

## 15/04 ✅ MCP exploration + ADR-007 + triangulación GeoLabor

### Qué hice
- Investigué la "Estrategia del JAB" (Claude + NotebookLM via MCP) tras verla en un video
- Descarté MCP de NotebookLM por seguridad: usa browser automation con cookies de Google — viola el principio de mínimo privilegio
- Descarté triangulación automatizada via MCP: las APIs externas no reciben el contexto completo que yo sí doy manualmente con archivos en Gemini/DeepSeek — es un downgrade disfrazado de automatización
- Evalué 20 MCP servers y los filtré por seguridad real — quedaron 6 aprobados
- Creé ADR-007: principio de mínimo privilegio para acceso de IA a sistemas locales
- Verifiqué con `git log --all --full-history -- .env` que mi `.env` nunca se commiteó — ADR-006 funcionó desde día uno
- Preparé documento de triangulación para GeoLabor con 5 bloques de preguntas a enviar a Gemini y DeepSeek

### Qué entendí
- MCP no es seguro por defecto — la mayoría de servers requieren permisos amplios que violan GDPR y mínimo privilegio
- La regla que se desprende: MCP es seguro cuando los datos que viajan son propios y públicos; es riesgo GDPR cuando son PII de terceros
- PostgreSQL MCP en producción = pérdida de base legal GDPR (los datos del lead viajan a servidores de Anthropic)
- Filesystem MCP no es Projects con contexto infinito — los archivos leídos consumen tokens igual que si los pegara manualmente
- Projects con archivos curados por mí > Filesystem MCP con acceso amplio. Mi método actual es el correcto
- Triangulación multi-LLM via MCP pierde el valor porque cada IA necesita contexto completo — ese contexto lo doy yo manualmente con archivos, no lo puede reconstruir un prompt automático
- La fricción de configurar seguridad es intencional — es el costo de ser creíble como Solutions Architect

### Decisiones tomadas
- **ADR-007 creado**: principio de mínimo privilegio para acceso IA — universal (serie 000)
- **6 MCP servers aprobados para uso personal**: Sequential Thinking, Fetch, Brave Search, SQLite, Filesystem (restringido a /docs y /adr), Git (solo repos públicos con historial limpio)
- **MCP servers rechazados**: todos los que requieren acceso a PII de terceros (PostgreSQL con datos reales, Google Drive con docs de clientes, Slack, Telegram con notificaciones de leads)
- **Triangulación automatizada descartada**: sigue siendo manual, es la que da mejor calidad
- **Cuenta única por ahora**: mail profesional cubre experimentación. Separación de cuentas se implementa cuando haya cliente real con datos sensibles — decisión Type 2
- **NotebookLM se usa manual**: subir docs, generar audios/resúmenes, sin MCP

### Pendiente de actualizar en ADR-007
- Añadir sección: "MCP y GDPR — datos propios vs PII de terceros"
- Añadir sección: lista de los 6 MCP servers aprobados con justificación de cada uno
- Añadir sección: decisión de triangulación manual vs automatizada

### Archivos creados hoy
- `adr/ADR-007-principio-minimo-privilegio-acceso-ia.md`
- `triangulacion-geolabor-piloto.md` — documento para enviar a Gemini y DeepSeek

### Pendiente para próxima sesión
- Completar triangulación GeoLabor (enviar .md + fotos minutas + plantillas Excel a Gemini y DeepSeek)
- Sintetizar las tres respuestas conmigo
- Retomar PA1 Sesión 4: conectar N8N al nodo PostgreSQL, construir Capa 1 (placeholder HMAC) y Capa 2 (UPSERT con CTE atómica)
- Actualizar ADR-007 con las secciones pendientes

## 16/04 ✅ GeoLabor — Triangulación completa + diseño cerrado + sistema de ADRs expandido

### Qué hice

Completé la triangulación multi-LLM del piloto GeoLabor iniciada el 15/04. Envié
el documento de síntesis a DeepSeek (2 rondas) y a Gemini (2 rondas), analicé
todas las respuestas con Claude, y sinteticé los hallazgos en decisiones de diseño
concretas y documentadas. Al cierre de la sesión, el diseño del piloto está
completamente validado desde tres perspectivas independientes y listo para
construcción.

En paralelo, expandí el sistema de ADRs con dos nuevas decisiones de serie 100
(ADR-104 y ADR-105) y produje la primera consolidación de ADRs de la serie 300
(proyecto GeoLabor). Actualicé el archivo consolidado personal para reflejar el
estado completo a 16 de abril.

### Qué entendí

El hallazgo técnico más importante de la triangulación vino de DeepSeek en la
primera ronda: no existe una plantilla Excel única para los ensayos de GeoLabor.
Hay al menos tres variantes con lógicas de cálculo fundamentalmente distintas, y
en las variantes con cuarteo los pesos no se insertan en Foglio2 directamente sino
en Foglio4. Si el sistema hubiera escrito en la ubicación incorrecta, habría
producido porcentajes sistemáticamente falsos de forma silenciosa — el tipo de
error más peligroso porque parece correcto.

El hallazgo arquitectónico más importante vino de Gemini en la ronda final: en
geotecnia, los datos de un ensayo pueden terminar como evidencia en un peritaje.
Si el sistema procesa la imagen, el técnico confirma, se genera el Excel y la
imagen original desaparece, se rompe la cadena de custodia. Este no era un problema
técnico — era un problema legal que ninguna IA había detectado porque requiere
pensar como abogado, no como ingeniero.

Entendí también la diferencia entre lo que aporta cada modelo en la triangulación:
DeepSeek es el más preciso para analizar artefactos reales y detectar riesgos
técnicos ocultos. Gemini es el más fuerte en validación estratégica y en detectar
implicaciones de negocio que trascienden lo técnico. Claude aporta la síntesis,
la identificación de tensiones con los ADRs existentes y la estructuración del
razonamiento para que los tres modelos hablen sobre el mismo diseño.

Sobre JavaScript vs Python para la manipulación del Excel: los tres modelos
convergen en que `openpyxl` es técnicamente superior para este caso de uso, y
que `exceljs` tiene limitaciones reales al preservar gráficos en plantillas
existentes. Esta es una decisión Type 1 que afecta el stack de Fase 1, por eso
merece excepción documentada en ADR-101 y no simplemente adoptarse por inercia
de las recomendaciones de las IAs.

La pregunta más valiosa de toda la triangulación la hizo Gemini al final: si una
solución es técnicamente excelente pero difícil de explicar al cliente, ¿la
implementas? La respuesta correcta no es simplificar por comodidad — es buscar
la manera de explicar lo complejo, porque un sistema que solo su arquitecto puede
defender no es soberanía técnica del cliente. Es dependencia del proveedor con
otra etiqueta.

Sobre Arc42 y C4: ya estaba haciendo Arc42 de forma fragmentada sin saberlo.
Los ADRs cubren las decisiones de diseño, el diagrama de sistema cubre las vistas
de contenedores, el GDPR data mapping cubre los conceptos transversales. Formalizarlo
dentro de un estándar reconocido no es burocracia adicional — es hacer visible para
alguien externo el trabajo que ya existía.

### Decisiones tomadas

**Selector manual de tipo de ensayo**: la interfaz presenta un desplegable donde el
técnico elige el tipo antes de subir la imagen. Elimina la detección automática que
introduciría complejidad accidental con consecuencias graves si falla.

**Python vía Execute Command para la inyección Excel**: excepción justificada a
ADR-101 por limitación técnica real de `exceljs`. Documentada en ADR-301. Para el
piloto gratuito se usa Execute Command; Flask se evalúa en la primera versión de
pago cuando el sistema crezca a múltiples variantes.

**Triángulo DIN 18123 incluido en el piloto**: plantilla predefinida con scatter ya
configurado. El script escribe los tres porcentajes en celdas de entrada; el gráfico
actualiza el punto automáticamente al abrir en Excel Desktop. Es el "Wow Factor"
que diferencia el sistema de una herramienta de transcripción.

**Validación OCR en dos capas obligatorias y secuenciales**: primero validación
algorítmica (suma de pesos vs massa netta secca declarada manualmente — bloquea
si diferencia supera el 1%), luego confirmación visual humana con imagen al lado.
Ninguna capa reemplaza a la otra porque cada una detecta un tipo de error diferente.

**Almacén de evidencia con UUID**: cada imagen procesada se guarda en el VPS con
ruta determinista. El Excel generado contiene el UUID en celda oculta. PostgreSQL
registra json_raw_ocr y json_confirmado como campos separados — permite reconstruir
qué extrajo la IA y qué corrigió el técnico, que son dos momentos de evidencia
distintos. Tabla propia `geolabor_evidencias`, no reutilizar tabla `eventos` de PA1.

**Variante simple como primera entrega**: granulometría 0-30 mm, un solo rango de
tamices, pesos en Foglio2. Las variantes con cuarteo y sedimentación son la versión
de pago o la segunda entrega del piloto.

**ADR-104 creado**: Arc42 + C4 como estándar de documentación desde GeoLabor en
adelante. PA1 se migra cuando llegue a producción en Bloque 6.

**ADR-105 creado**: política de audiencia de ADRs — internos vs entregables. Serie
000 y 100 son siempre personales. Serie 200+ se clasifica ADR por ADR. Los
entregables se traducen al idioma del cliente como último paso antes de la entrega.

**Serie 300 para GeoLabor**: ADR-300 (entregable), ADR-301 (personal), ADR-302
(entregable), ADR-303 (entregable). Carpeta `adr/entregable/` + `adr/personal/`
en el proyecto.

### Archivos creados hoy

- `geolabor-piloto-master-context.md` — documento maestro de contexto con 9
  problemas documentados, resoluciones validadas y 5 decisiones pendientes.
  Diseñado para iniciar nuevas sesiones sin perder contexto.
- `triangulacion-geolabor-sintesis-claude.md` — síntesis que se envió a Gemini
  y DeepSeek como base de la triangulación.
- `geolabor-piloto-triangulacion-completa.md` — síntesis de todas las decisiones
  validadas, producida a mitad de sesión.
- `ADR-CONSOLIDADAS-geolabor-piloto_16-04-2026_.md` — consolidado de la serie 300.
- `ADR-CONSOLIDADAS-pablo-cuevas_16-04-2026_.md` — consolidado personal actualizado
  con ADR-104, ADR-105 y excepción de ADR-101 referenciando ADR-301. Total: 16 ADRs.
- `ADR-104-arc42-c4-estandar-documentacion.md` — individual.
- `ADR-105-politica-audiencia-adrs.md` — individual.
- `ADR-300-arquitectura-piloto-geolabor.md` — individual, entregable.
- `ADR-301-python-excepcion-adr101.md` — individual, personal.
- `ADR-302-almacen-evidencia-trazabilidad.md` — individual, entregable.
- `ADR-303-validacion-ocr-dos-capas.md` — individual, entregable.
- `geolabor-problema-9-arc42-c4.md` — entrada para insertar en Parte 3 del master context.

### Pendiente para próxima sesión

**PA1 Sesión 4** (prioridad): conectar N8N al nodo PostgreSQL con credenciales
del vault, construir Capa 1 (placeholder HMAC) y Capa 2 (UPSERT con CTE atómica).

**GeoLabor — antes de construir**:
- Email de scope al contacto de GeoLabor documentando por escrito qué incluye
  el piloto gratuito y qué queda fuera. Nada de código hasta que esto esté enviado.
- Mapeo técnico detallado de la variante simple: plantilla base → celdas destino
  por tamiz en Foglio2 → celdas de entrada del triángulo.
- Definir el origen exacto de la massa netta secca en la pantalla de confirmación
  (decisión pendiente número 1 del master context).
- Actualizar el master context con la corrección de PA1 (es pablocuevas.it,
  no Suanfarma) y con la entrada del Problema 9 (Arc42/C4).
- Verificar el plazo de retención de 5 años del ADR-302 con el laboratorio.