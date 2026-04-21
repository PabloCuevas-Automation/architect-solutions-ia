# DECISIONES ARQUITECTÓNICAS CONSOLIDADAS
## Pablo Cuevas — Fase 1 Constructor de Sistemas de Automatización

**Versión**: 2.0 Consolidada  
**Fecha**: 16 de abril 2026  
**Estado**: ✅ Activo — documento único de referencia  
**Consolidó**: ADR-000 a ADR-006, ADR-101 a ADR-105  
**Supersede**: ADR-CONSOLIDADAS-pablo-cuevas_15-04-2026_.md v1.0  
**Formato**: Searchable, cross-referenced, no redundancias

---

## ÍNDICE RÁPIDO — Busca aquí

| ID | Título | Serie | Estado | Cuándo consultar |
|----|----|------|--------|-----------------|
| ADR-000 | 7 Principios Arquitectónicos Fundamentales | Universal | ✅ Activo | Decisión nueva / dudas sobre criterio |
| ADR-001 | Documentación de Decisiones vía ADRs | Universal | ✅ Activo | Antes de crear nuevo ADR |
| ADR-002 | PII Management Obligatorio | Universal | ✅ Activo | Inicio de cualquier proyecto |
| ADR-003 | Convenciones de Nombres (kebab-case) | Universal | ✅ Activo | Crear carpetas/archivos |
| ADR-004 | 6-Layer Production Infrastructure Checklist | Universal | ✅ Activo | Antes de deployar a producción |
| ADR-005 | 5-Layer Workflow Audit Template | Universal | ✅ Activo | Antes de dar workflow por completo |
| ADR-006 | Gestión de Credenciales (.env + .gitignore) | Universal | ✅ Activo | Proyecto nuevo |
| ADR-101 | JavaScript primero, Python en Fase 2 | Aprendizaje | ✅ Validado | Decisión ya ejecutada — ver excepción ADR-301 |
| ADR-102 | Proyecto web como infraestructura negocio | Aprendizaje | ✅ Validado | Modelo para futuros proyectos |
| ADR-103 | Aprendizaje por construcción, no abstracción | Aprendizaje | ✅ Activo | Cuando enfrentes tecnología nueva |
| ADR-104 | Arc42 + C4 como estándar de documentación | Aprendizaje | ✅ Activo | Inicio de cualquier proyecto nuevo |
| ADR-105 | Política de audiencia de ADRs — internos vs entregables | Aprendizaje | ✅ Activo | Al clasificar o entregar ADRs a un cliente |

---

---

# SERIE 000 — PRINCIPIOS Y POLÍTICAS UNIVERSALES

## ADR-000: Siete Principios Arquitectónicos Fundamentales

**Estado**: ✅ Accepted / Aceptada  
**Fecha**: 28 enero 2026  
**Revisión**: Al completar Fase 1 (mes 6)

### Los 7 Principios (No-negociables)

| Principio | Significado | Aplicación práctica |
|-----------|------------|-------------------|
| **1. Jerarquía de Valor: Decisiones > Sistemas > Código** | Mi valor está en decisiones que evitan código, no en líneas escritas | Antes de construir: "¿Debemos construir esto?" — optimizar decisión, no implementation |
| **2. Técnica como Infraestructura** | Aprendo técnica hasta donde mejora criterio. Herramientas son medios, no fin | Si una herramienta no me sirve → discard sin remordimiento |
| **3. Datos > Código** | SQL enseña cómo piensa empresa. Estructura de datos revela lógica de negocio | Diseño schema ANTES de código. Migración de datos es lo difícil |
| **4. Compliance = Poder (Diferenciador Europeo)** | GDPR no es overhead — es ventaja competitiva en mercado europeo | Compliance desde día 1. COMPLIANCE.md en todo proyecto. Rechazar proyectos que ignoren |
| **5. Soberanía Técnica (Anti Lock-in)** | Mis sistemas deben funcionar si vendor X cae. Control > Conveniencia | Self-hosted cuando posible. APIs propias sobre plataformas cerradas |
| **6. Progreso = Sistemas en Producción** | Valor medible: código funcionando, clientes satisfechos. Portfolio > certificaciones | 1 proyecto terminado > 5 tutoriales. Feedback real > autoevaluación |
| **7. Arquitecto ≠ Programador Senior** | Desarrollador pregunta "¿Cómo?". Arquitecto pregunta "¿Debemos?" | Mi rol es decidir, cuestionar requirements, proponer alternativas |

**Cómo se aplican**: Cada proyecto alineado con estos principios por default. Si viola uno → ADR específico justificando por qué.

**Evolución**: Estos principios evolucionan con experiencia real, no teoría. Se añaden cuando resolvieron problema 2+ veces.

---

## ADR-001: Documentación de Decisiones vía ADRs

**Estado**: ✅ Accepted / Aceptada  
**Fecha**: 28 enero 2026  
**Scope**: Todos los proyectos

### La Decisión

Documentar todas las decisiones arquitectónicas significativas en Architecture Decision Records (Markdown, versionadas en Git), aceptando overhead de ~15 min por ADR para ganar trazabilidad y aprendizaje sistematizado.

### Qué documentar

✅ **SÍ documentes**:
- Decisiones que afectan múltiples componentes
- Decisions Type 1 (difíciles/costosas de revertir)
- Trade-offs importantes
- Políticas transversales (compliance, security)

❌ **NO documentes**:
- Decisiones triviales reversibles
- Preferencias personales sin impacto
- Detalles de implementación de código

### Estructura según tipo

**Decisiones Estratégicas** (Type 1, alto impacto):
1. Executive Summary
2. Context (problema, restricciones)
3. Alternatives Considered (mín. 2)
4. Decision (qué y por qué)
5. Consequences (positivas + negativas)
6. Trade-offs
7. References

**Decisiones Tácticas** (Type 2, reversibles):
1. Context (breve)
2. Decision
3. Why
4. Trade-offs (si aplica)

### Por qué NO las alternativas

| Alternativa | Problema | |
|---|---|---|
| No documentar | Conocimiento se pierde. Errores se repiten. Sin aprendizaje sistematizado | ❌ |
| Wiki/Confluence | Separado del código. Desactualizado. No version-controlled | ❌ |
| ADRs + Git | ✅ Version-controlled, portable, estándar industry | ✅ Elegida |

---

## ADR-002: PII Management — Política Obligatoria en Todos los Proyectos

**Estado**: ✅ Accepted / Aceptada  
**Fecha**: 28 enero 2026  
**Scope**: **TODOS los proyectos, sin excepción**

### Contexto Legal

- **GDPR**: Ley UE desde 2018. Multas: hasta €20M o 4% revenue global
- **Alcance**: Cualquier empresa procesando datos de ciudadanos UE (incluso fuera UE)
- **Enforcement**: Activo y creciente (miles de multas ya emitidas)

### Qué es PII (Personally Identifiable Information)

```
Directos: nombre, email, teléfono, ID, pasaporte
Online: IP, cookies, device IDs, user IDs
Ubicación: GPS, domicilio, lugares frecuentes
Biométrico: huellas, facial recognition, voz
Financiero: cuentas, tarjetas, ingresos
Salud: historiales médicos, recetas
Especial: raza, religión, opiniones políticas, orientación sexual
```

### 5 Requisitos Core (No-negociables)

| Requisito | Qué significa | Implementación |
|-----------|----------------|-----------------|
| **1. Data Minimization** | Recolectar SOLO lo necesario. Cuestionar cada campo | Agregate/anonymized > individual data |
| **2. Explicit Consent** | Nunca asumir. Opt-in claro, granular por propósito | Fácil de retirar |
| **3. Deletion Mechanisms** | Right to be forgotten (GDPR Art. 17). Automática | Cascade deletion. Documentada retención |
| **4. Audit Trail** | Log de acceso a PII (quién, cuándo, qué, por qué) | Immutable logs. Retención 2+ años |
| **5. Automatic Retention** | Datos eliminados después del período, automatizado | Configurable por tipo dato |

### Entregable obligatorio: COMPLIANCE.md

Cada proyecto debe tener un `COMPLIANCE.md` en raíz con:
- Inventario PII: qué guardamos
- Política retención: cuánto tiempo
- Proceso eliminación: cómo lo hacen usuarios
- Mecanismo consentimiento: cómo lo obtenemos

### Por qué NO las alternativas

| Alternativa | Resultado |
|---|---|
| Ignorar compliance | ❌ €20M multa. Data breach = carrera destruida. Clientes serios verifican |
| Paranoia máxima | ❌ 50-70% overhead desarrollo. Over-engineering |
| Pragmatic (elegida) | ✅ 15-20% overhead. Proporcional. Ventaja competitiva |

### Overhead aceptado

- **Tiempo**: 15-20% de desarrollo (agregado en proyecto)
- **Beneficio**: Protección legal, confianza cliente, diferenciación profesional

---

## ADR-003: Convenciones de Nombres — kebab-case Universal

**Estado**: ✅ Accepted  
**Fecha**: 29 enero 2026

### La Decisión

**Usar kebab-case (minúsculas + hyphens) para TODOS los nombres de carpetas y archivos técnicos.**

```
✅ CORRECTO:
  architect-solutions-ia/
  data-privacy-audit/
  tipos-datos.js

❌ INCORRECTO:
  ARCHITECT SOLUTIONS IA  (espacios)
  MiCarpeta  (mayúsculas inconsistentes)
  mi.carpeta  (puntos)
  mi-carpeta_final  (separadores mezclados)
```

### Por qué

Espacios requieren comillas en terminal → errores:
```powershell
cd ARCHITECT SOLUTIONS IA    # Error
cd "ARCHITECT SOLUTIONS IA"  # Requiere comillas
```

kebab-case funciona sin comillas en 100% de casos (Windows/Linux/Mac).

### Excepciones

- Config files: `.env`, `.gitignore`, `README.md`
- Extensions: `.js`, `.md`, `.json`

---

## ADR-004: 6-Layer Production Infrastructure Checklist

**Estado**: ✅ Accepted (Proposed originally, now Active)  
**Fecha**: 1 marzo 2026  
**Tipo**: Tactical / Checklist obligatoria antes de deployar

### Contexto

Un workflow sin servidor, sin seguridad, sin backups, sin manejo de fallos = demo, no producto. Necesito checklist que fuerce decisiones conscientes sobre cada capa antes de construir.

### La Decisión

**Adoptar 6-layer checklist como verificación obligatoria antes de producción.**

Cada capa resuelve una categoría de fallo real identificada en diseño de Fase 1.

### Las 6 Capas (con preguntas mínimas)

| Capa | Qué resuelve | Preguntas mínimas |
|-----|---|---|
| **1. Application & Logic** | Lógica de negocio. N8N core + APIs | ¿Qué problema resuelve? ¿APIs con qué autenticación? ¿Code node maneja datos inconsistentes? |
| **2. Persistence** | Dónde viven los datos. PostgreSQL | ¿Deduplicación implementada? ¿Schema documentado? ¿Queries con parámetros (sin SQL injection)? |
| **3. Hosting** | Dónde habita sistema. Docker + VPS | ¿Containerizado con Docker Compose? ¿Variables en `.env`? ¿Servidor en UE (GDPR)? |
| **4. Perimeter & Security** | Defensa del perímetro. Proxy + HTTPS + Hardening | ¿Reverse proxy delante de N8N? ¿HTTPS con certificado activo? ¿Firewall solo puertos necesarios? ¿SSH con key (no password)? ¿PostgreSQL NO expuesto? ¿Credenciales en vault? |
| **5. Resilience** | Qué pasa si algo falla. Error handling + Fallback | ¿Retry con backoff en APIs externas? ¿Dead Letter Queue si infraestructura cae? ¿Descarte temprano si datos basura? ¿Sistema idempotente? |
| **6. Continuity** | Backups + Observabilidad | ¿Backup automático PostgreSQL + workflows? ¿Almacenado fuera servidor? ¿Alertas si workflow falla? ¿Health check si N8N cae? |

### Regla de uso

Antes de deliverar: reviso todas 6 capas. Si falta una → documento por qué en ADR (deuda técnica consciente). Sin justificación → no a producción.

### Consequencias

**✅ Positivas**:
- Cada proyecto tiene punto de partida estructurado
- Elimina riesgo de olvidar capas críticas bajo presión
- Diferencia deliverable (sistema documentado + seguro) de automatizador (solo workflows)

**⚠️ Negativas**:
- ~30 min de checklist por sistema
- Riesgo de completar mecánicamente sin criterio real

---

## ADR-005: 5-Layer Workflow Audit Template

**Estado**: ✅ Active  
**Fecha**: 17 marzo 2026  
**Tipo**: Universal Principle (000-series)

### Contexto

Un workflow que funciona ≠ workflow entregable. Sin revisión sistemática, brechas en seguridad, resiliencia, GDPR y mantenibilidad pasan desapercibidas.

ADR-004 = infraestructura sistema. ADR-005 = calidad interna workflow. Complementarias.

### La Decisión

**Aplicar 5-layer audit a cada workflow antes de considerarlo completo.**

Si un layer falla → documentado como deuda técnica consciente con fecha de resolución.

### 5 Layers de Audit

```markdown
## 1. Seguridad
- [ ] Credenciales en vault (no en JSON exportado)
- [ ] Webhook con validación de origen (o documentado por qué no aplica)
- [ ] Sin PII logueado innecesariamente

## 2. Resiliencia
- [ ] On Error configurado en cada nodo HTTP
- [ ] Fallback si API externa falla
- [ ] Idempotente — ejecutar 2 veces no crea duplicados

## 3. Observabilidad
- [ ] Nombres descriptivos en todos nodos
- [ ] Execution log permite reconstruir flujo sin adivinar
- [ ] Documentación mínima en canvas

## 4. GDPR
- [ ] PII identificado
- [ ] Cada PII tiene propósito justificado
- [ ] Sin campos "por si acaso"

## 5. Mantenibilidad
- [ ] Alguien ajeno entiende workflow en 5 minutos
- [ ] Naming refleja QUÉ (no CÓMO)
```

### Relación con ADR-004

| | ADR-004 | ADR-005 |
|---|---|---|
| Qué cubre | Infraestructura sistema | Calidad interna workflow |
| Cuándo | Antes de entregar | Durante + después construir |
| Alcance | Servidor, HTTPS, backups | Credenciales, errores, GDPR |

---

## ADR-006: Gestión de Credenciales — .env + .gitignore

**Estado**: ✅ Activo  
**Fecha**: 13 abril 2026  
**Scope**: Todos los proyectos, sin excepción

### El Problema

Credenciales hardcodeadas quedan en historial Git permanentemente. Incluso si se eliminan, están ahí para siempre. **Reversible es imposible.**

### La Decisión

**Nunca credenciales en el código. Siempre en .env local.**

| Qué | Dónde | Git | Por qué |
|-----|----|----|--------|
| Secretos reales | `.env` | ❌ .gitignore | Nunca a GitHub |
| Variables sin valores | `.env.example` | ✅ Sí | Documenta qué variables existen |
| Código | Usa `${VARIABLE}` | ✅ Sí | Referencias, no valores directos |

### Defensa en profundidad

- `.gitignore` en raíz del repo
- `.gitignore` en cada proyecto
- Si falla una capa → la otra protege

```
proyecto/
  ├── .gitignore        ← excluye .env
  ├── .env              ← secretos reales, nunca a GitHub
  └── .env.example      ← plantilla sin valores, sí a GitHub
```

### Consecuencia

Cualquier archivo con credenciales sin este patrón = deuda técnica de seguridad prioridad máxima.

---

---

# SERIE 100 — DECISIONES DE APRENDIZAJE Y CARRERA

## ADR-101: JavaScript Primero, Python en Fase 2

**Estado**: ✅ Aceptada Y VALIDADA  
**Fecha decisión**: 24 enero 2026  
**Fecha validación**: 21 febrero 2026  
**Tipo**: Estratégica (afecta timeline completo Fase 1)  
**Excepción activa**: ADR-301 (GeoLabor) — Python para inyección Excel, scope limitado

### Contexto

Descubrimiento crítico (24/01): **N8N usa JavaScript nativamente, no Python.**

- N8N code nodes = JavaScript
- Expresiones `{{ }}` = JavaScript
- Transformación datos = `.map()`, `.filter()`, `.reduce()`
- Python = indirecto (APIs externas)

Además: Curso de Desarrollo Web del gobierno Trento (prueba 16/02) + proyecto web como infraestructura negocio = **JavaScript ya era obligatorio**.

### La Decisión

**Priorizar JavaScript en Fase 1. Python → Fase 2.**

Trade-off: Retraso 2-3 meses en RAG/scripts Python. Ganancia: N8N productivo 2-3 meses antes, base JS sólida.

### Por qué NO las alternativas

| Alternativa | Problema |
|---|---|
| Seguir roadmap original (Python 70%) | Ignora realidad de N8N. Doble trabajo: Python + JS igual. |
| JavaScript + Python paralelo | Sobrecarga cognitiva. Dilución de profundidad. |

### Validación (21/02) ✅

| Criterio | Resultado |
|---|---|
| Examen JavaScript | ✅ Aprobado 16/02 |
| Sitio web pablocuevas.it | ✅ Live 19/02, 98/100 PageSpeed |
| Firebase + formulario | ✅ Activo recibiendo leads |
| Base para N8N Code node | ✅ Arrays, objetos, JSON, funciones dominados |

**Veredicto**: Decisión correcta. El trade-off fue proporcional.

### Excepción documentada

ADR-301 documenta la excepción activa de Python para la inyección de datos en
plantillas Excel en el proyecto GeoLabor. Esta excepción está justificada por
la limitación técnica real de `exceljs` para preservar gráficos en plantillas
existentes, y aplica exclusivamente a ese proyecto y ese componente. No modifica
el principio general de esta decisión.

---

## ADR-102: Proyecto Académico como Infraestructura de Negocio Real

**Estado**: ✅ Aceptada Y VALIDADA  
**Fecha decisión**: 10 febrero 2026  
**Fecha validación**: 21 febrero 2026  
**Tipo**: Estratégica — posicionamiento de mercado

### El Dilema

Proyecto académico obligatorio (curso Trento) → dos caminos:

1. **Mínimo viable**: Template + práctica. 3-5 días. Aprueba curso.
2. **Infraestructura real**: Sistema profesional con dominio propio, Firebase, GDPR, performance. 9 sesiones intensas. Sirve para negocio + curso.

### La Decisión

**Opción 2: Usar proyecto del curso para construir pablocuevas.it, sitio profesional de servicios IA.**

Lógica: Un recurso (el curso) satisface dos necesidades. Tiempo inverso = valor académico + valor de negocio simultáneamente.

### Lo que se construyó

**Stack**: HTML5 + CSS3 + JavaScript ES6 + Bootstrap 5 + GSAP + Firebase Firestore + Vercel CI/CD

**Funcionalidades**:
- Navbar glass morphism + GSAP scroll animations
- Hero con video loop (desktop/mobile fallback)
- 5 secciones: Chi Sono, Soluzioni (5 videos + tabs), Come Lavoro (4 pasos), Contatti (Firebase), FAB WhatsApp
- Responsive (992px / 768px / 576px)
- Legal: Privacy + Cookie Policy (iubenda) + Términos propios

**Métricas** (PageSpeed 19/02):
- Rendimiento: 98/100
- Accesibilidad: 97/100
- Recomendaciones: 100/100
- SEO: 100/100

**Estado actual**: pablocuevas.it live, recibiendo leads, formulario conectado a Firebase.

### Consecuencias

**✅ Logró**:
- Eliminó trabajo futuro (el sitio habría requerido 2-4 semanas de Fase 1)
- Generó evidencia técnica real (98/100 no es número de práctica)
- Validó patrón de aprendizaje (construcción real > tutorial)
- Creó pipeline de leads activo ahora

**⚠️ Deuda técnica consciente**:
- Automatización notificación leads: pendiente
- Email profesional pablo@pablocuevas.it: diferido a primer ingreso

---

## ADR-103: Aprendizaje por Construcción, No Abstracción

**Estado**: ✅ Aceptada  
**Fecha**: 21 febrero 2026  
**Tipo**: Estratégica fundamental — afecta cómo abordar cada tecnología

### La Observación Empírica

Post-Fase 0 (aprendizaje teórico en abstracto) + proyecto web (sistema real):

- **Abstracción sola**: Estudiar JavaScript en abstracto avanzaba lentamente
- **Construcción real**: Construir pablocuevas.it con JavaScript avanzó en 9 sesiones a sistema completo 98/100 PageSpeed

Mismo tiempo, resultados cualitativamente distintos. La diferencia: **contexto y urgencia real**.

### La Distinción Clave

```
Pensamiento sistémico, ADRs, GDPR, trade-offs
→ Se aprende reflexionando y documentando
→ La abstracción es el vehículo correcto

JavaScript, N8N, Firebase, Docker, APIs
→ Se aprende construyendo sistemas reales
→ La construcción es el vehículo correcto
```

No es que la teoría sea mala. **Es aplicar el vehículo equivocado al objetivo equivocado.**

### La Decisión

**Aprender siempre tecnologías nuevas construyendo sistemas con propósito real.**

Antes de tocar una tecnología nueva:
1. Definir el proyecto ancla concreto que la justifica
2. Resolver ese problema — así aprendes
3. Estudiar conceptos en contexto, no antes

**Regla operativa**: Si no puedo nombrar el problema concreto que esta tecnología resuelve *hoy*, no es momento de aprenderla.

### Aplicación inmediata

N8N se aprende mientras construyo la automatización real del formulario (Firebase → notificación). No después de "terminar" de estudiar JavaScript — es intencional.

### Patrón para tecnologías futuras

| Tecnología (Fase) | Proyecto ancla | Cuándo |
|---|---|---|
| Docker / VPS (Fase 1 Bloque 6) | Desplegar N8N self-hosted para primer cliente | Cuando cliente lo pida |
| PostgreSQL avanzado (Fase 1 Bloque 5) | BD real de workflow complejo | Cuando workflow lo necesite |
| Python (Fase 2) | Script que JS no puede | Cuando N8N no pueda hacerlo |
| FastAPI (Fase 2) | Endpoint real que N8N consume | Cuando cliente lo requiera |

### Lo que NO implica

- ❌ No significa saltarse conceptos fundamentales
- ❌ No significa construir sin criterio (ADR-000 sigue siendo base)
- ❌ La teoría no es mala — Fase 0 fue correcta

**Significa**: Aprender conceptos en contexto de uso real, no antes de él.

### Criterios de éxito

- ✅ Automatización formulario funcionando antes 10/03
- ✅ Aprendo más sobre N8N en 2 semanas construyendo que 4 semanas tutoriales
- ✅ Code node no es bloqueo porque JS autónomo se practica en paralelo
- ✅ Cada tecnología nueva tiene proyecto ancla definido antes de empezar

---

## ADR-104: Arc42 + C4 como Estándar de Documentación Arquitectónica

**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — afecta cómo se documenta todo proyecto desde este punto  
**Supersede**: Estándar informal de 6 pasos usado en PA1

### Contexto

Hasta este punto, Pablo ha construido su propio estándar de documentación
proyecto a proyecto: ADRs, diagrama de sistema, GDPR data mapping, schema
PostgreSQL, README y runbook. Ese estándar funciona pero es informal e invisible
para alguien externo: un cliente, un colaborador o un empleador no sabe cómo
leerlo porque no tienen el contexto de cómo fue construido.

Al analizar el piloto de GeoLabor, se identificó que Pablo ya estaba haciendo
Arc42 de forma fragmentada sin saberlo: los ADRs cubren las decisiones de diseño,
el diagrama de sistema cubre las vistas de contenedores, el GDPR data mapping
cubre los conceptos transversales. La pregunta no era si adoptar Arc42 y C4,
sino si formalizar lo que ya existe dentro de un marco estándar reconocido.

### La Decisión

Adoptar **Arc42** como estructura de documentación de arquitectura y **C4 Model**
como lenguaje de diagramación para todos los proyectos desde el piloto GeoLabor
en adelante, integrándolos con el sistema de ADRs ya existente.

### Qué es cada uno y cómo se complementan

**Arc42** es un framework de documentación con doce secciones estructuradas.
No todas aplican a todos los proyectos — se usa la profundidad proporcional a
la complejidad del sistema. Las secciones más relevantes para los proyectos
actuales son: requisitos y objetivos de calidad, restricciones técnicas y
organizativas, contexto y alcance del sistema, decisiones de diseño (donde
viven los ADRs), conceptos transversales (GDPR, seguridad, resiliencia),
vistas de despliegue, y riesgos conocidos.

**C4 Model** establece cuatro niveles de abstracción para diagramas. El nivel
de Contexto muestra el sistema en su entorno y está dirigido al cliente no
técnico. El nivel de Contenedor muestra las aplicaciones, bases de datos y
servicios que componen el sistema. El nivel de Componente muestra qué hay dentro
de cada contenedor. El nivel de Código es opcional y solo aplica para componentes
complejos. Cada nivel responde a una pregunta distinta para una audiencia distinta.

**La relación con los ADRs**: Arc42 es el contenedor de toda la documentación.
C4 proporciona el lenguaje visual dentro de Arc42. Los ADRs son el registro de
razonamiento que vive en la sección de Decisiones de Diseño de Arc42. Los tres
son capas complementarias, no alternativas.

### Por qué este estándar y no el informal de PA1

El estándar informal de PA1 funciona pero es invisible para alguien externo.
Arc42 y C4 son estándares reconocidos en la industria europea de arquitectura
de software: cualquier arquitecto senior puede heredar un proyecto documentado
con estos estándares y empezar a trabajar sin que Pablo le explique el sistema
de documentación. Eso es portabilidad real del trabajo.

### Cómo se aplica en la práctica

Para proyectos piloto o de alcance reducido como GeoLabor, se usan las secciones
Arc42 relevantes sin completar las doce. Para proyectos de producción con clientes
reales, se completa Arc42 en su totalidad como parte del entregable — la
documentación Arc42 completa es parte del precio, no un extra opcional. Los
diagramas C4 se producen en Mermaid cuando sea posible para mantenerlos
versionables en Git.

### Consecuencias

**✅ Positivas**: Todos los proyectos futuros tienen documentación predecible y
reconocible. El esfuerzo de aprender el estándar se amortiza en cada proyecto
siguiente.

**⚠️ Deuda técnica consciente**: PA1 no tiene documentación Arc42/C4 completa.
Se migra cuando PA1 llegue a producción real (Bloque 6). GeoLabor es el primer
proyecto documentado con el estándar desde el inicio.

### Referencias

Simon Brown — C4 Model: https://c4model.com  
Arc42: https://arc42.org

---

## ADR-105: Política de Audiencia de ADRs — Internos vs Entregables

**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — afecta cómo se clasifica y entrega documentación en todos los proyectos

### Contexto

El sistema de ADRs cubre decisiones de naturaleza muy distinta: algunas existen
porque Pablo está en Fase 1 de un roadmap de aprendizaje personal, otras existen
porque el cliente necesita entender por qué el sistema está construido así para
poder confiar en él. Hasta ahora todos los ADRs viven en el mismo espacio sin
distinguir a quién están dirigidos, lo que genera dos problemas: entregar ADRs
personales a un cliente expone información interna innecesaria, y no entregar
ADRs relevantes al cliente reduce la confianza en el sistema.

### La Decisión

Cada ADR tiene un campo de **audiencia** que determina si forma parte del paquete
de documentación entregable al cliente o si permanece en el repositorio personal.

### Los dos tipos y su criterio de clasificación

Un ADR es **entregable** cuando el cliente tiene un interés legítimo en entender
esa decisión para confiar en el sistema o para poder operarlo sin depender de
Pablo. La pregunta de clasificación es: *¿necesita el cliente entender esta
decisión para confiar en el sistema o para poder mantenerlo?*

Un ADR es **personal** cuando la decisión existe por razones internas: el roadmap
de aprendizaje de Pablo, las limitaciones de su stack actual en Fase 1, sus
trade-offs de tiempo y recursos como profesional en formación. El cliente no gana
nada leyendo estos ADRs.

### Estructura de carpetas en cada proyecto

```
adr/
  ├── entregable/    ← ADRs que van al cliente, en su idioma
  └── personal/      ← ADRs internos, nunca al cliente
```

### Regla sobre el idioma

Los ADRs entregables se redactan en el idioma del cliente al momento de la
entrega final, no durante la construcción. El flujo correcto es construir y
documentar en español, traducir como último paso antes de la entrega.

### Aplicación a las series existentes

La serie 000 (principios universales) y la serie 100 (aprendizaje y carrera)
son siempre **personales**. Nunca se entregan a clientes.

Las series 200, 300 y siguientes (proyectos específicos) pueden contener ADRs
de ambos tipos. La clasificación se decide ADR por ADR al momento de crearlo.

### Consecuencias

**✅ Positivas**: La documentación entregable al cliente es siempre limpia,
relevante y profesional. No contiene información sobre el roadmap de aprendizaje
de Pablo ni sobre las limitaciones de su stack actual.

**⚠️ Deuda técnica consciente**: Los proyectos anteriores (PA1 incluido) no
tienen esta clasificación aplicada retroactivamente. Se aplica desde GeoLabor
en adelante. PA1 se clasifica cuando llegue a producción real.

---

---

# REFERENCIAS CRUZADAS Y ESTADO ACTUAL

## Cómo Navegar Este Documento

**Buscar rápido**: Ve a "ÍNDICE RÁPIDO" arriba.

**Necesito verificar si algo es válido**: Busca el ADR correspondiente.

**Necesito crear un ADR nuevo**: Lee ADR-001 (cómo documentar decisiones).

**Necesito validar mi proyecto antes de producción**: Usa ADR-004 (6-layer checklist) + ADR-005 (workflow audit).

**Necesito entender por qué hago algo así**: Ve a ADR-000 (principios) o ADR-103 (aprendizaje).

**Necesito documentar un proyecto nuevo**: Lee ADR-104 (Arc42 + C4) + ADR-105 (audiencia de ADRs).

## Principios que Guían Todo

Cuando dudes entre opciones → vuelve a ADR-000:

1. **Decisiones > Código**
2. **Técnica como infraestructura**
3. **Datos > Código**
4. **Compliance = Poder**
5. **Soberanía técnica**
6. **Progreso = Producción**
7. **Arquitecto ≠ Senior Programmer**

## Estado Operativo (Abril 2026)

| Serie | Count | Activos | Validados | Próxima revisión |
|---|---|---|---|---|
| 000-series (Universal) | 7 | 7 | 7 | Cuando toque tema |
| 100-series (Aprendizaje) | 5 | 5 | 5 | Fin Fase 1 |
| 200-series (PA1) | 0 | - | - | Durante construcción PA1 |
| 300-series (GeoLabor) | 4 | 4 | 4 | Al completar primera entrega piloto |

**Total**: 16 ADRs consolidadas en este documento.

## Deuda Técnica Documentada (Consciente)

| ADR | Deuda | Estado | Resolver en |
|---|---|---|---|
| ADR-101 | Python pospuesto — excepción activa en ADR-301 | ⏳ Intencional | Fase 2 (principio general) |
| ADR-102 | Notificación leads pablocuevas.it | ⏳ Intencional | PA1 Bloque 5 |
| ADR-103 | N8N en paralelo con JS autónomo | 🔄 En curso | Bloques 1-5 |
| ADR-104 | PA1 sin documentación Arc42/C4 completa | ⏳ Intencional | Bloque 6 (PA1 a producción) |
| ADR-105 | PA1 sin clasificación de audiencia de ADRs | ⏳ Intencional | Bloque 6 (PA1 a producción) |

**La deuda es conocida, documentada y acotada. No es sorpresa.**

---

## Cómo Actualizar Este Documento

Cuando crees un ADR nuevo:
1. Agrégalo a la tabla "ÍNDICE RÁPIDO" con serie, título, estado y cuándo consultar
2. Añade el contenido completo en la sección de serie correspondiente
3. Actualiza la tabla de Estado Operativo (counts y totales)
4. Si genera deuda técnica, añádela a la tabla de deuda
5. Si es de serie 000 o 100, vive aquí. Si es de serie 200+, vive en el consolidado del proyecto y se vincula desde aquí

**Importante**: Este documento es el registro maestro de las series 000 y 100.
Las series de proyecto (200, 300+) tienen su propio archivo consolidado.

---

**Documento consolidado por**: Pablo Cuevas + Claude (Anthropic)  
**Fecha consolidación**: 16 de abril 2026  
**Supersede**: ADR-CONSOLIDADAS-pablo-cuevas_15-04-2026_.md  
**Próxima revisión**: Al crear el primer ADR de la serie 200 (PA1) o al finalizar el piloto GeoLabor

---

*"Los principios son brújula, no mapa. Guían, no dictan."*  
*"Decisiones no documentadas = Decisiones perdidas."*  
*"Aprendizaje por construcción = Construcción por propósito."*
