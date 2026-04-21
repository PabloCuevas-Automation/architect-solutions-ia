# ADR-301: Python para Inyección de Datos en Excel — Excepción a ADR-101

**Proyecto**: GeoLabor s.a.s. — Piloto de automatización  
**Serie**: 300 — Proyecto GeoLabor  
**Audiencia**: Personal  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Táctica — excepción justificada a un principio de serie 100  
**Overrides parcialmente**: ADR-101 (JavaScript primero, Python en Fase 2)

---

## Contexto

ADR-101 documenta la decisión de mantener Python fuera del stack hasta Fase 2,
priorizando JavaScript como lenguaje único en Fase 1. Esa decisión sigue siendo
válida como principio general. Sin embargo, el piloto de GeoLabor requiere
modificar plantillas Excel existentes que contienen fórmulas activas, gráficos
de curva granulométrica, y un gráfico scatter para el triángulo DIN 18123.

La librería JavaScript `exceljs` tiene limitaciones documentadas al manipular
archivos `.xlsx` con objetos embebidos: puede corromper los gráficos existentes
al escribir en las celdas de la plantilla. Esto no es una limitación de quien
lo implementa, es una limitación técnica real de la librería frente a este caso
de uso específico.

Esto fue identificado y validado independientemente por DeepSeek y Gemini durante
la triangulación, y fue señalado por Claude como una decisión que debía tomarse
conscientemente en lugar de adoptarse por inercia de las recomendaciones de las IAs.

---

## La Decisión

Usar Python con `openpyxl` para la escritura de datos en las plantillas Excel
de GeoLabor. Python se invoca desde N8N mediante el nodo Execute Command,
que llama a un script `.py` pasando los datos confirmados como argumento JSON.

Esta excepción aplica exclusivamente a la generación de Excel en el piloto
de GeoLabor. No autoriza el uso de Python en ningún otro componente de este
proyecto ni en proyectos paralelos como PA1.

---

## Por qué Execute Command y no un microservicio Flask

Flask es la arquitectura más limpia a largo plazo porque separa la lógica de
generación de Excel en un servicio independiente que N8N invoca vía HTTP,
lo que facilita versionar y mantener ese componente de forma autónoma.

Sin embargo, para un piloto gratuito de primera entrega, Flask añade un
componente más al docker-compose, un puerto adicional que gestionar, y
complejidad de despliegue que no está justificada por el alcance actual.
Execute Command es más simple, suficiente para la variante simple, y puede
migrarse a Flask cuando el sistema crezca a múltiples variantes en producción.

La decisión de migrar a Flask se evalúa en la primera versión de pago del
sistema, no en el piloto.

---

## Condiciones bajo las cuales esta excepción aplica

Esta excepción está justificada cuando se cumplen las tres condiciones siguientes:
el caso de uso requiere modificar plantillas Excel con gráficos existentes, la
librería JavaScript disponible no puede hacerlo sin riesgo de corrupción, y el
alcance del proyecto justifica introducir un lenguaje adicional. Si alguna de estas
condiciones cambia, la excepción debe revisarse.

---

## Deuda técnica que genera

Introducir Python en el stack del piloto significa que el entorno de construcción
y el VPS de producción deben tener Python instalado. Esto se gestiona dentro del
contenedor Docker del proyecto, no en el sistema operativo del host, para mantener
el aislamiento. La imagen Docker del script Python se documenta en el docker-compose
del proyecto junto con las instrucciones de setup.

---

**Autor**: Pablo Cuevas  
**Fecha**: 16 de abril 2026  
**Relacionado con**: ADR-101 (decisión original sobre Python)
