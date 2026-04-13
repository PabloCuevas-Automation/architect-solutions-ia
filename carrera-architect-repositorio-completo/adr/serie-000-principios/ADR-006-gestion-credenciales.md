# ADR-006 — Gestión de Credenciales y Secretos

**Serie**: 000 — Principios universales  
**Fecha**: 13 de abril 2026  
**Estado**: ✅ Activo  
**Aplica a**: Todos los proyectos

---

## Contexto

Las credenciales hardcodeadas en archivos de código quedan expuestas en el historial de Git de forma permanente, incluso si se eliminan posteriormente. Este riesgo es irreversible.

---

## Decisión

En todos los proyectos, sin excepción:

1. Las credenciales viven exclusivamente en un archivo `.env` local
2. El `.env` siempre está en `.gitignore` — nunca se sube a GitHub
3. Un `.env.example` con las variables sin valores sí se versiona — documenta qué variables existen
4. El código referencia variables con `${VARIABLE}` — nunca valores directos

---

## Estructura estándar por proyecto

```
proyecto/
    ├── .gitignore        ← incluye .env
    ├── .env              ← secretos reales, nunca a GitHub
    └── .env.example      ← plantilla sin valores, sí a GitHub
```

---

## Defensa en profundidad

Se mantiene un `.gitignore` en la raíz del repositorio completo Y uno en cada proyecto. Si falla una capa, la otra protege.

---

## Consecuencia

Cualquier archivo que contenga credenciales sin este patrón implementado es deuda técnica de seguridad de prioridad máxima.