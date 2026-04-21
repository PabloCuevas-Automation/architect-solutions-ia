# PROYECTO PILOTO — GeoLabor s.a.s.
## Automatización de Granulometría: Escaneo → Excel Completo

**Versión**: 1.0  
**Fecha**: 10 de abril 2026  
**Estado**: 🔄 Diseño — pendiente análisis de Excel completados  
**Autor**: Pablo Cuevas  
**Empresa**: Geo-Labor s.a.s. — Via del Garda, 46/L — 38068 Rovereto (TN)  
**Contacto interno**: Jefe directo de Pablo (operario en el laboratorio)

---

## 1. CONTEXTO Y OPORTUNIDAD

### El laboratorio

GeoLabor es un laboratorio geotécnico con sede en Rovereto/Mori con muchos años de operación en la zona. Realizan múltiples tipos de pruebas para determinar propiedades de terrenos, tierra y roca. Prácticamente todas las pruebas siguen el mismo patrón operativo: datos anotados en papel → transcripción manual → Excel con cálculos y gráficos → entregable al cliente.

### El dolor real

El cuello de botella no es el Excel ni los cálculos — esos ya funcionan con plantillas predeterminadas. El dolor es la **transcripción manual**: un técnico se sienta, lee lo que escribió a mano en la "minuta di prova", y tipea número por número en las celdas correctas del Excel. Cada número mal tipeado produce un error en la curva granulométrica que puede pasar desapercibido hasta que el cliente lo detecta.

### Volumen estimado

Aproximadamente 3 granulometrías por día laboral (variable). ~60 por mes. ~720 por año.

### Oportunidad de mercado más amplia

Excel es la herramienta universal de las PYME italianas en Rovereto — todo pasa por ahí y casi nadie sabe automatizar desde o hacia él. GeoLabor es el caso de estudio para posicionar "automatización de procesos basados en Excel" como servicio vendible en la zona.

---

## 2. SCOPE DEL PILOTO (GRATUITO)

### Lo que incluye

- **Granulometría estándar** (UNI EN 933-1 y UNI CEN ISO/TS 17892-4)
- **Gráfico de curva granulométrica** (Foglio1 del Excel actual)
- **Gráfico triangular simplificado** (Foglio3 nuevo — clasificación DIN 18123 por ghiaia/sabbia/limo+argilla)
- **Cálculo automático de D10, D30, D60, Cu y Cc** por interpolación (elimina lectura manual del gráfico)

### Lo que NO incluye (requiere proyecto de pago)

- Límites de Atterberg
- Clasificación completa KORN.LAB (USCS, DIN, UNI EN con segundo triángulo de fracción fina)
- Otros tipos de prueba (Coefficiente di Appiattimento, etc.)
- Sedimentazione como prueba aislada
- Integración con sistemas de gestión del laboratorio

### Acuerdo explícito

Piloto gratuito con scope cerrado. Método de estudio, portfolio y marketing con la marca GeoLabor. El sistema completo para las demás pruebas es un proyecto aparte con precio. **Dejar por escrito** para evitar scope creep.

---

## 3. FLUJO DE AUTOMATIZACIÓN

```
[Técnico completa minuta di prova a mano]
         ↓
[Escanea la hoja → JPEG en carpeta del escáner]
         ↓
[Sube el JPEG a web app (página simple con HTTPS)]
         ↓
[N8N recibe la imagen via webhook]
         ↓
[API de visión (Claude/Anthropic) extrae valores numéricos]
         ↓
[Pantalla de confirmación — técnico valida números]
         ↓
[N8N genera Excel completo con todos los fogli]
         ↓
[Técnico descarga Excel y rellena datos del cliente manualmente]
         ↓
[Técnico guarda el Excel en la carpeta del V.A. del cliente en el servidor]
```

### Principios de diseño del flujo

- **El papel no desaparece** — si el sistema falla, el técnico transcribe a mano como siempre. Riesgo cero de pérdida de datos o bloqueo del trabajo.
- **Datos personales del cliente fuera del sistema** — la IA extrae solo valores numéricos y metadatos técnicos. Committente, località, progetto los rellena el técnico en el Excel descargado.
- **Pantalla de confirmación obligatoria** — la IA puede leer mal un número. El técnico valida antes de generar el Excel. Sin confirmación, sin Excel.
- **Archivos no se almacenan en el servidor** — el JPEG se procesa, el Excel se genera, se entrega y se borran ambos.

---

## 4. ESTRUCTURA DEL EXCEL DE SALIDA

### Fogli siempre presentes (los que no tienen datos quedan vacíos)

| Foglio | Contenido | Fuente de datos |
|--------|-----------|-----------------|
| Foglio1 | Gráfico de curva granulométrica | Datos de Foglio2 |
| Foglio2 | Datos de setacciatura: tamices, pesos trattenuti, % passante, D10/D30/D60, Cu, Cc | Valores extraídos por IA del escaneo |
| Foglio3 | Gráfico triangular DIN 18123 (ghiaia/sabbia/limo+argilla) | Porcentajes calculados en Foglio2 |

### Para la granulometría completa (cuando hay múltiples rangos)

| Foglio | Contenido |
|--------|-----------|
| Foglio curvatura | Gráfico de curva granulométrica completa |
| Foglio >0.063 mm a <90 mm | Setacciatura gruesa |
| Foglio >0.063 mm a <5.6 mm | Setacciatura fina |
| Foglio sedimentazione | Datos de sedimentación (si aplica — si no hay datos, queda vacío) |
| Foglio triangular | Gráfico triangular DIN 18123 |

**Decisión de diseño**: los fogli siempre están presentes en el archivo. Los que no tienen datos quedan con estructura pero sin valores. Las fórmulas no generan error porque no tienen input — simplemente quedan en blanco. Esto mantiene un formato predecible y consistente.

---

## 5. MEJORA SOBRE EL PROCESO ACTUAL: D10, D30, D60 AUTOMÁTICOS

### Proceso actual (manual)

El técnico lee el gráfico de curva granulométrica a ojo. Busca donde la curva cruza la línea del 10%, 30% y 60% de passante, y baja al eje horizontal para leer el diámetro en mm. Subjetivo y variable entre personas.

### Proceso automatizado (interpolación)

El Foglio2 ya tiene para cada tamiz el diámetro (mm) y el % passante. D10 es el diámetro exacto donde el passante es 10%. Si ningún tamiz cae justo en 10%, se interpola entre los dos tamices más cercanos (uno con passante justo por debajo y otro justo por encima).

**Ejemplo concreto**: si el setaccio de 0,125 mm tiene 8% passante y el de 0,25 mm tiene 15% passante, D10 cade entre estos dos. La fórmula calcula exactamente dónde.

Con D10, D30 y D60 calculados automáticamente:
- **Cu** (coefficiente di uniformità) = D60 / D10
- **Cc** (coefficiente di curvatura) = D30² / (D60 × D10)

**Resultado**: el técnico ingresa solo los pesos trattenuti por tamiz. El Excel calcula todo: curva, D10, D30, D60, Cu, Cc — sin tocar el gráfico con la matita.

---

## 6. GRÁFICO TRIANGULAR — DISEÑO

### Qué es

Diagrama ternario según DIN 18123 que clasifica el suelo en zonas según los porcentajes de ghiaia, sabbia y limo+argilla. Los tres vértices representan 100% de cada componente.

### Zonas de clasificación

| Zona | Descripción |
|------|-------------|
| Gr | Ghiaia |
| saGr | Sabbia con ghiaia |
| grSa | Ghiaia con sabbia |
| Sa | Sabbia |
| siGr / clGr | Limo/argilla con ghiaia |
| siSa / clSa | Limo/argilla con sabbia |
| grsiSa / grclSa | Ghiaia con limo/sabbia |
| Si / Cl | Limo / Argilla |

### Input

Los tres porcentajes que el Foglio2 ya calcula:
- Ghiaia (fila 46 del Foglio2 actual)
- Sabbia (fila 47)
- Limo + Argilla (fila 48)

### Prueba de concepto

Se generó un gráfico triangular interactivo funcional como prueba de concepto. Validado con los datos del ejemplo de KORN.LAB (71.87% ghiaia, 28.11% sabbia, 0.02% fines = clasificación Gr/GW). **Coincide con el resultado del programa alemán.**

### Nota sobre versión completa (fuera de scope del piloto)

El programa KORN.LAB genera además:
- **Segundo triángulo inferior** — subdivide la fracción fina entre limo y argilla usando los límites de Atterberg (lp / Wl)
- **Parámetros adicionales** — rapporto di grano, kf (permeabilidad Beyer), clasificaciones USCS/DIN/UNI EN completas
- Todo es calculable desde los datos de granulometría + Atterberg, pero requiere proyecto de pago

---

## 7. DATOS QUE LA IA EXTRAE DEL ESCANEO

### De la tabla de setacciatura (por cada muestra en la hoja)

| Dato | Ejemplo |
|------|---------|
| Peso trattenuto por cada tamiz | 62.6, 86.4, 129.2, ... (columna de pesos) |

### Metadatos técnicos (cabecera y pie de la hoja)

| Dato | Ejemplo |
|------|---------|
| Verbale d'accettazione | 30/26 |
| Sperimentatore | Bottini |
| Data arrivo | 31.03 |
| Data prova | 01.04 |
| Sondaggio | 2.44 |
| Campione | MINDICHT |
| Profondità | DS2 |
| Massa lorda umida (g) | 4846 |
| Massa lorda secca (g) | 4421.8 |
| Massa netta secca per granulometria (g) | 3466.0 |
| Tara nº / massa (g) | R39 / 955.8 |

### Datos que NO extrae la IA (los rellena el técnico)

| Dato |
|------|
| Committente |
| Riferimento |
| Località |
| Progetto |
| Descrizione/Beschreibung |

### Múltiples muestras por hoja

Una minuta di prova puede contener desde 1 hasta 8+ muestras (columnas). Cada columna genera un Excel independiente.

**Naming del archivo de salida**: `Granulometria_{verbale}_{sondaggio}_{campione}.xlsx`

---

## 8. ENTORNO TÉCNICO DEL LABORATORIO

### Infraestructura existente

| Componente | Detalle |
|------------|---------|
| Sistema operativo | Windows 11 |
| Red | PCs conectadas entre sí via servidor NAS |
| Unidades de red | Server (Y:), Dati (Z:), Lexar (D:) |
| Escáner | Conectado a PC, output va a Server (Y:) via NAS |
| Formato de escaneo | PDF por defecto, **cambiado a JPEG** para el piloto |
| Internet | Disponible, lento pero funcional |
| Permisos | Pablo tiene permisos para crear carpetas |

### Estructura de archivos del laboratorio

```
Server (Y:) /
└── pc2/
    └── GEOLABOR-LAVORO/
        └── TUTTO 2026/
            └── PROVE 2026/
                └── [CLIENTE] /           ← carpeta por cliente
                    ├── controllo di produzione 2026/
                    ├── Ordini 2026/
                    └── V.A. {N}-26/      ← Verbale di Accettazione
                        ├── Coefficiente di Appiattimento/
                        └── Granulometria/ ← aquí van los Excel
```

### Patrón de trabajo actual

1. Técnico pesa las muestras y anota en minuta di prova (papel)
2. Va a la PC, busca la carpeta del cliente en el servidor
3. Copia un Excel de granulometría anterior
4. Cambia los datos: pesos por tamiz + metadatos del cliente
5. El Excel recalcula curva y porcentajes
6. Lee D10/D30/D60 del gráfico a ojo, los escribe manualmente
7. Calcula Cu y Cc
8. Abre KORN.LAB (programa externo alemán) para generar gráfico triangular (da problemas)
9. Imprime y/o envía al cliente

---

## 9. VARIANTES DE GRANULOMETRÍA IDENTIFICADAS

### Por norma

| Norma | Archivos disponibles |
|-------|---------------------|
| UNI EN 933-1 | 4 archivos Excel de un cliente |
| UNI CEN ISO/TS 17892-4 | 1 archivo Excel de otro cliente |

### Por rango de tamices

| Tipo | Rango | Notas |
|------|-------|-------|
| Estándar (0-30 mm) | Tamices de 0.063 a 31.5 mm | Plantilla ya analizada en detalle |
| Gruesa (>8 mm a 90 mm) | Tamices grandes | Misma estructura, diferentes tamices |
| Fina (<8 mm) | Tamices pequeños | Misma estructura, diferentes tamices |
| Completa | <8 mm + >8 mm a 90 mm + sedimentazione | Un archivo con los tres rangos |

### Decisión de diseño pendiente

Analizar los Excel completados para determinar si una sola plantilla base cubre todas las variantes o si se necesitan plantillas diferentes. La estructura de la tabla es idéntica (tamiz → peso → % trattenuto → % passante) — lo que cambia son los tamices y la norma de referencia.

---

## 10. INFRAESTRUCTURA Y COSTOS

### Arquitectura

```
[PC laboratorio — navegador web]
        ↓ HTTPS
[VPS Hetzner — N8N + web app]
        ↓ API call
[Anthropic API — Claude visión]
        ↓ respuesta JSON
[N8N — genera Excel con openpyxl]
        ↓ descarga
[PC laboratorio — técnico descarga Excel]
```

### Costos mensuales

| Componente | Costo | Notas |
|------------|-------|-------|
| VPS Hetzner CX22 | €4-6/mes | **Compartido con sistema de leads** — no es costo nuevo |
| API Anthropic (visión) | €4-12/mes | ~60 llamadas/mes × $0.01-0.03 por imagen |
| Dominio | €0 | Ya tiene pablocuevas.it — subdominio gratuito |
| N8N | €0 | Self-hosted, gratuito |
| Generación Excel | €0 | openpyxl, open source |
| **Total adicional** | **€4-12/mes** | Solo el costo de API |

### Valor entregado

10-15 minutos ahorrados por prueba × 60 pruebas/mes = **10-15 horas de trabajo humano liberadas por mes**. Ese es el argumento de venta para el sistema completo.

### Acceso y control

- **Pablo controla**: VPS, N8N, workflows, credenciales, deployment, actualizaciones
- **GeoLabor accede**: web app para subir escaneo y descargar Excel — nada más
- **Dominio sugerido**: `geolabor.pablocuevas.it` (subdominio profesional sin costo extra)

### Seguridad

| Medida | Estado |
|--------|--------|
| HTTPS | Obligatorio — Certbot en VPS |
| Webhook con autenticación | Token o basic auth — no público |
| Archivos no persistentes | JPEG y Excel se borran después de entrega |
| Sin datos personales en el sistema | Solo datos técnicos de suelos |
| Datos comerciales del cliente | Fuera del sistema — los rellena el técnico |

---

## 11. INTERFAZ DE USUARIO — WEB APP

### Pantalla 1 — Subir escaneo

- Botón "Carica scansione" o drag & drop
- Acepta JPEG (y opcionalmente PDF como fallback si el escáner falla)
- Indicador de progreso mientras la IA procesa

### Pantalla 2 — Confirmación de datos

- Tabla con todos los valores extraídos por la IA
- Campos editables para corregir errores de lectura
- Metadatos extraídos: sondaggio, campione, profondità, masas
- Si la hoja tiene múltiples muestras, mostrar todas con indicador visual
- Botón "Conferma e genera Excel"

### Pantalla 3 — Descarga

- Enlace de descarga del Excel generado (o múltiples si hay varias muestras)
- Nombre del archivo: `Granulometria_{verbale}_{sondaggio}_{campione}.xlsx`
- El archivo se borra del servidor después de la descarga (o tras X minutos)

---

## 12. RESILIENCIA — QUÉ PASA CUANDO FALLA

| Fallo | Consecuencia | Solución |
|-------|-------------|----------|
| VPS caído | Web app no carga | Health check con alerta a Pablo + técnico transcribe a mano |
| API de visión falla | No se extraen datos | Mensaje "servicio no disponible" + técnico reintenta o transcribe a mano |
| IA lee mal un número | Dato incorrecto en Excel | Pantalla de confirmación — técnico corrige antes de generar |
| Internet del lab cae | No puede subir escaneo | Espera a que vuelva — el papel no se pierde |
| Escáner falla | No hay JPEG | Foto con teléfono como fallback (menor calidad pero funcional) |

**Principio**: el peor caso siempre es "hoy transcribo a mano como antes." Nunca hay pérdida de datos ni bloqueo del trabajo.

---

## 13. RELACIÓN CON PROYECTO ANCLA 1

### PA1 es la plataforma, GeoLabor es el primer producto

PA1 construye la infraestructura necesaria:
- VPS configurado con Docker Compose
- HTTPS con Certbot
- N8N en producción
- PostgreSQL operativo
- Health check y monitoreo
- Backup automático

Sin PA1 completado, no hay dónde correr GeoLabor. El orden correcto:
1. Terminar PA1 Sesión 3+ (construcción en N8N)
2. Completar Bloques 5 y 6 (PostgreSQL + deploy)
3. En paralelo: analizar Excel de GeoLabor y diseñar el workflow
4. Cuando el VPS esté corriendo: montar GeoLabor como workflow adicional

### Lo que GeoLabor añade al portfolio

- Primer caso de uso real con cliente interno
- Demuestra capacidad de automatización basada en Excel (servicio vendible en Rovereto)
- Marca conocida (GeoLabor) como referencia
- Combina IA (visión), N8N y generación de documentos — stack completo

---

## 14. PREGUNTAS PENDIENTES PARA TRIANGULACIÓN

### Técnicas

1. ¿Qué API de visión usar (Claude vs GPT-4o)? Criterios: precisión en tablas numéricas manuscritas, costo por llamada, disponibilidad de API
2. ¿Una plantilla Excel cubre todas las variantes de norma o se necesitan plantillas separadas?
3. ¿El gráfico triangular en Excel (Foglio3) se puede construir con scatter plot nativo o necesita generación programática?
4. ¿La interpolación para D10/D30/D60 usa escala logarítmica (estándar en granulometría) o lineal?
5. ¿Cómo manejar hojas con 8+ muestras donde la caligrafía es más difícil de leer?

### De diseño

6. ¿Web app con framework (React) o HTML puro? Para un formulario de upload + confirmación + descarga, ¿cuál es el mínimo viable?
7. ¿El Excel se genera con openpyxl copiando la plantilla original o se construye desde cero? Copiar la plantilla preserva formato, gráficos y fórmulas existentes
8. ¿Cómo identifica N8N qué tipo de granulometría es (estándar, fina, gruesa, completa) antes de llenar el Excel?

### De negocio

9. ¿Cuánto cobrar por el sistema completo que reemplace KORN.LAB?
10. ¿El modelo es licencia mensual, pago único, o por uso?
11. ¿Hay otros laboratorios en la zona que podrían ser clientes del mismo sistema?

---

## 15. ARCHIVOS DISPONIBLES PARA ANÁLISIS

### Ya analizados

| Archivo | Tipo | Estado |
|---------|------|--------|
| `Granulometria_0-30_mm_ITALIANO_UNI_CEN_ISO-TS_17892-4.xlsx` | Plantilla italiana | ✅ Estructura mapeada — Foglio2 filas 26-44 para tamices, columna D para pesos |
| `Granulometria_0-30_mm_TEDESCO_UNI_CEN_ISO-TS_17892-4.xls` | Plantilla alemana | Pendiente análisis detallado |
| Fotos de minuta di prova (2 muestras) | Escaneo de ejemplo | ✅ Valores legibles, estructura clara |
| Foto de minuta di prova (8+ muestras) | Escaneo complejo | ⚠️ Caligrafía más difícil |
| Foto de KORN.LAB output | Referencia del gráfico triangular | ✅ Zonas y parámetros identificados |
| Foto de Excel en pantalla (Foglio1) | Referencia de curva granulométrica | ✅ Estructura visual confirmada |

### Pendientes de subir y analizar

| Archivo | Tipo |
|---------|------|
| 4 Excel completados — cliente con UNI EN 933-1 | Granulometrías reales para validación |
| 1 Excel completado — cliente con UNI CEN ISO/TS 17892-4 | Granulometría real para validación |
| 1 Excel completado — granulometría completa (<8mm + >8mm + sedimentazione) | Caso completo para validación |
| Escaneo real del escáner del laboratorio | Imagen en calidad real de producción |

---

**Documento creado**: 10 de abril 2026  
**Autor**: Pablo Cuevas  
**Para triangulación con**: Claude (Anthropic) + Gemini + DeepSeek  
**Próxima revisión**: Al recibir y analizar los Excel completados