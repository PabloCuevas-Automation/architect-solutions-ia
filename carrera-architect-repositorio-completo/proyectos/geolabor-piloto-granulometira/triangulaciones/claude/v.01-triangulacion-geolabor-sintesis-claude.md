# Triangulación GeoLabor Piloto — Síntesis Claude
## Para revisión por Gemini

**Fecha**: 16 de abril 2026
**Preparado por**: Pablo Cuevas (AI Solutions Architect en formación)
**Contexto**: Triangulación multi-LLM para validar el diseño de un piloto de automatización para GeoLabor s.a.s.
**IAs consultadas hasta ahora**: DeepSeek (x2 rondas) + Claude (análisis y síntesis)
**Siguiente**: Esta síntesis va a Gemini para opinión independiente

---

## 1. Quién es Pablo y por qué importa el contexto

Pablo es un AI Solutions Architect en formación basado en Rovereto, Trento, Italia. Su stack actual es N8N como motor de automatización, PostgreSQL como capa de persistencia, JavaScript como lenguaje de lógica, y Docker para containerización. Trabaja con un principio arquitectónico documentado: **Python está fuera del stack hasta Fase 2** (ADR-101 — decisión consciente y documentada). Su diferenciador de mercado es GDPR by design + documentación arquitectónica como entregable para el cliente, no solo workflows funcionales. GeoLabor es su laboratorio geotécnico actual, donde trabaja y donde está desarrollando este piloto como prueba de concepto gratuita.

---

## 2. El problema de negocio

GeoLabor realiza ensayos granulométricos de suelos. El proceso actual tiene dos pasos manuales costosos en tiempo:

Primero, el técnico toma datos del ensayo anotando manualmente los pesos retenidos en cada tamiz en una minuta impresa (hoja de papel). La escritura es a mano, en italiano, con números decimales. Una minuta puede contener datos de varias muestras a la vez.

Segundo, el técnico transcribe esos datos desde el papel a una plantilla Excel predefinida. El Excel tiene fórmulas que calculan automáticamente los porcentajes de retención y paso, genera la curva granulométrica y proporciona datos para la clasificación del suelo. Este proceso de transcripción manual consume tiempo y es fuente de errores.

El dolor: tiempo repetitivo + riesgo de error humano en datos que luego van a informes técnicos oficiales.

---

## 3. La solución propuesta (diseño original de Pablo)

El flujo diseñado originalmente es el siguiente. El técnico escanea la minuta manuscrita y la sube a una interfaz web local. N8N recibe la imagen vía webhook, la envía a una API de visión (Claude Sonnet/Opus) para extracción OCR de los datos de la tabla de tamices, y devuelve los datos al frontend como tabla editable. El técnico revisa y corrige los datos en la pantalla de confirmación, aprueba, y el sistema genera automáticamente la plantilla Excel correcta ya rellena y lista para descargar.

El scope del piloto, acordado verbalmente con GeoLabor, es granulometría estándar más el triángulo de clasificación DIN 18123. Todo lo demás (límites de Atterberg, clasificación USCS completa, etc.) está explícitamente fuera del piloto gratuito y sería proyecto de pago.

---

## 4. Lo que DeepSeek descubrió analizando los Excel reales

Esta es la contribución más valiosa de la primera ronda con DeepSeek. Al analizar los archivos Excel reales del laboratorio, encontró algo que el diseño original no contemplaba: **no existe una plantilla única**. Hay al menos tres variantes con lógicas de cálculo fundamentalmente distintas.

La primera variante es la granulometría simple (rango 0-30 mm aprox.), que usa un solo rango de tamices con pesos en la columna D del Foglio2. La segunda es la granulometría con sedimentación, que tiene tres rangos diferenciados (gruesa, fina, sedimentación), usa Foglio4 para cálculos de cuarteo y Foglio3 para sedimentación, con referencias cruzadas complejas entre hojas. La tercera es la granulometría con cuarteo pero sin sedimentación, estructura similar a la completa pero con Foglio3 vacío.

La implicación crítica de este hallazgo es la siguiente: en las variantes con cuarteo, los pesos que ingresan al Excel **no se insertan en Foglio2 directamente**. Se insertan en Foglio4 (celdas B21:B28), y las fórmulas de Excel hacen el escalado automático hacia Foglio2. Si el sistema escribe los pesos en la ubicación equivocada, los porcentajes calculados serán incorrectos, y el cliente usará esos datos para clasificar suelos sin saber que hay un error sistemático.

DeepSeek también identificó que la interpolación para D10/D30/D60 debe ser **logarítmica, no lineal**, porque los diámetros de tamices siguen una progresión logarítmica por diseño normativo. El error de usar interpolación lineal puede llegar al 20-30% en suelos finos, lo que es técnicamente inaceptable para un informe oficial.

---

## 5. Las respuestas de Pablo a DeepSeek (ronda 1)

Pablo respondió con varias decisiones de diseño que resuelven problemas concretos de forma elegante.

Para el problema de la detección de variante de plantilla, Pablo propone eliminar la detección automática y reemplazarla por un **selector manual en la interfaz**. El técnico elige el tipo de prueba antes de subir la imagen, el frontend envía ese parámetro junto con la imagen al webhook de N8N, y N8N sabe exactamente qué plantilla base usar. Esta solución es arquitectónicamente sólida porque es escalable (añadir nuevos tipos de prueba solo requiere añadir una opción al selector y una plantilla), es robusta (cero ambigüedad en el tipo), y es correcta desde GDPR (no extrae metadatos del escaneo que podrían incluir PII del cliente del laboratorio).

Para el triángulo DIN 18123, Pablo propone predefinir la plantilla una sola vez con el gráfico de dispersión ya configurado. El script solo escribe los tres valores de porcentaje (grava, arena, finos) en las celdas de entrada, y el gráfico dibuja el punto automáticamente. No es necesario generar el gráfico programáticamente en cada ejecución.

Para el OCR, Pablo reconoce que si falla de forma sistemática el técnico simplemente vuelve al proceso manual, lo que en la práctica no es una pérdida real. La apuesta es que los fallos serán minoritarios y atribuibles a la calidad de la conexión con la API, no a la capacidad del modelo.

Para los metadatos (committente, località, etc.), Pablo decide no extraerlos del escaneo y dejar que el técnico los ingrese manualmente si los necesita, por coherencia con los principios GDPR.

---

## 6. Lo que DeepSeek propuso en la ronda 2

La segunda ronda validó las decisiones de Pablo y añadió detalle técnico de implementación. Confirmó que el selector manual es la mejor solución para la detección de variantes. Validó la estrategia del triángulo predefinido. Proporcionó pseudocódigo de interpolación logarítmica en Python. Recomendó usar Python con `openpyxl` para la manipulación del Excel, invocado desde N8N vía nodo "Execute Command" o como microservicio Flask.

---

## 7. El análisis de Claude sobre lo que DeepSeek propone

DeepSeek hace un análisis técnico sólido y granular. El valor real de sus dos rondas está en el hallazgo sobre las variantes de plantilla, la advertencia sobre Foglio4, y la precisión sobre interpolación logarítmica. Esos tres puntos son contribuciones genuinas que cambian decisiones de diseño.

Sin embargo, hay una landmina técnica que DeepSeek introduce sin notarla: toda su propuesta de implementación está construida sobre Python. DeepSeek no sabe que Pablo tiene un ADR documentado que pospone Python a Fase 2. Para DeepSeek, Python con `openpyxl` es la solución obvia porque técnicamente lo es. Para Pablo, adoptarla sin decisión consciente significa empezar un proyecto de trabajo gratuito con una tecnología fuera de su stack, sin experiencia real en ella, añadiendo complejidad que no estaba en el plan.

La alternativa dentro del stack actual de Pablo es `exceljs`, una librería JavaScript que permite leer una plantilla Excel existente, escribir valores en celdas específicas y guardar el archivo. Si la plantilla ya tiene las fórmulas configuradas (como los Excel de GeoLabor ya las tienen), el gráfico de curva granulométrica se actualiza automáticamente al abrir el archivo porque referencia esas celdas. La interpolación logarítmica también se puede implementar en JavaScript sin problema. La limitación de `exceljs` frente a `openpyxl` es que la manipulación de gráficos de dispersión más complejos es menos madura, pero para el caso del triángulo predefinido con un punto scatter, debería ser suficiente.

Esto no significa que Python sea una mala decisión. Significa que es una **decisión Type 1** (difícil de revertir una vez que el proyecto está construido) que merece ser tomada explícitamente, no absorbida por inercia de la recomendación de otra IA.

Hay una segunda tensión que ninguna IA ha abordado con suficiente profundidad: el riesgo del error OCR plausible que pasa desapercibido. DeepSeek habla de la pantalla de confirmación como mitigación, lo cual es correcto. Pero no hace la pregunta de segundo orden: ¿tiene el técnico suficiente contexto en esa pantalla para detectar un "27" donde debería decir "37"? Un error catastrófico (peso de 4 dígitos leído como 3) lo detecta cualquiera. Un error plausible (un dígito cambiado en un número de dos cifras) puede pasar a las 6pm después de una jornada larga. La mitigación correcta es la validación de coherencia numérica: la suma de pesos registrados vs la masa neta seca declarada en la minuta. Si la diferencia supera un umbral (por ejemplo, 1%), el sistema alerta al técnico antes de que confirme. Sin esta validación, la pantalla de confirmación da una falsa sensación de control.

Finalmente, hay una deuda de proceso que ninguna IA puede resolver: Pablo menciona que el scope del piloto "ya lo ha hablado con GeoLabor", pero la conversación fue verbal. Para que esa protección sea real, necesita estar documentada por escrito, aunque sea en un email de tres líneas donde ambas partes acuerden explícitamente qué entra y qué no entra en el piloto gratuito. No es desconfianza en Angelo — es arquitectura de protección del proyecto para ambas partes.

---

## 8. Decisiones abiertas que necesitan resolución antes de implementar

Estas son las decisiones que Pablo tiene que tomar de forma explícita antes de escribir una sola línea de código. No son preguntas técnicas — son decisiones de arquitectura con consecuencias reales.

La primera es la decisión sobre el lenguaje para la manipulación del Excel. ¿Se usa JavaScript con `exceljs` (dentro del stack actual, menor complejidad para un piloto gratuito) o se introduce Python con `openpyxl` (más capaz, pero fuera del stack y requiere una revisión consciente de ADR-101)? Si se elige Python, ¿cómo se invoca desde N8N — nodo Execute Command o microservicio Flask — y cuál es el criterio para elegir entre las dos?

La segunda es la decisión sobre el scope del triángulo DIN 18123. ¿Entra en el piloto gratuito o se deja para la versión de pago? Pablo indicó que quiere incluirlo, pero DeepSeek señaló que la generación programática del triángulo es extremadamente compleja. La estrategia del triángulo predefinido resuelve esa complejidad, pero necesita ser validada técnicamente: ¿`exceljs` o `openpyxl` pueden escribir los tres valores de porcentaje en las celdas de entrada del scatter predefinido y preservar el gráfico correctamente?

La tercera es la decisión sobre la validación de coherencia numérica. ¿Se implementa la verificación suma-de-pesos vs masa-neta-seca en la pantalla de confirmación, o se deja esa validación al criterio visual del técnico?

La cuarta es la documentación del scope por escrito con GeoLabor antes de empezar a construir.

---

## 9. Preguntas para Gemini

Estas son las preguntas que Pablo quiere que Gemini analice con toda la información anterior como contexto.

**Pregunta 1 — La decisión de lenguaje (la más importante):** Dado que el stack actual de Pablo es JavaScript y que Python está documentado como fuera del scope de Fase 1 (por decisión arquitectónica consciente, no por ignorancia), ¿qué recomiendas para la manipulación del Excel en este piloto? ¿Es `exceljs` suficiente para escribir valores en celdas de una plantilla Excel existente que ya tiene fórmulas y un gráfico de dispersión predefinido, y que la curva granulométrica y el punto del triángulo se actualicen correctamente al abrir el archivo? ¿O las limitaciones de `exceljs` son tan significativas que justifican introducir Python ahora?

**Pregunta 2 — La arquitectura del flujo completo:** El flujo propuesto es: interfaz web local (HTML/JS) → el técnico elige tipo de prueba + sube imagen → webhook N8N → API visión Claude → devuelve JSON con pesos → frontend muestra tabla editable → técnico confirma → N8N escribe Excel a partir de plantilla base → devuelve archivo para descarga. ¿Ves algún punto de fragilidad sistémica en este flujo que no haya sido identificado? ¿Cambiarías el orden de algún paso o añadirías alguna capa?

**Pregunta 3 — La validación de coherencia numérica:** ¿Tiene sentido técnico implementar una validación que compare la suma de los pesos registrados por OCR contra la masa neta seca que el técnico declara manualmente en la pantalla de confirmación, con alerta si la diferencia supera el 1%? ¿Es esto estándar en sistemas de procesamiento de datos de laboratorio, o hay una validación más apropiada para este contexto?

**Pregunta 4 — El triángulo DIN 18123 con plantilla predefinida:** La estrategia propuesta es crear una vez una plantilla Excel que ya tenga el triángulo dibujado como imagen de fondo y un gráfico scatter configurado con una sola serie de un punto. El script solo escribe los tres valores de porcentaje (grava, arena, finos) en celdas de entrada, y el punto aparece en la posición correcta del triángulo automáticamente. ¿Es este enfoque técnicamente robusto? ¿Hay alguna limitación en cómo Excel maneja los gráficos scatter cuando se modifican las celdas fuente programáticamente (sin abrir el archivo) que pueda causar que el gráfico no se actualice correctamente?

**Pregunta 5 — El riesgo del error OCR plausible:** Claude propone una validación de coherencia (suma de pesos vs masa seca) para mitigar errores que el técnico no detecta visualmente. DeepSeek propone mostrar la imagen al lado de la tabla editable para facilitar la revisión. ¿Cuál de estas dos mitigaciones es más efectiva para el tipo de error más peligroso — el error numéricamente plausible que pasa a las 6pm? ¿O ambas son necesarias y complementarias?

**Pregunta 6 — La decisión de scope del triángulo:** ¿Tiene sentido incluir el triángulo DIN 18123 en un piloto gratuito, dado que añade complejidad técnica (aunque sea resuelta con plantilla predefinida) y que el laboratorio actualmente usa KORN.LAB para esa parte? ¿O es estratégicamente correcto incluirlo porque es el punto de mayor diferenciación del sistema frente al proceso manual actual?

---

*Documento preparado para triangulación. Contexto adicional disponible: diagramas de sistema, schema PostgreSQL, GDPR data mapping, ADRs consolidadas.*
