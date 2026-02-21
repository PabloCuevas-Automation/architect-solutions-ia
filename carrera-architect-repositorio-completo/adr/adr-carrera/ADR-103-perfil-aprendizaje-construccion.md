# ADR-002: Perfil de Aprendizaje — Construcción sobre Abstracción

**Fecha**: 21 de febrero 2026  
**Estado**: ✅ Aceptada  
**Autor**: Pablo  
**Tipo**: Estratégica fundamental — afecta cómo se aborda cada tecnología en todas las fases  
**Contexto**: Post-proyecto web, inicio de Fase 1 con N8N  
**Audiencia**: Yo mismo en cada momento que enfrente una tecnología nueva

---

## Resumen Ejecutivo

Tras completar la Fase 0 (pensamiento sistémico teórico) y el proyecto web (primer sistema real en producción), identifiqué un patrón claro en cómo aprendo de forma efectiva: **aprendo tecnologías resolviendo problemas pragmáticos concretos, no estudiándolas en abstracto**. Esta decisión documenta ese perfil y establece que toda tecnología nueva en el roadmap debe vincularse a un sistema con propósito real y entregable concreto antes de ser estudiada como disciplina aislada.

La consecuencia inmediata es la decisión de aprender N8N en paralelo con JavaScript mientras construyo la automatización real del formulario de contacto, en lugar de completar un curso de N8N antes de tocarlo en producción.

---

## Contexto

### Lo que la Fase 0 demostró

La Fase 0 (reprogramación mental, ADRs, pensamiento sistémico) fue correcta y necesaria. Estudiar teoría primero forjó la base de pensamiento arquitectónico que distingue cómo analizo sistemas ahora. Sin esa base, el proyecto web habría sido un ejercicio técnico sin criterio — código sin decisiones.

**Esa fase funcionó en abstracto porque su producto era mental, no técnico.** El output de "aprender a pensar sistémicamente" no requiere construcción — requiere reflexión, análisis y documentación. El ADR-000 es la evidencia de que ese aprendizaje ocurrió.

### Lo que el proyecto web reveló

Cuando llegaron las tecnologías concretas (JavaScript, CSS, Firebase, GSAP), el modo de aprendizaje cambió. No fue un momento exacto de revelación — fue gradual. Lo que se hizo visible es una tendencia natural hacia lo pragmático:

- Estudiar sintaxis de JavaScript en abstracto avanzaba lentamente
- Construir pablocuevas.it con JavaScript avanzó en 9 sesiones a un sistema completo en producción con 98/100 en PageSpeed

El mismo tiempo, resultados cualitativamente distintos. La diferencia no fue el esfuerzo — fue el contexto. Con un problema real que resolver (la página tiene que funcionar, el formulario tiene que conectarse, el video tiene que hacer loop), el aprendizaje tenía dirección y urgencia natural.

### La distinción clave

No es que la teoría sea mala. Es que **la teoría sirve para forjar criterio, y la práctica construyendo sirve para dominar herramientas**. Son modos distintos para objetivos distintos:

```
Pensamiento sistémico, ADRs, GDPR, trade-offs
→ Se aprende reflexionando y documentando
→ La abstracción es el vehículo correcto

JavaScript, N8N, Firebase, Docker, APIs
→ Se aprende construyendo sistemas reales
→ La construcción es el vehículo correcto
```

Aplicar el modo equivocado al objetivo equivocado es ineficiencia disfrazada de rigor.

---

## Alternativas Consideradas

### Opción A: Completar curso de N8N antes de construir

**Descripción**: Estudiar N8N sistemáticamente — documentación, tutoriales, ejercicios guiados — y luego aplicar al problema real del formulario.

**Pros**:
- Base teórica completa antes de producción
- Menos errores iniciales
- Orden "correcto" pedagógicamente

**Contras**:
- Contradice el patrón de aprendizaje demostrado empíricamente
- N8N en abstracto no tiene la urgencia que genera aprendizaje profundo
- El problema real del formulario existe ahora — posponerlo tiene costo de oportunidad real
- Riesgo de "tutorial hell": estudiar N8N sin producir nada vendible durante semanas

**Por qué NO**: Ignora evidencia directa del proyecto web. Aplicar el modo de aprendizaje incorrecto a la herramienta correcta produce resultados mediocres.

---

### Opción B: Terminar de dominar JavaScript autónomamente antes de tocar N8N

**Descripción**: Completar los días 26-35 del plan de 42 días (APIs, transformaciones, async/await en profundidad) de forma independiente y sin asistencia de IA antes de instalar N8N.

**Pros**:
- Base JS sólida y autónoma para el Code node de N8N
- Sin dependencia de IA para escribir lógica básica
- Más preparado técnicamente al primer contacto con N8N

**Contras**:
- Semanas adicionales sin tocar N8N
- El aprendizaje de JS en abstracto ya demostró ser más lento
- El Code node de N8N enseña JS en contexto real — es un refuerzo, no un requisito previo completo

**Por qué NO es la opción elegida pero tiene mérito parcial**: La base JS autónoma importa para el Code node. La solución no es esperar a dominar JS completamente — es practicar JS de forma autónoma en paralelo mientras N8N avanza. Ver consecuencias.

---

### Opción C: N8N y JS en paralelo, aprendiendo mientras construyo ⭐ ELEGIDA

**Descripción**: Instalar N8N ahora. Usar el problema real (Firebase → notificación de leads) como proyecto ancla. Aprender N8N resolviendo ese problema. Practicar JS de forma autónoma en paralelo para fortalecer lo que el Code node va a exigir.

**Por qué SÍ**:
- Vincula N8N a un entregable concreto con propósito real e inmediato
- Reproduce el patrón de aprendizaje que demostró ser efectivo en el proyecto web
- N8N visual enseña la lógica de workflows; el Code node refuerza JS; ambos se potencian
- La urgencia real (leads sin notificación automática) genera el contexto de aprendizaje correcto

---

## Decisión

**Aprender siempre tecnologías construyendo sistemas con propósito real.**

La implementación inmediata: N8N y JavaScript se aprenden en paralelo mientras construyo la automatización real del formulario de contacto (Firebase Firestore → detección de nuevo lead → notificación formateada).

**El proyecto ancla no es opcional.** Sin él, el aprendizaje pierde dirección y urgencia. Antes de tocar cualquier tecnología nueva en el roadmap, definir el proyecto ancla concreto que la justifica.

---

## Consecuencias

### Inmediatas

- N8N se instala antes de "terminar" de estudiar JS — es intencional, no un atajo
- JavaScript se practica de forma autónoma en paralelo: el Code node va a exigir escribir lógica sin IA generando el código. Esto hay que construirlo deliberadamente, no asumir que viene solo
- El primer workflow no es un ejercicio de práctica — es infraestructura de negocio real

### De largo plazo — cómo abordar cada tecnología futura

Este patrón se aplica a todo lo que viene en Fase 1, 2 y 3:

| Tecnología futura | Proyecto ancla antes de estudiarla |
|-------------------|-----------------------------------|
| Docker / VPS | Desplegar N8N self-hosted para primer cliente |
| PostgreSQL avanzado | Base de datos real de un workflow complejo |
| APIs avanzadas | Integración real que un cliente necesita |
| Python (Fase 2) | Script que resuelve un problema que JS no puede |
| FastAPI (Fase 2) | Endpoint real que N8N consume |

**Regla operativa**: Si no puedo nombrar el problema concreto que esta tecnología resuelve hoy, no es el momento de aprenderla.

### Lo que este perfil NO implica

- No significa saltarse conceptos fundamentales — significa aprender los conceptos en contexto de uso real, no antes de él
- No significa que la teoría es mala — la Fase 0 fue correcta. El pensamiento sistémico se aprende reflexionando
- No significa construir sin criterio — el ADR-000 (pensamiento sistémico) sigue siendo la base sobre la que se construye

---

## Criterios de Éxito

Esta decisión será correcta si:

- ✅ La automatización del formulario está funcionando antes del 10/03
- ✅ Aprendo más sobre N8N en 2 semanas construyendo que en 4 semanas de tutoriales
- ✅ El Code node no se convierte en un bloqueo porque JS autónomo se practica en paralelo
- ✅ Cada tecnología nueva en el roadmap tiene un proyecto ancla definido antes de empezar

---

## Nota de autoconocimiento

Este ADR es diferente a los anteriores. No documenta una decisión sobre herramientas o estrategia de mercado — documenta cómo funciona mi proceso de aprendizaje. Eso lo hace más permanente que cualquier decisión táctica.

En 18 meses, cuando llegue a FastAPI o a arquitecturas de datos complejas, este documento responde la pregunta "¿por dónde empiezo?" antes de que la haga.

La respuesta siempre es la misma: **encuentra el problema concreto primero. La tecnología viene sola después.**

---

**Autor**: Pablo Cuevas  
**Fecha**: 21 de febrero 2026  
**Revisión**: No tiene fecha de revisión — es un documento de autoconocimiento, no un plan táctico
