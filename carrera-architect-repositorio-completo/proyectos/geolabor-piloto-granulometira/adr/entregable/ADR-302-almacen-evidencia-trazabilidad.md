# ADR-302: Almacén de Evidencia y Trazabilidad de Ensayos

**Proyecto**: GeoLabor s.a.s. — Piloto de automatización  
**Serie**: 300 — Proyecto GeoLabor  
**Audiencia**: Entregable  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — requisito legal en entorno técnico regulado

---

## Contexto

En geotecnia, los datos de un ensayo granulométrico no son solo información
técnica. Pueden terminar como evidencia en un peritaje si hay un fallo
estructural posterior en una obra donde se usaron esos datos para clasificar
el suelo. Si el sistema procesa la imagen, el técnico confirma los datos, se
genera el Excel, y la imagen original no está almacenada y vinculada al Excel,
se rompe la cadena de custodia de la evidencia.

El Excel existe pero fue producido por un sistema automatizado que pudo haber
cometido un error. Sin la imagen original vinculada al Excel, no es posible
demostrar que el Excel es fiel a la minuta manuscrita del ensayo.

Este riesgo fue identificado durante el proceso de diseño y validado como el
hallazgo más importante de la fase de revisión arquitectónica del piloto.

---

## La Decisión

El sistema almacena cada imagen procesada y la vincula al Excel generado
mediante un identificador único. Esta trazabilidad no es opcional y no
puede desactivarse — es una propiedad estructural del sistema.

---

## Los tres componentes del almacén de evidencia

El primer componente es el almacenamiento de la imagen. Cada imagen que entra
al sistema se guarda en el servidor en una carpeta dedicada con una ruta
determinista: `/evidencias/ensayos/{ID_ensayo}_{timestamp}_{tipo_prueba}.jpg`.
El identificador del ensayo es un UUID generado por el sistema al inicio del
flujo, antes de cualquier procesamiento.

El segundo componente es el registro en base de datos. Cada ensayo queda
registrado en PostgreSQL con los siguientes campos: identificador del ensayo,
timestamp, ruta de la imagen almacenada, JSON de los datos extraídos por OCR
antes de cualquier edición humana, JSON de los datos confirmados por el técnico
después de su revisión, identificador del técnico que procesó el ensayo, y tipo
de prueba seleccionado. La distinción entre los dos campos JSON es fundamental:
permite reconstruir en el futuro qué extrajo el sistema y qué corrigió el
técnico, que son dos momentos de evidencia distintos.

El tercer componente es el vínculo en el Excel. El archivo Excel generado
contiene el identificador del ensayo en una celda dedicada. Si en el futuro
alguien necesita encontrar la imagen original que dio origen a un Excel
específico, ese identificador es el puente entre ambos.

---

## Lo que esto permite

Con el almacén de evidencia activo, el sistema puede responder las siguientes
preguntas en cualquier momento posterior al ensayo: qué imagen se procesó,
qué extrajo el sistema de esa imagen, qué corrigió el técnico antes de
confirmar, y qué archivo Excel se generó como resultado. Esa cadena completa
es la trazabilidad del ensayo desde la minuta manuscrita hasta el informe.

---

## Política de retención

Las imágenes y los registros de trazabilidad se conservan por un mínimo de
cinco años desde la fecha del ensayo, o hasta que el laboratorio confirme
explícitamente que los datos de ese periodo ya no son necesarios para ningún
proyecto activo. Esta política debe revisarse si los proyectos asociados a
los ensayos tienen plazos de responsabilidad legal más largos.

---

**Autor**: Pablo Cuevas  
**Fecha**: 16 de abril 2026  
**Relacionado con**: ADR-300 (arquitectura general del piloto)
