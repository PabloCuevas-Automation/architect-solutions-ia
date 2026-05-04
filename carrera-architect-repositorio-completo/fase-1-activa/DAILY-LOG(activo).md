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

## 20/04 ✅ PA1 Sesión 4 — N8N integrado en Docker Compose + conexión a PostgreSQL

### Qué hice
- Migré N8N de docker run suelto a docker-compose junto con PostgreSQL y pgAdmin
- Resolví conflicto de encryption key entre volumen antiguo y variable nueva del .env
- Extraje la encryption key original del volumen n8n_data y la moví al .env
- Añadí n8n_data como volumen external: true en el compose para preservar workflows
- Conecté N8N a PostgreSQL desde el vault de credenciales — test exitoso
- Actualicé docker/README.md con comandos operativos del stack
- Actualicé .env.example con la variable N8N_ENCRYPTION_KEY

### Qué entendí
- Contenedores en docker run separado viven en redes aisladas — no se ven entre sí
- El nombre del servicio en el compose (postgres) es el host interno — la conexión
  nunca sale de la red privada de Docker
- La encryption key separada de los datos es el patrón correcto — key y datos
  juntos en el volumen es deuda técnica de seguridad
- SSL desactivado es correcto en local porque ambos servicios están en la misma
  red privada Docker — no hay tráfico externo que interceptar
- docker compose up -d es el único comando necesario para levantar todo el stack
  desde ahora — docker run ya no se usa

### Deuda técnica documentada
- Usuario PostgreSQL con mínimos privilegios pendiente — Bloque 7
- SSL entre N8N y PostgreSQL no aplica en local, revisar en producción — Bloque 6
- Encryption key vive en .env local — nunca en el repositorio, nunca en producción
  sin gestión explícita

### Comandos operativos del stack
- Levantar: docker compose up -d (desde carpeta docker/)
- Parar: docker compose down
- Ver estado: docker ps

### Pendiente al cierre
PA1 Sesión 5 — construir Capa 1 (placeholder HMAC) y Capa 2 
(CTE atómica + lógica de deduplicación) en el workflow

---

## 21/04 ✅ PA1 Sesión 5 — Capa 1 y Capa 2 construidas y validadas

### Qué hice
- Añadí capa1-validacion-hmac entre Webhook1 y procesador-body1
  (placeholder HMAC — TODO Bloque 7)
- Construí capa2-persistencia-upsert con CTE atómica en nodo Postgres
  usando Execute Query con los 7 parámetros del formulario
- Añadí capa2-merge-datos para fusionar resultado de PostgreSQL con
  datos originales del webhook (accesibles via $('capa1-validacion-hmac'))
- Añadí capa2-if-es-nuevo para rutear lead nuevo (true) vs duplicado (false)
- Añadí capa2-logica-duplicado para calcular horas desde timestamp_creacion
  y clasificar duplicado_real vs lead_reactivado
- Añadí capa2-if-reactivado para rutear hacia procesador-body1 si es
  lead_reactivado, o detener si es duplicado_real
- Registré pgAdmin conectado a leads_db y validé los datos con SELECT * FROM leads

### Qué entendí
- Los datos del webhook llegan bajo $json.body — no directamente en $json.
  El procesador-body1 los aplana, pero la Capa 2 está antes de ese nodo
  y necesita acceder via $json.body
- La CTE devuelve solo 3 campos (id, timestamp_creacion, es_nuevo) —
  capa2-merge-datos es necesario para que el resto del workflow tenga
  todos los datos disponibles
- new Date(string) es obligatorio para operar con timestamps de PostgreSQL —
  sin la conversión la resta devuelve NaN y la lógica de 24h nunca funciona
- El Stop and Error requiere configuración mínima — sin ella bloquea el workflow

### Validaciones superadas
- Lead nuevo: insertado en PostgreSQL con status pendiente ✅
- Duplicado real (< 24h): detectado, flujo detenido, sin llamar a Abstract
  ni enviar Telegram ✅
- horas_desde_creacion: 0.58 — duplicado real correctamente clasificado ✅
- pgAdmin confirma registro en tabla leads con todos los campos correctos ✅
- accion, razon, prioridad, nivel_resolucion en null — correcto,
  los llenará la Capa 3 ✅

### Flujo actual del workflow
Webhook1
  → capa1-validacion-hmac (placeholder HMAC)
  → capa2-persistencia-upsert (CTE atómica → PostgreSQL)
  → capa2-merge-datos (fusiona datos webhook + resultado PostgreSQL)
  → capa2-if-es-nuevo
      → true  → procesador-body1 → HTTP Abstract → enriquecedor-clasificador
               → IF → Switch → Telegram
      → false → capa2-logica-duplicado (calcula horas)
              → capa2-if-reactivado
                  → true  → procesador-body1 (mismo flujo que lead nuevo)
                  → false → flujo termina (duplicado_real ignorado)

## 25/04 ✅ PA1 Sesión 6 — Registro de eventos + fix crítico PostgreSQL + ADRs serie 200

### Qué hice

- Levanté el stack Docker (PostgreSQL + pgAdmin + N8N) con `docker compose up -d`
- Construí los tres nodos de registro de eventos en tabla `eventos`:
  `capa2-registrar-lead-recibido`, `capa2-registrar-duplicado`,
  `capa2-registrar-lead-reactivado`
- Identifiqué y resolví el bug crítico del campo Query Parameters de N8N
  (múltiples iteraciones de debugging con triangulación Claude + DeepSeek)
- Corregí el mismo bug en `capa2-persistencia-upsert` — el `messaggio` con
  coma rompía el array de parámetros silenciosamente en producción
- Añadí dos Code nodes de restauración de datos después de los nodos
  PostgreSQL para preservar el item completo en el flujo:
  `capa2-restaurar-datos-recibido` y `capa2-restaurar-datos-reactivado`
- Actualicé `capa2-logica-duplicado` para calcular `dias_desde_creacion`
  directamente en la rama `lead_reactivado`, sin nodos intermedios
- Validé los tres casos con Thunder Client + pgAdmin con JOIN entre
  `eventos` y `leads`: `lead_recibido`, `duplicado_detectado`,
  `lead_reactivado` — todos correctos y con datos coherentes
- Limpié las tablas de prueba y verifiqué contadores en 0
- Creé los ADRs de la serie 200 (ADR-200 a ADR-205) — individuales
  y consolidado

### Qué entendí

**Hallazgo crítico — Query Parameters en N8N rompe con valores complejos:**
El campo Query Parameters parsea el array como texto antes de enviarlo a
PostgreSQL. Cualquier coma, comilla o carácter especial dentro de un valor
rompe ese parsing — los parámetros `$1, $2, $3...` no se mapean
correctamente y PostgreSQL recibe menos parámetros de los esperados.

La raíz del problema: en modo `fx`, N8N envía el array entero como un
solo parámetro `$1`. En modo normal, las comillas anidadas dentro de
strings JSON o los timestamps sin comillas descomponen el array.

La solución definitiva: expresiones `{{ $json.campo }}` directamente en
el SQL con Query Parameters vacío. N8N evalúa las expresiones primero y
construye el SQL completo antes de enviarlo. PostgreSQL recibe SQL ya
formado — sin arrays intermedios, sin parsing ambiguo.

**Regla operativa permanente:**
> En nodos PostgreSQL de N8N: expresiones `{{ $json.campo }}` directamente
> en el SQL. Query Parameters siempre vacío. Sin excepciones.

**Por qué los Code nodes de restauración son necesarios:**
Los nodos PostgreSQL con INSERT sin RETURNING devuelven `{"success": true}`
y reemplazan el item del flujo. Todo lo que viene después recibe solo ese
objeto — Abstract, Telegram, y el resto del sistema quedan sin datos.
La solución: `$('nombre-nodo').first()` recupera el item de cualquier
nodo anterior en el contexto de ejecución, aunque el nodo actual haya
devuelto otro valor.

**Por qué `capa2-restaurar-datos-recibido` referencia `capa2-merge-datos`:**
Es el último nodo antes de la bifurcación con todos los campos fusionados
del lead más los resultados de PostgreSQL (`id`, `es_nuevo`,
`timestamp_creacion`).

**Por qué `capa2-restaurar-datos-reactivado` referencia
`capa2-logica-duplicado`:**
Es el último nodo de la rama de duplicados con los campos calculados
`horas_desde_creacion` y `dias_desde_creacion` que no existen en
`capa2-merge-datos`.

**La triangulación funcionó correctamente:**
DeepSeek fue más preciso en el análisis del síntoma concreto (mensaje
de error + screenshot). Claude identificó la causa raíz estructural.
La síntesis de ambos llegó a la solución correcta más rápido que
cualquiera de los dos por separado.

### Decisiones tomadas

- **Regla operativa ADR-202**: Query Parameters vacío en todos los nodos
  PostgreSQL de N8N — expresiones inline en el SQL
- **`json_build_object` en el SQL** para construir JSONB dentro de
  PostgreSQL en lugar de serializar JSON desde N8N
- **Detalle de `duplicado_detectado` simplificado** a solo
  `horas_desde_creacion` — `email` y `servizio` ya están en `leads`
  y se recuperan con JOIN. El dato de valor único es el tiempo
  transcurrido que distingue doble click de intento de recontacto
- **`dias_desde_creacion` calculado solo en rama `lead_reactivado`**
  de `capa2-logica-duplicado` — no tiene sentido en `duplicado_real`
- **Serie 200 de ADRs creada**: ADR-200 a ADR-205, todas audiencia
  personal, consolidadas en `ADR-CONSOLIDADAS-PA1_25-04-2026.md`

### Flujo actual del workflow (estado al cierre)

```
Webhook1
  → capa1-validacion-hmac (placeholder HMAC — Bloque 7)
  → capa2-persistencia-upsert (CTE atómica → PostgreSQL, expresiones inline)
  → capa2-merge-datos (fusiona datos webhook + resultado PostgreSQL)
  → capa2-if-es-nuevo
      → true  → capa2-registrar-lead-recibido (PostgreSQL)
                → capa2-restaurar-datos-recibido ($('capa2-merge-datos').first())
                → procesador-body1
                → HTTP Abstract
                → enriquecedor-clasificador
                → IF → Switch → Telegram
      → false → capa2-logica-duplicado (calcula horas + dias_desde_creacion)
              → capa2-if-reactivado
                  → true  → capa2-registrar-lead-reactivado (PostgreSQL)
                             → capa2-restaurar-datos-reactivado
                               ($('capa2-logica-duplicado').first())
                             → procesador-body1 (mismo flujo que lead nuevo)
                  → false → capa2-registrar-duplicado (PostgreSQL) → fin
```

### Archivos creados hoy

- `ADR-201-cte-atomica-deduplicacion.md`
- `ADR-202-patron-queries-postgresql-n8n.md`
- `ADR-203-jsonb-campo-detalle.md`
- `ADR-204-write-before-processing.md`
- `ADR-205-nivel2-no-procesa.md`
- `ADR-CONSOLIDADAS-PA1_25-04-2026.md` — consolidado serie 200
- `entrada-daily-log-25-04-2026.md` — esta entrada

### Deuda técnica documentada

- **ADR-202**: advertencia de SQL injection en N8N al usar expresiones
  inline — evaluar sanitización explícita en Bloque 7
- **ADR-200**: URL ngrok temporal en formulario — reemplazar por URL
  VPS en Bloque 6
- **Diagrama sistema v4.2**: actualizar para reflejar los tres nodos
  de eventos y los dos Code nodes de restauración en Capa 2
- **ADR-CONSOLIDADAS-pablo-cuevas**: actualizar para referenciar que
  la serie 200 existe en su propio archivo consolidado

## 28/04 ✅ PA1 Sesión 7 — Capa 3 eventos: Abstract + Clasificación

### Qué hice

- Levanté el stack Docker (PostgreSQL + pgAdmin + N8N) con `docker compose up -d`
  — confirmé que el warning `No services to build` es inofensivo (todos los
  servicios usan imágenes pre-construidas, no hay nada que compilar)
- Construí los tres nodos de Capa 3 aplicando ADR-202 desde el inicio, sin
  iteraciones de debugging:
  - `capa3-registrar-abstract-consultado` — PostgreSQL con `json_build_object`
  - `capa3-registrar-abstract-error` — PostgreSQL en rama de error de HTTP Abstract
  - `capa3-registrar-clasificacion` — PostgreSQL después de `enriquecedor-clasificador`
- Añadí dos Code nodes de restauración:
  - `capa3-restaurar-datos-abstract` — devuelve output de `HTTP Abstract (email)`
  - `capa3-restaurar-datos-clasificacion` — devuelve output de `enriquecedor-clasificador`
- Activé la rama de error en `HTTP Abstract (email)` via Settings →
  On Error → `Continue (using error output)` — genera salida `Error`
  separada de la salida `Success`
- Validé los tres eventos en pgAdmin con JOIN `eventos` + `leads` —
  todos correctos y con JSONB coherente
- Actualicé diagrama sistema a v4.3 — sincronizado con la realidad
  construida: flujo real con nombres de nodos exactos, JSONB de eventos
  corregidos, UPDATE de reactivación marcado como deuda técnica explícita
- Actualicé ADR-CONSOLIDADAS-PA1 con aplicación de ADR-202 y ADR-203
  en Capa 3 y decisiones de campos JSONB de los tres eventos nuevos

### Qué entendí

**El Code node de restauración es soberanía sobre el flujo de datos:**
Los nodos PostgreSQL con INSERT sin RETURNING devuelven `{"success": true}`
y destruyen el item. El patrón `return [$('nombre-nodo').first()]` no es
solo un fix técnico — es una decisión arquitectónica consciente de qué
datos salen de cada nodo. Puedes limpiar, filtrar o transformar antes de
continuar. Eso es control explícito del flujo, no magia implícita.

**La rama de error de HTTP en N8N no es automática:**
Por defecto N8N detiene el flujo si un nodo HTTP falla. Para capturar ese
error y registrarlo en `eventos`, hay que activar explícitamente
`Continue (using error output)` en Settings. Eso abre una segunda salida
en el nodo — la rama roja. Sin ese toggle, el error nunca llega al nodo
PostgreSQL.

**GDPR data minimization en la práctica — Abstract devuelve ~30 campos:**
De todo el JSON de Abstract, guardamos solo 4 campos con valor operativo
real: `deliverability_status`, `quality_score`, `is_live_site`,
`address_risk`. El resto se descarta. No se guarda lo que no se usa —
eso es minimización real, no solo teoría.

**El nombre del nodo en `$()` debe ser idéntico al del canvas:**
`$('HTTP Abstract')` falla si el nodo se llama `HTTP Abstract (email)`.
Mayúsculas, paréntesis y espacios incluidos — referencia exacta de
string, no búsqueda aproximada.

### Decisiones tomadas

- **Campos guardados en `abstract_consultado`**: `deliverability_status`,
  `quality_score`, `is_live_site`, `address_risk` — mínimo operativo,
  sin PII redundante
- **`abstract_error` guarda solo `error_message`** — el único dato
  disponible cuando la API falla por completo
- **`clasificacion_realizada` guarda**: `accion_recomendada`, `razon`,
  `prioridad`, `nivel_riesgo` — los campos que determinaron la decisión
  de negocio, trazabilidad completa
- **Workflow no se versiona en Git** — el JSON exportado de N8N puede
  contener URLs de ngrok o credenciales hardcodeadas. Se mantiene solo
  en local hasta Bloque 6, cuando ngrok desaparece y el entorno es limpio
- **GeoLabor en standby** — PA1 debe estar en producción antes de
  reactivar GeoLabor. PA1 actúa como banco de errores y base de patrones.
  Nada de GeoLabor hasta que Bloque 6 esté completo

### Flujo actual del workflow (estado al cierre)

```
Webhook1
  → capa1-validacion-hmac (placeholder HMAC — Bloque 7)
  → capa2-persistencia-upsert (CTE atómica → PostgreSQL)
  → capa2-merge-datos
  → capa2-if-es-nuevo
      → true  → capa2-registrar-lead-recibido (PostgreSQL)
                → capa2-restaurar-datos-recibido
                → procesador-body1
                → HTTP Abstract (email)
                    → Success → capa3-registrar-abstract-consultado (PostgreSQL)
                                → capa3-restaurar-datos-abstract
                                → enriquecedor-clasificador
                                → capa3-registrar-clasificacion (PostgreSQL)
                                → capa3-restaurar-datos-clasificacion
                                → IF → Switch → Telegram
                    → Error   → capa3-registrar-abstract-error (PostgreSQL) → fin
      → false → capa2-logica-duplicado
              → capa2-if-reactivado
                  → true  → capa2-registrar-lead-reactivado (PostgreSQL)
                             → capa2-restaurar-datos-reactivado
                             → procesador-body1 (mismo flujo desde HTTP Abstract)
                  → false → capa2-registrar-duplicado (PostgreSQL) → fin
```

### Archivos creados / actualizados hoy

- `pa1-diagrama-sistema.md` — actualizado a v4.3
- `ADR-CONSOLIDADAS-PA1` — actualizado con decisiones de Sesión 7

## 01/05 ✅ PA1 — Documentación al día + decisiones de arquitectura pendientes

### Qué hice

- Decidí poner GeoLabor en standby hasta que PA1 esté en producción real —
  PA1 funciona como banco de errores y aprendizaje antes de replicar el
  patrón en GeoLabor
- Actualicé el diagrama de sistema a v4.3 — refleja los cinco nodos de
  Capa 3 y los dos Code nodes de restauración nuevos
- Enmendé ADR-203 con los campos JSONB reales implementados en Sesión 7:
  los tres eventos de Capa 3 tenían campos de diseño, no los campos
  construidos. Corrección aplicada en el ADR standalone y en el consolidado
- Actualicé ADR-CONSOLIDADAS-PA1 a v1.1 con la enmienda del ADR-203 y
  el estado operativo al día
- Actualicé ADR-CONSOLIDADAS-pablo-cuevas a v2.1 — añadí ADR-007 al índice
  (estaba en el repo pero no en el documento) y actualicé la serie 200
  de "0 activos" a "6 activos" con referencia al archivo consolidado de PA1
- Definí el objetivo de PA1: producción real, con VPS, sin ngrok.
  No "funciona en local" — funciona en la red
- Identifiqué brecha GDPR en las notificaciones de Telegram: el sistema
  envía PII (nome, email, messaggio) a través de Telegram, que no es un
  procesador GDPR-compliant y cuyos servidores están fuera de la UE.
  Decisión de diseño pendiente de formalizar en ADR-206
- Revisión superficial del tema VPS — candidatos evaluados por precio,
  recursos y ubicación de servidor para GDPR

### Qué entendí

**Documentación desincronizada es deuda invisible:**
ADR-203 tenía los campos de diseño originales, no los construidos. Sin
la enmienda, cualquier consulta futura al ADR habría dado información
incorrecta sobre qué vive en la DB. La documentación exacta no es
burocracia — es la diferencia entre un sistema auditable y uno opaco.

**Telegram no es una extensión del sistema — es un canal externo:**
Cuando el notificador manda nombre y email por Telegram, esos datos salen
de la infraestructura controlada. El lead está en PostgreSQL (EU, bajo
control); la notificación viaja a servidores de Telegram (fuera de UE,
sin garantías GDPR). Son dos cosas distintas. La solución no es eliminar
Telegram — es cambiar qué se manda: solo el ID del lead + servicio +
acción recomendada. Sin PII. El detalle se consulta en la DB.

### Decisiones tomadas

- **GeoLabor en standby** hasta que PA1 esté en producción
- **Objetivo de PA1 redefinido**: producción real = VPS + dominio propio +
  sin ngrok + N8N accesible públicamente
- **ADR-206 identificado**: Minimización PII en Notificaciones Telegram —
  pendiente de escribir antes de construir la notificación final

### Archivos actualizados hoy

- `pa1-diagrama-sistema.md` → v4.3
- `adr/ADR-203-jsonb-campo-detalle.md` → enmendado 01/05
- `ADR-CONSOLIDADAS-PA1_01-05-2026.md` → v1.1 (supersede _25-04-2026)
- `ADR-CONSOLIDADAS-pablo-cuevas_01-05-2026_.md` → v2.1 (supersede _16-04-2026)

## 03/05 ✅ Bloque 6 — Arquitectura de seguridad VPS completa + planificación de deploy

### Qué hice

Sesión de arquitectura y planificación completa. No se tocó código.
El objetivo era diseñar la infraestructura de seguridad del VPS antes de
contratar el servidor — siguiendo el principio de ADR-010: seguridad antes
que producción.

**Triangulación de seguridad VPS — 4 rondas con Claude + Gemini + DeepSeek + ChatGPT:**
- Ronda 1: Evaluación de proveedores VPS y decisión Hetzner CX22
- Ronda 2: Hardening del sistema, aislamiento multi-proyecto, backups
- Ronda 3: Documento consolidado completo — 12 capas validadas
- Ronda 4: Gaps finales — TLS explícito, logs Docker, egress filtering
- Simulación de ataque por ChatGPT — validación final del diseño
- Gemini y DeepSeek validaron la simulación e identificaron el último gap crítico

**Documentos generados (8 archivos):**
- `ADR-008-arquitectura-seguridad-vps.md` — 9 capas de seguridad, universal
- `ADR-009-seguridad-workflows-n8n.md` — auditoría de workflows, universal
- `ADR-010-secuencia-deploy-seguridad-primero.md` — orden correcto de deploy, universal
- `ADR-207-reconstruccion-capa4-pre-produccion.md` — PA1 específico
- `CHECKLIST-PRE-PRODUCCION-PA1.md` — 40 ítems, todos deben ser ✅
- `RUNBOOK-VPS-001.md` — guía de ejecución completa paso a paso
- `DIAGRAMA-SEGURIDAD-VPS.html` — diagrama visual interactivo de las 9 capas
- `WORKFLOW-AUDIT-TEMPLATE.md` — plantilla de auditoría de seguridad por workflow

**ADR-007 generado como archivo individual:**
- Existía en el consolidado pero no como archivo independiente en `serie-000-principios/`
- Generado y añadido: MCP servers aprobados, rechazados, regla GDPR, evaluación de nuevos servers

**Consolidado pablo-cuevas actualizado a v3.0:**
- Incorpora ADR-007 completo, ADR-008, ADR-009, ADR-010
- Índice actualizado: 16 entradas en series 000+100
- Estado operativo: 11 ADRs en serie 000, 27 en total del sistema
- Tabla de deuda actualizada: ADR-010 marcado como 🔴 bloquea deploy

**Descubrimiento crítico — Capa 4 viola ADR-206:**
Al revisar el workflow real (pa1-diagrama-sistema.md), se confirmó que los
nodos de notificación actuales envían nome, cognome, email, azienda, servizio
y messaggio por Telegram. Esto viola directamente ADR-206 (aprobado el 02/05).
Esta contradicción no es deuda técnica futura — es una violación activa que
debe resolverse antes de que entre cualquier dato real de cliente.

**Corrección de numeración de ADRs:**
ADR-207, 208, 209 eran incorrectos — contenido universal numerado como PA1.
Renombrados: ADR-008, ADR-009, ADR-010. El ADR-207 de PA1 es ahora la
reconstrucción de Capa 4.

**Reorganización del repositorio:**
- `sistema-leads-pablocuevas/adr/ADR-207-arquitectura-seguridad-vps.md` → eliminar
- `serie-000-principios/` → añadir ADR-007, 008, 009, 010
- `sistema-leads-pablocuevas/adr/` → añadir ADR-207-reconstruccion-capa4
- `audits/workflows/_templates/` → mover WORKFLOW-AUDIT-TEMPLATE.md
- `audits/workflows/` → renombrar audit existente con sufijo `_5layer_v1_17-03-2026`

### Qué entendí

**La diferencia entre riesgo operativo y riesgo existencial:**
Un workflow que falla tiene segunda oportunidad — el lead está en PostgreSQL,
hay logs, hay estado `pendiente`. Una brecha de datos PII no tiene segunda
oportunidad — notificación GDPR en 72h, responsabilidad legal, pérdida de
confianza irrecuperable. Diseñar para estos dos escenarios requiere prioridades
asimétricas: la seguridad del segundo escenario no se negocia aunque retrase el deploy.

**El gap que nadie menciona — Docker ignora UFW:**
Docker manipula iptables directamente. Si mapeas `5432:5432` en el compose,
PostgreSQL está expuesto a internet aunque UFW diga que está bloqueado. La solución
correcta es eliminar el bloque `ports` en servicios que solo se comunican entre
contenedores — sin mapeo de puertos, Docker no toca iptables.

**Egress filtering es la defensa que convierte una brecha en un incidente contenible:**
Sin egress filtering, un atacante que entra en un contenedor puede exfiltrar datos
a cualquier servidor externo silenciosamente. Con la cadena DOCKER-USER bloqueando
todo excepto HTTPS/HTTP/DNS, el atacante queda atrapado dentro. Para el GDPR, la
diferencia es total: los datos no salen del servidor → no hay breach en sentido legal.

**La triangulación tiene rendimientos decrecientes — saber cuándo parar:**
Cuatro rondas con cuatro IAs produjeron gaps reales en cada ronda. A partir de
cierto punto, los gaps son refinamientos de implementación, no decisiones arquitectónicas
nuevas. La señal de cierre es la convergencia total en lo estructural.

**Los workflows son el daño — la infraestructura solo contiene el daño:**
Todo el hardening del VPS no evita que un workflow mal construido exfiltre datos
usando los propios canales legítimos del sistema (Telegram con HTTPS permitido).
La seguridad de aplicación es un proceso continuo de auditoría, no una configuración puntual.

**El replay attack es GDPR, no solo integridad:**
Un campo de texto libre como `messaggio` puede contener datos de categoría especial
(salud, situación familiar, etc.). Reproducir esa petición 10.000 veces no es
solo corrupción de datos — es procesamiento masivo no consentido de datos especiales
bajo Art. 9 GDPR. El HMAC con timestamp pasa de Bloque 7 a prerequisito de producción.

### Decisiones tomadas

- **ADR-008**: Arquitectura de seguridad VPS — 9 capas, universal, todos los proyectos
- **ADR-009**: Seguridad de workflows N8N — 3 preguntas de auditoría, universal
- **ADR-010**: Secuencia de deploy: seguridad antes que producción — Type 1, universal
- **ADR-207**: Reconstrucción de Capa 4 antes de producción — PA1 específico
- **Hetzner CX22 confirmado**: empresa alemana, DPA firmable, datacenter EU,
  convergencia unánime de las cuatro IAs
- **Suanfarma límite multi-tenant**: VPS compartido válido para pilotos.
  Cuando pase a cliente de pago → VPS dedicado (compliance farmacéutico)
- **Replay attack sube de Bloque 7 a prerequisito pre-producción**: campo
  `messaggio` puede contener datos GDPR Art. 9 — no es deuda técnica aceptable

### Vulnerabilidades identificadas en PA1 (a resolver antes del deploy)

| Vulnerabilidad | Riesgo | Resolver |
|----------------|--------|---------|
| HMAC como placeholder (Capa 1) | Cualquier petición HTTP procesada | Antes de Bloque 6 |
| Capa 4 envía PII a Telegram | Violación activa ADR-206 | Antes de Bloque 6 |
| messaggio sin sanitizar | XSS persistente en interfaz de administración | Antes de Bloque 6 |
| SQL injection en expresiones inline | Riesgo con HMAC desactivado | Mitigado por HMAC — Bloque 7 |
| Replay attack sin timestamp | GDPR Art. 9 si messaggio tiene datos especiales | Antes de Bloque 6 |

### Archivos creados / actualizados hoy

**Nuevos — serie 000:**
- `serie-000-principios/ADR-007-principio-minimo-privilegio-acceso-ia.md`
- `serie-000-principios/ADR-008-arquitectura-seguridad-vps.md`
- `serie-000-principios/ADR-009-seguridad-workflows-n8n.md`
- `serie-000-principios/ADR-010-secuencia-deploy-seguridad-primero.md`

**Nuevos — PA1:**
- `sistema-leads-pablocuevas/adr/ADR-207-reconstruccion-capa4-pre-produccion.md`
- `sistema-leads-pablocuevas/checklist/CHECKLIST-PRE-PRODUCCION-PA1.md`
- `sistema-leads-pablocuevas/runbooks/RUNBOOK-VPS-001.md`
- `sistema-leads-pablocuevas/diagrams/DIAGRAMA-SEGURIDAD-VPS.html`
- `sistema-leads-pablocuevas/audits/workflows/_templates/WORKFLOW-AUDIT-TEMPLATE.md`

**Actualizados:**
- `serie-consolidados/ADR-CONSOLIDADAS-pablo-cuevas_02-05-2026.md` → v3.0
- `proyectos/triangulacion-seguridad-vps-proyectos/` → documentos de triangulación

**Eliminados / renombrados:**
- `sistema-leads-pablocuevas/adr/ADR-207-arquitectura-seguridad-vps.md` → ELIMINAR
  (era número incorrecto — contenido movido a ADR-008 en serie-000)
- `audits/workflows/WORKFLOW-AUDIT-TEMPLATE.md` → MOVER a `_templates/`
- `audits/workflows/sistema-leads-enriquecido-audit.md` →
  RENOMBRAR a `sistema-leads-enriquecido_5layer_v1_17-03-2026.md`

### Pendiente para próxima sesión

**FASE A — Resolver vulnerabilidades (en local, antes de contratar Hetzner):**

1. **Implementar HMAC real en Capa 1**
   - Reemplazar placeholder por validación real de firma HMAC
   - Añadir timestamp en el payload del formulario
   - Ventana de rechazo: > 300 segundos
   - Verificar con Thunder Client: sin header → rechaza, firma inválida → rechaza, firma válida → procesa

2. **Reconstruir Capa 4 según ADR-206 y ADR-207**
   - Eliminar nome, cognome, email, messaggio de los nodos de Telegram
   - Nuevo formato: ID + servicio + acción + riesgo + link
   - El link apunta a la interfaz de administración (prerequisito: VPS operativo)
   - Verificar que el mensaje de Telegram NO contiene PII

3. **Sanitizar messaggio en procesador-body1**
   - Añadir escape de HTML antes del INSERT en PostgreSQL
   - Verificar con payload `<script>alert('xss')</script>` → debe almacenarse sanitizado

4. **Re-auditar sistema-leads-enriquecido:**
   - `sistema-leads-enriquecido_5layer_v2_02-05-2026.md` — actualizar estado de deudas resueltas
   - `sistema-leads-enriquecido_security_02-05-2026.md` — nuevo security audit (ADR-009)

**FASE B — VPS (después de resolver FASE A):**
- Contratar Hetzner CX22 (datacenter Alemania o Finlandia)
- Firmar DPA inmediatamente tras contratar
- Ejecutar RUNBOOK-VPS-001.md completo
- Completar CHECKLIST-PRE-PRODUCCION-PA1.md (todos los ítems en ✅)
- Snapshot post-hardening antes de primer proyecto

**FASE C — Deploy PA1:**
- Completar checklist pre-producción
- Deploy con vulnerabilidades resueltas
- Primer lead real en producción

---

**Nota de sesión**: Sesión íntegramente de arquitectura y planificación.
Próxima sesión arranca directo en código — HMAC real en Capa 1.