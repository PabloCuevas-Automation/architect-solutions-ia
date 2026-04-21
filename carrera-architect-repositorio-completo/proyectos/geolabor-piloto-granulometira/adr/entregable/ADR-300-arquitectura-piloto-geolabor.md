# ADR-300: Arquitectura del Piloto de Automatización Granulométrica

**Proyecto**: GeoLabor s.a.s. — Piloto de automatización  
**Serie**: 300 — Proyecto GeoLabor  
**Audiencia**: Entregable  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — define el diseño completo del sistema

---

## Contexto

El proceso actual de ensayos granulométricos en GeoLabor requiere que el técnico
transcriba manualmente los pesos retenidos en cada tamiz desde una minuta manuscrita
a una plantilla Excel. Este proceso consume tiempo repetitivo y es fuente de errores
humanos en datos que van a informes técnicos oficiales.

El scope del piloto gratuito es granulometría estándar más triángulo de clasificación
DIN 18123, para la variante simple (rango 0-30 mm) como primera entrega. Las
variantes con cuarteo y sedimentación, los límites de Atterberg, y cualquier otro
tipo de ensayo quedan explícitamente fuera del piloto y son objeto de un proyecto
de pago separado.

---

## La Decisión

Construir un sistema de automatización con las siguientes capas. Una interfaz web
local donde el técnico selecciona el tipo de ensayo y sube el escaneo de la minuta.
N8N como motor de orquestación que recibe la imagen, la envía a Claude Vision para
extracción OCR, y coordina el flujo completo. Una pantalla de confirmación con
validación en dos capas antes de generar el Excel. Un script Python que escribe los
datos en la plantilla Excel correspondiente. PostgreSQL para trazabilidad completa
de cada ensayo. Almacenamiento de la imagen original vinculada al Excel generado
mediante un UUID único.

---

## Decisión crítica: selector manual de tipo de ensayo

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

## Flujo del sistema (variante simple)

El técnico selecciona el tipo de ensayo y sube el escaneo. El frontend valida que
el selector tiene una selección antes de habilitar el botón de procesar. N8N genera
un UUID como identificador del ensayo y almacena la imagen original. N8N envía la
imagen a Claude Vision con un prompt estructurado que solicita los pesos por tamiz
en formato JSON. El sistema devuelve la tabla editable al frontend con la imagen
al lado. El técnico ingresa la massa netta secca manualmente, revisa los datos,
y el sistema valida la coherencia numérica antes de permitir la confirmación. Una
vez confirmado, el script Python escribe los datos en la plantilla correcta y
genera el Excel con el UUID en una celda oculta. El archivo queda disponible
para descarga con la advertencia de abrirlo en Microsoft Excel Desktop.

---

## Scope explícito del piloto gratuito

Está incluido: granulometría simple (un solo rango de tamices, pesos en Foglio2),
triángulo de clasificación DIN 18123 con punto automático, validación OCR en dos
capas, almacén de evidencia con UUID, trazabilidad en PostgreSQL.

Está excluido: granulometría con cuarteo (Foglio4), granulometría con sedimentación
(Foglio3), límites de Atterberg, clasificación USCS completa, múltiples muestras
en una hoja, informes automáticos, integración con KORN.LAB. Cualquier expansión
de este scope es un proyecto de pago separado.

---

## ADRs relacionados de este proyecto

ADR-301 documenta la decisión técnica de usar Python para la inyección de datos
en Excel. ADR-302 documenta el diseño del almacén de evidencia y su justificación
legal. ADR-303 documenta el sistema de validación OCR en dos capas.

---

**Autor**: Pablo Cuevas  
**Fecha**: 16 de abril 2026
