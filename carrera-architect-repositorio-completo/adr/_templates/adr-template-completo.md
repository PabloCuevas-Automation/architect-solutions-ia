# ADR-XXX: [Título Descriptivo en Presente Imperativo]

**Fecha**: YYYY-MM-DD  
**Estado**: 🟡 Propuesta | ✅ Aceptada | ⚠️ Deprecated | 🔄 Superseded by ADR-YYY  
**Autor**: [Tu nombre]  
**Decisores**: [Quién participó en la decisión - puede ser solo tú]  
**Tipo**: Estratégica | Táctica | Organizacional | Técnica  

---

## Resumen Ejecutivo (Opcional - solo para decisiones grandes)

> [1-2 frases que resuman: Contexto → Decisión → Impacto principal]
>
> Ejemplo: "En el contexto de necesitar automatizar workflows rápidamente, enfrentando la elección entre N8N y Make, decidí usar N8N self-hosted para lograr mayor control y menor costo, aceptando mayor complejidad de setup inicial."

---

## Contexto

### Situación Actual
[¿Qué está pasando que requiere una decisión?]
- Estado actual del sistema/proyecto
- Problema específico que enfrentas
- Restricciones conocidas (tiempo, dinero, habilidades)

### Por Qué Esta Decisión Es Importante
[¿Por qué esto es arquitectónicamente significativo?]
- ¿Qué impacta? (código, costos, equipo, timeline)
- ¿Qué tan reversible es?
- ¿Qué pasa si NO decidimos nada?

### Factores Clave
[Bullets de factores que influyen en la decisión]
- Factor 1 (ej: Presupuesto limitado a €X/mes)
- Factor 2 (ej: Necesito resultados en 6 semanas)
- Factor 3 (ej: Solo 10h/semana disponibles)

---

## Alternativas Consideradas

### Opción 1: [Nombre de la alternativa]

**Descripción breve**: [1-2 líneas de qué implica esta opción]

**Pros**:
- ✅ Ventaja 1
- ✅ Ventaja 2
- ✅ Ventaja 3

**Contras**:
- ❌ Desventaja 1
- ❌ Desventaja 2
- ❌ Desventaja 3

**Criterios de evaluación**:
| Criterio | Rating (1-5) | Notas |
|----------|-------------|-------|
| Costo | X/5 | [Comentario] |
| Complejidad | X/5 | [Comentario] |
| Tiempo de implementación | X/5 | [Comentario] |
| Mantenibilidad | X/5 | [Comentario] |

---

### Opción 2: [Nombre de la alternativa]

[Repetir estructura de Opción 1]

---

### Opción 3: [Nombre de la alternativa] - ELEGIDA ⭐

[Repetir estructura pero marcar como elegida]

---

### Opciones Descartadas Rápidamente

[Si hubo opciones que ni siquiera evaluaste en detalle, lista por qué]
- **Opción X**: Descartada porque [razón breve]
- **Opción Y**: Fuera de alcance porque [razón breve]

---

## Decisión

### Qué Decidimos
[Declara la decisión en 1-2 frases claras y directas]

Ejemplo: "Elegimos usar N8N self-hosted como nuestra herramienta principal de automatización para los próximos 6 meses de Fase 1."

### Por Qué Esta Opción

[2-4 párrafos explicando el razonamiento. Organiza por categorías si ayuda:]

**Desde perspectiva técnica**:
[Por qué técnicamente es la mejor opción para el problema]

**Desde perspectiva de negocio/objetivos**:
[Cómo se alinea con metas de tiempo/dinero/impacto]

**Desde perspectiva de equipo/habilidades**:
[Por qué es factible con las habilidades actuales]

**Factor decisivo**:
[¿Cuál fue EL factor que hizo que esta opción ganara?]

---

## Consecuencias

### Impactos Positivos ✅

1. **[Beneficio 1]**: [Explicación breve de cómo este beneficio se materializa]
2. **[Beneficio 2]**: [Explicación]
3. **[Beneficio 3]**: [Explicación]
4. **[Beneficio 4]**: [Explicación]
5. **[Beneficio 5]**: [Explicación]

### Impactos Negativos ⚠️

1. **[Costo/limitación 1]**: [Explicación + mitigación si existe]
2. **[Costo/limitación 2]**: [Explicación + mitigación]
3. **[Costo/limitación 3]**: [Explicación + mitigación]
4. **[Costo/limitación 4]**: [Explicación + mitigación]
5. **[Costo/limitación 5]**: [Explicación + mitigación]

### Trade-offs Clave

[Resume los trade-offs más importantes en formato "Sacrificamos X para ganar Y"]

Ejemplo:
> Sacrificamos **simplicidad de setup** (Make es más fácil) para ganar **control sobre infraestructura y costos menores** (N8N self-hosted).

---

## Riesgos Identificados

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|-------------|---------|------------|
| [Riesgo 1] | Alta/Media/Baja | Alto/Medio/Bajo | [Cómo mitigamos] |
| [Riesgo 2] | Alta/Media/Baja | Alto/Medio/Bajo | [Cómo mitigamos] |
| [Riesgo 3] | Alta/Media/Baja | Alto/Medio/Bajo | [Cómo mitigamos] |

---

## Criterios de Validación

### Cómo Sabremos Si Esta Fue Buena Decisión

**Corto plazo** (primeras [X] semanas):
- ✅ Indicador 1: [Métrica concreta]
- ✅ Indicador 2: [Métrica concreta]

**Medio plazo** ([X] meses):
- ✅ Indicador 3: [Métrica concreta]
- ✅ Indicador 4: [Métrica concreta]

**Largo plazo** ([X] meses/años):
- ✅ Indicador 5: [Métrica concreta]

### Señales de Alerta (cuándo reconsiderar)

Si observamos:
- 🚨 [Señal de alerta 1]
- 🚨 [Señal de alerta 2]
- 🚨 [Señal de alerta 3]

Entonces debemos **reconsiderar esta decisión** y potencialmente crear un nuevo ADR que supersede este.

---

## Implementación (Opcional - solo si relevante)

### Pasos Siguientes Inmediatos
1. [Acción 1 - Responsable - Deadline]
2. [Acción 2 - Responsable - Deadline]
3. [Acción 3 - Responsable - Deadline]

### Dependencias
- Necesitamos [X] antes de implementar
- Bloqueado por [Y] hasta [fecha]

### Timeline Estimado
- Semana 1: [Hitos]
- Semana 2: [Hitos]
- Semana X: [Completado]

---

## Referencias y Contexto Adicional

### Documentos Relacionados
- [Link o path a documento relacionado 1]
- [Link o path a documento relacionado 2]

### ADRs Relacionados
- ADR-XXX: [Título] - [Cómo se relaciona]
- Supersede a: ADR-YYY
- Supersedido por: ADR-ZZZ (cuando aplique)

### Fuentes Externas
- [Artículo/documentación que influyó en la decisión]
- [Blog post relevante]
- [Conversación con experto]

### Discusiones Importantes
[Si hubo debates significativos, resúmelos aquí]
- Persona A argumentó que [X] pero decidimos que [Y] porque [Z]

---

## Historial de Cambios

| Fecha | Cambio | Razón |
|-------|--------|-------|
| YYYY-MM-DD | Creado ADR en estado Propuesta | [Razón] |
| YYYY-MM-DD | Cambiado a Aceptada | [Consenso alcanzado] |
| YYYY-MM-DD | Actualización: [qué cambió] | [Por qué] |

---

## Notas Adicionales (Opcional)

[Cualquier contexto adicional que no encaje arriba pero que sea útil]
- Lecciones aprendidas durante la decisión
- Supuestos que estamos haciendo
- Áreas de incertidumbre
- Experimentos que queremos hacer

---

**Firmado**: [Tu nombre]  
**Fecha de compromiso**: [Fecha]  

---

## Instrucciones de Uso de Este Template

### Secciones Obligatorias
- ✅ Título, Fecha, Estado
- ✅ Contexto
- ✅ Alternativas (mínimo 2)
- ✅ Decisión
- ✅ Consecuencias

### Secciones Opcionales (Usa según necesidad)
- ⚪ Resumen Ejecutivo (solo decisiones grandes)
- ⚪ Criterios de evaluación en tabla (solo si comparas muchas opciones)
- ⚪ Riesgos identificados (solo si hay riesgos significativos)
- ⚪ Implementación (solo si es decisión que requiere pasos de acción)
- ⚪ Notas adicionales (solo si hay contexto importante que no encaja arriba)

### Longitud Target
- **Con todas las secciones**: 3-4 páginas
- **Solo secciones obligatorias**: 1-2 páginas

### Cuándo Usar Este Template
- ✅ Decisiones estratégicas que afectan múltiples meses
- ✅ Decisiones con múltiples alternativas complejas
- ✅ Decisiones que requieren justificación formal
- ✅ Primera vez que usas ADRs en un proyecto

---

*Template versión 1.0 - Creado: 2026-01-21*
