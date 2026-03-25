# 📋 Guía de Uso: Templates de ADR

## Tienes 2 Templates Disponibles

### 1. ADR-TEMPLATE-COMPLETO.md
**Usa cuando**:
- Decisión estratégica (afecta >3 meses)
- Múltiples alternativas complejas (3+)
- Alto costo de reversión
- Necesitas justificación formal
- Primera vez documentando un tipo de decisión

**Longitud resultante**: 2-4 páginas  
**Tiempo**: 45-90 minutos de escritura

**Ejemplo de decisiones que usan template completo**:
- "Adoptar microservicios vs monolito"
- "Elegir cloud provider (AWS vs Azure vs GCP)"
- "Definir stack tecnológico para nuevo proyecto"
- Tu ADR-000 (Pensamiento Sistémico Primero)

---

### 2. ADR-TEMPLATE-MINIMO.md
**Usa cuando**:
- Decisión táctica (afecta <3 meses)
- 2-3 alternativas claras
- Reversible con bajo/medio costo
- Solo necesitas registro rápido
- Ya tienes clara la decisión

**Longitud resultante**: 1 página  
**Tiempo**: 15-30 minutos de escritura

**Ejemplo de decisiones que usan template mínimo**:
- "Usar PostgreSQL vs MySQL para este proyecto"
- "Elegir library de testing (Jest vs Vitest)"
- "Definir naming convention para variables"
- "Usar Docker Compose vs shell scripts para setup local"

---

## Regla de Oro: Matriz de Decisión

| Impacto → | Bajo | Medio | Alto |
|-----------|------|-------|------|
| **Reversibilidad Alta** | 📝 Mínimo | 📝 Mínimo | 📄 Completo |
| **Reversibilidad Media** | 📝 Mínimo | 📄 Completo | 📄 Completo |
| **Reversibilidad Baja** | 📄 Completo | 📄 Completo | 📄📄 Completo++ |

**Leyenda**:
- 📝 Mínimo = ADR-TEMPLATE-MINIMO.md
- 📄 Completo = ADR-TEMPLATE-COMPLETO.md
- 📄📄 Completo++ = Template completo + secciones adicionales

---

## Workflow Recomendado

### Paso 1: Decide si necesitas ADR
**Pregúntate**: "¿Alguien en 6 meses preguntará 'por qué hicimos esto'?"
- **Sí** → Necesitas ADR
- **No** → Comentario en código/ticket es suficiente

### Paso 2: Elige template
**Pregúntate**: "¿Costo de revertir esta decisión en 3 meses?"
- **Alto** → Template Completo
- **Medio/Bajo** → Template Mínimo

### Paso 3: Copia el template
```bash
# Para decisión estratégica
cp ADR-TEMPLATE-COMPLETO.md ADR-004-nombre-decision.md

# Para decisión táctica
cp ADR-TEMPLATE-MINIMO.md ADR-005-nombre-decision.md
```

### Paso 4: Rellena el template
- **NO** intentes rellenar todo de una vez
- Empieza por: Contexto → Alternativas → Decisión
- Añade: Consecuencias
- Opcional: Resto de secciones según necesidad

### Paso 5: Revisa con checklist
- [ ] ¿Título claro? (Verbo + objeto)
- [ ] ¿Contexto explica POR QUÉ decidir?
- [ ] ¿Al menos 2 alternativas con pros/contras?
- [ ] ¿Decisión explicada claramente?
- [ ] ¿Consecuencias POSITIVAS y NEGATIVAS?
- [ ] ¿Puedo leerlo en <10 minutos?

### Paso 6: Marca como Aceptada
Cambia estado de "Propuesta" → "Aceptada"

---

## Mejores Prácticas de ADRs

### ✅ DO (Haz esto)

1. **Escribe en presente imperativo**
   - ✅ "Usar PostgreSQL como base de datos"
   - ❌ "Usaremos PostgreSQL como base de datos"
   - ❌ "Decisión sobre base de datos"

2. **Documenta alternativas REALES**
   - ✅ "Consideré Make y N8N, ambas viables"
   - ❌ "Consideré 10 opciones..." (si solo evaluaste 2)

3. **Sé honesto con trade-offs**
   - ✅ "Aceptamos mayor complejidad por mejor control"
   - ❌ "Esta opción es perfecta sin desventajas"

4. **Usa números cuando sea posible**
   - ✅ "Costo: €20/mes vs €100/mes"
   - ❌ "Esta opción es más barata"

5. **Documenta CUÁNDO tomaste la decisión**
   - Contexto cambia con el tiempo
   - Una decisión correcta en 2026 puede ser incorrecta en 2027

6. **Escribe para tu yo futuro**
   - Asume que olvidarás el contexto en 6 meses
   - Explica lo suficiente para entender por qué decidiste

### ❌ DON'T (Evita esto)

1. **No inventes alternativas retrospectivamente**
   - Solo documenta opciones que REALMENTE consideraste
   - Si solo evaluaste 2, está bien

2. **No escribas en pasado**
   - ❌ "Decidimos usar X porque..."
   - ✅ "Uso X porque..."

3. **No omitas las consecuencias negativas**
   - Toda decisión tiene trade-offs
   - Si no listas ninguno, no estás siendo honesto

4. **No hagas ADRs de 10 páginas para decisiones tácticas**
   - Si supera 3 páginas, probablemente son múltiples decisiones
   - Separa en varios ADRs

5. **No uses jargon innecesario**
   - Escribe para que alguien nuevo al proyecto entienda
   - Explica acrónimos la primera vez

6. **No documentes TODO como ADR**
   - Pregúntate: "¿Esto es arquitectónicamente significativo?"
   - No: → Comentario en código / Ticket / Decisión trivial

---

## Organización de ADRs en tu Proyecto

### Estructura de Carpetas Recomendada
```
/docs
  /adr
    ADR-000-pensamiento-sistemico-primero.md  (fundacional)
    ADR-001-usar-n8n-sobre-make.md
    ADR-002-postgresql-como-db-principal.md
    ADR-003-deploy-en-aws-no-gcp.md
    /templates
      ADR-TEMPLATE-COMPLETO.md
      ADR-TEMPLATE-MINIMO.md
    README.md  (índice de todos los ADRs)
```

### README.md de ADRs (Ejemplo)

```markdown
# Architecture Decision Records

Este directorio contiene todas las decisiones arquitectónicas 
significativas del proyecto.

## Índice de Decisiones

| ADR | Título | Estado | Fecha |
|-----|--------|--------|-------|
| 000 | Pensamiento Sistémico Primero | Aceptada | 2026-01-21 |
| 001 | Usar N8N sobre Make | Aceptada | 2026-01-XX |
| 002 | PostgreSQL como DB Principal | Propuesta | 2026-01-XX |
| 003 | Deploy en AWS | Deprecated | 2026-01-XX |

## Cómo Crear un Nuevo ADR

1. Copia template apropiado de /templates
2. Nombra: `ADR-XXX-titulo-descriptivo.md`
3. Rellena secciones
4. Actualiza esta tabla
```

---

## Evolución de ADRs: Qué Hacer Cuando...

### Escenario 1: Quiero cambiar una decisión ya tomada

**NO** edites el ADR original. En su lugar:

1. Crea nuevo ADR (ej: ADR-005)
2. En el nuevo ADR, sección "Contexto", menciona:
   ```
   "ADR-002 decidió usar PostgreSQL. Sin embargo, después de 6 meses,
   descubrimos que [problema]. Este ADR supersede ADR-002."
   ```
3. Actualiza ADR-002:
   - Cambia estado a "Superseded by ADR-005"
   - **NO** borres contenido

### Escenario 2: Mi decisión estaba equivocada

**Está bien**. No todos los ADRs son decisiones correctas.

1. Crea ADR nuevo explicando qué aprendiste
2. Marca el viejo como "Deprecated" o "Superseded"
3. En el nuevo ADR, documenta:
   - Qué no funcionó
   - Qué aprendiste
   - Nueva decisión basada en aprendizaje

**Ejemplo**:
```
ADR-003 decidió usar MongoDB. Después de 3 meses, descubrimos
que el 90% de nuestras queries son relacionales. Este ADR 
documenta la migración a PostgreSQL.

Lecciones: Subestimamos la importancia de relaciones en nuestros datos.
```

### Escenario 3: Nueva información invalida mi decisión

Actualiza el ADR existente con:
- Nueva sección "Actualización [fecha]"
- Qué cambió en el contexto
- Si la decisión sigue válida o necesita revisión

---

## Ejemplos Rápidos de Buenos Títulos

### ✅ Buenos Títulos
- "Usar PostgreSQL como base de datos principal"
- "Adoptar N8N para automatización de workflows"
- "Implementar autenticación JWT sobre sessions"
- "Deplegar en AWS EC2 sobre Kubernetes"
- "Escribir tests con Jest sobre Vitest"

### ❌ Malos Títulos
- "Decisión sobre base de datos" (muy vago)
- "Usaremos PostgreSQL" (futuro, no presente)
- "Por qué elegimos PostgreSQL" (pregunta, no declaración)
- "Base de datos" (no es una decisión)
- "ADR sobre PostgreSQL" (redundante - ya sabemos que es ADR)

---

## Checklist Final Antes de Publicar ADR

### Completitud
- [ ] Fecha está presente
- [ ] Estado es claro (Propuesta/Aceptada)
- [ ] Contexto explica POR QUÉ necesitas decidir
- [ ] Al menos 2 alternativas listadas con pros/contras
- [ ] Decisión explicada claramente
- [ ] Consecuencias positivas Y negativas documentadas

### Claridad
- [ ] Alguien nuevo al proyecto puede entenderlo
- [ ] No uso jargon sin explicar
- [ ] Números específicos donde es posible (costos, tiempos)
- [ ] Título describe QUÉ decidí

### Utilidad Futura
- [ ] En 6 meses, entenderé por qué decidí esto
- [ ] Documenté el contexto que podría olvidar
- [ ] Expliqué por qué otras opciones NO funcionaban

### Longitud
- [ ] Template Mínimo: ~1 página (300-500 palabras)
- [ ] Template Completo: 2-3 páginas (800-1200 palabras)
- [ ] Si es >3 páginas, ¿son múltiples decisiones que debería separar?

---

## Recursos Adicionales

### Inspiración de ADRs Reales
- [GitHub: adr/madr](https://github.com/adr/madr) - MADR template
- [AWS Prescriptive Guidance](https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/welcome.html)
- Tus propios análisis: `/outputs/Analisis_5_ADRs_Reales.md`

### Herramientas (Opcional - No Necesarias)
- [adr-tools](https://github.com/npryce/adr-tools) - CLI para gestionar ADRs
- VSCode extension: "ADR Manager"

### Lectura Recomendada
- [Michael Nygard's blog post](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions) - Original ADR concept
- [ThoughtWorks Technology Radar](https://www.thoughtworks.com/radar/techniques/lightweight-architecture-decision-records) - ADRs as practice

---

## Próximos Pasos para Pablo

### Esta Semana (Fase 0)
1. ✅ Analizaste 5 ADRs reales
2. ✅ Creaste ADR-000 (pensamiento sistémico)
3. ✅ Tienes templates listos para usar
4. 🎯 **SIGUIENTE**: Aplicar mentalidad ADR a futuras decisiones

### Próximas Decisiones que Podrían Ser ADRs
- **ADR-001**: "Usar N8N sobre Make para automatización"
- **ADR-002**: "Invertir en Claude Pro + Gemini Plus"
- **ADR-003**: "SQL antes que Python en roadmap de aprendizaje"

Cada una de estas podría usar el **template mínimo** (1 página) ya que son decisiones tácticas con alternativas claras.

---

**Última actualización**: 2026-01-21  
**Versión**: 1.0  
**Autor**: Claude (Mentor AI de Pablo)

---

*"Un ADR no tiene que ser perfecto. Tiene que ser útil."*
