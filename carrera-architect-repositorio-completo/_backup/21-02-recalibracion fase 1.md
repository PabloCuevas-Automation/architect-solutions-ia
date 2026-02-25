# Plan de 42 Días: JavaScript Orientado a N8N
## VERSION 2.0 — Recalibración Roadmap Fase 0 → Fase 0.5

**Fecha creación**: 27 de enero 2026  
**Última actualización de estado**: 21 de febrero 2026

---

## ⚠️ ESTADO ACTUAL DEL DOCUMENTO — LEER ANTES DE CONTINUAR

```
DÍAS 1-25 (27 enero - 19 febrero): ✅ EJECUTADOS Y SUPERADOS
DÍAS 26-42 (20 febrero - 10 marzo): 🎯 ACTIVOS — referencia vigente
```

**Qué cambió respecto al plan original:**
- El proyecto web del curso (días 15-25 aproximadamente) se ejecutó en paralelo intensivo, no de forma secuencial, resultando en un sistema completo en producción antes del cierre del plan
- El examen JS fue el 16/02 (no el 5/02 de la versión 1.0): ✅ Aprobado
- La base de JS construida supera lo estimado: Firebase SDK integrado, GSAP, módulos ES6, IntersectionObserver, todo en contexto real
- N8N no se instaló en el Día 41 como planificado — sigue pendiente pero con mejor base de la esperada

**Cómo usar este documento desde hoy:**
- Los días 1-25 son historial — no ejecutar, no referenciar como pendiente
- Los días 26-42 son la continuidad activa, especialmente la preparación para N8N
- El objetivo del Día 42 (N8N instalado y listo) sigue siendo válido

---

## 📊 RESUMEN DE EJECUCIÓN REAL (días 1-25)

| Bloque | Plan | Realidad |
|--------|------|----------|
| Terminal + Git | Días 3-4 | ✅ 29/01 — completado con ADR-003 Naming |
| Variables, tipos, objetos | Días 3-8 | ✅ 02/02 — completado |
| Arrays, funciones, lógica | Días 9-14 | ✅ Completado en preparación examen |
| Examen JavaScript | Día 16 (16/02) | ✅ Aprobado 16/02 |
| Proyecto web del curso | Días ~15-25 | ✅ pablocuevas.it live — 98/100 PageSpeed |
| String methods, error handling | Días 21-24 | ✅ Implementados en proyecto web |
| APIs conceptual | Días 28+ | 🎯 Siguiente bloque activo |

---

## 🎯 BLOQUE ACTIVO — Días 26-42 (desde 20 febrero)

> Los días que siguen son referencia de continuidad. La secuencia exacta puede ajustarse según el ritmo real, pero el destino es N8N instalado y primer workflow funcionando.

---

### **Día 26 (Sábado 21 feb) — Data Transformation Challenges**
JS N8N: Ejercicios complejos de transformación. Datasets JSON reales. Transform estructura A→B, filtrar+agrupar, nested data a flat data.

### **Día 27 (Domingo 22 feb) — Mini-Workflows en JS**
Simula workflows N8N en JS puro. 3 mini-workflows: Trigger→Filter→Transform→Output. Usar funciones para simular "nodes". Objetivo: pensar en workflows, no en scripts lineales.

---

## 📅 SEMANA 5: APIs + JSON INTEGRADO (23 feb - 1 mar)

### **Día 28 — APIs: Conceptos Fundacionales**
REST APIs conceptual. Cliente-Servidor. URL structure. Métodos HTTP (GET, POST, PUT, PATCH, DELETE).

### **Día 29 — APIs: Status Codes + Headers**
2xx/4xx/5xx profundo. Headers: Content-Type, Authorization, Bearer. Instalar Postman o Thunder Client. Primer request GET a JSONPlaceholder.

### **Día 30 — APIs: Fetch + Async/Await**
`fetch()` en JavaScript. `async/await` funcional. Try/catch en llamadas API. Práctica con REST Countries API.

### **Día 31 — APIs: Autenticación**
API Keys (header vs query param). Bearer tokens. OAuth conceptual (no implementar, entender flujo). Casos N8N: cómo N8N gestiona credenciales.

### **Día 32 — APIs: Proyecto Integrador**
Script completo: consume API → transforma datos → output estructurado. Documentado con README. Usa todos los conceptos de la semana.

### **Días 33-34 (fin de semana) — Consolidación Bloque 2**
Repaso express de todo lo visto. ¿Puedes construir un script que llame a una API, transforme el JSON y produzca un output útil sin consultar documentación?

---

## 📅 SEMANA 6: CONSOLIDACIÓN + PREPARACIÓN N8N (2-8 mar)

### **Día 35 — Consolidación JS completa**
Repaso de todos los conceptos N8N-críticos: map/filter/find, JSON navigation, template literals, optional chaining, error handling. Identifica gaps reales.

### **Día 36 — N8N: Instalación Local**
```bash
# Opción Docker (recomendada — Docker ya instalado)
docker run -p 5678:5678 n8nio/n8n

# Opción npm global
npm install n8n -g && n8n
```
Accede a localhost:5678. Verifica que abre. Lee "Getting Started" en docs oficiales.

### **Día 37 — N8N: Interfaz y Primer Workflow**
Familiarización con interfaz: canvas, nodes, connections, execution panel. Crear primer workflow: trigger manual → HTTP Request → Set node → output. Objetivo: entender el modelo mental de N8N.

### **Día 38 — N8N: Code Node**
El nodo más importante. JavaScript puro en N8N. `$input.all()`, `$json`, `items`. Transformar datos de entrada. Conectar lo aprendido en JS con N8N real.

### **Día 39 — N8N: Primer Workflow Real**
**🎯 Este es el objetivo de negocio**: Firebase Firestore → N8N detecta nuevo lead → formatea datos → envía notificación (email o Telegram). Primera automatización con propósito real.

### **Días 40-41 — Documentación y ADR**
Escribir ADR completo: "JavaScript Orientado a N8N: Decisiones de Aprendizaje". Documentar el primer workflow real: qué hace, por qué, alternativas consideradas. README del workflow.

### **Día 42 — DÍA N8N PRODUCTIVO**

```
Al llegar aquí tendrás:
✅ JavaScript sólido orientado a N8N (validado por proyecto web)
✅ JSON/APIs dominados
✅ 1 proyecto real en portfolio (pablocuevas.it)
✅ GitHub profesional
✅ Pensamiento arquitectónico (ADRs)
✅ Error handling robusto
✅ N8N instalado y primer workflow funcionando
✅ Primera automatización real (notificación leads)
```

**Esto es el inicio real de Fase 1 productiva, no el final de la preparación.**

---

## 📊 DISTRIBUCIÓN REAL DE HORAS (actualizada)

| Fase | Horas ejecutadas/estimadas | Días |
|------|---------------------------|------|
| Fase 0-0.5 + JS básico | ~40h ejecutadas | Días 1-14 |
| Proyecto web + JS aplicado | ~60h ejecutadas | Días 15-25 |
| APIs + JS avanzado | ~25h estimadas | Días 26-35 |
| N8N instalación + primeros workflows | ~20h estimadas | Días 36-42 |
| **TOTAL** | **~145h** | **42 días** |

---

## 🎯 CHECKPOINTS DE VALIDACIÓN

### ✅ Checkpoint 1 (completado — 16/02):
- Examen JavaScript aprobado
- Terminal/Git/GitHub funcional
- Proyecto web en producción

### 🎯 Checkpoint 2 (objetivo — 10/03):
- APIs consumidas sin consultar docs básicas
- N8N instalado y funcionando en local
- Primer workflow real ejecutándose (Firebase → notificación)

### ⏳ Checkpoint 3 (objetivo — 31/03):
- 3 workflows documentados
- Outreach activo a primeros prospectos en Rovereto/Trento
- Primer caso de estudio en preparación

---

**Documento creado**: 27 enero 2026  
**Estado actualizado**: 21 febrero 2026  
**Próxima revisión**: Al completar Día 42 / Checkpoint 2
