# DECISIONES ARQUITECTÓNICAS CONSOLIDADAS
## Pablo Cuevas — Fase 1 Constructor de Sistemas de Automatización

**Versión**: 2.1 Consolidada  
**Fecha**: 01 de mayo 2026  
**Estado**: ✅ Activo — documento único de referencia  
**Consolidó**: ADR-000 a ADR-006, ADR-101 a ADR-105  
**Supersede**: ADR-CONSOLIDADAS-pablo-cuevas_16-04-2026_.md v2.0  
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
| ADR-007 | Principio de Mínimo Privilegio para Acceso IA | Universal | ✅ Activo | Antes de conectar cualquier MCP o herramienta IA |
| ADR-101 | JavaScript primero, Python en Fase 2 | Aprendizaje | ✅ Validado | Decisión ya ejecutada — ver excepción ADR-301 |
| ADR-102 | Proyecto web como infraestructura negocio | Aprendizaje | ✅ Validado | Modelo para futuros proyectos |
| ADR-103 | Aprendizaje por construcción, no abstracción | Aprendizaje | ✅ Activo | Cuando enfrentes tecnología nueva |
| ADR-104 | Arc42 + C4 como estándar de documentación | Aprendizaje | ✅ Activo | Inicio de cualquier proyecto nuevo |
| ADR-105 | Política de audiencia de ADRs — internos vs entregables | Aprendizaje | ✅ Activo | Al clasificar o entregar ADRs a un cliente |

> **Serie 200 (PA1) y serie 300 (GeoLabor)**: viven en sus propios archivos consolidados.
> Ver referencias cruzadas al final de este documento.

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

Espacios requieren comillas en terminal → errores. URLs con espacios se encodean mal → %20. Case-sensitivity inconsistente entre OS (Windows vs Linux). kebab-case es legible, universal, estándar en web.

### Excepciones documentadas

- `README.md`, `COMPLIANCE.md` — convención industry para archivos raíz
- Variables en JavaScript → camelCase (`emailData`)
- Clases → PascalCase (`UserManager`)

---

## ADR-004: 6-Layer Production Infrastructure Checklist

**Estado**: ✅ Accepted  
**Fecha**: 15 febrero 2026  
**Scope**: Obligatorio antes de cualquier deploy a producción

### Las 6 Capas (en orden de ejecución)

| Capa | Categoría | Qué verificar |
|------|-----------|---------------|
| **1. Application** | Código funciona | Tests pasando, error handling, logging |
| **2. Persistence** | Datos seguros | Backups, migrations, connection pooling |
| **3. Hosting** | Infraestructura lista | VPS/cloud configurado, recursos suficientes |
| **4. Security** | Acceso controlado | HTTPS, autenticación, secrets en vault |
| **5. Resilience** | Fallas manejadas | Health checks, restart policies, alertas |
| **6. Continuity** | Operación sostenible | Runbook, monitoreo, proceso de actualización |

### Regla de uso

No se puede pasar a producción si cualquier capa tiene un ❌ sin justificación documentada. Las deudas técnicas intencionales se documentan en el ADR del proyecto antes del deploy.

---

## ADR-005: 5-Layer Workflow Audit Template

**Estado**: ✅ Accepted  
**Fecha**: 15 febrero 2026  
**Scope**: Obligatorio antes de dar un workflow N8N por completo

### Las 5 Capas de Auditoría

| Capa | Categoría | Preguntas clave |
|------|-----------|-----------------|
| **1. Security** | Acceso protegido | ¿Webhook autenticado? ¿Credentials en vault? ¿Ningún secret hardcodeado? |
| **2. Resilience** | Fallas manejadas | ¿Qué pasa si cada nodo externo falla? ¿Hay rama de error? ¿Lead se pierde? |
| **3. Observability** | Estado visible | ¿Puedo ver qué pasó con cada lead? ¿Logs útiles? ¿Alertas si algo falla? |
| **4. GDPR** | Datos protegidos | ¿Solo guardo lo necesario? ¿PII en el sitio correcto? ¿Retención configurada? |
| **5. Maintainability** | Sostenible a largo plazo | ¿Naming descriptivo? ¿Puedo entender el flujo en 6 meses? ¿Deuda documentada? |

### Regla de uso

Se ejecuta al cerrar cada sesión de construcción significativa. No es optional antes de considerar un bloque completo.

---

## ADR-006: Gestión de Credenciales — .env + .gitignore

**Estado**: ✅ Accepted  
**Fecha**: 13 abril 2026  
**Scope**: Universal — todo proyecto con credenciales

### La Decisión

**Patrón .env + .gitignore como defensa en profundidad para credenciales.**

```
proyecto/
  ├── .env          ← credenciales reales (NUNCA al repo)
  ├── .env.example  ← plantilla versionada (SÍ al repo)
  └── .gitignore    ← incluye .env explícitamente
```

### Defensa en profundidad

- `.gitignore` en raíz del repo — primera línea de defensa
- `.gitignore` en cada proyecto — si falla la raíz, el proyecto se protege solo
- `.env.example` versionado — cualquiera puede reconstruir el entorno sin credenciales reales

### Verificación obligatoria antes de primer commit

```bash
git log --all --full-history -- .env
# Si devuelve algo → las credenciales YA están en el historial → rotar todas
# Si no devuelve nada → correcto
```

### Regla absoluta

Antes de recibir cualquier código con credenciales: .env + .gitignore implementados y verificados. Sin excepción.

---

## ADR-007: Principio de Mínimo Privilegio para Acceso IA

**Estado**: ✅ Accepted  
**Fecha**: 15 abril 2026  
**Scope**: Universal — cualquier integración de herramienta IA con sistemas locales

### La Decisión

**Las herramientas IA (MCP servers, APIs, agentes) reciben acceso mínimo necesario para su tarea. El acceso amplio se rechaza aunque sea conveniente.**

### Regla central: MCP y GDPR

| Tipo de datos | ¿MCP seguro? | Razón |
|---|---|---|
| Datos propios / públicos | ✅ Sí | Sin PII de terceros involucrada |
| PII de terceros (leads, clientes) | ❌ No | Los datos viajan a servidores del proveedor IA → pérdida de base legal GDPR |

**PostgreSQL MCP con datos reales = violación GDPR.** Los datos del lead viajan a servidores de Anthropic al ser leídos por Claude. Base legal del tratamiento se pierde.

### MCP servers aprobados (uso personal)

| Server | Uso aprobado | Restricción |
|---|---|---|
| Sequential Thinking | Razonamiento complejo | Sin acceso a datos externos |
| Fetch | Consultar URLs públicas | Solo URLs públicas |
| Brave Search | Búsqueda web | Solo búsquedas públicas |
| SQLite | DB local de pruebas | Solo datos propios, nunca PII real |
| Filesystem | Leer documentación | Restringido a `/docs` y `/adr` únicamente |
| Git | Operaciones de repo | Solo repos públicos con historial limpio de .env |

### Triangulación multi-LLM

La triangulación automatizada via MCP se descartó: cada IA necesita contexto completo para dar respuestas de calidad. Ese contexto lo provee Pablo manualmente con archivos — no puede reconstruirse en un prompt automático. La triangulación manual es superior en calidad.

---

---

# SERIE 100 — DECISIONES DE APRENDIZAJE Y CARRERA

## ADR-101: JavaScript Primero, Python en Fase 2

**Estado**: ✅ Validado  
**Fecha**: 28 enero 2026

### La Decisión

JavaScript como lenguaje principal en Fase 1. Python se pospone a Fase 2 excepto donde hay razón técnica dominante (excepción documentada en ADR-301 para GeoLabor).

### Por qué JS primero

N8N usa JS en sus Code nodes. El proyecto web (pablocuevas.it) ya corre en JS. Aprender dos lenguajes en paralelo fragmenta el aprendizaje sin beneficio real en Fase 1. Un lenguaje dominado > dos lenguajes mediocres.

### Excepción activa

ADR-301 (GeoLabor) justifica Python para integración con librerías de análisis de suelos. Decisión técnica, no de moda.

---

## ADR-102: Proyecto Web como Infraestructura de Negocio

**Estado**: ✅ Validado  
**Fecha**: 28 enero 2026

### La Decisión

pablocuevas.it no es solo portfolio — es la infraestructura del negocio. El formulario de contacto es el punto de entrada de leads reales. El sistema de gestión de leads (PA1) es la automatización de ese negocio.

### Qué implica

Cada mejora al sistema tiene impacto directo en el negocio. No es un ejercicio académico. La URL de producción es la prueba de concepto más poderosa para un cliente potencial.

### Deuda activa

Notificación de leads: la URL ngrok temporal en el formulario se reemplaza por URL VPS en Bloque 6.

---

## ADR-103: Aprendizaje por Construcción, no por Abstracción

**Estado**: ✅ Activo  
**Fecha**: 28 enero 2026

### La Decisión

Cada tecnología nueva necesita un proyecto ancla concreto antes de empezar. No se estudia N8N en abstracto — se construye el sistema de leads. No se estudia PostgreSQL en abstracto — se diseña el schema de PA1.

### El patrón

```
Problema real → Tecnología necesaria → Construcción → Comprensión
```

No al revés. La comprensión emerge de la construcción, no la precede.

### Evidencia

pablocuevas.it: sistema completo en producción en 9 sesiones siguiendo este principio.

---

## ADR-104: Arc42 + C4 como Estándar de Documentación

**Estado**: ✅ Activo  
**Fecha**: 16 abril 2026

### La Decisión

Arc42 como framework de documentación de arquitectura. C4 como lenguaje visual dentro de Arc42. Aplicado desde GeoLabor en adelante. PA1 se migra al llegar a producción (Bloque 6).

### Por qué este estándar

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

## Estado Operativo (Mayo 2026)

| Serie | Count | Activos | Archivo |
|---|---|---|---|
| 000-series (Universal) | 8 | 8 | Este documento |
| 100-series (Aprendizaje) | 5 | 5 | Este documento |
| 200-series (PA1) | 6 | 6 | `ADR-CONSOLIDADAS-PA1_25-04-2026.md` |
| 300-series (GeoLabor) | 4 | 4 | `ADR-CONSOLIDADAS-geolabor-piloto_16-04-2026_.md` |

**Total en este documento**: 13 ADRs (series 000 y 100).  
**Total del sistema**: 23 ADRs distribuidos en 3 archivos.

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

Cuando crees un ADR nuevo de serie 000 o 100:
1. Agrégalo a la tabla "ÍNDICE RÁPIDO"
2. Añade el contenido completo en la sección de serie correspondiente
3. Actualiza la tabla de Estado Operativo
4. Si genera deuda técnica, añádela a la tabla de deuda

Cuando crees un ADR de serie 200+:
1. Va al archivo consolidado del proyecto correspondiente
2. Actualiza solo el count en la tabla de Estado Operativo de este documento

**Importante**: Este documento es el registro maestro de las series 000 y 100.
Las series de proyecto (200, 300+) tienen su propio archivo consolidado.

---

**Documento consolidado por**: Pablo Cuevas + Claude (Anthropic)  
**Fecha consolidación**: 01 de mayo 2026  
**Supersede**: ADR-CONSOLIDADAS-pablo-cuevas_16-04-2026_.md v2.0  
**Próxima revisión**: Al crear ADR-008 (serie 000) o ADR-106 (serie 100)

---

*"Los principios son brújula, no mapa. Guían, no dictan."*  
*"Decisiones no documentadas = Decisiones perdidas."*  
*"Aprendizaje por construcción = Construcción por propósito."*
