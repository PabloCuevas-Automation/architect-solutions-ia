# ADR-009: Seguridad de Aplicación en Workflows N8N

**Fecha**: 2 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — proceso de auditoría universal  
**Serie**: 000 (principio universal — aplica a todos los proyectos)  
**Audiencia**: 🔒 Personal

---

## Contexto

La triangulación de seguridad y la simulación de ataque revelaron que
la arquitectura de infraestructura (ADR-008) puede contener eficazmente
un ataque hasta cierto punto, pero tiene un límite estructural conocido:

> *La infraestructura contiene el daño. Los workflows son el daño.*

Los workflows de N8N son código ejecutable con acceso a credenciales de APIs,
bases de datos con PII, y canales de comunicación al exterior (Telegram, email,
HTTP requests). Un workflow con una vulnerabilidad de lógica puede convertir
el sistema en su propia herramienta de exfiltración — sin que ninguna capa
de infraestructura lo detecte como tráfico anómalo.

Ejemplos concretos identificados en PA1:

**Capa 1 — HMAC placeholder**: sin validación de origen, cualquier petición
HTTP al webhook es procesada. Un atacante con la URL puede inyectar datos
controlados, agotar créditos de Abstract API, y sondear el comportamiento
del sistema.

**Capa 4 — PII en Telegram**: la implementación actual enviaba nombre,
email y messaggio directamente a Telegram. Un atacante que modifique el
workflow puede usar el canal de Telegram existente para exfiltrar
la base de datos completa — usando HTTPS legítimo que el egress filtering
permite.

**messaggio sin sanitizar**: el campo de texto libre del formulario se
almacenaba sin limpiar. Si `messaggio` contiene JavaScript, la interfaz
de administración tiene XSS persistente.

**SQL injection en expresiones inline**: el patrón `{{ $json.email }}`
en queries PostgreSQL es interpolación de strings. Con HMAC desactivado,
un atacante puede inyectar SQL a través del formulario.

---

## La distinción fundamental

**Seguridad de infraestructura** (ADR-008): controla quién puede llegar
al sistema y qué puede hacer a nivel de red, OS y contenedores.

**Seguridad de aplicación** (este ADR): controla qué hace el sistema
con los datos que recibe, cómo los procesa, y a dónde los envía.

Ambas capas son necesarias y complementarias. Una sin la otra deja
la mitad del sistema desprotegida.

---

## Decisión

### Principio 1 — Los workflows son código. Se tratan como código.

Un workflow nuevo o modificado que toque datos de clientes requiere
revisión de seguridad antes de activarse en producción. La revisión
usa el Workflow Audit Template — un documento de una página con tres
preguntas estándar.

Esto no es burocracia — es la diferencia entre descubrir una vulnerabilidad
antes o después de que haya datos reales expuestos.

### Principio 2 — Todo input externo es hostil hasta que se valide

Cualquier dato que llega desde el exterior del sistema — el formulario web,
una API, un webhook de terceros — puede estar manipulado. El primer nodo
que toca esos datos es responsable de:

1. Validar que los campos existen y son del tipo esperado
2. Sanitizar el contenido antes de persistirlo o mostrarlo
3. Rechazar la petición si la validación falla — no procesarla silenciosamente

### Principio 3 — Mínimo privilegio también para workflows

Un workflow de reporting semanal no necesita acceso a `messaggio`.
Un workflow de health check no necesita acceder a la tabla `leads`.
Si un workflow tiene acceso a más datos de los que necesita, ese
acceso extra es superficie de ataque innecesaria.

### Principio 4 — Los canales de salida son vectores de exfiltración

Todo nodo que envíe datos al exterior — Telegram, email, HTTP request,
webhook a terceros — es un canal potencial de exfiltración si el workflow
es comprometido o mal configurado.

Antes de activar cualquier workflow, revisar explícitamente:
- ¿Qué datos pasan por cada canal de salida?
- ¿Son los mínimos necesarios para la función del workflow?
- ¿Podría un atacante que modifique este nodo usarlo para sacar más datos?

---

## Vulnerabilidades concretas de PA1 y sus resoluciones

### V1 — HMAC como placeholder (Capa 1)
**Riesgo**: sin validación de origen, el webhook acepta cualquier petición.
**Resolución**: implementar HMAC real antes de producción (pre-Bloque 6).
**Verificación**: enviar request sin header `X-Webhook-Signature` → debe rechazar.

### V2 — PII en notificaciones Telegram (Capa 4)
**Riesgo**: violación directa de ADR-206. PII sale de la infraestructura.
**Resolución**: reconstruir Capa 4 según ADR-210.
**Verificación**: procesar lead de prueba y confirmar que Telegram no recibe
nombre, email ni messaggio.

### V3 — SQL injection en expresiones inline
**Riesgo**: con HMAC desactivado, inputs controlados por atacante pueden
inyectar SQL en las queries de N8N.
**Resolución**: el HMAC real (V1) mitiga el vector principal. Evaluar
sanitización adicional de inputs en Bloque 7.
**Estado**: deuda técnica con cobertura parcial via HMAC.

### V4 — messaggio sin sanitizar
**Riesgo**: XSS persistente en la interfaz de administración cuando
se renderice en un navegador.
**Resolución**: añadir sanitización en `procesador-body1` antes de producción.
**Verificación**: enviar `messaggio: "<script>alert('xss')</script>"` →
PostgreSQL debe almacenar `&lt;script&gt;alert(&#x27;xss&#x27;)&lt;/script&gt;`.

### V5 — Exfiltración por canal legítimo de Telegram
**Riesgo**: un atacante que modifique el workflow `notificador-procesar-nivel1`
puede usar el nodo de Telegram existente para enviar datos de PostgreSQL.
El egress filtering permite HTTPS — Telegram usa HTTPS.
**Resolución**: control de versiones de workflows + auditoría de cambios.
No tiene solución de infraestructura — es un control de proceso.

---

## Las tres preguntas de auditoría

Antes de activar cualquier workflow nuevo o modificado en producción:

**Pregunta 1 — ¿Qué entra y está validado?**
Listar cada campo externo que el workflow usa. Para cada uno:
¿se valida el tipo? ¿se sanitiza antes de usarlo en queries o outputs?
¿se rechaza la petición si la validación falla?

**Pregunta 2 — ¿Tiene este workflow acceso a más datos de los que necesita?**
Revisar las credenciales de base de datos y los nodos de acceso a datos.
Si el workflow solo necesita leer `servizio` y `id`, ¿por qué tiene
acceso a `email` y `messaggio`? Reducir el scope a lo mínimo necesario.

**Pregunta 3 — ¿Qué canales de salida usa y qué datos les pasan?**
Listar cada nodo que envíe datos fuera del workflow:
Telegram, email, HTTP request, PostgreSQL, webhook externo.
Para cada canal: ¿qué datos pasan exactamente? ¿Son los mínimos necesarios?
¿Podría un atacante que modifique este nodo exfiltrar más datos?

---

## Consecuencias

**✅ Positivas**:
- Las vulnerabilidades de lógica se detectan antes de que haya datos reales
- El vector de exfiltración por canales legítimos queda mitigado por proceso
- Documentación auditable de due diligence GDPR por workflow
- Reutilizable como estándar en GeoLabor, Suanfarma y proyectos futuros

**⚠️ Negativas**:
- Añade tiempo de revisión antes de cada activación en producción
- No elimina todos los riesgos de lógica — un error de revisión puede
  dejar pasar una vulnerabilidad
- Requiere disciplina de proceso continua, no es una configuración puntual

---

## Referencias

- ADR-008 — Arquitectura de seguridad VPS (capa de infraestructura complementaria)
- ADR-210 — Reconstrucción de Capa 4 (resolución de V2)
- ADR-010 — Secuencia de deploy: seguridad antes que producción
- Workflow Audit Template — documento de auditoría por workflow
- ADR-202 — Patrón de queries PostgreSQL en N8N (relacionado con V3)

---

**Creado**: 2 de mayo 2026  
**Última revisión**: 2 de mayo 2026
