# TRIANGULACIÓN — Piloto GeoLabor
## Documento de contexto para revisión multi-IA

**Fecha**: 15 de abril 2026  
**Autor**: Pablo Cuevas  
**Objetivo**: Obtener perspectivas independientes sobre el diseño del piloto de automatización para GeoLabor  
**IAs consultadas**: Claude (Anthropic), Gemini (Google), DeepSeek

---

## QUIÉN SOY

Solutions Architect en formación. Construyo sistemas de automatización con N8N, PostgreSQL y APIs. Mi diferenciador es GDPR by design + documentación arquitectónica como entregable. Basado en Rovereto, Italia.

---

## QUÉ ES GEOLABOR

Laboratorio geotécnico en Rovereto, Italia. Realizan ensayos de suelo (granulometría, límites de Atterberg, compresión, etc.). El proceso actual es 100% manual: el técnico realiza el ensayo, anota resultados en un formulario de papel, y después alguien transcribe esos datos a plantillas Excel.

---

## QUÉ QUIERO AUTOMATIZAR (SCOPE CERRADO)

**Solo un tipo de ensayo**: granulometría (DIN 18123).  
**Solo dos outputs**: la plantilla Excel de granulometría + el triángulo de clasificación de suelos.  
**Todo lo demás está fuera de scope.**

---

## FLUJO PROPUESTO

```
[Foto/escaneo del formulario de papel completado a mano]
    ↓
[Web app simple — interfaz para subir la imagen]
    ↓
[N8N recibe la imagen]
    ↓
[API de visión (Claude Vision o similar) — extrae datos del formulario]
    ↓
[Pantalla de confirmación — el técnico valida/corrige los datos extraídos]
    ↓
[N8N genera la plantilla Excel de granulometría con los datos validados]
    ↓
[N8N genera el triángulo de clasificación de suelos (segundo Excel)]
```

---

## DOCUMENTOS QUE TENGO

- Fotos reales de formularios de granulometría completados a mano (minutas del laboratorio)
- Plantilla Excel de granulometría (la que usan actualmente, vacía)
- Plantilla Excel del triángulo de clasificación de suelos (la que usan actualmente)

---

## CONDICIONES DEL PILOTO

- **Gratuito**: es un proyecto de portfolio, no cobro por esto
- **GeoLabor es mi empleador actual**: trabajo ahí, conozco el proceso de primera mano
- **Objetivo doble**: resolver un problema real del laboratorio + tener un caso de portfolio demostrable
- **Bloqueado hasta tener VPS**: necesito N8N en producción (no local con ngrok) para que los técnicos lo usen

---

## PREGUNTAS PARA TRIANGULACIÓN

### 1. Arquitectura del flujo
- ¿El flujo propuesto tiene sentido? ¿Falta algún paso crítico?
- ¿La pantalla de confirmación humana está en el lugar correcto del flujo?
- ¿Hay algún riesgo de que datos incorrectos pasen sin ser detectados?

### 2. OCR/Visión sobre formularios manuscritos
- ¿Qué API de visión recomiendan para extraer datos de formularios escritos a mano en italiano?
- ¿Cuál es la tasa de error esperada en OCR de escritura manual?
- ¿Debería estructurar el formulario de alguna manera para mejorar la extracción? (ej: campos delimitados, formato específico)

### 3. Generación de Excel desde N8N
- ¿N8N puede generar archivos Excel con formato específico (celdas formateadas, gráficos)?
- ¿O es mejor usar una API/librería externa para la generación del Excel?
- ¿Cómo manejo la plantilla existente del laboratorio? ¿La relleno o genero una nueva?

### 4. Scope y riesgos
- ¿Ven riesgos de scope creep? ¿Qué límites adicionales debería establecer?
- ¿Qué pasa si la extracción OCR falla consistentemente con ciertos tipos de escritura?
- ¿Es realista como primer piloto o es demasiado complejo?

### 5. Valor como portfolio
- ¿Este proyecto demuestra capacidad de Solutions Architect o solo de automatizador?
- ¿Qué documentación debería producir para maximizar el valor del portfolio?
- ¿Qué le falta al diseño para que sea presentable a un cliente que no es GeoLabor?

---

## QUÉ ESPERO DE CADA IA

- **Perspectivas independientes**: no busco validación, busco puntos ciegos
- **Crítica constructiva**: si algo es frágil o mal diseñado, quiero saberlo
- **Alternativas concretas**: si proponen algo diferente, con justificación técnica

---

**Nota**: Este documento se envía idéntico a las tres IAs para garantizar que las respuestas sean comparables.