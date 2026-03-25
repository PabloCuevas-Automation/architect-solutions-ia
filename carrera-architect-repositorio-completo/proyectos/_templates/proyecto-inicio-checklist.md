# PROYECTO INICIO CHECKLIST

**Proyecto**:
**Fecha inicio**:
**Cliente / Contexto**:

> Completá este documento ANTES de abrir N8N o escribir una línea de código.
> Si no podés responder una pregunta, no estás listo para construir.

---

## 1. Problema de negocio

**¿Qué problema resuelve este sistema?**
(En una frase, sin mencionar tecnología)

**¿Quién lo usa y con qué frecuencia?**

**¿Cómo se resuelve hoy sin automatización?**

**¿Cuál es el valor medible de automatizarlo?**
(horas ahorradas / errores evitados / tiempo de respuesta)

---

## 2. Diseño del sistema

**Diagrama de flujo** (dibujá en papel antes de continuar):
```
[Trigger] → [...] → [Output]
```

**¿Qué dispara el workflow?**
- [ ] Webhook (evento externo)
- [ ] Schedule (tiempo fijo)
- [ ] Manual
- [ ] Otro: ___

**¿Qué APIs externas consume?**
| API | Propósito | Tipo de auth | Plan gratuito |
|-----|-----------|--------------|---------------|
| | | | |

**¿Dónde viven los datos?**
- [ ] Solo en memoria (sin persistencia)
- [ ] Firebase
- [ ] PostgreSQL
- [ ] Otro: ___

---

## 3. PII y GDPR

**¿El sistema toca datos personales?**
- [ ] Sí → completar tabla
- [ ] No → continuar

**Inventario PII:**
| Campo | Tipo | Propósito | Retención |
|-------|------|-----------|-----------|
| | | | |

**¿Hay consentimiento explícito para procesar estos datos?**

**¿Cómo se eliminan si un usuario lo pide?**

---

## 4. ADRs aplicables

**¿Qué ADRs universales aplican a este proyecto?**
- [ ] ADR-000 — Principios arquitectónicos
- [ ] ADR-002 — Manejo PII
- [ ] ADR-004 — Infraestructura producción
- [ ] ADR-005 — Workflow audit

**¿Hay decisiones específicas de este proyecto que merezcan ADR propio?**
(lista provisional, se formalizan durante la construcción)

---

## 5. Alcance y límites

**¿Qué está DENTRO del scope?**

**¿Qué está FUERA del scope? (explícito)**

**¿Cuál es el criterio de "terminado"?**

---

## 6. Deuda técnica aceptada desde el inicio

| Deuda | Razón | Resolución |
|-------|-------|------------|
| | | |

---

**Firma**: Pablo Cuevas
**Estado**: Listo para construir / Pendiente de respuestas