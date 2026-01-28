# ADR-000: Principios Arquitectónicos Fundamentales

**Fecha:** 28 enero 2026  
**Estado:** ✅ Aceptada  
**Autor:** Pablo  
**Tipo:** Metodología Transversal  
**Scope:** Todos mis proyectos

---

## Resumen

Estos son mis **principios no-negociables** como Solution Architect.  
Guían cada decisión técnica que tomo.

---

## Principios

### 1. Jerarquía de Valor: Decisiones > Sistemas > Código

**Significado:**
- Mi valor NO está en líneas de código que escribo
- Mi valor está en decisiones que evitan código innecesario
- Mi valor está en sistemas que funcionan sin mí

**Aplicación práctica:**
- Antes de construir: "¿Debemos construir esto?"
- Durante construcción: "¿Qué pasa si esto falla?"
- Después: "¿Cómo funciona sin mi intervención?"

---

### 2. Técnica como Infraestructura (No como Fin)

**Significado:**
- Aprendo técnica hasta donde mejora mi criterio
- No busco ser "experto en X" - busco resolver problemas
- Herramientas son medios, no objetivos

**Aplicación práctica:**
- Python, JavaScript, SQL = herramientas
- Arquitectura, sistemas, trade-offs = conocimiento real
- Si herramienta no me sirve → la descarto sin remordimiento

---

### 3. Datos > Código

**Significado:**
- SQL enseña cómo piensa empresa
- Estructura de datos revela lógica de negocio
- Cambiar código es fácil; migrar datos es difícil

**Aplicación práctica:**
- Diseño schema de datos ANTES de código
- Normalización pensada, no por default
- Migrations testeadas exhaustivamente

---

### 4. Compliance = Poder (Diferenciador Europeo)

**Significado:**
- GDPR no es "overhead" - es ventaja competitiva
- Mercado europeo penaliza ignorantes de compliance
- Quien entiende compliance + IA = diferenciado

**Aplicación práctica:**
- Compliance desde día 1 (no retrofitted)
- COMPLIANCE.md en todo proyecto
- Rechazar proyectos que ignoren esto

---

### 5. Soberanía Técnica (Anti-Lock-in)

**Significado:**
- Mis sistemas deben poder funcionar si vendor X cae
- Dependencia = fragilidad
- Control > Conveniencia

**Aplicación práctica:**
- Self-hosted cuando posible
- APIs propias sobre plataformas cerradas
- Exit strategy documentada por vendor

---

### 6. Progreso = Sistemas en Producción (No Cursos)

**Significado:**
- Conocimiento sin aplicación es teatro
- Valor medible: código funcionando, clientes satisfechos
- Portfolio > certificaciones

**Aplicación práctica:**
- 1 proyecto terminado > 5 tutoriales
- Feedback real > autoevaluación
- Iterar rápido > perfeccionismo

---

### 7. Arquitecto ≠ Programador Senior

**Significado:**
- Developer pregunta: "¿Cómo lo construyo?"
- Arquitecto pregunta: "¿Debemos construirlo?"
- Mi rol es decidir, no solo ejecutar

**Aplicación práctica:**
- Cuestiono requirements
- Propongo alternativas
- Documento trade-offs
- Escalo decisiones complejas

---

## Aplicación en Proyectos

**Cada proyecto debe:**
1. Alinearse con estos principios (default)
2. Si viola principio → ADR específico justificando por qué

**Ejemplo:**
- Proyecto X usa vendor lock-in (viola principio 5)
- ADR documenta: "Elegimos AWS Lambda porque [razones] y aceptamos lock-in porque [trade-off justificado]"

---

## Evolución

Estos principios evolucionan basado en **experiencia real**, no en teoría.

**Añado principio cuando:**
- Me salvó de error grave 2+ veces
- Veo pattern repetido en múltiples proyectos
- Refleja aprendizaje profundo

**Elimino principio cuando:**
- Descubro que era dogma, no sabiduría
- Contexto cambió y ya no aplica

---

## Referencias

- **ADR-001:** Documentación vía ADRs (cómo implemento estos principios)
- **ADR-002:** Compliance GDPR (aplicación específica principio 4)
- **Roadmap:** De 0 a Solution Architect (origen de principios)

---

**Firmado:** Pablo  
**Revisión próxima:** Al completar Fase 1 (mes 6)

*"Principios son brújula, no mapa. Guían, no dictan."*