# ADR-001: Priorizar JavaScript sobre Python en Fase 1

**Fecha**: 2026-01-24  
**Estado**: ✅ Aceptada y VALIDADA  
**Autor**: Pablo  
**Tipo**: Estratégica (afecta timeline completo de Fase 1)  
**Contexto**: Fase 0.5 - Descubrimiento de prerrequisitos para N8N  
**Supersede**: Decisión implícita del roadmap original (Python en Fase 1)  
**Validación**: 21 de febrero 2026

---

## Resumen Ejecutivo

En el contexto de preparación para usar N8N como herramienta de monetización rápida (objetivo: facturar en semanas 8-10), enfrentando el descubrimiento de que **N8N usa JavaScript nativamente** (no Python), y teniendo prueba de JavaScript el 16 de febrero del curso de Trento, **decidí priorizar JavaScript sobre Python en Fase 1**, aceptando posponer Python a Fase 2 y retrasar proyectos dependientes de Python (como RAG básico) en 2-3 meses.

---

## Contexto

### Descubrimiento Crítico (24 de enero)

Durante análisis de prerrequisitos para empezar N8N productivamente, descubrí que:

- **N8N code nodes usan JavaScript** (no Python)
- **Expresiones `{{ }}` usan sintaxis JavaScript**
- **Transformación de datos usa métodos JavaScript** (`.map()`, `.filter()`, `.reduce()`)
- Python solo se puede usar *indirectamente* (llamadas a APIs externas, scripts separados)

Esto contradice mi asunción inicial de que "N8N es no-code y Python es el glue code". **La realidad es que JavaScript es el glue code dentro de N8N.**

### Restricciones Temporales

1. **Prueba de JavaScript el 16 de febrero**
   - Curso de desarrollo web del gobierno de Trento
   - Evaluación sobre: HTML, CSS, JavaScript
   - Ya invertí tiempo en este curso — no cancelable

2. **Proyecto final del curso: Sitio web personal (10-19 febrero)**
   - Perfectamente alineado con necesidad de negocio
   - Sitio web para promocionar servicios de automatización
   - No es tiempo "perdido" — es infraestructura de marketing

### Contexto de Negocio

Mercado local (Rovereto/Trento) tiene **poca competencia** en automatización con N8N. Ventana de first-mover que requiere llegar a N8N productivo lo antes posible.

---

## Alternativas Consideradas

### Opción A: Seguir Roadmap Original (Python + SQL 70%)
**Por qué NO**: Ignora realidad técnica de N8N. JavaScript es el lenguaje nativo de N8N, no Python. Estudiar Python primero habría requerido aprender JS igualmente para N8N, creando doble trabajo.

### Opción B: JavaScript + Python en Paralelo (50%/50%)
**Por qué NO**: Sobrecarga cognitiva sin beneficio claro. Aprender dos lenguajes simultáneamente habría diluido la profundidad en ambos. Violaba el principio de simplicidad primero.

### Opción C: JavaScript primero, Python en Fase 2 ⭐ ELEGIDA
**Por qué SÍ**: Sinergia única — el curso de Trento ya exigía JavaScript, N8N lo requiere nativamente, y el proyecto web del curso servía como infraestructura de marketing real.

---

## Decisión

**Priorizar JavaScript en Fase 1 completa.** Python se pospone a Fase 2 donde encaja naturalmente con FastAPI, automatizaciones avanzadas y proyectos de ML/AI más complejos.

**Trade-off aceptado**: Retraso de 2-3 meses en proyectos que requieren Python (RAG básico, scripts de análisis). Ganancia: llegada a N8N productivo 2-3 meses antes, con base JS sólida y proyecto web funcional.

---

## Consecuencias

### Positivas ✅
- JavaScript aprendido en contexto real (proyecto web + curso)
- Proyecto web construido como infraestructura de marketing activa
- Base técnica lista para N8N Code node desde el primer día
- No hay deuda de "tener que aprender JS después para N8N"

### Negativas aceptadas ⚠️
- Python pospuesto a Fase 2
- RAG y automatizaciones Python-dependientes retrasadas ~3 meses
- Menor versatilidad técnica en los primeros 6 meses

---

## ✅ VALIDACIÓN — 21 de febrero 2026

**Esta sección documenta el resultado real de la decisión tomada el 24/01.**

### Resultados obtenidos

| Criterio de éxito | Resultado |
|-------------------|-----------|
| Examen JavaScript aprobado | ✅ Aprobado el 16/02/2026 |
| Proyecto web en JS funcional | ✅ pablocuevas.it live desde 19/02/2026 |
| Firebase integrado con JS | ✅ Formulario activo recibiendo leads |
| GSAP, módulos ES6, DOM, eventos | ✅ Todos implementados en producción |
| Base para N8N Code node | ✅ Arrays, objetos, JSON, funciones dominados |

### Métricas del proyecto web (evidencia de JS funcional)
- PageSpeed Rendimiento: **98/100**
- PageSpeed Accesibilidad: **97/100**
- PageSpeed Recomendaciones: **100/100**
- PageSpeed SEO: **100/100**

### Aprendizaje adicional no anticipado

La decisión de usar el proyecto académico como infraestructura de negocio real demostró un patrón de aprendizaje clave: **aprender por construcción de sistemas con propósito real** es significativamente más efectivo que estudiar en abstracto. En 9 sesiones documentadas se construyó un sistema completo en producción que habría requerido meses en un contexto de estudio puro.

Este patrón debe guiar cómo se aborda N8N: no como herramienta a estudiar, sino como sistema para resolver el problema concreto de notificación de leads del formulario de contacto.

### Veredicto

**Decisión correcta.** El trade-off fue proporcional al beneficio. Python llega en Fase 2 con una base JS sólida que facilitará la transición, no la complicará.

---

**Autor**: Pablo Cuevas  
**Fecha decisión**: 24 de enero 2026  
**Fecha validación**: 21 de febrero 2026
