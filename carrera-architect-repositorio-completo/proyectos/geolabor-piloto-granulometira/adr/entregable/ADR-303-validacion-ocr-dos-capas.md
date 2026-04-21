# ADR-303: Sistema de Validación OCR en Dos Capas

**Proyecto**: GeoLabor s.a.s. — Piloto de automatización  
**Serie**: 300 — Proyecto GeoLabor  
**Audiencia**: Entregable  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Táctica — diseño de control de calidad de datos

---

## Contexto

El sistema extrae datos numéricos de minutas manuscritas mediante OCR. Los
errores de extracción son inevitables a cierta tasa. El riesgo más peligroso
no es el error catastrófico que cualquier persona detecta visualmente, como
un peso de cuatro dígitos leído como tres. El riesgo real es el error
numéricamente plausible: un "37" leído como "27", o un "127" leído como "172".
Este tipo de error puede pasar desapercibido en una revisión visual rápida,
especialmente al final de una jornada de trabajo.

Una pantalla de confirmación sin validación algorítmica previa no es una
garantía de calidad. Es una falsa sensación de control.

---

## La Decisión

El sistema aplica dos capas de validación en secuencia antes de generar el
Excel final. Ninguna de las dos es opcional y ninguna reemplaza a la otra.
Son complementarias porque cada una detecta un tipo de error diferente.

---

## Capa 1 — Validación algorítmica

El sistema suma automáticamente todos los pesos parciales registrados por el
OCR y los compara contra la massa netta secca total, que el técnico ingresa
manualmente en la pantalla de confirmación. Si la diferencia entre la suma
y la massa netta secca supera el 1%, el sistema muestra una alerta roja y
bloquea el botón de confirmación. El técnico no puede proceder hasta resolver
la discrepancia, ya sea corrigiendo los pesos en la tabla o verificando el
valor de la massa netta secca.

La massa netta secca se ingresa siempre manualmente por el técnico, nunca
por OCR. Esta decisión es intencional: usar como referencia de validación
un dato que también viene del OCR significaría que el sistema valida sus
propios errores contra sí mismo. La referencia de validación debe ser
siempre verdad terreno proporcionada por el humano.

Esta capa detecta lo que el humano no puede ver con fiabilidad: discrepancias
numéricas acumuladas entre valores individuales.

## Capa 2 — Confirmación visual humana

La pantalla de confirmación muestra la imagen original del escaneo al lado
de la tabla editable con los valores extraídos. Los valores que el sistema
extrajo con baja confianza se resaltan visualmente para dirigir la atención
del técnico hacia los puntos de mayor riesgo.

El técnico revisa los valores comparando directamente con la imagen original
y corrige los que sean incorrectos. Solo después de que la capa algorítmica
no encuentre discrepancias, el botón de confirmación está disponible.

Esta capa detecta lo que el algoritmo no puede determinar: errores de contexto,
valores que son numéricamente coherentes pero visualmente incorrectos respecto
a la minuta original.

---

## Secuencia obligatoria

La capa algorítmica siempre precede a la confirmación humana. El técnico no
puede dar el OK final hasta que el algoritmo haya pasado la validación de
coherencia numérica. Este orden no puede invertirse ni omitirse.

---

## Lo que este diseño garantiza

Con las dos capas activas, el sistema no puede generar un Excel con datos
que no hayan pasado por un control de coherencia matemática y por una
confirmación visual humana informada. Eso no garantiza cero errores, pero
sí garantiza que cualquier error que llegue al Excel fue visible para el
técnico en el momento de la confirmación y fue aceptado conscientemente.

---

**Autor**: Pablo Cuevas  
**Fecha**: 16 de abril 2026  
**Relacionado con**: ADR-300 (arquitectura general), ADR-302 (trazabilidad)
