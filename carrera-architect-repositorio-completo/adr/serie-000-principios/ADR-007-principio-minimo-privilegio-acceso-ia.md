# ADR-007: Principio de Mínimo Privilegio para Acceso IA

**Estado**: ✅ Aceptada  
**Fecha**: 15 de abril 2026  
**Tipo**: Estratégica — política de seguridad universal  
**Serie**: 000 (principio universal — aplica a cualquier integración IA)  
**Audiencia**: 🔒 Personal  
**Scope**: Cualquier integración de herramienta IA con sistemas locales o datos de clientes

---

## Contexto

Durante la exploración de MCP (Model Context Protocol) servers como mecanismo
de integración entre Claude y sistemas locales, se identificó que la mayoría
de los servers disponibles requieren permisos amplios que violan el principio
de mínimo privilegio y en muchos casos la base legal GDPR.

El caso concreto que disparó esta decisión: PostgreSQL MCP permitiría a Claude
leer directamente la base de datos de leads. Conveniente — pero los datos del
lead viajarían a servidores de Anthropic al ser leídos por Claude, perdiendo
la base legal del tratamiento bajo GDPR.

La misma lógica aplica a cualquier herramienta IA (agentes, APIs, MCPs)
que acceda a sistemas con PII de terceros.

---

## La Regla Central: MCP y GDPR

| Tipo de datos | ¿Acceso IA seguro? | Razón |
|---|---|---|
| Datos propios / públicos | ✅ Sí | Sin PII de terceros involucrada |
| Documentación y ADRs | ✅ Sí | No contienen PII operativa |
| Código fuente sin credenciales | ✅ Sí | Datos técnicos propios |
| PII de terceros (leads, clientes) | ❌ No | Datos viajan a servidores del proveedor IA → pérdida de base legal GDPR |
| Bases de datos en producción con PII | ❌ No | PostgreSQL MCP = violación GDPR |
| Credenciales y tokens | ❌ No | Exposición de secretos |

**Regla**: MCP es seguro cuando los datos que viajan son propios y públicos.
Es riesgo GDPR cuando son PII de terceros.

---

## Decisión

**Las herramientas IA (MCP servers, APIs, agentes) reciben acceso mínimo
necesario para su tarea. El acceso amplio se rechaza aunque sea conveniente.**

### MCP Servers aprobados para uso personal

| MCP Server | Tipo de datos | Justificación |
|---|---|---|
| Sequential Thinking | Sin datos externos | Razonamiento interno — sin PII |
| Fetch | URLs públicas | Solo contenido público — sin PII |
| Brave Search | Búsqueda web pública | Sin acceso a sistemas internos |
| SQLite | DB local de experimentación | Sin PII de clientes reales |
| Filesystem | Restringido a `/docs` y `/adr` | Solo documentación propia — sin PII |
| Git | Solo repos públicos con historial limpio | Sin credenciales en historial |

### MCP Servers rechazados

| MCP Server | Por qué rechazado |
|---|---|
| PostgreSQL (con datos reales) | PII de leads viajan a servidores de Anthropic → pérdida de base legal GDPR |
| Google Drive (docs de clientes) | Documentos con PII de terceros |
| Slack / Telegram | Notificaciones con PII de leads |
| Cualquier MCP con acceso a producción | Mismo problema — PII fuera de infraestructura controlada |

### Triangulación multi-LLM

La triangulación sigue siendo **manual**, no automatizada via MCP.

**Razón**: Cada IA necesita contexto completo para dar respuestas de calidad.
Ese contexto lo construye Pablo manualmente con archivos curados — un prompt
automático enviado por MCP no puede reconstruir el nivel de contexto que da
el método manual. La fricción del método manual es intencional y produce
mejor resultado.

### Filesystem MCP — restricción específica

Aprobado solo para `/docs` y `/adr`. No para el repositorio completo.

**Razón**: Filesystem MCP no es equivalente a Projects con contexto infinito.
Los archivos leídos consumen tokens igual que si se pegaran manualmente.
Con Projects y archivos curados se obtiene mejor calidad por menos coste.
El acceso amplio al filesystem no añade valor real y aumenta el riesgo de
exponer accidentalmente archivos con credenciales o PII.

---

## Consecuencias

**✅ Positivas**:
- Base legal GDPR mantenida — PII de terceros nunca sale de infraestructura controlada
- Diferenciador de mercado documentable: "las herramientas IA usadas en el
  desarrollo de tu sistema no tienen acceso a tus datos"
- Menor superficie de ataque — cada integración IA tiene scope limitado

**⚠️ Negativas**:
- Triangulación multi-LLM más lenta (manual vs automatizada)
- Algunas tareas convenientes con MCP amplio deben hacerse de otra forma
- Requiere evaluar cada nuevo MCP server antes de conectarlo

---

## Cómo evaluar un nuevo MCP server

Antes de conectar cualquier MCP server nuevo, responder:

1. ¿Qué datos pueden viajar a través de este MCP?
2. ¿Alguno de esos datos es PII de terceros?
3. ¿Esos datos abandonarían la infraestructura controlada?
4. ¿El beneficio de conveniencia justifica el riesgo?

Si la respuesta a 2 o 3 es SÍ → rechazar sin excepción.

---

## Evolución futura

Cuando Suanfarma u otro cliente enterprise requiera integraciones IA más
avanzadas, el principio no cambia — el scope permitido puede ampliarse solo
si se mantiene PII dentro de infraestructura bajo DPA. Opciones a evaluar
en Fase 2: modelos locales (Ollama), APIs con DPA firmado, sandboxing
por cliente.

---

## Referencias

- ADR-002 — PII Management (política base que este ADR extiende)
- ADR-006 — Gestión de credenciales (complementario)
- ADR-008 — Arquitectura de seguridad VPS (mismo principio aplicado a infraestructura)
- Exploración MCP 15/04/2026 — evaluación de 20+ MCP servers

---

**Creado**: 15 de abril 2026  
**Última revisión**: 2 de mayo 2026
