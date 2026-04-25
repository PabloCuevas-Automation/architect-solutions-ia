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

### Pendiente para próxima sesión

**PA1 Sesión 7 — Capa 3 eventos:**
Registrar `abstract_consultado`, `abstract_error` y
`clasificacion_realizada` en la tabla `eventos` aplicando el patrón
ADR-202 desde el inicio — sin iteraciones de debugging.

Antes de empezar: verificar que no queden nodos PostgreSQL en el
workflow con Query Parameters activo : REVISADO Y NO HAY QUERY PARAMETERS EN LOS NODOS POSTGRESQL


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