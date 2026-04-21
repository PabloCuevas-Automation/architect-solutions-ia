# GeoLabor Piloto — Documento Maestro de Contexto
## Problemas identificados, resoluciones validadas y decisiones pendientes

**Versión**: 1.0  
**Fecha**: 16 de abril 2026  
**Autor**: Pablo Cuevas  
**Estado**: Diseño completo validado por triangulación — listo para construcción  
**Propósito**: Documento de inicio de conversación. Contiene TODO lo necesario
para retomar el proyecto sin perder contexto arquitectónico.

---

## QUIÉN ES PABLO — CONTEXTO MÍNIMO NECESARIO

Pablo Cuevas es un AI Solutions Architect en formación basado en Rovereto, Trento,
Italia. Su stack activo en Fase 1 es N8N (motor de automatización), PostgreSQL
(persistencia), JavaScript (lógica), y Docker/Docker Compose (containerización).
Python está documentado como fuera del stack hasta Fase 2 — esto es una decisión
arquitectónica consciente en ADR-101, no ignorancia. Su diferenciador de mercado
es GDPR by design más documentación arquitectónica como entregable.

GeoLabor s.a.s. es el laboratorio geotécnico donde Pablo trabaja actualmente.
El piloto de automatización que se describe en este documento es trabajo gratuito,
diseñado como prueba de concepto y pieza de portfolio. El contacto en GeoLabor
no es Angelo (que es el contacto de Suanfarma, proyecto PA1 separado).

El sistema PA1 (Suanfarma) es un proyecto paralelo de gestión de leads con N8N
y PostgreSQL. Tiene su propio diseño, ADRs y estado de construcción. No mezclar
contextos entre los dos proyectos.

---

## PARTE 1 — EL PROBLEMA DE NEGOCIO

GeoLabor realiza ensayos granulométricos de suelos. El flujo actual tiene dos
pasos manuales que generan fricción real. El técnico registra los pesos retenidos
en cada tamiz de una tabla impresa escribiendo a mano durante el ensayo. Luego
transcribe esos datos desde el papel a una plantilla Excel predefinida que contiene
fórmulas para calcular porcentajes de retención y paso, generar la curva
granulométrica, y proporcionar datos para la clasificación del suelo.

El dolor es doble: tiempo repetitivo en transcripción que no genera valor, y riesgo
de error humano en datos que van a informes técnicos oficiales. En geotecnia, una
clasificación de suelo incorrecta puede terminar en un peritaje legal si hay un
fallo estructural posterior en la obra donde se usaron los datos.

El scope del piloto gratuito, acordado verbalmente, es granulometría estándar más
triángulo de clasificación DIN 18123. Todo lo demás — límites de Atterberg,
clasificación USCS completa, sedimentación — es proyecto de pago.

---

## PARTE 2 — EL FLUJO PROPUESTO (diseño original de Pablo)

El técnico abre una interfaz web local en el laboratorio. Selecciona el tipo de
ensayo en un selector desplegable, arrastra el escaneo JPEG de la minuta manuscrita,
y presiona procesar. N8N recibe la imagen vía webhook y la envía a Claude Sonnet
u Opus vía API de visión para extracción OCR. El sistema devuelve los datos como
tabla editable al frontend. El técnico revisa, corrige si es necesario, y confirma.
El sistema genera el Excel con la plantilla correcta ya rellenada, disponible para
descarga.

Este flujo es el diseño de partida. Lo que sigue documenta todos los problemas
que la triangulación identificó en él, cómo se resolvieron, y qué queda pendiente.

---

## PARTE 3 — PROBLEMAS IDENTIFICADOS Y CÓMO SE RESOLVIERON

Esta sección es el núcleo del documento. Cada problema está descrito con su
origen, su riesgo real, y la resolución validada por la triangulación.

---

### Problema 1 — La asunción de plantilla única (riesgo crítico)

**Origen**: DeepSeek, ronda 1, análisis de los Excel reales del laboratorio.

**El problema**: El diseño original asumía que existe "una plantilla" y que el
sistema simplemente vuelca los datos en ella. Al analizar los archivos reales,
DeepSeek descubrió que hay al menos tres variantes con lógicas de cálculo
fundamentalmente distintas.

La variante simple (granulometría 0-30 mm aprox.) tiene un solo rango de tamices
con pesos que se insertan directamente en la columna D del Foglio2. La variante
con sedimentación tiene tres rangos diferenciados, usa Foglio4 para cálculos de
cuarteo y Foglio3 para sedimentación, con referencias cruzadas complejas entre
hojas. La variante con cuarteo sin sedimentación tiene estructura similar a la
completa pero con Foglio3 vacío.

El riesgo real: en las variantes con cuarteo, los pesos NO se insertan en Foglio2
directamente — se insertan en Foglio4 (celdas B21:B28), y las fórmulas de Excel
escalan automáticamente hacia Foglio2. Si el sistema escribe en la ubicación
incorrecta, los porcentajes calculados son incorrectamente incorrectos de forma
silenciosa. El técnico vería números plausibles que son sistemáticamente falsos.

**Resolución**: Selector manual de tipo de prueba en la interfaz. El técnico elige
el tipo antes de subir la imagen. El frontend envía el parámetro `tipo_prueba` junto
con la imagen al webhook de N8N. N8N sabe exactamente qué plantilla base usar y
en qué celdas escribir cada valor.

**Por qué esta solución y no detección automática**: La detección automática requiere
que el sistema infiera el tipo de ensayo desde el contenido de la imagen o los
metadatos del escaneo, lo que introduce complejidad accidental alta y un nuevo punto
de fallo. El selector manual elimina la ambigüedad completamente. La pérdida de
"magia" (automatización total) se acepta a cambio de previsibilidad y fiabilidad.

**Escalabilidad de la solución**: Cuando GeoLabor quiera añadir nuevos tipos de
ensayo en una versión de pago, solo se añade una opción al selector y se mapea
la plantilla correspondiente. No se toca lógica central.

**Estado**: Decisión cerrada. Convergencia total de los tres modelos y Pablo.

---

### Problema 2 — La interpolación lineal para D10/D30/D60 (error técnico silencioso)

**Origen**: DeepSeek, ronda 1.

**El problema**: La interpolación para calcular D10, D30 y D60 (diámetros
representativos del suelo) debe ser logarítmica, no lineal. Los diámetros de tamices
siguen una progresión logarítmica por diseño normativo. Si el sistema usa
interpolación lineal, el error en suelos finos puede llegar al 20-30%, lo cual
es técnicamente inaceptable para un informe oficial.

La fórmula correcta es: `log(Dx) = log(d1) + (log(d2) - log(d1)) × (x - p1) / (p2 - p1)`
donde d1 y d2 son diámetros de tamices adyacentes, y p1 y p2 son los porcentajes
pasantes correspondientes.

**Resolución**: Implementar interpolación logarítmica en el script Python que
genera el Excel. Pablo explícitamente quiere implementar esta parte — valora
la precisión técnica del resultado como diferenciador.

**Estado**: Decisión cerrada. Pendiente implementación en el script Python.

---

### Problema 3 — JavaScript vs Python para la manipulación del Excel (tensión con ADR-101)

**Origen**: DeepSeek ronda 2 + Gemini ronda 1. La tensión con el ADR la identificó Claude.

**El problema**: El diseño del piloto requiere leer una plantilla Excel existente,
escribir valores en celdas específicas, y preservar fórmulas, formatos y gráficos.
JavaScript con `exceljs` tiene limitaciones documentadas para este caso: puede
corromper plantillas con gráficos complejos al modificarlas programáticamente.
Python con `openpyxl` es técnicamente superior para este trabajo.

Sin embargo, ADR-101 documenta que Python está fuera del stack hasta Fase 2.
Esa es una decisión consciente y documentada, no una limitación de conocimiento.
Introducir Python en un piloto gratuito sin una decisión explícita significaría
violar el ADR por inercia de la recomendación de las IAs, no por criterio propio.

**Resolución**: Python vía nodo Execute Command de N8N, documentado como deuda
técnica consciente con nota en ADR-101. No es una violación del ADR sino una
revisión justificada: la limitación técnica real de `exceljs` para preservar
gráficos en plantillas existentes justifica la excepción en este proyecto específico.

La implementación concreta para el piloto es un script Python invocado desde N8N
vía nodo Execute Command, pasando los datos confirmados como argumento JSON.
Un microservicio Flask separado es la arquitectura más limpia a largo plazo
(permite reemplazar la lógica de generación sin tocar N8N), pero añade complejidad
innecesaria para un piloto gratuito. Flask se evalúa en la primera versión de pago.

**Estado**: Decisión cerrada. Convergencia de DeepSeek y Gemini en Python.
Claude señaló la tensión con ADR-101. Todos coinciden en que la decisión debe
ser consciente y documentada como excepción justificada.

**Pendiente**: Nota en ADR-101 documentando la excepción con criterio explícito.

---

### Problema 4 — El triángulo DIN 18123 y la complejidad de generación programática

**Origen**: DeepSeek ronda 1.

**El problema**: Generar un gráfico ternario en Excel desde cero con código es
extremadamente complejo. Manipular series de dispersión con coordenadas calculadas
para diagramas triangulares requiere un nivel de control sobre los objetos de Excel
que las librerías disponibles (tanto `exceljs` como `openpyxl`) manejan con
dificultad significativa.

**Resolución**: Plantilla predefinida con scatter. El triángulo se dibuja una sola
vez manualmente en una plantilla Excel: el triángulo DIN 18123 como imagen de fondo
y un gráfico scatter configurado con una sola serie de un punto. Las coordenadas
X e Y del punto se calculan mediante transformación de coordenadas trilineales a
cartesianas y se almacenan en celdas ocultas que alimentan la serie del gráfico.
El script Python solo escribe los tres valores de porcentaje (grava, arena, finos)
en las celdas de entrada; el gráfico actualiza el punto automáticamente al abrir
el archivo en Excel Desktop.

**Detalle técnico crítico identificado por Gemini**: Excel no actualiza los gráficos
mientras el archivo es escrito programáticamente. Los actualiza al abrir el archivo.
Si el técnico abre el archivo en Google Drive preview u otro visor web, el gráfico
no se refrescará. La interfaz debe incluir una advertencia explícita: "Abrir con
Microsoft Excel Desktop para visualizar correctamente los gráficos."

**Por qué el triángulo entra en el piloto**: Es el "Wow Factor" del sistema.
Si el piloto solo entrega la tabla de Excel, el valor percibido es el de una
herramienta de transcripción. Si entrega el punto clasificado automáticamente en
el triángulo DIN, demuestra comprensión del dominio geotécnico y reemplaza algo
que el laboratorio actualmente hace con software externo (KORN.LAB). Esa es la
diferencia entre una herramienta útil y un sistema indispensable.

**Estado**: Decisión cerrada. Convergencia total de los tres modelos.

---

### Problema 5 — El error OCR plausible que pasa desapercibido (el riesgo más peligroso)

**Origen**: Claude, análisis de síntesis.

**El problema**: La pantalla de confirmación sola no es suficiente. El riesgo
no es el error catastrófico que cualquiera detecta — un peso de cuatro dígitos
leído como tres. El riesgo real es el error numéricamente plausible: un "37" leído
como "27", o un "127" leído como "172". A las 6pm después de una jornada de trabajo,
un técnico revisando visualmente puede pasar ese error sin detectarlo.

Una pantalla de confirmación que el técnico puede aprobar sin validación algorítmica
previa no es una validación real — es una falsa sensación de control.

**Resolución**: Validación en dos capas obligatorias y secuenciales, sintetizada
por Gemini como la solución superior a las propuestas individuales de Claude y
DeepSeek.

La primera capa es algorítmica: N8N suma todos los pesos parciales registrados y
los compara contra la massa netta secca total. Si la diferencia supera el 1%, el
sistema bloquea el flujo y lanza una alerta roja visible. El técnico no puede
generar el Excel hasta resolver la discrepancia. El algoritmo detecta lo que el
humano no ve.

La segunda capa es humana (HITL): la imagen original se muestra al lado de la
tabla editable. Los valores que Claude Vision extrajo con baja confianza se
resaltan en rojo. El humano detecta anomalías visuales y de contexto que el
algoritmo no puede. Ambas capas son complementarias, no alternativas.

La secuencia es inviolable: primero pasa el check algorítmico, luego el técnico
da el OK manual. Sin el OK del algoritmo, el botón de confirmar no está disponible.

**Punto abierto**: La massa netta secca es el valor de referencia para la
validación algorítmica. No está definido en el diseño actual si ese valor lo
ingresa el técnico manualmente en la pantalla de confirmación o si el OCR intenta
extraerlo del escaneo. Si el OCR lo extrae y lo extrae incorrectamente, la
validación tiene un punto de fallo en su propio dato de referencia. Esta decisión
de diseño debe resolverse en el mapeo técnico detallado.

**Estado**: Diseño de la solución cerrado. Punto de implementación abierto
(origen de la massa netta secca).

---

### Problema 6 — Validación de parámetros antes de consumir tokens (eficiencia de recursos)

**Origen**: Gemini, pregunta de validación condicional al final de la ronda 1.

**El problema**: Si el técnico sube una imagen pero olvida seleccionar el tipo de
prueba en el selector, el sistema no debe llamar a la API de visión de Claude.
Cada llamada consume tokens con costo real. Un error de usuario no debe tener
costo económico para el sistema.

**Resolución**: Defensa en dos capas siguiendo el principio de "nunca confíes
en el cliente". La primera capa está en el frontend: el botón de procesar permanece
deshabilitado hasta que el selector tenga una selección válida. La segunda capa
está en el primer nodo del webhook de N8N: un nodo IF verifica que `tipo_prueba`
no sea null. Si es null, el workflow responde inmediatamente con un JSON de error
y se detiene antes de invocar ninguna API externa. El frontend muestra un mensaje
específico y accionable.

Este patrón es arquitectónicamente equivalente al placeholder HMAC en la Capa 1
del sistema PA1: el primer nodo actúa como portero que valida que la request tiene
todo lo necesario antes de consumir recursos. Es el mismo principio aplicado a un
contexto diferente.

**Estado**: Decisión cerrada. Convergencia total.

---

### Problema 7 — La trazabilidad como requisito legal (el hallazgo más importante de la triangulación)

**Origen**: Gemini, ronda final.

**El problema**: Este fue el hallazgo más valioso de toda la triangulación porque
requiere pensar como abogado, no como ingeniero. En geotecnia, si hay un fallo
estructural en una obra, alguien preguntará qué decían los datos originales del
ensayo. Si el sistema procesa la imagen, el técnico confirma los datos, se genera
el Excel, y la imagen original no está almacenada y vinculada al Excel, se rompe
la cadena de custodia de la evidencia.

El Excel existe pero fue producido por un sistema automatizado que pudo haber
cometido un error. Sin la imagen original vinculada al Excel, no es posible
demostrar que el Excel es fiel a la minuta manuscrita.

**Resolución**: Almacén de evidencia con tres componentes integrados.

El primero es el almacenamiento de la imagen en el VPS en una carpeta
`/evidencias/ensayos/` con nomenclatura determinista:
`{ID_ensayo}_{timestamp}_{tipo_prueba}.jpg`. El ID es un UUID generado por N8N
al inicio del flujo, antes de cualquier procesamiento.

El segundo es el registro en PostgreSQL usando la tabla `eventos` (ya diseñada
para el sistema PA1, estructura reutilizable): `ID_ensayo | timestamp |
path_imagen | json_raw_ocr | json_confirmado_tecnico | id_tecnico | tipo_prueba`.
El `json_raw_ocr` guarda lo que Claude Vision extrajo antes de edición humana.
El `json_confirmado` guarda lo que el técnico aprobó. Esto permite reconstruir
en el futuro qué hizo la IA y qué corrigió el técnico, que son evidencias distintas.

El tercero es el vínculo en el Excel: una celda oculta en el archivo generado
contiene el `ID_ensayo`. Si alguien abre el Excel y necesita la imagen original,
tiene el ID para encontrarla.

**Por qué cambia la naturaleza del sistema**: Con el almacén de evidencia, el piloto
deja de ser "herramienta que genera Excel" y pasa a ser "sistema con trazabilidad
completa desde la minuta hasta el informe". Eso tiene valor comercial para cualquier
laboratorio que trabaje con certificaciones o que haya tenido que defender sus datos.

**Estado**: Decisión de diseño cerrada. Pendiente implementación y definición exacta
del schema de la tabla en PostgreSQL para este proyecto.

---

### Problema 8 — La protección del scope contra expansión no remunerada

**Origen**: DeepSeek ronda 1. Reforzado por Claude en la síntesis.

**El problema**: El scope fue acordado verbalmente. En trabajo gratuito, la memoria
de lo acordado puede divergir entre las partes con el tiempo. "Pensaba que la
sedimentación también entraba" es una frase que destruye proyectos piloto. No es
desconfianza en el contacto de GeoLabor — es arquitectura de protección del
proyecto para ambas partes.

**Resolución**: Documento de scope escrito antes de comenzar la construcción.
Un email de tres líneas es suficiente: qué incluye el piloto gratuito, qué queda
explícitamente fuera, y qué constituiría expansión de scope y cobraría. Cuando
ambas partes tienen el mismo texto por escrito, no hay espacio para la divergencia
de memoria.

**Estado**: Pendiente. Nada de código hasta que esto esté enviado y confirmado.

---

# Problema 9 — Ausencia de estándar de documentación reconocible externamente

**Origen**: Reflexión de Pablo durante el cierre de la triangulación,
sesión 16 de abril 2026.

---

## El problema

El sistema de documentación construido durante PA1 (ADRs, diagrama de sistema,
GDPR data mapping, schema, README, runbook) funciona correctamente pero es un
estándar informal e invisible para alguien externo. Un cliente, un colaborador
o un empleador no sabe cómo leerlo porque el marco de referencia no es reconocido
en la industria. Cada arquitecto que construye su propio sistema de documentación
crea un dialecto propio que solo él habla con fluidez.

Adicionalmente, al analizar lo que Pablo ya producía, se identificó que estaba
haciendo Arc42 de forma fragmentada sin saberlo: los ADRs cubren las decisiones
de diseño, el diagrama de sistema cubre las vistas de contenedores, el GDPR data
mapping cubre los conceptos transversales. La pregunta no era si adoptar un
estándar sino si formalizar lo que ya existe dentro de un marco reconocible.

---

## La resolución

Adopción de **Arc42** como estructura de documentación de arquitectura y
**C4 Model** como lenguaje de diagramación estándar para todos los proyectos
desde GeoLabor en adelante. Decisión formalizada en ADR-104.

Arc42 y C4 son complementarios con el sistema de ADRs existente, no alternativos.
Los ADRs son el registro de razonamiento de cada decisión individual y viven
dentro de la sección de Decisiones de Diseño de Arc42. C4 proporciona el lenguaje
visual estándar para los diagramas dentro de Arc42. Los tres juntos forman el
sistema de documentación completo que Pablo entrega como parte de cada proyecto.

Para proyectos piloto se usan las secciones Arc42 proporcionales al alcance.
Para proyectos de producción con clientes reales, Arc42 completo es parte del
entregable y del precio.

GeoLabor es el primer proyecto documentado con este estándar desde el inicio.
PA1 se migra progresivamente cuando llegue a producción en el Bloque 6,
sin reescritura completa.

---

**Estado**: Decisión cerrada. Formalizada en ADR-104.

## PARTE 4 — DECISIONES DE DISEÑO CONSOLIDADAS

Esta sección resume el estado actual del diseño del sistema, integrando todas
las resoluciones de la sección anterior. Es la referencia de qué se construye.

El flujo completo del sistema piloto es el siguiente. El técnico abre la interfaz
web local, selecciona el tipo de ensayo en el selector desplegable, y arrastra el
escaneo. El frontend valida que el selector tiene una selección antes de habilitar
el botón de procesar. Al procesar, envía al webhook N8N un JSON con `tipo_prueba`
e imagen en base64 (o como multipart). El primer nodo de N8N verifica que
`tipo_prueba` no sea null — si es null, responde con error y para. N8N genera un
UUID como `ID_ensayo` y almacena la imagen en `/evidencias/ensayos/`. N8N envía
la imagen a Claude Sonnet u Opus vía API de visión con un prompt estructurado que
solicita los pesos por tamiz en JSON. N8N registra en PostgreSQL el evento
`ocr_ejecutado` con el JSON raw. N8N devuelve el JSON al frontend. El frontend
renderiza la tabla editable con los valores extraídos, resaltando en rojo los de
baja confianza, y muestra la imagen original al lado. El técnico ingresa la massa
netta secca (o la confirma si el OCR la extrajo). El frontend calcula la diferencia
entre la suma de pesos y la massa netta secca. Si la diferencia supera el 1%, muestra
alerta roja y bloquea el botón de confirmar. Si la diferencia es aceptable, el técnico
da el OK. N8N recibe la confirmación con los datos validados, ejecuta el script Python
vía nodo Execute Command pasando el JSON de datos confirmados y el parámetro
`tipo_prueba`. El script Python copia la plantilla base correcta, escribe los valores
en las celdas correspondientes (Foglio2 para la variante simple, Foglio4 para las
variantes con cuarteo), calcula la interpolación logarítmica para D10/D30/D60, escribe
los tres porcentajes para el triángulo, y guarda el archivo con el `ID_ensayo` en
una celda oculta. N8N registra el evento `excel_generado` en PostgreSQL. N8N devuelve
el archivo al frontend para descarga. La interfaz muestra la advertencia de abrir
en Excel Desktop.

**Stack del sistema piloto**: N8N (orquestación), Python con openpyxl (generación
Excel), PostgreSQL (trazabilidad), interfaz HTML/JS local (frontend), Claude
Sonnet/Opus API (OCR/visión), Docker (containerización), VPS Hetzner (hosting).

**Primera entrega del piloto**: Variante simple únicamente (granulometría 0-30 mm,
un solo rango, pesos en Foglio2). Las variantes con cuarteo y sedimentación son
la versión 2 o primera expansión de pago.

---

## PARTE 5 — DECISIONES PENDIENTES Y PREGUNTAS ABIERTAS

Estas son las decisiones que no están cerradas y que necesitan resolución durante
o antes de la construcción.

**Decisión pendiente 1 — Origen de la massa netta secca**: ¿El técnico la ingresa
siempre manualmente en la pantalla de confirmación, o el OCR intenta extraerla del
escaneo? Si el OCR la extrae y falla en ese dato, la validación algorítmica tiene
un punto de fallo en su referencia. La opción más segura es ingreso manual siempre,
pero esto añade un paso al técnico. Esta decisión afecta el diseño de la pantalla
de confirmación.

**Decisión pendiente 2 — Execute Command vs microservicio Flask**: Para el piloto,
Execute Command es la opción correcta por simplicidad. Cuando el sistema crezca a
múltiples variantes con lógicas distintas en producción, un microservicio Flask
separado tiene arquitectura más limpia (permite versionar y reemplazar la lógica
de generación independientemente de N8N). ¿En qué punto del roadmap se justifica
esa migración?

**Decisión pendiente 3 — Múltiples muestras en una hoja**: La triangulación
identificó que una minuta puede contener varias muestras en columnas separadas.
El diseño actual no especifica cómo maneja el sistema este caso. ¿La interfaz
pregunta si generar un Excel por muestra o uno consolidado? ¿El OCR devuelve un
JSON con múltiples arrays de pesos? Esto no bloquea la primera entrega (variante
simple con una muestra), pero necesita decisión antes de la segunda entrega.

**Decisión pendiente 4 — Schema exacto de la tabla PostgreSQL para evidencias**:
La tabla `eventos` del sistema PA1 tiene una estructura con JSONB en el campo
`detalle`. Para GeoLabor se necesita extender o crear una tabla específica que
registre `json_raw_ocr` y `json_confirmado` como campos separados (para poder
reconstruir la diferencia entre lo que extrajo la IA y lo que confirmó el técnico).
La decisión es si reutilizar la estructura de `eventos` de PA1 con un `tipo_evento`
específico para este sistema, o crear una tabla propia para el piloto.

**Decisión pendiente 5 — Coordenadas del triángulo DIN 18123**: La conversión de
coordenadas trilineales (grava%, arena%, finos%) a cartesianas (X, Y) para el
punto en el scatter requiere una fórmula específica que depende de la geometría
exacta del triángulo DIN 18123 en la plantilla. Esta fórmula no está definida
todavía. Es necesario construir la plantilla Excel una vez y medir las coordenadas
de los vértices para calibrar la transformación.

---

## PARTE 6 — DOCUMENTOS A PRODUCIR ANTES DE CONSTRUCCIÓN

Estos cinco documentos deben existir antes de que se escriba una sola línea de
código o se configure un nodo en N8N.

El primero es el email de scope al contacto de GeoLabor, con el texto explícito
de qué incluye el piloto gratuito, qué queda fuera, y qué constituye expansión
de scope de pago. Tres líneas son suficientes.

El segundo es el ADR-201, que documenta todas las decisiones de arquitectura
específicas de este proyecto: selector manual, Python vía Execute Command como
excepción justificada, triángulo predefinido con scatter, validación en dos capas,
almacén de evidencia, variante simple como primera entrega. Este ADR es el primero
de la serie 200 (proyectos específicos) en el sistema de ADRs de Pablo.

El tercero es la nota en ADR-101 documentando la revisión de la decisión sobre
Python: por qué la limitación técnica real de `exceljs` para preservar gráficos
justifica introducir Python en este proyecto, y bajo qué condiciones aplica esta
excepción.

El cuarto es el mapeo técnico detallado: una tabla que especifica para la variante
simple cuál es la plantilla base, qué celdas reciben cada valor extraído del OCR
(columna D de Foglio2, filas exactas por diámetro de tamiz), y dónde se escriben
los tres porcentajes del triángulo. Este documento es la referencia que usa el
script Python.

El quinto es el diagrama de flujo N8N completo, con cada nodo especificado, sus
condiciones de routing, y los puntos de validación (check de tipo_prueba,
validación de coherencia numérica, confirmación del técnico, llamada al script
Python).

---

## PARTE 7 — CONTEXTO DE LA TRIANGULACIÓN

Esta sección documenta qué aportó cada IA para que en futuras sesiones se entienda
la proveniencia de cada decisión.

**DeepSeek** aportó los hallazgos técnicos más importantes: la existencia de tres
variantes de plantilla con el problema crítico de Foglio4, la necesidad de
interpolación logarítmica para D10/D30/D60, y la revisión meta del documento de
síntesis que Pablo preparó para Gemini. Es el modelo con mejor análisis técnico
granular de artefactos reales.

**Gemini** aportó la validación estratégica del selector manual, el criterio sobre
Python como deuda técnica consciente (con el error de citar ADR-001 en lugar de
ADR-101, detalle corregido), el detalle técnico crítico sobre Excel Desktop para
la actualización de gráficos, el diseño de la jerarquía de validación OCR en dos
capas, el argumento estratégico del triángulo como "Wow Factor", y el hallazgo
más importante de la fase final: la trazabilidad como requisito legal en un
entorno regulado. También planteó la pregunta filosófica más valiosa de toda la
triangulación sobre si un arquitecto implementa soluciones que no puede explicar
al cliente.

**Claude** aportó la identificación de la tensión con ADR-101 en la recomendación
de Python, la identificación del error OCR plausible (no el catastrófico sino el
numéricamente creíble) como el riesgo más peligroso, la estructura de síntesis
que permitió que los tres modelos hablaran sobre el mismo diseño, y el análisis
de las respuestas de Gemini y DeepSeek para separar hallazgos reales de ruido.

**La triangulación como metodología**: Pablo proporciona contexto completo a cada
IA de forma manual con archivos. No usa MCP para triangulación automatizada porque
las APIs externas reciben menos contexto que el que Pablo da manualmente — eso
sería un downgrade disfrazado de automatización (decisión documentada en ADR-007).

---

## PARTE 8 — PRINCIPIOS ARQUITECTÓNICOS QUE GUÍAN ESTE PROYECTO

Estos principios provienen del ADR-000 de Pablo y aplican a todas las decisiones
del piloto.

La decisión de usar un selector manual en lugar de detección automática aplica
el principio de simplicidad: ante dos soluciones equivalentes en resultado, la
más simple es la correcta. La detección automática no es más valiosa para el
usuario — solo es más sofisticada técnicamente, lo que es complejidad accidental.

La decisión de incluir el almacén de evidencia aplica compliance desde el diseño:
la trazabilidad no es una feature que se añade al final, es un requisito de negocio
en un laboratorio técnico regulado.

La decisión de documentar Python como excepción en ADR-101 en lugar de ignorar
el ADR aplica la deuda técnica consciente: el atajo tiene un costo futuro conocido
(evaluación de Flask para la versión de producción), está documentado, y su fecha
de revisión está definida.

La decisión de usar el selector para habilitar escalabilidad aplica anti lock-in:
el sistema no está acoplado a ninguna lógica de detección propietaria. Añadir un
nuevo tipo de ensayo es añadir una opción al selector y copiar una plantilla.

---

**Documento preparado por**: Pablo Cuevas + Claude (Anthropic)  
**Fecha**: 16 de abril 2026  
**Uso**: Inicio de nueva sesión de trabajo. Proporcionar este documento completo
como contexto antes de cualquier pregunta técnica sobre el piloto GeoLabor.  
**No mezclar con**: Sistema PA1 (leads: pablocuevas.it) — proyectos separados con contactos
y ADRs distintos.
