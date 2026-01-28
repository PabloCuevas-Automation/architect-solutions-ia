# ADR-001: Priorizar JavaScript sobre Python en Fase 1

**Fecha**: 2026-01-24  
**Estado**: ✅ Aceptada  
**Autor**: Pablo  
**Tipo**: Estratégica (afecta timeline completo de Fase 1)  
**Contexto**: Fase 0.5 - Descubrimiento de prerrequisitos para N8N  
**Supersede**: Decisión implícita del roadmap original (Python en Fase 1)

---

## Resumen Ejecutivo

En el contexto de preparación para usar N8N como herramienta de monetización rápida (objetivo: facturar en semanas 8-10), enfrentando el descubrimiento de que **N8N usa JavaScript nativamente** (no Python), y teniendo prueba de JavaScript el 5 de febrero del curso de Trento, **decidí priorizar JavaScript sobre Python en Fase 1**, aceptando posponer Python a Fase 2 y retrasar proyectos dependientes de Python (como RAG básico) en 2-3 meses.

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

1. **Prueba de JavaScript el 5 de febrero (12 días)**
   - Curso de desarrollo web del gobierno de Trento
   - Evaluación sobre: HTML, CSS, JavaScript básico
   - Ya invertí tiempo en este curso
   - No puedo postponer o cancelar la prueba

2. **Proyecto final del curso: Sitio web personal (15-20 febrero)**
   - Perfectamente alineado con necesidad de negocio
   - Sitio web para promocionar servicios de automatización
   - No es tiempo "perdido" - es infraestructura de marketing

### Contexto de Negocio (Input del Profesor)

Mi profesor del curso validó el enfoque de N8N con plan go-to-market concreto:

**Fase 1 (2-3 semanas)**: Curso N8N → crear 2-4 automatizaciones  
**Fase 2 (1-3 meses)**: 
- Dar gratis a 3 empresas grandes (social proof)
- Crear sitio web (ya tengo del curso)
- Artículo en prensa local (Adige)

**Fase 3**: Marketing continuo + networking  
**Fase 4**: Pricing model (€100/mes subscription hasta €5.000-10.000 custom)

**Validación clave**: Mercado local (Rovereto/Trento) tiene **poca competencia** en automatización con N8N.

### Prerrequisitos Identificados para N8N

Para usar N8N productivamente (no solo demo), necesito:

**Nivel 1 - JavaScript Básico**:
- Variables (`let`, `const`, `var`)
- Tipos de datos (string, number, boolean, null, undefined)
- Operadores y condicionales (`if/else`, operadores lógicos)
- Funciones (declaración, arrow functions, parámetros)
- Ciclos (`for`, `while`, `forEach`)

**Nivel 2 - JavaScript Intermedio**:
- Arrays y métodos (`.map()`, `.filter()`, `.reduce()`, `.find()`)
- Objetos (creación, acceso, destructuring)
- JSON (parse, stringify, navegación de objetos complejos)

**Nivel 3 - APIs y HTTP**:
- Métodos HTTP (GET, POST, PUT, DELETE, PATCH)
- Headers, Body, Response, Status Codes
- Autenticación (API Keys, Bearer tokens, OAuth conceptual)

**Tiempo estimado total**: ~20-25 horas de estudio antes de ser productivo en N8N.

### Por Qué Esta Decisión Es Importante Ahora

1. **Tengo prueba en 12 días** - ya estoy estudiando JavaScript de todos modos
2. **Ventana de oportunidad local** - mercado pequeño, first-mover advantage
3. **Sinergia única temporal** - curso + negocio + timeline alineados
4. **Monetización en riesgo** - objetivo €1.5k/mes en mes 6 requiere empezar N8N pronto

Si pospongo esta decisión:
- Estudio JS para la prueba (forzado) pero luego lo "abandono" para estudiar Python
- Pierdo 2-3 semanas de ventana competitiva local
- Llego a N8N en mes 3-4 en lugar de mes 2-3 (retrasa monetización)

---

## Alternativas Consideradas

### Opción A: Seguir Roadmap Original (Python + SQL 70%)

**Descripción**: Mantener distribución original del roadmap: SQL 30%, Python 20%, N8N 25%. Estudiar Python como lenguaje principal de Fase 1, usar JavaScript solo mínimamente para pasar prueba del curso.

**Pros**:
- ✅ Alineado con roadmap diseñado y validado (triangulado entre 3 AIs)
- ✅ Python es más universal para Fases 2-4 (FastAPI, RAG, MLOps)
- ✅ Ecosistema Python para IA superior (LangChain, Pandas, NumPy)
- ✅ No necesito "aprender otro lenguaje" después - continuidad
- ✅ Comunidad más grande para Python + IA/ML

**Contras**:
- ❌ **Ignora realidad técnica**: N8N code nodes usan JavaScript, NO Python
- ❌ **Desperdicia sinergia**: Ya estudio JS (prueba 5/02), ignorar eso es ineficiente
- ❌ **Doble trabajo después**: Tendría que aprender JS para N8N avanzado de todos modos
- ❌ **Monetización más lenta**: Python tarda 3-4 meses en ser vendible
- ❌ **Sobrecarga cognitiva**: JS (curso forzado) + Python (roadmap) simultáneos
- ❌ **Pierde ventana local**: Mercado Rovereto/Trento puede saturarse

**Por qué NO**: Ignora realidad técnica de N8N y desperdicia sinergia única (curso + mercado + timeline). Prioriza "seguir el plan" sobre adaptar al contexto real.

---

### Opción B: JavaScript + Python en Paralelo (50%/50%)

**Descripción**: Estudiar ambos lenguajes simultáneamente. JavaScript para N8N, Python para mantener alineación con roadmap Fase 2-4. Distribución: SQL 20%, JS 25%, Python 25%, N8N 20%.

**Pros**:
- ✅ No "pierdo" Python para el futuro
- ✅ Preparado para N8N (JS) Y para Fase 2 (Python)
- ✅ Flexibilidad máxima - puedo usar cualquiera de los dos
- ✅ Roadmap original no se "rompe" - solo se ajusta

**Contras**:
- ❌ **Sobrecarga cognitiva brutal**: Dos sintaxis, dos ecosistemas, dos sets de errores
- ❌ **Progreso más lento en ambos**: Mejor dominar uno que ser mediocre en dos
- ❌ **Confusión garantizada**: `.map()` en JS vs list comprehensions en Python, `===` vs `==`
- ❌ **Dispersión de foco**: 21h/semana divididas en demasiadas direcciones
- ❌ **No resuelve problema de timeline**: Sigo llegando tarde a N8N productivo

**Por qué NO**: Con solo 21h/semana disponibles, dividir entre dos lenguajes me haría mediocre en ambos. "Jack of all trades, master of none." Mejor dominar JavaScript profundamente (2 meses) que conocer superficialmente dos lenguajes.

---

### Opción C: Priorizar JavaScript, Posponer Python (ELEGIDA) ⭐

**Descripción**: Estudiar JavaScript intensivamente hasta dominar N8N productivamente (~2 meses), luego transicionar a Python en Fase 2. Distribución Fase 1 ajustada: SQL 20%, JavaScript 30-35%, N8N 25%, Proyectos 15%, Compliance 10%.

**Pros**:
- ✅ **Capitaliza sinergia única**: Curso JS + prueba 5/02 + proyecto web + N8N = todo alineado
- ✅ **Eficaz en N8N inmediatamente**: JavaScript es el lenguaje nativo
- ✅ **Un lenguaje a la vez**: Progreso profundo, no superficial
- ✅ **Monetización más rápida**: N8N productivo en semana 10-12 vs semana 16-18
- ✅ **Valida mercado local**: 3 automatizaciones gratis → feedback real temprano
- ✅ **Sitio web ready**: Proyecto del curso = infraestructura de marketing gratuita
- ✅ **First-mover advantage**: Actúo rápido en mercado con poca competencia

**Contras**:
- ❌ **Python se retrasa 2-3 meses**: FastAPI en Fase 2 comienza más tarde
- ❌ **RAG básico pospuesto**: Proyecto que usa Python no será en Fase 1
- ❌ **Desviación del roadmap original**: Requiere ajuste mental de "no seguí el plan"
- ❌ **Posible confusión futura**: Al cambiar a Python, tendré que "cambiar chip"
- ❌ **Riesgo de dependencia JS**: Si me gusta mucho JS, podría resistir aprender Python después

**Por qué SÍ**: 
Maximiza aprovechamiento de contexto único (curso + prueba + mercado local + timeline), permite monetización rápida que valida el camino, reduce sobrecarga cognitiva al enfocarse en un lenguaje a la vez, y la "deuda técnica" (aprender Python después) es aceptable porque Python será necesario en Fase 2 de todos modos.

---

## Decisión

**He decidido**: Priorizar JavaScript sobre Python durante Fase 1 completa (meses 1-6), estudiar JavaScript intensivamente hasta dominar N8N productivamente, y posponer Python formalmente hasta inicio de Fase 2 (mes 7).

### Razonamiento Detallado

**Desde perspectiva técnica**:

N8N usa JavaScript nativamente. Los code nodes, las expresiones `{{ item.json.field }}`, y toda la transformación de datos dentro de N8N se hace con JavaScript. Python solo puede integrarse indirectamente mediante:
- Llamadas HTTP a scripts Python externos
- Webhooks que ejecutan Python en servidor separado
- APIs que escribiría en Python (pero esto es Fase 2, no Fase 1)

Si estudio Python primero, cuando llegue a N8N (semana 10-12) tendré que aprender JavaScript de todos modos para usar N8N eficazmente. Eso significa:
- Aprender Python (semanas 1-10)
- "Pausar" ese aprendizaje
- Aprender JavaScript (semanas 11-14)
- Recién ahí ser productivo en N8N

Eso es **desperdiciar 4-6 semanas** en un camino subóptimo.

**Desde perspectiva de objetivos de negocio**:

Mi objetivo en Fase 1 es **€1.5-2.5k/mes en mes 6**. Para lograrlo necesito:
1. Empezar N8N productivamente lo antes posible (semana 10-12 objetivo)
2. Crear 2-4 automatizaciones para portfolio (semanas 12-16)
3. Dar gratis a 3 empresas grandes (social proof, semanas 14-18)
4. Activar pipeline comercial (mes 4-5)
5. Facturar consistentemente (mes 6)

**Cada semana cuenta.** Si retraso N8N 3-4 semanas por estudiar Python primero, comprometo el timeline completo de monetización.

**Desde perspectiva de sinergia única**:

Tengo convergencia temporal de 3 factores que no volverán a alinearse:

1. **Curso de Trento con prueba 5/02**: Estoy *forzado* a estudiar JavaScript de todos modos
2. **Proyecto web del curso (15-20/02)**: Necesito sitio para negocio, el curso me lo regala
3. **Mercado local pequeño**: Rovereto/Trento tiene poca competencia en N8N - ventana temporal

Si ignoro esta sinergia y estudio Python:
- Estudio JS "a medias" solo para aprobar prueba (desperdicio)
- Pierdo oportunidad de sitio web profesional gratis del curso
- Mercado local puede saturarse mientras aprendo Python

**Factor decisivo que hizo que esta opción ganara**:

El descubrimiento de que **N8N usa JavaScript nativamente** cambió todo. No es cuestión de "preferencia personal" o "qué lenguaje me gusta más". Es que **técnicamente, JavaScript es el camino correcto** para N8N, y tengo sinergia única que hace este camino más eficiente ahora que en cualquier otro momento.

---

## Consecuencias

### Impactos Positivos ✅

1. **Monetización acelerada (crítico)**
   - N8N productivo en semana 10-12 (vs semana 16-18 con Python primero)
   - Primeras automatizaciones vendibles en mes 3 (vs mes 4-5)
   - Mayor probabilidad de lograr €1.5k/mes en mes 6
   - Validación temprana de mercado (¿hay demanda real?)

2. **Aprovechamiento máximo del curso de Trento**
   - Prueba 5/02: estudio intensivo JS tiene propósito dual (aprobar + N8N)
   - Proyecto web: infraestructura de marketing gratuita profesional
   - No es tiempo "robado" del roadmap - es sinergia
   - Certificado del gobierno como bonus (credibilidad local)

3. **Menor sobrecarga cognitiva**
   - Un lenguaje a la vez: profundidad sobre amplitud
   - No confundo sintaxis (JS vs Python)
   - Menos "context switching" mental
   - Más fácil alcanzar flow state en estudio

4. **Eficacia inmediata en N8N**
   - Entiendo nativamente lo que hace cada code node
   - Puedo debuggear expresiones `{{ }}` sin traducir mentalmente
   - Modificar workflows complejos sin googlear sintaxis
   - Automatizaciones más robustas desde el inicio

5. **First-mover advantage en mercado local**
   - Rovereto/Trento tiene poca competencia en automatización
   - Actuar rápido = capitalizar ventana temporal
   - 2-3 meses de ventaja sobre competencia futura
   - Posicionamiento temprano como "el experto local en automatización"

6. **Timeline claro y realista**
   - Hasta 5/02: JS básico (50% tiempo)
   - 6-20/02: JS intermedio + proyecto web (40% tiempo)
   - 21-28/02: JSON + APIs (30% tiempo)
   - Marzo: N8N productivo (con bases sólidas)

### Impactos Negativos ⚠️

1. **Python pospuesto 2-3 meses**
   - Fase 2 (FastAPI) comienza más tarde: mes 8-9 vs mes 7
   - Necesitaré "ponerte al día" en Python cuando llegue el momento
   - Posible resistencia mental ("ya domino JS, ¿para qué Python?")
   - Mitigación: Python es *necesario* en Fase 2-4, no opcional

2. **Proyectos que usan Python se retrasan**
   - RAG básico (Fase 1, mes 5-6): pospuesto a Fase 2
   - Web scraping con BeautifulSoup: pospuesto
   - Scripts de procesamiento de datos complejos: limitados
   - Mitigación: Estos proyectos no son críticos para monetización temprana

3. **Desviación del roadmap original**
   - El roadmap fue diseñado con Python en Fase 1
   - Ajustar mentalmente que "no seguí el plan exacto"
   - Posible inseguridad: "¿Tomé la decisión correcta al desviarme?"
   - Mitigación: ADRs documentan *por qué* el contexto justifica cambio

4. **Ecosistema Python para IA se retrasa**
   - LangChain, LlamaIndex, Pandas: no los veré hasta Fase 2
   - OpenAI/Claude Python SDKs: uso limitado en Fase 1
   - Integraciones IA avanzadas: postponed
   - Mitigación: JavaScript también tiene SDKs de OpenAI/Claude (menos maduros pero funcionales)

5. **Posible "pérdida" si N8N no monetiza como esperado**
   - Si mercado local no responde como predijo profesor
   - Si automatización no tiene demanda real
   - Habría invertido 2-3 meses en JS que podría haber sido Python
   - Mitigación: Validación temprana (mes 3-4) permite pivotar si necesario

### Trade-offs Clave

**Sacrifico**:
- Continuidad con roadmap original (Python en Fase 1)
- Tiempo de preparación para Fase 2-4 (Python será necesario)
- Ecosistema IA/ML temprano (Pandas, NumPy, ML libraries)

**Para ganar**:
- Monetización acelerada (2-3 meses más temprano)
- Sinergia única temporal (curso + mercado + timeline)
- Menor sobrecarga cognitiva (un lenguaje a la vez)
- Eficacia inmediata en N8N (lenguaje nativo)
- First-mover advantage en mercado local

> **Trade-off fundamental**: Sacrifico "preparación perfecta para el futuro" (Python desde día 1) para ganar "validación rápida de mercado" (N8N productivo en semana 10-12).

---

## Riesgos Identificados

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|------------|
| **No paso prueba JS 5/02** | Baja | Medio | Ya tengo conocimientos básicos; 50% tiempo dedicado es suficiente |
| **Me "enamoro" de JS y resisto aprender Python después** | Media | Alto | Compromiso explícito: Python empieza mes 7 sin excepción. ADR-002 documentará esa transición. |
| **Mercado local no responde como espera profesor** | Media | Alto | Validación temprana (mes 3-4 con 3 empresas gratis). Si no funciona, pivotar antes de mes 6. |
| **N8N cambia a usar Python** (improbable) | Muy Baja | Bajo | N8N es maduro, poco probable cambio radical. Si pasa, JavaScript sigue siendo útil. |
| **Pierdo momentum en SQL** | Media | Medio | SQL sigue siendo 20% de tiempo (4h/semana). No desaparece, solo se ajusta proporción. |

---

## Criterios de Validación

### Cómo Sabré Si Esta Fue Buena Decisión

**Corto plazo (5 de febrero - Semana 2)**:
- ✅ **Aprobé prueba de JavaScript** con nota decente (no solo "pasar raspando")
- ✅ Puedo escribir funciones JS sin googlear sintaxis básica

**Medio plazo (Mes 3 - Semana 12)**:
- ✅ **Tengo 2-4 automatizaciones N8N funcionales** (no demos, reales)
- ✅ Entiendo code nodes en N8N sin copiar/pegar de Stack Overflow
- ✅ Puedo modificar expresiones `{{ }}` con confianza
- ✅ **He dado gratis a 1-2 empresas** (comenzando validación de mercado)

**Largo plazo (Mes 6 - Final Fase 1)**:
- ✅ **Facturo €1.5k+ por mes** usando N8N como herramienta principal
- ✅ Sitio web del curso está siendo usado para negocio (no está abandonado)
- ✅ He recibido 2-3 contactos orgánicos por automatización (mercado local responde)
- ✅ **Transición a Python en Fase 2 no se siente "comenzar de cero"** (tengo fundamentos de programación por JS)

### Señales de Alerta (cuándo reconsiderar)

Si observamos en **Mes 4-5**:
- 🚨 Cero ingresos con N8N después de 2 meses productivo
- 🚨 Mercado local saturado o no responde
- 🚨 Clientes piden específicamente Python (no JS/N8N)
- 🚨 Me cuesta entender N8N incluso con JS (señal de problema mayor)

Entonces debemos **reconsiderar** y potencialmente:
- Acelerar transición a Python (no esperar mes 7)
- Pivotar de N8N a otra estrategia
- Crear ADR-00X documentando el cambio

---

## Implementación

### Timeline Unificado

| Fecha | Hito | Resultado Esperado |
|-------|------|-------------------|
| **24 Ene → 5 Feb** | JS intensivo (50% tiempo) | Aprobar prueba + base sólida para N8N |
| **6 Feb → 20 Feb** | Proyecto web + JS avanzado | Sitio profesional listo + arrays/objetos dominados |
| **21 Feb → 28 Feb** | JSON + APIs | Prerrequisitos N8N 100% completos |
| **Marzo →** | **EMPEZAR N8N** | Productivo con bases sólidas |

### Ajustes Requeridos en Roadmap

**Fase 1 - Nueva distribución semanal (21h/semana)**:

| Actividad | Antes (Original) | Ahora (Ajustado) | Diferencia |
|-----------|------------------|------------------|------------|
| SQL | 6-8h (30%) | 4-5h (20%) | ↓ 2-3h |
| **JavaScript** | 0h (0%) | **6-7h (30-35%)** | ↑ 6-7h |
| Python | 4-5h (20%) | 0h (0%) | ↓ 4-5h |
| N8N | 5-6h (25%) | 5-6h (25%) | = |
| Proyectos | 3-4h (15%) | 3h (15%) | ↓ 1h |
| Compliance | 1-2h (5%) | 2h (10%) | ↑ 1h |

**Proyectos Fase 1 - Ajustes**:
- ❌ Eliminar: RAG básico (requiere Python)
- ❌ Posponer: Web scraping con BeautifulSoup
- ✅ Mantener: Pipeline de datos (JavaScript puede hacerlo)
- ✅ Añadir: 3 automatizaciones gratis para empresas (según plan profesor)

**Métricas de validación - Nuevas**:
- ✅ Añadir: "Primera automatización N8N funcional antes de mes 3"
- ✅ Añadir: "Primer ingreso con N8N antes de mes 4"
- ✅ Mantener: "€1.5k/mes antes de mes 6"

**Mantener sin cambios**:
- ✅ Pensamiento arquitectónico (ADRs)
- ✅ GDPR/Compliance awareness
- ✅ Evitar vendor lock-in
- ✅ Documentación de decisiones

### Pasos Siguientes Inmediatos

**Esta semana (hasta 5/02)**:
- [x] Documentar decisión en ADR-001
- [ ] Dedicar 50% tiempo disponible (~10h) a JavaScript básico
- [ ] Repasar: variables, condicionales, funciones, ciclos
- [ ] Preparar prueba (practicar ejercicios del curso)

**Semanas 2-3 (6/02 - 20/02)**:
- [ ] ✅ Aprobar prueba JavaScript 5/02
- [ ] Completar proyecto web del curso (sitio profesional para negocio)
- [ ] Estudiar arrays y métodos (`.map()`, `.filter()`, `.find()`, `.reduce()`)
- [ ] Dominar objetos (creación, acceso, destructuring)

**Semanas 4-5 (21/02 - 28/02)**:
- [ ] Dominar JSON (parse, stringify, navegación profunda de objetos nested)
- [ ] APIs: métodos HTTP, status codes, headers
- [ ] Práctica con APIs públicas (fetch, async/await básico)
- [ ] Autenticación básica (API keys, Bearer tokens)

**Marzo (Mes 3 - Semana 9+)**:
- [ ] **EMPEZAR N8N** con todos los prerrequisitos completos
- [ ] Crear primera automatización compleja (5+ nodes, con code nodes)
- [ ] Comenzar plan del profesor: 2-4 automatizaciones portfolio
- [ ] Identificar 3 empresas grandes para dar servicio gratis

---

## Relación con ADR-000

Este ADR es una **aplicación práctica directa** de ADR-000:

**ADR-000 decidió**: "Pensamiento sistémico > Skills verticales"

**ADR-001 aplica ese principio**: 
- En lugar de "seguir el roadmap ciegamente" (ejecutor mindset)
- Analicé contexto real (N8N usa JS, tengo prueba 5/02, mercado local)
- **Decidí adaptar** basado en información nueva
- Documenté el razonamiento (arquitecto mindset)

**Lección arquitectónica**:
> Los buenos arquitectos adaptan planes basados en nueva información. Los malos arquitectos siguen planes obsoletos por inercia.

Esta decisión demuestra que entendí ADR-000: priorizo **tomar la decisión correcta para el contexto** sobre "seguir el plan original".

---

## Referencias y Contexto Adicional

### Documentos Relacionados
- **ADR-000**: Pensamiento Sistémico antes que Skills Verticales (decisión "madre")
- **Roadmap original**: `/docs/ROADMAP_ARCHITECT_SOLUTION_IA_COMPLETO.pdf`
- **Nota de recalibración**: Documento markdown con descubrimiento N8N + plan profesor (24/01/2026)

### Fuentes de la Decisión
- **Conversación con mentor (Claude)**: 24/01 - Análisis de prerrequisitos N8N
- **Input del profesor del curso**: Plan go-to-market validado para mercado local
- **Documentación N8N**: Confirmación de que code nodes usan JavaScript
- **Contexto personal**: Prueba 5/02 como restricción temporal

### Supuestos Clave
1. N8N seguirá usando JavaScript como lenguaje nativo
2. Mercado local Rovereto/Trento tiene baja competencia (validar en mes 3-4)
3. JavaScript aprendido ahora facilitará Python después (transferencia de conceptos)
4. Sitio web del curso será útil para negocio (no quedará abandonado)

---

## Historial de Cambios

| Fecha | Cambio | Razón |
|-------|--------|-------|
| 2026-01-24 | ADR-001 creado en estado "Propuesta" | Descubrimiento de prerrequisitos N8N + prueba JS 5/02 |
| 2026-01-24 | Cambiado a "Aceptada" después de evaluación | Análisis completo de 3 alternativas, consecuencias claras, trade-offs aceptables |

---

## Notas Adicionales

### Sobre Transición Futura a Python (Mes 7)

Cuando llegue el momento de aprender Python (inicio Fase 2), esta decisión me habrá dado:

**Ventajas transferibles de JavaScript**:
- ✅ Conceptos de programación ya internalizados (variables, funciones, ciclos, condicionales)
- ✅ Entendimiento de async/await (JavaScript → Python async es muy similar)
- ✅ Experiencia con JSON (idéntico en ambos lenguajes)
- ✅ Experiencia con APIs y HTTP (transferible 100%)
- ✅ Manejo de errores (try/catch en JS → try/except en Python)
- ✅ Debugging mindset (leer errores, googlear soluciones)

**Lo que será diferente (y deberé aprender)**:
- ❌ Sintaxis Python (indentación significativa, no `{}`)
- ❌ List comprehensions (único de Python, muy poderoso)
- ❌ Type hints (opcional en Python, no existe en JS tradicional)
- ❌ Pandas/NumPy (sin equivalente directo en JS para data science)

**Estimación**: Con JavaScript dominado, aprender Python tomará ~3-4 semanas (vs 6-8 semanas desde cero).

### Sobre la "Deuda Técnica" Aceptada

**Deuda**: Python no aprendido en Fase 1

**Por qué es deuda aceptable**:
1. Python no es crítico para monetización Fase 1 (N8N + JS son suficientes)
2. Fase 2 *requiere* Python de todos modos (FastAPI es Python nativo)
3. Aprender Python en mes 7 con fundamentos de JS es más rápido que Python desde cero en mes 1
4. El "interés" de esta deuda (tiempo extra después) es menor que el "costo" de aprenderlo ahora (retraso en monetización)

**Cuándo se "paga" la deuda**: Mes 7, primeras 3-4 semanas de Fase 2.

**Cómo se paga**: Dedicar ~6-8h/semana por 3-4 semanas a Python intensivo antes de FastAPI.

### Comparación de Lenguajes para Contexto

#### JavaScript para N8N (Fase 1)

**Fortalezas**:
- ✅ Lenguaje nativo de N8N (code nodes, expresiones)
- ✅ Excelente para transformación de datos en workflows
- ✅ Async nativo (manejo de APIs múltiples simultáneas)
- ✅ JSON nativo (es *JavaScript Object Notation*)
- ✅ Ecosistema web maduro (fetch, axios, express si necesario)

**Limitaciones**:
- ❌ Ecosistema IA/ML muy inferior a Python
- ❌ Menos libraries para data science pesado
- ❌ TypeScript (si se usa) añade complejidad

**Uso principal en mi roadmap**: Automatización con N8N (Fase 1), integraciones web, manipulación de datos en workflows.

---

#### Python para Backend/IA (Fase 2+)

**Fortalezas**:
- ✅ THE lenguaje para IA/ML (LangChain, PyTorch, TensorFlow)
- ✅ FastAPI para APIs modernas (Fase 2)
- ✅ Pandas/NumPy para data processing pesado
- ✅ Sintaxis clara y legible ("executable pseudocode")
- ✅ Comunidad IA/ML gigantesca

**Limitaciones en N8N**:
- ❌ Solo uso indirecto (llamadas HTTP)
- ❌ No nativo en code nodes
- ❌ Requiere setup externo (servidor/API separada)

**Uso principal en mi roadmap**: Backend APIs (Fase 2), RAG systems (Fase 3), ML/AI (Fase 3-4), data processing pesado.

---

### Conclusión del Análisis

**No es "JavaScript vs Python" como competencia.**  
**Es "JavaScript AHORA para N8N" y "Python DESPUÉS para backend/IA".**

Ambos lenguajes tienen su lugar en el roadmap completo. Esta decisión solo ajusta *cuándo* aprendo cada uno basado en:
- ✅ Necesidad técnica inmediata (N8N necesita JS)
- ✅ Sinergia temporal única (curso + prueba + proyecto web)
- ✅ Objetivos de monetización (N8N rápido = ingresos tempranos = validación)
- ✅ Reducción de sobrecarga cognitiva (un lenguaje profundo > dos superficiales)

---

**Firmado**: Pablo  
**Fecha de compromiso**: 24 de enero, 2026  
**Próxima revisión**: 5 de febrero (post-prueba JS) y 28 de febrero (pre-N8N)

---

**FIN DEL ADR-001**

*Este ADR documenta mi primera adaptación estratégica del roadmap basada en descubrimiento técnico y contexto único. Demuestra aplicación práctica de ADR-000: adaptar planes cuando nueva información lo justifica.*

---

## 📚 Elementos de Markdown Usados en Este ADR (Referencia de Aprendizaje)

Este ADR usa las siguientes técnicas de Markdown - apúntalas para referencia:

```markdown
# Título nivel 1 (uno solo por documento)
## Título nivel 2 (secciones principales)
### Título nivel 3 (subsecciones)

**Negrita** con doble asterisco
*Cursiva* con un asterisco

- Lista con guión
  - Sub-item con indentación
- ✅ ❌ ⚠️ 🚨 ⭐ Emojis para claridad

1. Lista numerada
2. Automáticamente continúa numeración

| Tablas | Con | Pipes |
|--------|-----|-------|
| Dato 1 | X/5 | Comentario |

> Citas importantes con >
> Segunda línea de cita

[Links a otros archivos](./ADR-000.md)
[Links externos](https://n8n.io)

- [ ] Checkbox vacío
- [x] Checkbox marcado (con x)

Línea horizontal con ---

`Código inline` con backticks simples

<!-- Comentarios que no se ven en preview -->
```

**Usa este ADR como referencia** cuando escribas futuros ADRs - tiene todos los elementos que necesitarás. 🎓
