# ADR-207: Reconstrucción de Capa 4 Antes de Producción

**Fecha**: 2 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Técnica — corrección de implementación existente  
**Serie**: 200 (decisión específica de PA1)  
**Audiencia**: 🔒 Personal  
**Supersede**: implementación actual de Capa 4 (notificaciones con PII completa)

---

## Contexto

La Capa 4 del sistema PA1 está implementada y funcional. Los nodos
`notificador-procesar-nivel1`, `notificador-revisar-nivel1` y
`notificador-revisar-nivel2` envían notificaciones a Telegram con
el siguiente contenido actual:

```
Nome: {nome} {cognome}
Email: {email}
Azienda: {azienda}
Servizio: {servizio}
Messaggio: {messaggio}
Hora: {timestamp_roma}
```

El 2 de mayo 2026 se aprobó ADR-206 (Minimización PII en Notificaciones
Telegram), que establece explícitamente que ningún dato personal puede
viajar por Telegram. La implementación actual viola ese ADR de forma directa.

**Esta contradicción no es deuda técnica futura — es una violación activa
de una decisión arquitectónica ya tomada.** Debe resolverse antes de que
cualquier dato real de cliente entre al sistema.

---

## La contradicción exacta

| Campo actual en Telegram | ¿Es PII? | ¿Permitido por ADR-206? |
|--------------------------|----------|------------------------|
| `nome` + `cognome` | ✅ SÍ | ❌ NO |
| `email` | ✅ SÍ | ❌ NO |
| `azienda` | ❌ NO | ✅ SÍ |
| `servizio` | ❌ NO | ✅ SÍ |
| `messaggio` | ✅ SÍ | ❌ NO |
| `timestamp_roma` | ❌ NO | ✅ SÍ |

Tres de los seis campos actuales son PII directa que no puede
salir de la infraestructura controlada.

---

## Decisión

**Reconstruir todos los nodos de notificación de Capa 4 antes de
desplegar en el VPS de producción.**

### Estructura de notificación nueva (todos los tipos)

```
🔔 Nuevo lead #{id}
Servicio: {servizio}
Acción: {accion_recomendada}
Riesgo: {nivel_riesgo} | Score: {quality_score}

→ Ver lead: https://admin.pablocuevas.it/leads/{id}
```

### Por tipo de notificación

**Nivel 1 — procesar:**
```
✅ Lead #{id} — PRIORIDAD ALTA
Servicio: {servizio}
Acción: Procesar
Riesgo: Bajo | Score: {quality_score}

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

**Nivel 1 — revisar_manualmente:**
```
🔎 Lead #{id} — REVISIÓN MANUAL
Servicio: {servizio}
Acción: Revisar manualmente
Razón: {razon}

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

**Nivel 2 — revisar_manualmente:**
```
⚠️ Lead #{id} — NIVEL 2
Servicio: {servizio}
Acción: Revisión manual requerida
Abstract no pudo verificar el dominio

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

**Lead reactivado:**
```
🔄 Lead #{id} — REACTIVADO
Servicio: {servizio}
Días desde primer contacto: {dias_desde_creacion}
Acción: {accion_recomendada}

→ Ver detalles: https://admin.pablocuevas.it/leads/{id}
```

### Qué viaja por Telegram tras la reconstrucción

| Campo | ¿Es PII? | Justificación |
|-------|----------|---------------|
| `id` | ❌ NO | Entero sin valor fuera del sistema |
| `servizio` | ❌ NO | Tipo de consulta — no identifica a la persona |
| `accion_recomendada` | ❌ NO | Metadato operativo |
| `nivel_riesgo` | ❌ NO | Metadato operativo |
| `quality_score` | ❌ NO | Metadato operativo |
| `razon` | ❌ NO | Razón de clasificación — no contiene PII |
| `dias_desde_creacion` | ❌ NO | Número entero |
| URL con `id` | ❌ NO | El id solo tiene valor con acceso a la interfaz |

### Lo que NO viaja nunca por Telegram

- `nome`, `cognome` — PII directo
- `email` — PII directo, identificador principal
- `azienda` — potencialmente PII (nombre de empresa pequeña = persona)
- `messaggio` — texto libre, puede contener cualquier dato personal

---

## Prerequisito: la interfaz de administración

El link en la notificación apunta a `https://admin.pablocuevas.it/leads/{id}`.
Esta interfaz debe existir antes de que la Capa 4 reconstruida entre en
producción — de lo contrario las notificaciones son accionables pero el
link no resuelve.

La interfaz es una página mínima en el mismo VPS:
- Autenticación básica HTTP (usuario + contraseña)
- Query a PostgreSQL por ID del lead
- Muestra todos los campos incluyendo PII — solo bajo autenticación
- Los PII nunca salen de la infraestructura propia

**Secuencia de implementación**:
1. VPS operativo (Runbook-VPS-001)
2. Interfaz de administración básica desplegada
3. DNS `admin.pablocuevas.it` apuntando al VPS
4. Capa 4 reconstruida apuntando a esa URL
5. Verificación completa con Thunder Client

---

## Sanitización de messaggio — trabajo adicional en procesador-body1

Aunque `messaggio` no viajará por Telegram tras la reconstrucción,
sí se almacena en PostgreSQL y se mostrará en la interfaz de administración
dentro de un navegador.

**Riesgo**: si `messaggio` contiene HTML o JavaScript, la interfaz de
administración tendrá XSS persistente. Un atacante que envíe un payload
en ese campo antes de implementar la sanitización tendrá un vector activo
en la interfaz web.

**Resolución**: añadir en `procesador-body1` un paso de sanitización
del campo `messaggio` antes de persistir en PostgreSQL:

```javascript
// En procesador-body1 — añadir antes del INSERT
const sanitize = (str) => {
  if (typeof str !== 'string') return '';
  return str
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#x27;');
};

items[0].json.messaggio = sanitize(items[0].json.messaggio);
```

Este paso se añade al mismo tiempo que la reconstrucción de Capa 4.

---

## Consecuencias

**✅ Positivas**:
- Capa 4 queda alineada con ADR-206 antes del primer dato real
- Telegram queda como canal de alerta, no como canal de datos
- Sanitización de messaggio elimina el vector XSS persistente
- El sistema es GDPR-compliant en la capa de notificaciones desde el primer lead

**⚠️ Negativas**:
- Requiere reconstruir tres nodos de notificación (trabajo de una sesión)
- Requiere construir la interfaz de administración (prerequisito del link)
- Las notificaciones tienen un paso adicional para ver el detalle del lead

---

## Referencias

- ADR-206 — Minimización PII en Notificaciones Telegram (decisión que origina este ADR)
- ADR-209 — Secuencia de deploy: seguridad antes que producción
- ADR-204 — Write-Before-Processing (los datos están en PostgreSQL antes de notificar)
- Bloque 5 — interfaz de administración (prerequisito de la URL del link)
- Runbook-VPS-001 — VPS prerequisito para la URL estable

---

**Creado**: 2 de mayo 2026  
**Última revisión**: 2 de mayo 2026
