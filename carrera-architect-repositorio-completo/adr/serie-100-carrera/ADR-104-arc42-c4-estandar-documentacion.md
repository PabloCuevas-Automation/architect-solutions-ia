# ADR-104: Arc42 + C4 como Estándar de Documentación Arquitectónica

**Serie**: 100 — Decisiones de aprendizaje y carrera  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — afecta cómo se documenta todo proyecto desde este punto  
**Supersede**: Estándar informal de 6 pasos usado en PA1

---

## Contexto

Hasta este punto, Pablo ha construido su propio estándar de documentación
proyecto a proyecto: ADRs individuales, diagrama de sistema, GDPR data mapping,
schema PostgreSQL, README y runbook. Ese estándar funciona, pero es informal y
no tiene un marco de referencia reconocido en la industria.

Al analizar el piloto de GeoLabor, se identificó que Pablo ya estaba haciendo
Arc42 de forma fragmentada sin saberlo: los ADRs cubren las decisiones de diseño,
el diagrama de sistema cubre las vistas de contenedores, el GDPR data mapping
cubre los conceptos transversales. La pregunta no es si adoptar Arc42 y C4, sino
si formalizar lo que ya existe dentro de un marco estándar.

---

## La Decisión

Adoptar **Arc42** como estructura de documentación de arquitectura y **C4 Model**
como lenguaje de diagramación para todos los proyectos desde el piloto GeoLabor
en adelante, integrándolos con el sistema de ADRs ya existente.

---

## Qué es cada uno y cómo se complementan

**Arc42** es un framework de documentación con doce secciones estructuradas.
No todas las secciones aplican a todos los proyectos — se usa la profundidad
proporcional a la complejidad del sistema. Las secciones más relevantes para
los proyectos actuales son: requisitos y objetivos de calidad, restricciones
técnicas y organizativas, contexto y alcance del sistema, decisiones de diseño
(donde viven los ADRs), conceptos transversales (GDPR, seguridad, resiliencia),
vistas de despliegue, y riesgos conocidos.

**C4 Model** establece cuatro niveles de abstracción para diagramas. El nivel
de Contexto muestra el sistema en su entorno y está dirigido al cliente no
técnico. El nivel de Contenedor muestra las aplicaciones, bases de datos y
servicios que componen el sistema, dirigido a otros desarrolladores o arquitectos.
El nivel de Componente muestra qué hay dentro de cada contenedor, dirigido al
equipo técnico. El nivel de Código es opcional y solo aplica para componentes
complejos. Cada nivel responde a una pregunta distinta para una audiencia distinta.

**La relación con los ADRs**: Arc42 es el contenedor de toda la documentación.
C4 proporciona el lenguaje visual dentro de Arc42. Los ADRs son el registro de
razonamiento que vive en la sección de Decisiones de Diseño de Arc42. Los tres
son capas complementarias, no alternativas.

---

## Por qué este estándar y no el informal de PA1

El estándar de seis pasos de PA1 fue construido por necesidad y funcionó.
Su problema no es que sea incorrecto sino que es invisible para alguien externo:
un cliente, un colaborador, o un empleador no sabe cómo leerlo porque no tienen
el contexto de cómo fue construido. Arc42 y C4 son estándares reconocidos en
la industria europea de arquitectura de software, lo que significa que cualquier
arquitecto senior puede heredar un proyecto documentado con estos estándares y
empezar a trabajar sin necesitar que Pablo le explique el sistema de documentación.
Eso es soberanía técnica real del cliente y portabilidad del trabajo de Pablo.

---

## Cómo se aplica en la práctica

Para proyectos piloto o de alcance reducido como GeoLabor, se usan las secciones
Arc42 relevantes sin completar las doce. El criterio es proporcionalidad: la
documentación debe ser suficiente para que otro arquitecto entienda el sistema,
no tan exhaustiva que su producción consuma más tiempo que la construcción.

Para proyectos de producción con clientes reales, se completa Arc42 en su totalidad
como parte del entregable. La documentación Arc42 completa es parte del precio,
no un extra opcional.

Los diagramas C4 se producen en texto usando Mermaid o PlantUML cuando sea posible,
para mantener los diagramas versionables en Git junto con el código.

---

## Consecuencias

Como consecuencia positiva, todos los proyectos futuros tienen una estructura de
documentación predecible y reconocible. El esfuerzo de aprender el estándar se
amortiza en cada proyecto siguiente. La documentación existente de PA1 y GeoLabor
puede migrarse progresivamente sin reescritura completa.

Como consecuencia negativa y deuda técnica consciente, PA1 no tiene documentación
Arc42/C4 completa. Se migra cuando PA1 llegue a producción real (Bloque 6), no
antes. GeoLabor es el primer proyecto que se documenta con el estándar desde el
inicio.

---

## Referencias

Simon Brown — C4 Model: https://c4model.com  
Arc42: https://arc42.org  
ADR-001 — política de documentación de decisiones  
ADR-000 — Principio 4: Compliance = Poder / Principio 6: Progreso = Producción

---

**Autor**: Pablo Cuevas  
**Fecha**: 16 de abril 2026
