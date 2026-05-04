# ADR-206: Minimización PII en Notificaciones Telegram

**Fecha**: 2 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Seguridad / GDPR / UX  
**Audiencia**: 🔒 Personal  

---

## Contexto

El sistema notifica la llegada de nuevos leads via Telegram (`PabloLeadsBot`).
La notificación debe permitir actuar rápido sin comprometer la base legal GDPR.

Telegram no tiene DPA firmado con el operador del sistema, sus servidores
están fuera de la UE, y no puede considerarse un procesador GDPR-compliant.
Cualquier dato personal (nombre, email, mensaje del lead) que viaje por
Telegram sale de la infraestructura controlada sin garantías legales.

Al mismo tiempo, una notificación que solo dice "llegó un lead" sin contexto
operativo no es accionable. El arquitecto necesita ver suficiente información
para decidir si actuar, sin exponer PII fuera del sistema.

---

## Alternativas evaluadas

**Opción A — PII completo en Telegram (nombre + email + mensaje):**  
UX máxima. Sin fricción. Incompatible con GDPR — los datos del lead viajan
a servidores de terceros fuera de la UE sin base legal. Descartada.

**Opción B — Solo ID interno en Telegram:**  
GDPR perfecto. UX inaceptable. Requiere abrir pgAdmin, filtrar por ID,
navegar tablas. No es operativo como flujo de trabajo diario. Descartada.

**Opción C — Identificadores + metadatos operativos + link a interfaz propia ⭐ ELEGIDA:**  
La notificación de Telegram contiene solo datos no-PII de valor operativo:
ID del lead, tipo de servicio, acción recomendada, score de riesgo.
Un link apunta a la interfaz de administración en el propio VPS, bajo HTTPS
y autenticación básica. Los PII se consultan ahí — nunca salen del sistema.

---

## Decisión

Las notificaciones de Telegram contienen **identificadores y metadatos
operativos únicamente**. Los datos personales del lead se consultan en
la interfaz de administración alojada en infraestructura propia.

### Contenido de la notificación (Capa 4)

```
🔔 Nuevo lead #47
Servicio: Automatización de procesos
Acción: Procesar
Riesgo: Bajo | Score: 0.91

→ Ver lead: https://n8n.pablocuevas.it/leads/47
```

### Qué viaja por Telegram
- `id` del lead (entero — sin valor fuera del sistema)
- `servizio` (tipo de consulta — no identifica a la persona)
- `accion_recomendada` + `nivel_riesgo` + `quality_score` (metadatos operativos)
- URL a interfaz propia

### Qué no viaja por Telegram
- `nome` — PII directo
- `email` — PII directo
- `messaggio` — puede contener PII adicional

---

## La interfaz de administración

Una página mínima en el VPS propio (mismo servidor que N8N + PostgreSQL).
Autenticación básica HTTP o token de acceso. Muestra los datos completos
del lead al acceder con el link de la notificación.

**Coste de implementación**: marginal — la infraestructura ya existe.
Es una query SQL + respuesta HTML/JSON en el mismo servidor.

**No es un proyecto nuevo**: es una extensión natural del VPS (Bloque 6).
Se implementa en Bloque 5 una vez el VPS esté operativo.

---

## Consecuencias

**✅ Positivas**:
- Notificación accionable sin exponer PII fuera del sistema
- Base legal GDPR mantenida — Telegram es solo el canal de alerta
- Diferenciador de mercado: se puede documentar y demostrar a clientes
- Interfaz de consulta reutilizable para futuros proyectos

**⚠️ Negativas**:
- Requiere construir la interfaz de administración (Bloque 5, post-VPS)
- Un paso adicional para ver PII vs. tenerlo directo en Telegram
- La URL del VPS debe ser estable — cualquier cambio de dominio rompe los links históricos de Telegram

---

## Referencias

- ADR-007 — Principio de mínimo privilegio para acceso IA (mismo razonamiento aplicado a canales externos)
- ADR-204 — Write-Before-Processing (los datos están en PostgreSQL antes de notificar)
- Bloque 5 — construcción de la interfaz de administración (post Bloque 6)
- Bloque 6 — VPS, prerequisito para que la URL del link sea estable

---

**Creado**: 2 de mayo 2026  
**Última revisión**: 2 de mayo 2026
