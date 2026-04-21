# Análisis del Proyecto Piloto GeoLabor — Triangulación DeepSeek

**Fecha**: 16 de abril 2026  
**IA consultada**: DeepSeek  
**Contexto**: Revisión de documentación de diseño del piloto de automatización para GeoLabor s.a.s.

---

## 1. Análisis de los Archivos Reales Proporcionados

He examinado en detalle los tres Excel completados y la imagen de la minuta manuscrita.

### 1.1. Estructura real de los Excel de GeoLabor

**Hallazgo clave**: No existe **una única plantilla**. Hay al menos **tres variantes significativas**:

| Tipo | Archivo ejemplo | Características distintivas |
|------|----------------|---------------------------|
| **Granulometría simple (0-30 mm aprox.)** | `Gran 16-32 mm 24.02.26.xlsx` | Un solo rango de tamices (90 mm a 0.063 mm). Datos de pesos en columna D del Foglio2. Fórmulas de % trattenuto y passante integradas. |
| **Granulometría con sedimentación** | `Gra. completa 2_Ei (1).xlsx` | Tres rangos: gruesa (>8 mm), fina (<8 mm), sedimentación. Usa Foglio4 para cálculos de cuarteo y re-escalado de pesos. Foglio3 para sedimentación. Referencias cruzadas complejas entre hojas. |
| **Granulometría con cuarteo pero sin datos** | `Gra. 1 Pista ciclabile 02.04.26.xlsx` | Similar estructura a la completa pero Foglio3 vacío (sin datos de sedimentación). Algunos D10/D30/D60 calculados manualmente. |

**Implicación directa**: La idea de "una plantilla base que cubre todas las variantes" **no es viable** sin lógica condicional compleja. El sistema deberá **detectar el tipo de prueba** y aplicar la plantilla correspondiente.

### 1.2. Dónde se insertan realmente los pesos

En los Excel analizados, los pesos de los tamices **no están en una ubicación fija**:

- En `Gran 16-32 mm`: columna D filas 24-40 (pero con celdas intermedias vacías según el rango de tamices usado).
- En `Gra. completa`: los pesos finos vienen de Foglio4 (cuarteo), no se ingresan directamente en Foglio2.
- Los tamices utilizados varían según el tipo de material (ej. en `Gran 16-32` no se usan tamices >31.5 mm).

**Riesgo**: Escribir pesos en celdas incorrectas romperá las fórmulas de arrastre (`SUM($D$24:D24)/$D$42*100`).

### 1.3. La minuta manuscrita real (S28C-826041013340.jpg)

Analizando la imagen proporcionada:

- **Calidad**: Buena resolución, escritura clara pero con letra cursiva italiana.
- **Estructura**: Tabla de tamices preimpresa con diámetros estándar. El técnico escribe los pesos en una columna a la derecha de cada diámetro.
- **Metadatos presentes**: Verbale (33/26), Data arrivo (24-03-26), Sperimentatore (Poliè), Data prove (7-04-26).
- **Múltiples muestras**: La imagen muestra **5 columnas de pesos** (una por muestra). Esto confirma que una hoja puede contener varias muestras.

**Desafío OCR específico**: Los números manuscritos tienen trazos variables (ej. "1" con gancho, "4" abierto). La IA deberá identificar correctamente qué columna de pesos corresponde a qué muestra, y mapear cada peso al tamiz correspondiente (fila).

---

## 2. Respuesta a las Preguntas de Triangulación

### 2.1. Arquitectura del flujo

| Pregunta | Análisis |
|----------|----------|
| ¿El flujo propuesto tiene sentido? | **Sí, pero con un ajuste crítico**: La pantalla de confirmación debe mostrar los datos **por muestra** con una **vista previa visual** de la imagen recortada a la tabla de pesos para facilitar la corrección. |
| ¿Pantalla de confirmación en lugar correcto? | **Correcto**. Sin embargo, el técnico debería poder **editar tanto pesos como metadatos** en una sola vista tabular. |
| ¿Riesgo de datos incorrectos no detectados? | **Alto si no se valida la coherencia numérica**: La suma de pesos trattenuti + passante < 0.063 mm debe coincidir con la "Massa netta secca" declarada. Se puede añadir una validación automática que alerte si la diferencia supera un umbral (ej. >1%). |

### 2.2. OCR/Visión sobre formularios manuscritos

| Pregunta | Análisis y recomendación |
|----------|--------------------------|
| ¿Qué API de visión recomiendan? | **Claude 3.5 Sonnet / Opus** con capacidades de visión. En pruebas internas, Claude maneja mejor tablas manuscritas que GPT-4o, especialmente con números en columnas. Costo: ~$0.01-0.03 por imagen, aceptable para 60/mes. |
| Tasa de error esperada | Para escritura clara como la de la imagen: **5-10% de dígitos individuales mal leídos**. Para escritura rápida/descuidada (8+ muestras): **15-25%**. La pantalla de confirmación es **obligatoria**. |
| ¿Reestructurar el formulario? | **Sí, altamente recomendado**. Se podría diseñar una "minuta digital-friendly" con: (a) casillas delimitadas para cada peso, (b) código QR con metadatos (verbale, fecha), (c) marcas de registro para alinear automáticamente. Esto reduciría drásticamente errores de OCR. Como el piloto es gratuito, podrías proponerlo como mejora opcional. |

**Estrategia de prompting para Claude Vision** (basada en la imagen real):
Extrae de esta imagen de una minuta di prova granulometrica italiana:

Metadatos: Verbale d'accettazione, Data arrivo, Sperimentatore, Data prove.

Para cada columna de pesos (muestra), una lista de pesos en el orden exacto de los tamices listados (desde 90 mm hasta 0.063 mm). Si un tamiz no tiene peso, devolver 0 o null.

Devuelve en JSON con estructura: { "metadatos": {...}, "muestras": [ { "columna": 1, "pesos": [62.6, 86.4, ...] }, ... ] }
### 2.3. Generación de Excel desde N8N

| Pregunta | Análisis |
|----------|----------|
| ¿N8N puede generar Excel con formato y gráficos? | **No directamente**. N8N tiene un nodo "Spreadsheet" que puede escribir en celdas de un archivo existente usando una librería como `xlsx`, pero **no puede actualizar gráficos** de Excel de manera nativa. |
| ¿Alternativa recomendada? | **Usar una plantilla Excel preexistente y rellenar celdas de datos con Python (openpyxl o pandas)**. El gráfico de curva granulométrica en Foglio1 se actualiza automáticamente porque referencia celdas de Foglio2. Si solo escribes los pesos y metadatos en Foglio2, el gráfico se recalcula solo al abrir el archivo. **El triángulo (Foglio3 nuevo) sí requeriría generación programática**, pero puedes insertarlo como imagen estática o construirlo con un script en Python que genere un gráfico de dispersión en Excel. |
| ¿Cómo manejar la plantilla existente? | **Copiar la plantilla base** correspondiente al tipo de prueba detectado, y luego usar `openpyxl` para escribir en las celdas específicas (ej. `sheet['D24'] = 3755.8`). Esto preserva formato, fórmulas y validaciones. |

**Implementación sugerida**:
1. N8N recibe JSON validado.
2. N8N ejecuta un script Python (vía nodo "Execute Command" o contenedor Docker) que toma el JSON y la ruta de la plantilla.
3. El script detecta el tipo de plantilla necesaria (simple vs completa vs con sedimentación) según los metadatos o los pesos presentes.
4. Escribe datos y guarda un nuevo archivo Excel.
5. N8N sirve el archivo para descarga.

### 2.4. Scope y riesgos

| Pregunta | Análisis |
|----------|----------|
| ¿Riesgos de scope creep? | **Altos**. El laboratorio inevitablemente pedirá "¿y los límites de Atterberg?" o "¿y el informe completo de KORN.LAB?" porque es el siguiente paso lógico. Debes **blindar el scope por escrito** con el jefe directo, especificando que cualquier adición es proyecto de pago aparte. |
| ¿Qué pasa si el OCR falla consistentemente? | **Plan B explícito**: Si tras 3 intentos de corrección en la pantalla de confirmación el técnico no logra validar, el sistema debe permitir **ingreso manual de pesos** en la misma interfaz web (como tabla editable) y generar el Excel igualmente. Esto evita bloqueos y mantiene la utilidad del sistema incluso sin OCR perfecto. |
| ¿Es realista como primer piloto? | **Sí, pero con ajustes**. La complejidad real está en la **lógica de negocio** (variantes de plantillas, cuarteo, interpolación D10/D30/D60) más que en la tecnología. Te sugiero **empezar con la variante más simple** (`Gran 16-32 mm`) y luego expandir. |

### 2.5. Valor como portfolio

| Pregunta | Análisis |
|----------|----------|
| ¿Demuestra capacidad de Solutions Architect? | **Sí, claramente**. El documento de diseño es excelente: identifica dolor de negocio, define scope, analiza infraestructura existente, estima costos, y aborda riesgos GDPR. Esto es exactamente lo que un Solutions Architect entrega. |
| ¿Qué documentación adicional? | Recomiendo añadir: (1) **Diagrama de arquitectura** visual (componentes, flujo de datos, seguridad). (2) **Mapeo explícito de campos** (de minuta → JSON → celdas Excel). (3) **Análisis de ROI** (horas ahorradas vs costo mensual). (4) **Lecciones aprendidas** post-implementación. |
| ¿Qué falta para un cliente externo? | **Un pricing claro** para el sistema completo, y **un demo grabado** mostrando el antes/después. También una **lista de laboratorios similares en la zona** como potenciales clientes (esto demuestra visión de mercado). |

---

## 3. Puntos Críticos No Considerados en el Diseño Original

### 3.1. Interpolación logarítmica para D10/D30/D60

El documento menciona interpolación pero no especifica que **debe ser en escala logarítmica**, porque los diámetros de tamices siguen una progresión logarítmica (ej. 0.063, 0.125, 0.25, 0.5, 1, 2, 4...). La fórmula correcta para Dx es:
log(Dx) = log(d1) + (log(d2) - log(d1)) * (x - p1) / (p2 - p1)

text
donde d1,d2 son diámetros de tamices adyacentes, p1,p2 son % passante correspondientes.

Si se usa interpolación lineal, los errores en D10/D30/D60 pueden ser significativos (hasta 20-30% de error en suelos finos). **Esto es crítico para la precisión del informe**.

### 3.2. Cálculo de % passante en granulometrías con cuarteo

En los Excel con Foglio4 (como `Gra. completa`), los pesos que aparecen en Foglio2 **no son los pesados directamente**, sino que han sido escalados mediante la fórmula:
M_tot = M_pesado * (Masa_fracción_fina_total / Masa_fracción_fina_cuartada)

text

Si el sistema escribe directamente los pesos del escaneo en Foglio2 sin pasar por Foglio4, **los porcentajes serán incorrectos**. La solución es:
- El sistema debe **escribir los pesos brutos en Foglio4** (celdas B21:B28) y dejar que las fórmulas de Excel hagan el escalado automáticamente a la columna D de Foglio4, que luego se enlaza a Foglio2.

### 3.3. Múltiples muestras en una hoja — naming y output

El diseño actual sugiere un Excel por muestra. Sin embargo, en la práctica el laboratorio a veces quiere **un solo Excel con múltiples hojas** (una por muestra) para el mismo cliente. Sería bueno ofrecer ambas opciones: "Generar Excel individual" o "Generar Excel consolidado".

### 3.4. El triángulo DIN 18123 en Excel

Generar un gráfico ternario en Excel desde cero con openpyxl es **extremadamente complejo** (requiere manipular series de dispersión con coordenadas calculadas). Opciones más viables:

- **Opción A (recomendada)**: Crear una plantilla Excel con el triángulo ya dibujado como imagen de fondo, y superponer un punto de dispersión (scatter) con las coordenadas calculadas. Esto es sencillo de programar.
- **Opción B**: Generar el triángulo como imagen PNG con matplotlib en Python, e insertarla en el Excel. Menos interactivo pero más fácil.
- **Opción C**: Dejar que el técnico use KORN.LAB para el triángulo (como ahora), y el piloto solo entrega la granulometría base. Esto simplifica el scope inicial.

---

## 4. Recomendaciones Priorizadas

### Inmediatas (antes de implementar)

1. **Analizar en detalle los 5 Excel completados pendientes** para mapear todas las variantes de plantilla y definir reglas de detección.
2. **Diseñar el flujo de validación de coherencia de masas** (suma de pesos vs massa netta secca) como alerta en la pantalla de confirmación.
3. **Probar Claude Vision con la imagen real `S28C-826041013340.jpg`** para medir precisión real y ajustar el prompt.
4. **Decidir el alcance del triángulo**: ¿se incluye en el piloto o se deja para la versión de pago? Yo recomiendo **excluirlo del piloto** para reducir complejidad y riesgo.

### A medio plazo (durante el piloto)

5. **Implementar interpolación logarítmica** para D10/D30/D60 en el script Python que genera el Excel.
6. **Crear una "minuta mejorada" opcional** con casillas delimitadas para mejorar OCR en futuras pruebas.
7. **Documentar el mapeo exacto de celdas** para cada tipo de plantilla en una tabla (minuta → JSON path → celda Excel).

### Estratégicas (para el portfolio)

8. **Preparar un análisis de ROI** con datos reales del laboratorio: horas ahorradas/mes × costo hora técnico = ahorro mensual. Eso justifica el precio del sistema completo.
9. **Identificar 2-3 laboratorios competidores en Trentino-Alto Adige** que usen procesos similares, como prospectos para el servicio de pago.

---

## 5. Conclusión

El proyecto es **viable y bien diseñado a nivel conceptual**. Los mayores riesgos están en la **variabilidad de las plantillas Excel reales** y en la **precisión del OCR sobre escritura manuscrita cursiva italiana**. Ambos se mitigan con una buena fase de análisis de los Excel existentes y una pantalla de confirmación robusta.

**Fallo más crítico identificado**: Asumir que una sola plantilla Excel cubre todas las granulometrías. La realidad es que hay al menos tres variantes con lógicas de cálculo diferentes (simple, con cuarteo, con sedimentación). El sistema debe ser capaz de **detectar el tipo** y aplicar la plantilla correcta.

**Siguiente paso recomendado**: Analizar los 5 Excel pendientes, clasificarlos por tipo, y definir una matriz de decisión (ej. si hay datos en Foglio4 → usar plantilla completa; si no → usar simple).

El valor como portfolio es **alto**, especialmente si documentas no solo el éxito técnico sino también los desafíos de negocio (gestión de scope, alineación con el cliente interno, ROI). Esto demuestra madurez como Solutions Architect.