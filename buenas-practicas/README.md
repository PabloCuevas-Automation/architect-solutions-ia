# Buenas Prácticas - Metodología de Trabajo

**Autor:** Pablo  
**Última actualización:** 28 enero 2026  
**Propósito:** Principios transversales que aplico en TODOS mis proyectos

---

## ¿Qué es esta carpeta?

Esta carpeta contiene mi **metodología de trabajo** como Solution Architect:
- Principios arquitectónicos fundamentales
- Políticas de compliance
- Templates reutilizables
- Decisiones que NO son específicas de un proyecto

**NO contiene:**
- ADRs de decisiones específicas de proyectos
- ADRs de mi roadmap de aprendizaje

---

## ¿Cuándo usar esto?

**Al empezar proyecto nuevo:**
1. Copia carpeta `/BUENAS_PRACTICAS/` a proyecto
2. Lee todos los ADRs (son pocos, fundamentales)
3. Aplica principios desde día 1
4. Crea ADRs específicos del proyecto en su propia carpeta

**Al tomar decisión arquitectónica:**
1. ¿Contradice principios de BUENAS_PRACTICAS? → Justifica con ADR
2. ¿Alineado con principios? → Documenta aplicación

---

## Contenido Actual

### ADRs (Principios)
- **ADR-000:** Principios Arquitectónicos Fundamentales
- **ADR-001:** Documentación de Decisiones vía ADRs
- **ADR-002:** Compliance GDPR Obligatorio

### Templates
- `COMPLIANCE_TEMPLATE.md` - Checklist compliance por proyecto
- `ADR_TEMPLATE_DETALLADO.md` - Para decisiones estratégicas
- `ADR_TEMPLATE_LIGERO.md` - Para decisiones tácticas

---

## Filosofía

> "Estos principios no son reglas rígidas.  
> Son guardrails que me protegen de malas decisiones.  
> Si los violo, lo documento con ADR explicando por qué."

**Ejemplo:**
- Principio: "Evitar vendor lock-in"
- Proyecto X: "Elegí vendor lock-in con AWS porque [razones]" → ADR documenta trade-off

---

## Evolución

Esta carpeta crece lentamente. No añado principios por capricho.

**Criterio para añadir nuevo ADR aquí:**
- ✅ Aplica a 80%+ de mis proyectos
- ✅ Refleja aprendizaje profundo (no moda pasajera)
- ✅ Me ha salvado de error real
- ❌ NO: decisiones específicas de tecnología ("Usar PostgreSQL")
- ❌ NO: preferencias personales ("Prefiero tabs vs spaces")