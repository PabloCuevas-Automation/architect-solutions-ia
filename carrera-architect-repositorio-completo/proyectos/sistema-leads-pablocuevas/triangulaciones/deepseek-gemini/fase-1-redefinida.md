# FASE 1: CONSTRUCTOR DE SISTEMAS DE AUTOMATIZACIÓN
## Roadmap Redefinido — Pablo Cuevas

**Versión**: 1.0  
**Fecha**: 25 de febrero 2026  
**Estado**: ✅ Activo  
**Supersede**: Plan de Acción Primeros 3 Meses + Adiciones Fase 1 + Recalibración v2.0

---

## ¿Quién eres al entrar a esta fase?

Entras con:
- JavaScript funcional (DOM, eventos, módulos ES6, async/await, arrays, objetos, JSON)
- Git + GitHub operativo con CI/CD
- Firebase Firestore integrado en proyecto real
- Proyecto web en producción (pablocuevas.it — 98/100 PageSpeed)
- Pensamiento arquitectónico básico (ADRs, trade-offs, deuda técnica consciente)
- GDPR by design implementado y verificado
- Docker instalado, sin usar productivamente aún
- N8N: territorio nuevo

---

## ¿Quién eres al salir de esta fase?

Salir de Fase 1 significa poder decir con evidencia real:

**"Diseño y construyo sistemas de automatización con N8N que son robustos, escalables, seguros y documentados. Entiendo cómo funcionan por dentro, puedo deployarlos, conectarlos a bases de datos y APIs, y explicarlos a cualquier cliente sin vergüenza técnica."**

No eres un automatizador que usa N8N. Eres alguien que construye sistemas donde N8N es el núcleo, y ese núcleo está bien conectado, bien protegido y bien documentado.

La diferencia: un automatizador entrega un workflow. Un arquitecto en formación entrega un sistema.

---

## PRINCIPIO RECTOR DE ESTA FASE

> **Cada tecnología que aprendes debe desbloquearte la siguiente y proyectarse en un entregable real.**

No aprendes SQL porque el roadmap lo dice. Lo aprendes cuando un workflow necesita persistir datos estructurados y te das cuenta de que Firebase no es suficiente. No aprendes Cloud porque es cool. Lo aprendes cuando tu primer cliente necesita que su automatización corra a las 3am sin tu laptop encendida.

El orden no es de menor a mayor complejidad. Es de menor a mayor necesidad real.

---

## STACK DE APRENDIZAJE — ORDEN SINÉRGICO

### CAPA 1: N8N CORE + JAVASCRIPT APLICADO
*Lo que desbloquea todo lo demás*

Antes de tocar cualquier otra tecnología, N8N tiene que ser tu territorio familiar. No experto — familiar. Sabes moverte, sabes debuggear, sabes qué nodo hace qué.

**JavaScript → N8N Code Node**
Ya tienes JavaScript. Ahora lo aplicas donde importa: el Code node de N8N. Este nodo es donde tu JS se convierte en lógica de negocio real dentro de un workflow.

Lo que necesitas dominar aquí:
- `$input.all()` — cómo llegan los datos al nodo
- `$json` — estructura de un item
- Transformar, filtrar, enriquecer items con JS puro
- Spread operator para no perder datos originales
- Optional chaining para datos inconsistentes de APIs reales
- Error handling dentro del Code node

**N8N como sistema, no como herramienta**

Desde el primer workflow, piensa así:
```
¿Qué dispara esto? (Trigger)
¿Qué datos entran? (Input)
¿Qué transformación ocurre? (Process)
¿Qué puede fallar? (Error path)
¿Qué sale? (Output)
¿Dónde viven los datos? (Storage)
¿Quién tiene acceso? (Security)
```

Estas preguntas no son para workflows complejos. Son para cada workflow desde el primero.

**Proyecto ancla Capa 1:**
Firebase Firestore → N8N detecta nuevo lead → formatea datos → envía notificación (Telegram o email). Este es tu problema real hoy. Resolverlo te enseña triggers, HTTP nodes, Code node y el modelo mental completo de N8N.

---

### CAPA 2: APIs + INTEGRACIONES REALES
*Lo que conecta tu sistema con el mundo*

N8N sin integraciones es un motor sin combustible. Las APIs son el combustible.

Ya tienes la base de fetch y async/await. Ahora lo aplicas en N8N con criterio de arquitecto:

**HTTP Request Node — el nodo más importante después del Code node**
- GET, POST, PUT, PATCH, DELETE en contexto real
- Headers, autenticación (API key, Bearer token, OAuth)
- Manejo de respuestas: qué haces con un 401, con un 500, con un timeout
- Rate limiting: qué pasa cuando llamas 1000 veces a una API con límite de 100/hora

**Credenciales en N8N**
N8N tiene un sistema de gestión de credenciales encriptadas. Nunca hardcodeas una API key en un workflow. Esto no es burocracia — es la diferencia entre un sistema que puedes entregar a un cliente y uno que no.

**Webhooks**
Entender webhooks es entender la mitad de las integraciones reales:
- Diferencia entre polling y webhook (cuándo usar cada uno)
- N8N como receptor de webhooks
- Verificación de signatures (seguridad básica)
- Idempotencia: qué pasa si el mismo webhook llega dos veces

**La sinergia con Capa 1:**
Cada API que integras en N8N es un ejercicio de JavaScript aplicado. El JSON que devuelve una API es el input de tu Code node. Si dominas la transformación de datos en JS, dominas la integración de APIs en N8N.

**Proyecto ancla Capa 2:**
Sistema de notificación de leads enriquecido. Cuando llega un lead por Firebase, N8N no solo notifica — también enriquece: consulta una API externa para obtener más datos de la empresa, clasifica el lead, y lo guarda estructurado. Mínimo 3 APIs integradas.

---

### CAPA 3: PERSISTENCIA DE DATOS — SQL + POSTGRESQL
*Lo que hace que tu sistema tenga memoria*

N8N procesa datos en tiempo real. Pero los sistemas reales necesitan memoria — guardar qué pasó, cuándo, con quién, qué resultado hubo. Ahí entra SQL.

No aprendes SQL porque el roadmap lo dice. Lo aprendes porque en algún punto un workflow va a necesitar:
- Guardar el historial de leads procesados
- Verificar si un registro ya existe antes de procesarlo (deduplicación)
- Consultar datos de configuración que cambian sin tocar el código
- Generar reportes de qué hizo tu sistema

**Nivel objetivo — SQL funcional para automatización:**
```sql
SELECT, WHERE, ORDER BY, LIMIT
JOINs (INNER, LEFT) — para combinar tablas
GROUP BY, COUNT, SUM — para reportes básicos
INSERT, UPDATE, DELETE — para escribir desde N8N
Subqueries básicas
Transacciones (BEGIN, COMMIT, ROLLBACK) — para operaciones críticas
Índices — qué son y por qué importan para performance
```

**PostgreSQL + N8N**
N8N tiene nodo nativo de PostgreSQL. Conectar N8N a una base de datos real es donde el sistema empieza a tener persistencia verdadera.

Lo que necesitas saber hacer:
- Crear schema básico para un caso de uso real
- Query desde N8N con parámetros dinámicos (sin SQL injection)
- Upsert — insertar si no existe, actualizar si existe
- Manejar errores de DB dentro del workflow

**La sinergia con Capas anteriores:**
Cuando tu workflow de leads (Capa 2) necesita no procesar el mismo lead dos veces, vas a la DB. Cuando necesitas saber cuántos leads procesaste esta semana, vas a la DB. SQL no es una capa separada — es la memoria de todo lo que construiste antes.

**GDPR aplicado a datos persistidos:**
Cada tabla que creas tiene implicaciones de compliance:
- ¿Qué datos son PII?
- ¿Cuánto tiempo los guardas?
- ¿Puedes eliminarlos si un usuario lo pide?
- ¿Quién tiene acceso?

Documenta esto desde el primer schema. No como burocracia — como diferenciador de mercado.

**Proyecto ancla Capa 3:**
Migra el sistema de leads a PostgreSQL. Cada lead que entra queda registrado con timestamp, fuente, datos enriquecidos, estado de procesamiento. Añade deduplicación — si el mismo email llega dos veces, el workflow lo detecta y no procesa dos veces. Genera un reporte semanal automático con SQL + N8N.

---

### CAPA 4: DEPLOY + CLOUD BÁSICO
*Lo que hace que tu sistema viva sin ti*

N8N en tu laptop local es un prototipo. N8N que un cliente puede usar mientras duermes es un sistema.

El salto de local a cloud es el salto de "esto funciona en mi máquina" a "esto es un producto entregable".

**Docker — de instalado a entendido**
Docker ya está instalado desde Fase 0.5. Ahora lo entiendes:
- Qué es un contenedor y por qué existe
- `docker-compose.yml` — definir tu stack completo como código
- Volúmenes — cómo persisten los datos de N8N y PostgreSQL
- Variables de entorno — configuración sin hardcodear
- Redes — cómo se hablan los contenedores entre sí

Tu stack completo en docker-compose:
```yaml
n8n + PostgreSQL + configuración de red + volúmenes
```
Un comando levanta todo. Eso es infraestructura como código, aunque básico.

**VPS — tu primer servidor real**
Un VPS (Virtual Private Server) es un servidor en la nube que controlas tú. Es más barato y más educativo que AWS para empezar.

Opciones reales para Fase 1:
- Hetzner (€4-6/mes, excelente para Europa, GDPR-friendly)
- DigitalOcean (€6/mes, buena documentación)

Lo que necesitas saber hacer:
- Conectarte por SSH
- Comandos básicos de Linux (ls, cd, mkdir, nano, systemctl)
- Instalar Docker en el servidor
- Deployar tu docker-compose en el servidor
- Configurar dominio (DNS básico)
- HTTPS con Certbot (obligatorio — sin HTTPS no entregas nada a un cliente)
- Firewall básico (ufw) — qué puertos abres y cuáles cierras

**N8N self-hosted en producción:**
- Variables de entorno de N8N (webhook URL, encryption key, timezone)
- Basic auth o autenticación para el panel de N8N
- Backups de workflows y credenciales
- Monitoreo básico: saber si N8N está caído

**AWS — conceptual en Fase 1, operativo en Fase 2**
AWS es más complejo y más caro para empezar. En Fase 1 necesitas entender qué es, qué servicios existen y por qué las empresas grandes lo usan. No necesitas deployar en AWS en Fase 1 — eso llega cuando los clientes lo exigen.

Lo que sí necesitas entender conceptualmente:
- EC2 (servidor virtual)
- S3 (almacenamiento de archivos)
- RDS (bases de datos gestionadas)
- IAM (gestión de permisos)

**La sinergia con todo lo anterior:**
Cuando tu sistema de leads (Capas 1-3) está en un VPS con HTTPS y corre solo, tienes tu primer producto entregable. No es sofisticado — pero es real, es tuyo y funciona.

**Proyecto ancla Capa 4:**
Deploy completo del sistema de leads en VPS. N8N + PostgreSQL corriendo en Hetzner con dominio propio, HTTPS, basic auth y backup automático semanal. El cliente puede entrar al panel de N8N desde cualquier lugar y ver sus automatizaciones corriendo.

---

### CAPA 5: SEGURIDAD + COMPLIANCE APLICADO
*Lo que hace que tu sistema sea entregable a clientes reales*

La seguridad no es una capa adicional que añades al final. Es un principio que aplicas en cada capa. Esta sección documenta qué significa en la práctica para cada componente de tu stack.

**Seguridad en N8N:**
- Credentials management — nunca en el workflow, siempre en el vault de N8N
- Webhook signature verification — validar que el webhook viene de quien dice venir
- Input sanitization en Code nodes — no confíes en datos externos
- Error messages — no expongas información interna en errores públicos
- Audit trail — loggear qué hizo cada workflow y cuándo

**Seguridad en PostgreSQL:**
- Usuario específico para N8N con permisos mínimos (no usar superuser)
- Conexión interna (no exponer puerto 5432 al exterior)
- Queries parametrizadas — prevenir SQL injection
- Backups encriptados

**Seguridad en el servidor:**
- SSH con clave, no con contraseña
- Firewall: solo los puertos necesarios (80, 443, 22)
- Updates automáticos de seguridad
- N8N detrás de reverse proxy (Nginx o Traefik)

**GDPR aplicado — más allá del banner de cookies:**
En Fase 1 ya tienes GDPR básico de tu web. Ahora lo aplicas a los sistemas que construyes para clientes:

- Data mapping: qué datos procesa el sistema, de dónde vienen, dónde van
- Minimización: solo procesar los datos que el workflow necesita
- Retención: automatizar la eliminación de datos después de X días
- Portabilidad: poder exportar los datos de un usuario si lo pide
- Registro de actividad: log de qué se procesó y cuándo

**La documentación como entregable:**
Cuando entregues un sistema a un cliente, la documentación de compliance es parte del entregable. No es opcional. Es lo que te diferencia de los automatizadores que instalan Make y desaparecen.

Template mínimo por proyecto:
```
- Qué datos procesa el sistema
- Cuáles son PII
- Dónde se almacenan y por cuánto tiempo
- Cómo se eliminan
- Quién tiene acceso
- Cómo se audita el acceso
```

---

### CAPA 6: SISTEMAS RESILIENTES
*Lo que hace que tu sistema no falle silenciosamente*

Un sistema que falla sin que nadie lo sepa es peor que un sistema que no existe. La resiliencia no es una feature avanzada — es el mínimo viable para sistemas que manejan datos de clientes reales.

**Error handling en N8N:**
- Error workflows — qué hace N8N cuando un nodo falla
- Retry con backoff — reintentar con espera creciente
- Dead letter — qué pasa con los datos cuando todo falla
- Alertas — notificación cuando algo falla (Telegram, email)

**El patrón de fallback:**
```
IA / API externa
    ↓ (si falla)
Reglas simples
    ↓ (si no aplica)
Escala a humano + contexto
```

Este patrón aparece en todo sistema robusto. Aprenderlo en N8N te da el framework mental para aplicarlo en sistemas más complejos en Fase 2.

**Monitoreo básico:**
- Saber si N8N está caído (healthcheck)
- Saber si un workflow no se ejecutó cuando debía
- Saber si una API externa está dando errores consistentes
- Logs estructurados — no console.log aleatorio, sino información útil para debuggear

**Idempotencia:**
¿Qué pasa si tu workflow se ejecuta dos veces con los mismos datos? El sistema tiene que dar el mismo resultado y no crear duplicados. Esto se diseña, no se improvisa.

---

## LOS TRES PROYECTOS ANCLA DE FASE 1

Estos proyectos no son ejercicios académicos. Son sistemas reales que integran múltiples capas y que pueden ser mostrados a clientes como evidencia de capacidad.

### PROYECTO 1 — Sistema de Gestión de Leads (Capas 1-3)
**Lo que demuestra:** N8N operativo, APIs integradas, SQL con persistencia, GDPR básico

**Spec:**
- Firebase Firestore como fuente de leads (tu web ya lo genera)
- N8N detecta nuevo lead en tiempo real
- Enriquecimiento: consulta al menos 2 APIs externas (ej: validación de email, datos de empresa)
- Clasificación con lógica condicional (lead caliente / tibio / frío)
- Persistencia en PostgreSQL con historial completo
- Deduplicación — el mismo email no se procesa dos veces
- Notificación por Telegram o email con datos formateados
- Reporte semanal automático

**Documentación obligatoria:**
- ADR de arquitectura del sistema
- Data mapping GDPR
- README con diagrama de flujo

---

### PROYECTO 2 — Sistema Deployado en Producción (Capas 4-5)
**Lo que demuestra:** Capacidad de entregar un sistema real a un cliente

**Spec:**
- Toma el Proyecto 1 y deployalo en VPS (Hetzner o DigitalOcean)
- Docker Compose con N8N + PostgreSQL
- Dominio propio con HTTPS (Certbot)
- Basic auth en el panel de N8N
- Backup automático semanal
- Monitoreo básico con alerta si N8N cae
- Firewall configurado correctamente
- Documentación de deployment para poder replicarlo

**El criterio real:** ¿Puedes apagar tu laptop y el sistema sigue funcionando? Si sí, está deployado.

**Documentación obligatoria:**
- Guía de deployment paso a paso
- Runbook: qué hacer si algo falla
- Costos mensuales documentados

---

### PROYECTO 3 — Sistema con Fallback y Resiliencia (Capa 6)
**Lo que demuestra:** Pensamiento arquitectónico aplicado — no solo "funciona" sino "funciona aunque falle"

**Spec:**
Elige un caso de uso real (puede ser para un cliente o propio):
- Nivel 1: automatización principal con N8N
- Nivel 2: si falla, reglas simples como fallback
- Nivel 3: si las reglas no aplican, escala a humano con contexto completo
- Log de qué nivel resolvió cada caso
- Alerta si el nivel 1 falla más del 5% del tiempo
- Reporte mensual de métricas por nivel

**Ejemplo concreto:**
Sistema de respuesta a consultas de clientes:
- N8N + IA clasifica y responde automáticamente (nivel 1)
- Si la IA no tiene confianza suficiente → respuesta genérica de FAQ (nivel 2)
- Si tampoco aplica → notificación urgente a Pablo con todo el contexto (nivel 3)

**Documentación obligatoria:**
- ADR: por qué tres niveles y no solo IA
- Métricas: en 100 casos, cuántos resuelve cada nivel
- Cost analysis: cuánto cuesta cada nivel por caso

---

## CHECKLIST DE VALIDACIÓN FASE 1 → FASE 2

No pasas a Fase 2 hasta cumplir estos criterios. No son todos técnicos — son sistémicos.

### Validación Técnica

**N8N:**
- [ ] Puedes construir un workflow desde cero sin consultar tutoriales para la estructura básica
- [ ] El Code node es tu herramienta habitual, no algo que evitas
- [ ] Tienes error workflows configurados en todos tus sistemas productivos
- [ ] Sabes diagnosticar por qué un workflow falló mirando el execution log

**JavaScript aplicado:**
- [ ] Transformas JSON complejo en N8N Code node sin fricción
- [ ] Manejas errores de APIs externas dentro de workflows
- [ ] Puedes escribir lógica condicional compleja sin googlear la sintaxis

**APIs:**
- [ ] Has integrado 5+ APIs diferentes en workflows reales
- [ ] Entiendes autenticación (API key, Bearer, OAuth) y la implementas correctamente
- [ ] Manejas rate limiting y timeouts en producción

**SQL:**
- [ ] Puedes escribir queries con JOIN de 2+ tablas sin googlear
- [ ] Tienes N8N conectado a PostgreSQL en al menos un sistema
- [ ] Implementaste deduplicación con SQL en un workflow real

**Deploy:**
- [ ] Tienes al menos un sistema corriendo en VPS con Docker
- [ ] HTTPS configurado y funcionando
- [ ] Sabes hacer SSH a tu servidor y diagnosticar problemas básicos
- [ ] Tienes backup automático de datos y workflows

**Seguridad:**
- [ ] Nunca tienes API keys hardcodeadas en workflows
- [ ] Tus servidores tienen firewall configurado
- [ ] Puedes hacer el data mapping GDPR de cualquier sistema que construiste

### Validación de Sistemas

- [ ] 3 proyectos completos documentados con ADR
- [ ] Al menos 1 sistema con fallback de 3 niveles
- [ ] Al menos 1 sistema deployado en producción real (no local)
- [ ] Portfolio GitHub con READMEs claros que explican arquitectura, no solo código

### Validación de Negocio

- [ ] Has contactado activamente a al menos 10 prospectos en Rovereto/Trento
- [ ] Tienes al menos 1 conversación comercial real en curso o completada
- [ ] Tus servicios están definidos con pricing claro
- [ ] Puedes explicar el valor de una automatización en términos de horas ahorradas o euros, no en términos técnicos

### Validación de Mentalidad (la más importante)

- [ ] Cuando describes un sistema, empiezas por el problema de negocio, no por la tecnología
- [ ] Documentas decisiones naturalmente — no como tarea, como hábito
- [ ] Identificas PII y implicaciones GDPR automáticamente al diseñar un sistema
- [ ] Puedes defender tus decisiones arquitectónicas ante alguien que te cuestione
- [ ] Cuando algo falla, tu primera pregunta es "¿qué diseño del sistema evitaría esto?" no "¿cómo lo arreglo?"

---

## SEÑALES DE ALERTA

Revisa esto cada mes. Sin excusas.

### 🔴 ALERTA ROJA — Para y recalibra

- Llevas más de 3 semanas sin construir nada que funcione en producción
- Estás aprendiendo tecnologías que nadie te ha pedido ni necesitas para tus proyectos ancla
- No puedes explicar un workflow tuyo a alguien no técnico
- Tienes workflows que no tienen error handling
- Llevas 4+ meses sin ninguna conversación comercial

**Acción:** Vuelve al proyecto ancla más cercano. Termínalo. Documéntalo. Muéstraselo a alguien.

### 🟡 ALERTA AMARILLA — Ajusta rumbo

- Solo construyes en local, nada deployado
- Tus ADRs los escribes por obligación, no porque te ayuden a pensar
- Las APIs que integras son siempre las mismas (JSONPlaceholder, APIs de prueba)
- No has hablado con ningún prospecto en 2 semanas

**Acción:** Sube el nivel de realidad. Deploya algo. Habla con alguien del mercado.

### 🟢 SEÑAL VERDE — Vas bien

- Cada proyecto que terminas enseña algo que aplicas en el siguiente
- Los clientes o prospectos te preguntan cosas que te hacen aprender
- Documentas porque te ayuda a pensar, no porque alguien te lo pide
- Cuando aparece un problema técnico, tu primera reacción es diseñar la solución antes de googlear

---

## PRINCIPIOS DE ESTA FASE (NO NEGOCIABLES)

**1. Aprendizaje por construcción**
Cada tecnología se aprende construyendo algo real con ella. No tutoriales en abstracto.

**2. Proyecto ancla obligatorio**
Antes de aprender algo nuevo, define a qué proyecto ancla se conecta. Si no tiene proyecto ancla, no es prioridad ahora.

**3. Evidencia antes que expansión**
No añades nuevas tecnologías al stack hasta tener evidencia real de que las anteriores funcionan en producción.

**4. Compliance desde el diseño**
GDPR no es una capa que se añade al final. Se diseña desde el primer diagrama.

**5. Deuda técnica consciente**
Puedes tomar atajos — pero los documentas, sabes cuándo los tienes y cuándo los pagarás.

**6. Anti vendor lock-in**
N8N self-hosted sobre N8N cloud. PostgreSQL sobre Firebase para datos críticos. Tus sistemas deben poder migrar si cambias de proveedor.

**7. El sistema completo, no el componente**
Optimizas el flujo completo, no el nodo más elegante. Un workflow hermoso que nadie usa vale menos que uno feo que resuelve un problema real.

---

## LO QUE NO ES PARTE DE FASE 1

Esto llega en Fase 2. Mencionarlo ahora es ruido:

- Python (FastAPI, scripts avanzados)
- AWS (más allá del concepto)
- RAG y sistemas IA complejos
- Fine-tuning de modelos
- Kubernetes
- MLOps
- Certificaciones cloud

---

## NOTA SOBRE EL TIEMPO

Esta fase no tiene fecha de fin predefinida. Tiene criterios de fin.

Podrías completarla en 4 meses. Podrías tardar 8. Lo que define el tiempo no es el calendario — es la velocidad con la que construyes sistemas reales y los expones a condiciones reales (clientes, producción, feedback).

Lo que acelera la fase: tener un cliente real que presiona. Lo que la ralentiza: construir en local sin nunca deployar ni hablar con nadie del mercado.

---

**Documento creado**: 25 de febrero 2026  
**Autor**: Pablo Cuevas + Claude Sonnet (Anthropic)  
**Próxima revisión**: Al completar Proyecto Ancla 1 o al cerrar primer cliente  
**Documentos que supersede**: Plan de Acción Primeros 3 Meses, Adiciones Fase 1, Recalibración v2.0 días 26-42
