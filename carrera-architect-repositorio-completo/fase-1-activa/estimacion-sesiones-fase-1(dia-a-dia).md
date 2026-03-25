# ESTIMACIÓN DE SESIONES — FASE 1
## Por bloque, basado en condiciones reales

**Versión**: 1.0  
**Fecha**: 25 de febrero 2026  
**Lógica**: Una sesión = 1.5 a 2 horas de foco real.  
Las estimaciones son referencias, no compromisos. Si un bloque tarda el doble, es información — ajusta el siguiente. Si tarda la mitad, avanza.  
**Actualiza este documento** cuando termines cada bloque con las sesiones reales usadas.

---

## CÓMO LEER ESTE DOCUMENTO

Cada bloque tiene:
- **Sesiones estimadas** — cuántas sesiones de 1.5-2h se estiman
- **Sesiones reales** — las completas tú cuando terminas el bloque
- **Distribución sugerida** — cómo distribuir las sesiones dentro del bloque
- **Señal de desbloqueo** — cómo sabes que puedes pasar al siguiente

La columna "sesiones reales" es la más importante del documento. Con 3 bloques completados tendrás datos reales de tu ritmo y las estimaciones siguientes serán más precisas.

---

## RESUMEN DE BLOQUES

| Bloque | Nombre | Sesiones estimadas | Sesiones reales |
|--------|--------|-------------------|-----------------|
| 0 | Preparación N8N | 3 | |
| 1 | Code Node + JS aplicado | 6 | |
| 2 | Triggers + Flujo real | 4 | |
| 3 | HTTP Request + APIs reales | 6 | |
| 4 | Lógica condicional + Routing | 4 | |
| 5 | Persistencia con PostgreSQL | 7 | |
| 6 | Deploy en producción | 6 | |
| 7 | Seguridad + Compliance | 4 | |
| 8 | Resiliencia y monitoreo | 4 | |
| **PA1** | **Proyecto Ancla 1** | **8** | |
| **PA2** | **Proyecto Ancla 2** | **6** | |
| **PA3** | **Proyecto Ancla 3** | **5** | |
| | **TOTAL** | **~63 sesiones** | |

63 sesiones a 2 horas = ~126 horas totales.  
A 3 sesiones por semana = ~21 semanas.  
A 4 sesiones por semana = ~16 semanas.  
Estas son estimaciones conservadoras — incluyen fricción real, bugs y consolidación.

---

## BLOQUE 0 — PREPARACIÓN N8N ✅
**Sesiones estimadas: 3**

### Distribución sugerida

**Sesión 1 — Instalación y exploración**
- Instala N8N con Docker: `docker run -p 5678:5678 n8nio/n8n`
- Accede a `localhost:5678`, crea cuenta local
- Explora la interfaz sin construir nada: canvas, panel de nodos, execution log, credentials
- Lee en docs.n8n.io: "Getting Started" y "Understanding the data structure"
- Tiempo estimado: 1.5h

**Sesión 2 — Primer workflow + disección**
- Construye el primer workflow sin código:
  `[Manual Trigger] → [Set node] → ejecuta y observa`
- Conecta dos Set nodes en cadena — observa cómo cambia el JSON
- Responde en DAILY-LOG:
  - ¿Qué estructura tiene un item en N8N?
  - ¿Dónde está el `$json`?
  - ¿Qué pasa si el Set node sobreescribe un campo que ya existía?
- Tiempo estimado: 1.5h

**Sesión 3 — Consolidación conceptual**
- Lee en docs.n8n.io: "Code node" (solo lectura, sin practicar)
- Dibuja en papel el flujo de datos de un workflow simple:
  `Trigger → Nodo A → Nodo B → Output`
  Incluye qué tiene el JSON en cada punto
- Escribe en DAILY-LOG con tus palabras: qué es un item, qué es un workflow, qué hace el execution log
- Tiempo estimado: 1.5h

### Señal de desbloqueo
Puedes explicar oralmente qué es un item en N8N y por qué todo es JSON, sin mirar apuntes. Si no puedes, repite la Sesión 3.

### Registro
- Sesiones reales usadas: 3
- Observaciones: ___

---

## BLOQUE 1 — CODE NODE + JAVASCRIPT APLICADO
**Sesiones estimadas: 6**

### Distribución sugerida

**Sesión 1 — Conexión JS → N8N**
- Construye el workflow base:
  `[Manual Trigger] → [Set node: 3 leads] → [Code node]`
- En el Code node, solo loguea lo que llega:
  ```javascript
  const items = $input.all();
  console.log(items);
  return items;
  ```
- Observa el execution log. Entiende la estructura completa de un item.
- Tiempo estimado: 1.5h

**Sesión 2 — Transformación con spread**
- Añade el campo `saludo` con template literal a cada lead
- Entiende por qué `...item.json` es necesario
- Experimenta: ¿qué pasa si NO usas spread y solo devuelves el campo nuevo?
- Tiempo estimado: 1.5h

**Sesión 3 — Filtrado**
- Implementa el filtro de leads sin empresa vacía
- Experimenta: ¿qué devuelve el workflow cuando el array filtrado está vacío?
- Documenta en DAILY-LOG: ¿cómo manejarías este caso en un sistema real?
- Tiempo estimado: 1.5h

**Sesión 4 — Clasificación**
- Implementa el campo `prioridad` basado en dominio de email
- Sin ayuda externa para la lógica — usa lo que ya sabes
- Si te bloqueas en algo específico, identifica exactamente qué es y resuélvelo puntualmente
- Tiempo estimado: 2h

**Sesión 5 — Error handling en Code node**
- Añade optional chaining y valores por defecto a todos los campos que pueden ser null
- Prueba con un lead incompleto — ¿el workflow falla o maneja el caso?
- Principio a internalizar: los datos de APIs externas siempre son inconsistentes
- Tiempo estimado: 1.5h

**Sesión 6 — Workflow integrador**
- Construye el workflow completo:
  `[Trigger] → [5 leads variados, algunos incompletos] → [Code: filtra + clasifica + enriquece] → [Set: output final]`
- Documenta en DAILY-LOG: qué decidiste hacer y por qué
- Ese razonamiento es más importante que el código
- Tiempo estimado: 2h

### Señal de desbloqueo
Construyes un Code node que transforma, filtra y enriquece items sin consultar sintaxis básica. Entiendes qué pasa cuando el array de salida está vacío. Puedes explicar por qué usas spread.

### Registro
- Sesiones reales usadas: 2
- Observaciones: Bloque 1 lo he podido hacer en 2 dias (01/03 y 02/03)

---

## BLOQUE 2 — TRIGGERS + FLUJO REAL
**Sesiones estimadas: 4**

### Distribución sugerida

**Sesión 1 — Schedule trigger**
- Crea workflow con Schedule trigger cada 1 minuto (solo para test)
- `[Schedule] → [Set: timestamp actual] → [Code: formatea la hora]`
- Observa cómo N8N ejecuta automáticamente sin que presiones nada
- Cámbialo a cada hora — intervalo real
- Tiempo estimado: 1.5h

**Sesión 2 — Webhook trigger**
- Crea workflow con Webhook trigger
- `[Webhook] → [Code: procesa el body] → [Respond to Webhook]`
- Usa Thunder Client en VS Code para enviarle un POST con JSON
- Observa cómo N8N recibe y responde
- Pregunta de reflexión: ¿qué pasa si alguien envía un POST malformado?
- Tiempo estimado: 1.5h

**Sesión 3 — Firebase → N8N**
- Investiga las opciones disponibles:
  - ¿Hay nodo nativo de Firebase en N8N?
  - ¿Puedes usar webhook desde Firebase?
  - ¿O usas Schedule que consulta Firebase cada X minutos?
- Elige la opción más simple que funcione
- Impleméntala
- Tiempo estimado: 2h

**Sesión 4 — ADR de la decisión**
- Escribe el ADR de por qué elegiste ese trigger para Firebase
- Documenta: opciones consideradas, trade-offs aceptados
- Verifica que el workflow se dispara automáticamente cuando llega un lead real
- Tiempo estimado: 1.5h

### Señal de desbloqueo
Tienes un workflow que se dispara automáticamente cuando llega un lead desde tu formulario. Funciona solo, sin que presiones ejecutar.

### Registro
- Sesiones reales usadas: 1
- Observaciones: Hice toda el bloque con una sesion real mediante Claude.

---

## BLOQUE 3 — HTTP REQUEST + APIs REALES
**Sesiones estimadas: 6**

### Distribución sugerida

**Sesión 1 — HTTP Request node sin autenticación**
- `[Manual Trigger] → [HTTP Request: GET restcountries.com] → [Code: extrae campos]`
- Observa la estructura del JSON que devuelve
- ¿Es un objeto o un array? ¿Cómo navegas hasta el dato que necesitas?
- Tiempo estimado: 1.5h

**Sesión 2 — Credentials en N8N**
- Elige una API gratuita con API key (OpenWeatherMap recomendada)
- Crea las credenciales en N8N — nunca en el workflow directamente
- Construye el workflow que la consume
- Responde en DAILY-LOG: ¿dónde guarda N8N la API key? ¿Está visible si exportas el workflow?
- Tiempo estimado: 1.5h

**Sesión 3 — Manejo de errores de API**
- Modifica el workflow para manejar 401, 429 y 500 de forma diferente
- Experimenta con "Continue on Error" y "Retry on Fail"
- Entiende cuándo usar cada uno
- Tiempo estimado: 2h

**Sesión 4 — Segunda y tercera API**
- Integra dos APIs más en workflows separados o combinados
- Objetivo: variedad de tipos de respuesta y autenticación
- Tiempo estimado: 2h

**Sesión 5 — Enriquecimiento de leads**
- Extiende el workflow de Firebase con al menos una API de enriquecimiento
- El lead que entra debe salir con más información de la que tenía
- Tiempo estimado: 2h

**Sesión 6 — Consolidación**
- Revisa todos los workflows de este bloque
- ¿Todos tienen manejo de errores? ¿Todas las credenciales están en el vault?
- Documenta en DAILY-LOG: qué patrones repetiste, qué decidiste diferente en cada caso
- Tiempo estimado: 1.5h

### Señal de desbloqueo
Tienes 3+ APIs integradas en workflows reales. Sabes dónde van las credenciales y por qué. Al menos un workflow tiene manejo explícito de los tres tipos de error principales.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## BLOQUE 4 — LÓGICA CONDICIONAL + ROUTING
**Sesiones estimadas: 4**

### Distribución sugerida

**Sesión 1 — IF node**
- Extiende el workflow de leads con bifurcación por prioridad
- `[leads] → [IF: prioridad === 'alta'] → SI: notificación / NO: guardar`
- Observa en el execution log cómo se marcan las ramas
- Tiempo estimado: 1.5h

**Sesión 2 — Switch node**
- Clasifica leads por idioma detectado
- 3+ ramas en el Switch node
- Cada rama lleva a un template de respuesta diferente
- Tiempo estimado: 1.5h

**Sesión 3 — El patrón de fallback**
- Construye explícitamente el patrón de 3 niveles:
  `API externa → si falla: reglas simples → si no aplica: escala a humano`
- Este patrón es el más importante del bloque — deja que tome el tiempo que necesite
- Tiempo estimado: 2h

**Sesión 4 — Integración y revisión**
- Combina los conceptos del bloque en un workflow único y coherente
- Documenta en DAILY-LOG: ¿cuándo elegirías IF vs Switch? ¿Cuándo pondrías la lógica en el Code node en lugar de en un nodo dedicado?
- Tiempo estimado: 1.5h

### Señal de desbloqueo
Construyes workflows que toman caminos diferentes según los datos. Tienes implementado el patrón de fallback de 3 niveles al menos una vez. Puedes explicar cuándo usas IF vs Switch.

### Registro
- Sesiones reales usadas: 3
- Observaciones: ___

---

## PROYECTO ANCLA 1 — SISTEMA DE GESTIÓN DE LEADS
**Sesiones estimadas: 8**
**Cuándo**: Después de completar Bloques 1-4 y con Bloque 5 en curso

Este proyecto integra todo lo construido hasta aquí. No es un ejercicio nuevo — es ensamblar lo que ya sabes en un sistema coherente y documentado.

### Distribución sugerida

**Sesiones 1-2 — Diseño antes de código**
- Dibuja el diagrama de flujo completo en papel
- Define el schema de PostgreSQL (aunque no esté implementado aún)
- Escribe el ADR de arquitectura antes de construir
- Identifica los PII y completa el data mapping GDPR
- Tiempo estimado: 2 x 1.5h

**Sesiones 3-5 — Construcción**
- Implementa el workflow principal
- Conecta Firebase → N8N → enriquecimiento → clasificación
- Implementa deduplicación básica (aunque sea con Set node por ahora)
- Tiempo estimado: 3 x 2h

**Sesiones 6-7 — Notificaciones y reporte**
- Implementa notificación por Telegram o email con datos formateados
- Implementa reporte semanal automático
- Prueba con leads reales
- Tiempo estimado: 2 x 1.5h

**Sesión 8 — Documentación**
- README con diagrama de flujo
- ADR completo y revisado
- Data mapping GDPR
- Entrada en DAILY-LOG: qué aprendiste que no esperabas aprender
- Tiempo estimado: 2h

### Señal de desbloqueo
El sistema procesa leads reales automáticamente, los clasifica, los enriquece y notifica. Si llega un lead mientras duermes, te enteras por la mañana con el contexto completo.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## BLOQUE 5 — PERSISTENCIA CON POSTGRESQL
**Sesiones estimadas: 7**

### Distribución sugerida

**Sesión 1 — Diseño del schema**
- Diseña en papel antes de escribir código
- Define tablas, columnas, relaciones y tipos de dato
- Documenta qué datos son PII y la política de retención
- Crea el schema en pgAdmin
- Tiempo estimado: 2h

**Sesión 2 — N8N conectado a PostgreSQL**
- Configura credenciales de PostgreSQL en N8N
- Ejecuta un INSERT simple desde un workflow
- Verifica en pgAdmin que el registro apareció
- Tiempo estimado: 1.5h

**Sesión 3 — Deduplicación**
- Implementa el patrón SELECT → IF existe → UPDATE / INSERT
- Prueba enviando el mismo lead dos veces
- Tiempo estimado: 2h

**Sesión 4 — Queries útiles**
- Escribe directamente en pgAdmin (sin N8N):
  - Leads por prioridad
  - Leads de esta semana
  - Leads sin procesar
- Objetivo: entender los datos, no solo insertarlos
- Tiempo estimado: 1.5h

**Sesión 5 — JOINs en contexto real**
- Añade la tabla `eventos_lead` al schema
- Registra eventos: lead recibido, lead enriquecido, notificación enviada
- Escribe una query con JOIN para ver el historial completo de un lead
- Tiempo estimado: 2h

**Sesión 6 — Reporte automático**
- Workflow semanal: Schedule → PostgreSQL query → Code: formatea → notificación
- El reporte tiene que ser útil, no solo técnicamente correcto
- Tiempo estimado: 1.5h

**Sesión 7 — Integración en Proyecto Ancla 1**
- Migra la persistencia del Ancla 1 de Firebase a PostgreSQL
- Mantén Firebase solo para la recepción inicial del formulario
- PostgreSQL para el historial y la lógica de negocio
- Tiempo estimado: 2h

### Señal de desbloqueo
N8N está conectado a PostgreSQL en producción. La deduplicación funciona. Puedes escribir queries de SELECT, INSERT, UPDATE y JOIN sin consultar la sintaxis básica.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## BLOQUE 6 — DEPLOY EN PRODUCCIÓN
**Sesiones estimadas: 6**

### Distribución sugerida

**Sesión 1 — Docker Compose local**
- Escribe el `docker-compose.yml` con N8N + PostgreSQL
- Variables en `.env`, nunca hardcodeadas
- Levanta con `docker compose up -d`
- Verifica que todo funciona igual que con Docker solo
- Tiempo estimado: 2h

**Sesión 2 — VPS contratado y configurado**
- Contrata Hetzner CX22 (~€4/mes)
- Conecta por SSH
- Actualiza el sistema
- Instala Docker
- Configura firewall (ufw)
- Tiempo estimado: 1.5h

**Sesión 3 — Deploy del stack en el servidor**
- Copia docker-compose y .env al servidor
- Levanta el stack con `docker compose up -d`
- Verifica que N8N está corriendo desde el navegador (por IP por ahora)
- Tiempo estimado: 1.5h

**Sesión 4 — HTTPS con Nginx + Certbot**
- Configura un subdominio (n8n.pablocuevas.it o similar)
- Instala Nginx como reverse proxy
- Obtén certificado SSL con Certbot
- Verifica acceso por HTTPS
- Tiempo estimado: 2h (puede haber fricción — es normal)

**Sesión 5 — Migración de workflows**
- Exporta workflows del N8N local
- Impórtalos en el N8N del servidor
- Reconecta credenciales (no se exportan — es correcto por seguridad)
- Verifica que los workflows funcionan en producción
- Tiempo estimado: 1.5h

**Sesión 6 — Backup automático**
- Escribe el script de backup
- Prográmalo con cron
- Verifica que genera el archivo correctamente
- Documenta el proceso de restauración (tan importante como el backup)
- Tiempo estimado: 1.5h

### Señal de desbloqueo
N8N corre en un VPS con HTTPS. Puedes apagar tu laptop y el sistema sigue procesando leads. Tienes backup automático configurado y sabes cómo restaurarlo.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## PROYECTO ANCLA 2 — SISTEMA DEPLOYADO EN PRODUCCIÓN
**Sesiones estimadas: 6**
**Cuándo**: Después de completar Bloques 5 y 6

Este proyecto toma el Ancla 1 y lo convierte en un producto entregable a un cliente.

### Distribución sugerida

**Sesiones 1-2 — Migración a producción**
- Mueve el sistema completo del Ancla 1 al VPS
- Verifica cada componente: Firebase → N8N → PostgreSQL → notificaciones
- Prueba con leads reales en producción
- Tiempo estimado: 2 x 2h

**Sesiones 3-4 — Hardening**
- Aplica las medidas de seguridad del Bloque 7 (puedes trabajar ambos en paralelo)
- Verifica que PostgreSQL no está expuesto
- Verifica que las credenciales están en el vault
- Configura basic auth en N8N si no está
- Tiempo estimado: 2 x 1.5h

**Sesión 5 — Documentación de entrega**
- Guía de deployment paso a paso
- Runbook: qué hacer si N8N cae, si PostgreSQL no responde, si las notificaciones fallan
- Costos mensuales documentados
- Tiempo estimado: 2h

**Sesión 6 — Criterio de aceptación**
- Apaga tu laptop durante 2 horas
- Envía un lead de prueba desde el formulario
- Verifica que el sistema lo procesó correctamente sin tu intervención
- Si funciona: el Ancla 2 está completo
- Tiempo estimado: 0.5h de prueba + 1h de ajustes si algo falla

### Señal de desbloqueo
El criterio es binario: ¿puedes apagar tu laptop y el sistema sigue funcionando? Sí o no.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## BLOQUE 7 — SEGURIDAD + COMPLIANCE APLICADO
**Sesiones estimadas: 4**

*Este bloque puede trabajarse en paralelo con el Bloque 6 — muchos ejercicios son parte del mismo proceso de deploy.*

### Distribución sugerida

**Sesión 1 — Audit de seguridad**
- Revisa tu stack contra la lista del plan táctico
- ¿Puerto 5432 expuesto? ¿Credenciales hardcodeadas? ¿N8N con auth?
- Documenta lo que encuentres — sin juzgar, solo registrar
- Corrige lo urgente en esta misma sesión
- Tiempo estimado: 1.5h

**Sesión 2 — Usuario PostgreSQL con mínimos privilegios**
- Crea el usuario `n8n_user` con solo los permisos necesarios
- Actualiza la conexión de N8N para usar este usuario
- Verifica que N8N sigue funcionando correctamente
- Tiempo estimado: 1h

**Sesión 3 — Data mapping GDPR**
- Para cada tabla de tu base de datos, completa la ficha del plan táctico
- No es un ejercicio académico — es la documentación real de tu sistema
- Tiempo estimado: 1.5h

**Sesión 4 — Job de retención automática**
- Workflow mensual que elimina registros según tu política
- Prueba con datos de test — nunca con datos reales como primera prueba
- Tiempo estimado: 1.5h

### Señal de desbloqueo
Puedes hacer el data mapping GDPR de cualquier sistema en menos de 30 minutos. Tu stack no tiene vulnerabilidades obvias. Tienes documentado quién accede a qué y por cuánto tiempo.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## BLOQUE 8 — RESILIENCIA Y MONITOREO
**Sesiones estimadas: 4**

### Distribución sugerida

**Sesión 1 — Error workflow global**
- Configura el workflow de error en N8N
- Prueba con un workflow que falla intencionalmente
- Verifica que recibes la notificación con contexto útil
- Tiempo estimado: 1.5h

**Sesión 2 — Retry y backoff**
- Configura retry en los workflows que llaman a APIs externas
- Define: máximo de reintentos, tiempo de espera, qué hacer si todos fallan
- Tiempo estimado: 1.5h

**Sesión 3 — Health check**
- Workflow de Schedule cada 5 minutos que verifica el estado del sistema
- Notificación si algo no responde
- Tiempo estimado: 1.5h

**Sesión 4 — Test de idempotencia + revisión final**
- Envía el mismo lead dos veces — ¿qué pasa?
- Si crea duplicados: arréglalo
- Si no: documenta cómo funciona la idempotencia en tu sistema
- Escribe el ADR de las decisiones de resiliencia
- Tiempo estimado: 2h

### Señal de desbloqueo
Cuando algo falla, te enteras antes que el cliente. Tienes error handling explícito en todos los workflows productivos. El sistema es idempotente para los casos principales.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## PROYECTO ANCLA 3 — SISTEMA CON FALLBACK Y RESILIENCIA
**Sesiones estimadas: 5**
**Cuándo**: Después de completar el Bloque 8 y con el Ancla 2 en producción

### Distribución sugerida

**Sesión 1 — Diseño del sistema de 3 niveles**
- Define el caso de uso real (respuesta a consultas, soporte, clasificación)
- Diseña los 3 niveles en papel antes de construir
- Escribe el ADR: por qué 3 niveles y no solo IA
- Tiempo estimado: 1.5h

**Sesiones 2-3 — Construcción**
- Implementa los 3 niveles con routing explícito
- Implementa el log de qué nivel resolvió cada caso
- Tiempo estimado: 2 x 2h

**Sesión 4 — Métricas reales**
- Ejecuta 20+ casos de prueba
- Documenta: en X casos, ¿cuántos resolvió cada nivel?
- ¿El nivel 1 está sobre o infrautilizado?
- Tiempo estimado: 1.5h

**Sesión 5 — Documentación y cost analysis**
- Cuánto cuesta cada nivel por caso procesado
- ROI del sistema: tiempo ahorrado vs costo de infraestructura
- README final
- Tiempo estimado: 1.5h

### Señal de desbloqueo
Tienes métricas reales de qué nivel resuelve qué. Puedes explicar el diseño y defender por qué 3 niveles ante alguien que lo cuestione.

### Registro
- Sesiones reales usadas: ___
- Observaciones: ___

---

## TABLA DE PROGRESO

Actualiza esto cuando completes cada bloque:

| Bloque | Estado | Sesiones estimadas | Sesiones reales | Notas |
|--------|--------|--------------------|-----------------|-------|
| 0 | ✅ Completada | 3 | 3 | |
| 1 | ✅ Completada | 6 | 2 | |
| 2 | ✅ Completada | 4 | 1 | |
| 3 | ✅ Completada | 6 | 2 | |
| 4 | ✅ Completada | 4 | 3 | |
| PA1 | ⬜ Pendiente | 8 | | |
| 5 | ⬜ Pendiente | 7 | | |
| 6 | ⬜ Pendiente | 6 | | |
| PA2 | ⬜ Pendiente | 6 | | |
| 7 | ⬜ Pendiente | 4 | | |
| 8 | ⬜ Pendiente | 4 | | |
| PA3 | ⬜ Pendiente | 5 | | |

Estados posibles: ⬜ Pendiente / 🔄 En curso / ✅ Completado / ⚠️ Bloqueado

---

**Documento creado**: 25 de febrero 2026  
**Primera acción**: Bloque 0, Sesión 1 — instalar N8N con Docker
