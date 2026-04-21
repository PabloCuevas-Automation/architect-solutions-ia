# DECISIONES ARQUITECTÓNICAS — PROYECTO GEOLABOR
## Piloto de Automatización Granulométrica

**Versión**: 1.0 Consolidada  
**Fecha**: 16 de abril 2026  
**Estado**: ✅ Activo — documento único de referencia del proyecto  
**Consolidó**: ADR-300 a ADR-303  
**Audiencia**: Mixta — ver campo de audiencia en cada ADR  
**Formato**: Searchable, cross-referenced, no redundancias

---

## NOTA SOBRE AUDIENCIA

Este documento contiene ADRs de dos tipos según la política ADR-105.

Los ADRs **entregables** forman parte del paquete de documentación que se
entrega al laboratorio. Están redactados en lenguaje de negocio y explican
decisiones del sistema que el cliente necesita entender para confiar en lo
que recibe. Al momento de la entrega final se traducen al italiano.

Los ADRs **personales** documentan decisiones internas de arquitectura y
stack. Nunca se entregan al cliente. Hacen referencia al roadmap personal
de Pablo y a decisiones de la serie 100.

---

## ÍNDICE RÁPIDO

| ID | Título | Audiencia | Estado | Cuándo consultar |
|----|--------|-----------|--------|-----------------|
| ADR-300 | Arquitectura del piloto de automatización | Entregable | ✅ Activo | Entender el sistema completo / onboarding |
| ADR-301 | Python para inyección Excel — excepción a ADR-101 | Personal | ✅ Activo | Si alguien pregunta por qué Python y no JS |
| ADR-302 | Almacén de evidencia y trazabilidad | Entregable | ✅ Activo | Auditorías / preguntas sobre custodia de datos |
| ADR-303 | Sistema de validación OCR en dos capas | Entregable | ✅ Activo | Preguntas sobre calidad de datos / controles |

---

---

# ADR-300 — Arquitectura del Piloto de Automatización Granulométrica

**Audiencia**: ✅ Entregable  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — define el diseño completo del sistema

---

### Contexto

El proceso actual de ensayos granulométricos en GeoLabor requiere que el técnico
transcriba manualmente los pesos retenidos en cada tamiz desde una minuta
manuscrita a una plantilla Excel. Este proceso consume tiempo repetitivo y es
fuente de errores humanos en datos que van a informes técnicos oficiales.

El scope del piloto gratuito es granulometría estándar más triángulo de
clasificación DIN 18123, para la variante simple (rango 0-30 mm) como primera
entrega. Las variantes con cuarteo y sedimentación, los límites de Atterberg, y
cualquier otro tipo de ensayo quedan explícitamente fuera del piloto y son objeto
de un proyecto de pago separado.

---

### La Decisión

Construir un sistema de automatización con las siguientes capas. Una interfaz web
local donde el técnico selecciona el tipo de ensayo y sube el escaneo de la minuta.
N8N como motor de orquestación que recibe la imagen, la envía a Claude Vision para
extracción OCR, y coordina el flujo completo. Una pantalla de confirmación con
validación en dos capas antes de generar el Excel. Un script Python que escribe los
datos en la plantilla Excel correspondiente. PostgreSQL para trazabilidad completa
de cada ensayo. Almacenamiento de la imagen original vinculada al Excel generado
mediante un UUID único.

---

### Decisión crítica — selector manual de tipo de ensayo

La interfaz presenta un selector desplegable donde el técnico elige el tipo de
ensayo antes de subir la imagen. Esta decisión reemplaza explícitamente la
alternativa de detección automática por parte del sistema.

La razón es que la detección automática requeriría que el sistema infiera el tipo
de ensayo desde el contenido de la imagen, introduciendo un punto de fallo con
consecuencias significativas: si el sistema clasifica incorrectamente el tipo,
escribe los datos en las celdas equivocadas y produce un Excel con porcentajes
incorrectos de forma silenciosa. El selector manual elimina esa ambigüedad
completamente. La pérdida de automatización total se acepta a cambio de
previsibilidad y fiabilidad en datos de uso técnico oficial.

El selector también hace el sistema escalable por diseño: añadir un nuevo tipo
de ensayo en el futuro requiere únicamente añadir una opción al selector y
proporcionar la plantilla Excel correspondiente, sin modificar lógica central.

---

### Flujo del sistema — variante simple

El técnico selecciona el tipo de ensayo y sube el escaneo. El frontend valida que
el selector tiene una selección antes de habilitar el botón de procesar. N8N genera
un UUID como identificador del ensayo y almacena la imagen original. N8N envía la
imagen a Claude Vision con un prompt estructurado que solicita los pesos por tamiz
en formato JSON. El sistema devuelve la tabla editable al frontend con la imagen al
lado. El técnico ingresa la massa netta secca manualmente, revisa los datos, y el
sistema valida la coherencia numérica antes de permitir la confirmación. Una vez
confirmado, el script Python escribe los datos en la plantilla correcta y genera el
Excel con el UUID en una celda oculta. El archivo queda disponible para descarga
con la advertencia de abrirlo en Microsoft Excel Desktop.

---

### Scope explícito del piloto gratuito

Está incluido: granulometría simple (un solo rango de tamices, pesos en Foglio2),
triángulo de clasificación DIN 18123 con punto automático, validación OCR en dos
capas, almacén de evidencia con UUID, trazabilidad en PostgreSQL.

Está excluido: granulometría con cuarteo (Foglio4), granulometría con sedimentación
(Foglio3), límites de Atterberg, clasificación USCS completa, múltiples muestras
en una hoja, informes automáticos, integración con KORN.LAB. Cualquier expansión
de este scope es un proyecto de pago separado.

---

### ADRs relacionados

ADR-301 documenta la decisión técnica de usar Python para la inyección de datos
en Excel. ADR-302 documenta el diseño del almacén de evidencia y su justificación
legal. ADR-303 documenta el sistema de validación OCR en dos capas.

---

---

# ADR-301 — Python para Inyección de Datos en Excel — Excepción a ADR-101

**Audiencia**: 🔒 Personal  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Táctica — excepción justificada a un principio de serie 100  
**Overrides parcialmente**: ADR-101 (JavaScript primero, Python en Fase 2)

---

### Contexto

ADR-101 documenta la decisión de mantener Python fuera del stack hasta Fase 2,
priorizando JavaScript como lenguaje único en Fase 1. Esa decisión sigue siendo
válida como principio general. Sin embargo, el piloto de GeoLabor requiere
modificar plantillas Excel existentes que contienen fórmulas activas, gráficos
de curva granulométrica, y un gráfico scatter para el triángulo DIN 18123.

La librería JavaScript `exceljs` tiene limitaciones documentadas al manipular
archivos `.xlsx` con objetos embebidos: puede corromper los gráficos existentes
al escribir en las celdas de la plantilla. Esto fue identificado y validado
independientemente por DeepSeek y Gemini durante la triangulación.

---

### La Decisión

Usar Python con `openpyxl` para la escritura de datos en las plantillas Excel
de GeoLabor. Python se invoca desde N8N mediante el nodo Execute Command, que
llama a un script `.py` pasando los datos confirmados como argumento JSON.

Esta excepción aplica exclusivamente a la generación de Excel en el piloto de
GeoLabor. No autoriza el uso de Python en ningún otro componente de este
proyecto ni en proyectos paralelos.

---

### Por qué Execute Command y no un microservicio Flask

Flask es la arquitectura más limpia a largo plazo porque separa la lógica de
generación de Excel en un servicio independiente. Sin embargo, para un piloto
gratuito de primera entrega añade complejidad no justificada por el alcance
actual. Execute Command es suficiente para la variante simple y puede migrarse
a Flask cuando el sistema crezca a múltiples variantes en producción.

La decisión de migrar a Flask se evalúa en la primera versión de pago del
sistema, no en el piloto.

---

### Condiciones bajo las cuales esta excepción aplica

Esta excepción está justificada cuando se cumplen las tres condiciones siguientes:
el caso de uso requiere modificar plantillas Excel con gráficos existentes, la
librería JavaScript disponible no puede hacerlo sin riesgo de corrupción, y el
alcance del proyecto justifica introducir un lenguaje adicional. Si alguna de
estas condiciones cambia, la excepción debe revisarse.

---

### Deuda técnica que genera

El entorno de construcción y el VPS de producción deben tener Python instalado.
Esto se gestiona dentro del contenedor Docker del proyecto, no en el sistema
operativo del host. La imagen Docker del script Python se documenta en el
docker-compose del proyecto.

---

---

# ADR-302 — Almacén de Evidencia y Trazabilidad de Ensayos

**Audiencia**: ✅ Entregable  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — requisito legal en entorno técnico regulado

---

### Contexto

En geotecnia, los datos de un ensayo granulométrico pueden terminar como evidencia
en un peritaje si hay un fallo estructural posterior en una obra donde se usaron
esos datos. Si el sistema procesa la imagen, el técnico confirma los datos, se
genera el Excel, y la imagen original no está almacenada y vinculada al Excel, se
rompe la cadena de custodia de la evidencia.

El Excel existe pero fue producido por un sistema automatizado que pudo haber
cometido un error. Sin la imagen original vinculada, no es posible demostrar que
el Excel es fiel a la minuta manuscrita del ensayo.

---

### La Decisión

El sistema almacena cada imagen procesada y la vincula al Excel generado mediante
un identificador único. Esta trazabilidad no es opcional y no puede desactivarse
— es una propiedad estructural del sistema.

---

### Los tres componentes

El primer componente es el almacenamiento de la imagen. Cada imagen se guarda en
el servidor con una ruta determinista:
`/evidencias/ensayos/{ID_ensayo}_{timestamp}_{tipo_prueba}.jpg`. El identificador
del ensayo es un UUID generado al inicio del flujo, antes de cualquier procesamiento.

El segundo componente es el registro en base de datos. Cada ensayo queda registrado
en PostgreSQL en la tabla `geolabor_evidencias` con los siguientes campos:
identificador del ensayo, timestamp, ruta de la imagen almacenada, JSON de los
datos extraídos por OCR antes de cualquier edición humana, JSON de los datos
confirmados por el técnico, identificador del técnico, y tipo de prueba seleccionado.
La distinción entre los dos campos JSON es fundamental: permite reconstruir en el
futuro qué extrajo el sistema y qué corrigió el técnico, que son dos momentos de
evidencia distintos.

El tercer componente es el vínculo en el Excel. El archivo generado contiene el
UUID del ensayo en una celda dedicada. Ese identificador es el puente entre
el Excel y la imagen original que lo originó.

---

### Lo que esto permite

Con el almacén de evidencia activo, el sistema puede responder en cualquier momento
posterior: qué imagen se procesó, qué extrajo el sistema, qué corrigió el técnico,
y qué archivo Excel se generó. Esa cadena completa es la trazabilidad del ensayo
desde la minuta manuscrita hasta el informe.

---

### Política de retención

Las imágenes y los registros de trazabilidad se conservan por un mínimo de cinco
años desde la fecha del ensayo, o hasta que el laboratorio confirme explícitamente
que los datos de ese periodo ya no son necesarios para ningún proyecto activo.
Este plazo debe verificarse contra los plazos de responsabilidad legal que aplican
a los proyectos específicos del laboratorio.

---

---

# ADR-303 — Sistema de Validación OCR en Dos Capas

**Audiencia**: ✅ Entregable  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Táctica — diseño de control de calidad de datos

---

### Contexto

El sistema extrae datos numéricos de minutas manuscritas mediante OCR. El riesgo
más peligroso no es el error catastrófico que cualquiera detecta visualmente. Es
el error numéricamente plausible: un "37" leído como "27", o un "127" leído como
"172". Este tipo de error puede pasar desapercibido en una revisión visual rápida.
Una pantalla de confirmación sin validación algorítmica previa no es una garantía
de calidad — es una falsa sensación de control.

---

### La Decisión

El sistema aplica dos capas de validación en secuencia antes de generar el Excel
final. Ninguna es opcional y ninguna reemplaza a la otra. Son complementarias
porque cada una detecta un tipo de error diferente.

---

### Capa 1 — Validación algorítmica

El sistema suma automáticamente todos los pesos parciales registrados y los compara
contra la massa netta secca total, que el técnico ingresa manualmente. Si la
diferencia supera el 1%, el sistema muestra una alerta roja y bloquea la
confirmación hasta que el técnico resuelva la discrepancia.

La massa netta secca se ingresa siempre manualmente, nunca por OCR. Esta decisión
es intencional: usar como referencia un dato que también viene del OCR significaría
que el sistema valida sus propios errores contra sí mismo. La referencia de
validación debe ser siempre verdad terreno proporcionada por el humano.

Esta capa detecta lo que el humano no puede ver con fiabilidad: discrepancias
numéricas acumuladas entre valores individuales.

---

### Capa 2 — Confirmación visual humana

La pantalla muestra la imagen original al lado de la tabla editable. Los valores
extraídos con baja confianza se resaltan visualmente. El técnico revisa comparando
directamente con la imagen y corrige los incorrectos. Solo después de que la capa
algorítmica haya pasado, el botón de confirmación está disponible.

Esta capa detecta lo que el algoritmo no puede determinar: errores de contexto y
valores que son numéricamente coherentes pero visualmente incorrectos.

---

### Secuencia obligatoria

La capa algorítmica siempre precede a la confirmación humana. Este orden no puede
invertirse ni omitirse.

---

### Lo que este diseño garantiza

El sistema no puede generar un Excel con datos que no hayan pasado por un control
de coherencia matemática y por una confirmación visual humana informada. Cualquier
error que llegue al Excel fue visible para el técnico en el momento de la
confirmación y fue aceptado conscientemente.

---

---

## REFERENCIAS CRUZADAS Y ESTADO

### Cómo navegar este documento

Para entender el sistema completo, leer ADR-300. Para entender por qué el sistema
guarda las imágenes, leer ADR-302. Para entender los controles de calidad de datos,
leer ADR-303. Para entender la decisión técnica sobre Python, leer ADR-301
(audiencia personal).

### ADRs de series superiores que aplican a este proyecto

| ADR | Título | Cómo aplica en GeoLabor |
|-----|--------|------------------------|
| ADR-000 | 7 Principios Arquitectónicos | Selector manual = simplicidad. Almacén evidencia = compliance desde diseño |
| ADR-002 | PII Management | Minimización de metadatos del escaneo. Sin extracción de datos del cliente del laboratorio |
| ADR-004 | 6-Layer Checklist | Obligatorio antes de deploy del piloto en VPS |
| ADR-005 | 5-Layer Workflow Audit | Obligatorio antes de dar el workflow por completo |
| ADR-006 | Gestión de Credenciales | API key de Claude Vision en vault de N8N, nunca en el workflow |
| ADR-104 | Arc42 + C4 | GeoLabor es el primer proyecto documentado con este estándar |
| ADR-105 | Audiencia de ADRs | Define qué ADRs de este documento son entregables y cuáles personales |

### Estado operativo (16 de abril 2026)

| ADR | Audiencia | Estado | Pendiente |
|-----|-----------|--------|-----------|
| ADR-300 | Entregable | ✅ Diseño cerrado | Traducción al italiano en entrega final |
| ADR-301 | Personal | ✅ Decisión cerrada | Nota en ADR-101 referenciando esta excepción |
| ADR-302 | Entregable | ✅ Diseño cerrado | Verificar plazo de retención con el laboratorio |
| ADR-303 | Entregable | ✅ Diseño cerrado | Traducción al italiano en entrega final |

---

**Documento consolidado por**: Pablo Cuevas + Claude (Anthropic)  
**Fecha consolidación**: 16 de abril 2026  
**Próxima revisión**: Al completar la primera entrega del piloto (variante simple)  
**No mezclar con**: ADR consolidadas de Pablo (serie 000-100) ni con decisiones del proyecto PA1
