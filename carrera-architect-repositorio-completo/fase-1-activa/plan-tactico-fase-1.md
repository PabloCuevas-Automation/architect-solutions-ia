# PLAN TÁCTICO — FASE 1
## Constructor de Sistemas de Automatización

**Versión**: 1.0  
**Fecha**: 25 de febrero 2026  
**Referencia**: `fase-1-redefinida.md` — este documento es la ejecución táctica de ese marco  
**Lógica**: No hay fechas ni días. Hay condiciones. Avanzas cuando las cumples, no cuando pasa el tiempo.

---

## CÓMO USAR ESTE DOCUMENTO

Cada bloque tiene:
- **Condición de entrada** — qué necesitas saber antes de empezar
- **Conceptos** — qué vas a entender
- **Ejercicios** — cómo lo practicas
- **Condición de salida** — cómo sabes que puedes avanzar
- **Señal de bloqueo** — cuándo parar y consolidar en lugar de avanzar

No saltes bloques. No avances sin cumplir la condición de salida. Si llevas más de 3 sesiones en un ejercicio sin progreso, es señal de bloqueo — consolida lo anterior antes de continuar.

Actualiza el DAILY-LOG con cada sesión significativa. No hace falta escribir mucho — fecha, qué hiciste, qué entendiste, qué quedó pendiente.

---

## BLOQUE 0 — PREPARACIÓN N8N
*Antes de construir cualquier workflow*

**Condición de entrada**: Docker instalado y funcionando (ya cumplida)

### Conceptos
- Qué es N8N y qué problema resuelve — no como herramienta, como sistema
- La diferencia entre Make/Zapier cloud y N8N self-hosted: control, datos, costos, vendor lock-in
- El modelo mental fundamental: todo en N8N es un flujo de items JSON entre nodos
- Qué es un item en N8N — la unidad básica de datos
- Qué es un workflow — una secuencia de transformaciones con lógica

### Ejercicios

**Ejercicio 1 — Instalación**
```bash
docker run -p 5678:5678 n8nio/n8n
```
Accede a `localhost:5678`. Crea cuenta local. Explora la interfaz sin construir nada todavía — canvas, panel de nodos, execution log, credentials.

**Ejercicio 2 — Lectura**
Lee estas secciones de la documentación oficial (docs.n8n.io):
- "Getting Started"
- "Understanding the data structure" — crítico
- "Code node" — solo léelo, no lo practiques todavía

**Ejercicio 3 — Primer workflow manual**
Construye sin código:
```
[Manual Trigger] → [Set node: crea objeto con nombre y email] → [ejecuta y observa el output]
```
Objetivo: entender cómo viajan los datos entre nodos, qué estructura tiene un item.

**Ejercicio 4 — Disección del output**
Después del ejercicio 3, responde en tu DAILY-LOG:
- ¿Qué estructura tiene el JSON que sale del Set node?
- ¿Dónde está el `$json` dentro de un item?
- ¿Qué pasa si conectas dos Set nodes en cadena?

### Condición de salida
Puedes explicar en una frase qué es un item en N8N y por qué todo en N8N es JSON. Si no puedes explicarlo sin mirar apuntes, no avanzas.

---

## BLOQUE 1 — N8N CODE NODE + JAVASCRIPT APLICADO
*El nodo más importante. Donde tu JS se convierte en lógica real*

**Condición de entrada**: Bloque 0 completado. Entiendes qué es un item.

### Conceptos
- `$input.all()` — cómo llegan los datos al Code node (array de items)
- `$json` — acceso al contenido de un item individual
- La diferencia entre trabajar con un item y trabajar con todos los items
- Inmutabilidad: por qué enriqueces con spread en lugar de modificar el item original
- Por qué el Code node devuelve siempre un array de items, nunca un objeto suelto

### Ejercicios

**Ejercicio 1 — Transformación básica**
Workflow:
```
[Manual Trigger] → [Set node: 3 leads con nombre, email, empresa] → [Code node]
```
En el Code node, transforma cada lead para añadir un campo `saludo` con template literal:
```javascript
return $input.all().map(item => ({
  json: {
    ...item.json,
    saludo: `Hola ${item.json.nombre}, te contactamos de parte de Pablo Cuevas`
  }
}));
```
Observa el output. Entiende por qué el spread operator está ahí.

**Ejercicio 2 — Filtrado**
Mismo workflow. En el Code node, filtra solo los leads cuya empresa no esté vacía:
```javascript
return $input.all()
  .filter(item => item.json.empresa && item.json.empresa.trim() !== '')
  .map(item => ({ json: item.json }));
```
Pregunta de reflexión: ¿qué pasa si ningún item pasa el filtro? ¿El workflow falla o continúa?

**Ejercicio 3 — Clasificación**
Añade un campo `prioridad` basado en reglas:
- Si el email es de un dominio corporativo (no gmail, hotmail, yahoo) → `alta`
- Si tiene empresa → `media`
- Si no → `baja`

Escríbelo sin ayuda. Si no puedes, identifica exactamente qué parte no sabes y resuélvela primero.

**Ejercicio 4 — Error handling**
¿Qué pasa si un item tiene `nombre: null`? Tu template literal va a fallar silenciosamente o dar un resultado extraño. Añade optional chaining y un valor por defecto:
```javascript
saludo: `Hola ${item.json.nombre ?? 'Cliente'}`
```
Principio: en N8N, los datos de APIs externas siempre son inconsistentes. Diseña para eso desde el primer nodo.

**Ejercicio 5 — Workflow completo con Code node**
Construye un workflow que simule el procesamiento de leads de tu Firebase:
```
[Manual Trigger] 
→ [Set node: 5 leads con datos variados, algunos incompletos]
→ [Code node: filtra, clasifica, enriquece]
→ [Set node: formatea output final]
```
Documenta en DAILY-LOG: qué decidiste hacer y por qué. Ese razonamiento es más importante que el código.

### Condición de salida
Puedes construir un Code node que transforme, filtre y enriquezca items sin consultar la sintaxis básica. Entiendes por qué usas spread. Entiendes qué pasa cuando el array de salida está vacío.

**Señal de bloqueo**: Si llevas 3 sesiones sin poder completar el Ejercicio 3 sin ayuda, vuelve a repasar map/filter/optional chaining en JavaScript puro antes de continuar.

---

## BLOQUE 2 — TRIGGERS + FLUJO REAL
*Tu workflow tiene que arrancar solo, no solo cuando lo ejecutas manualmente*

**Condición de entrada**: Bloque 1 completado.

### Conceptos
- Los tres tipos de trigger que usarás en Fase 1: Manual, Schedule (Cron), Webhook
- Cuándo usar cada uno: manual para testing, schedule para tareas periódicas, webhook para eventos en tiempo real
- Qué es un webhook y por qué es más eficiente que polling
- La URL de webhook de N8N: cómo funciona, cómo testearla
- Diferencia entre webhook de test y webhook de producción en N8N

### Ejercicios

**Ejercicio 1 — Schedule trigger**
Crea un workflow con Schedule trigger que se ejecute cada minuto (solo para testing):
```
[Schedule Trigger: cada 1 min] → [Set node: timestamp actual] → [Code node: formatea la hora]
```
Observa cómo N8N ejecuta el workflow automáticamente. Luego cámbialo a cada hora — que es un intervalo real.

**Ejercicio 2 — Webhook trigger**
Crea un workflow con Webhook trigger:
```
[Webhook Trigger] → [Code node: procesa el body recibido] → [Respond to Webhook]
```
Usa Thunder Client en VS Code para enviarle un POST con JSON. Observa cómo N8N recibe los datos.

Pregunta de reflexión: ¿qué pasa si alguien envía un POST malformado a tu webhook? ¿Tu workflow falla? ¿Debería?

**Ejercicio 3 — Firebase → N8N (tu primer workflow real)**
Este es el objetivo de negocio real: conectar tu formulario de pablocuevas.it con N8N.

Opciones según lo que encuentres viable:
- Firebase Firestore trigger (si existe nodo nativo — verifica en N8N)
- Webhook desde Firebase Functions (si necesitas código adicional)
- Schedule trigger que consulta Firebase cada X minutos (más simple, menos elegante)

Elige la opción más simple que funcione. Documenta en un ADR por qué elegiste esa opción y qué trade-offs aceptaste.

### Condición de salida
Tienes un workflow que se dispara automáticamente cuando llega un lead desde tu formulario. No importa si es simple — importa que funcione solo sin que presiones "ejecutar".

---

## BLOQUE 3 — HTTP REQUEST NODE + APIs REALES
*Conectar tu sistema con el mundo externo*

**Condición de entrada**: Bloques 1 y 2 completados. Tienes al menos un workflow con trigger automático.

### Conceptos
- El nodo HTTP Request: el nodo más universal de N8N
- Métodos HTTP en contexto real: cuándo cada uno en un workflow
- Headers de autenticación: API key en header vs query param, Bearer token
- El sistema de Credentials de N8N: por qué es crítico y cómo funciona
- Rate limiting: qué es, cómo detectarlo (status 429), cómo manejarlo
- La diferencia entre un error de tu código (4xx) y un error del servicio externo (5xx)

### Ejercicios

**Ejercicio 1 — Primera API sin autenticación**
```
[Manual Trigger] 
→ [HTTP Request: GET https://restcountries.com/v3.1/name/italy]
→ [Code node: extrae nombre, capital, población]
→ [Set node: output limpio]
```
Observa la estructura del JSON que devuelve la API. ¿Es un objeto o un array? ¿Cómo navegas hasta el dato que necesitas?

**Ejercicio 2 — API con autenticación**
Elige una API gratuita que requiera API key (OpenWeatherMap, NewsAPI, o similar). Crea las credenciales en N8N — nunca en el workflow directamente. Construye un workflow que la consuma y procese la respuesta.

Reflexión obligatoria en DAILY-LOG: ¿dónde guardó N8N tu API key? ¿Está visible en el workflow exportado? ¿Qué implica eso para entregar un workflow a un cliente?

**Ejercicio 3 — Manejo de errores de API**
Modifica el workflow del Ejercicio 2 para manejar:
- ¿Qué pasa si la API devuelve 401? (credenciales incorrectas)
- ¿Qué pasa si devuelve 429? (rate limit)
- ¿Qué pasa si devuelve 500? (error del servicio)

N8N tiene un mecanismo de "Continue on Error" y "Retry on Fail". Experimenta con ambos. Entiende cuándo usar cada uno.

**Ejercicio 4 — Enriquecimiento de leads**
Extiende tu workflow de Firebase (Bloque 2) con al menos una API externa:
- Validación de email (ZeroBounce, Hunter, o similar gratuito)
- Datos de empresa por dominio
- Cualquier enriquecimiento que tenga sentido para tu caso de uso

El criterio no es qué API usas — es que el lead que entra al workflow salga con más información de la que tenía.

### Condición de salida
Tienes 3+ APIs integradas en workflows reales. Sabes dónde van las credenciales y por qué. Tienes al menos un workflow con manejo explícito de errores de API.

---

## BLOQUE 4 — LÓGICA CONDICIONAL + ROUTING
*Tu sistema toma decisiones, no solo transforma datos*

**Condición de entrada**: Bloque 3 completado.

### Conceptos
- IF node vs Switch node: cuándo usar cada uno
- El patrón de routing: un workflow que hace cosas distintas según los datos
- Merge node: cómo reunificar caminos divergentes
- El concepto de "ramas" en un workflow y cómo afectan al execution log
- Por qué la lógica condicional compleja en un solo nodo es deuda técnica

### Ejercicios

**Ejercicio 1 — IF node básico**
Extiende el workflow de leads:
```
[leads procesados]
→ [IF node: prioridad === 'alta']
  → SI: [notificación inmediata por Telegram o email]
  → NO: [guardar sin notificación]
```

**Ejercicio 2 — Switch node para múltiples caminos**
Clasifica leads por idioma detectado (italiano, español, inglés) y enruta a diferentes templates de respuesta. Usa Switch node con 3+ ramas.

**Ejercicio 3 — El patrón de fallback**
Construye este patrón explícitamente:
```
[input]
→ [Intento 1: API externa]
  → Éxito: continúa con datos enriquecidos
  → Fallo: [Fallback: reglas simples sin API]
    → Aplica: continúa con datos básicos
    → No aplica: [escala a humano — notificación con contexto completo]
```
Este patrón aparecerá en todos tus sistemas productivos. Aprenderlo aquí te ahorra redescubrirlo en cada proyecto.

### Condición de salida
Puedes construir un workflow que tome caminos diferentes según los datos, con manejo explícito de cada camino incluyendo los de error.

---

## BLOQUE 5 — PERSISTENCIA DE DATOS CON POSTGRESQL
*Tu sistema tiene memoria*

**Condición de entrada**: Bloques 1-4 completados. Tienes workflows que procesan leads realmente.

### Conceptos
- Por qué Firebase no es suficiente para sistemas complejos: estructura rígida, queries limitadas, costos
- El modelo relacional en términos simples: tablas, columnas, filas, relaciones
- CRUD en SQL: SELECT, INSERT, UPDATE, DELETE
- JOINs: cuándo y por qué combinar tablas
- La diferencia entre guardar datos crudos y guardar datos estructurados
- Índices: qué son y cuándo importan (cuando tu tabla tiene miles de filas)
- Transacciones: qué pasa si algo falla a mitad de una operación

### Ejercicios

**Ejercicio 1 — Primer schema**
Diseña en papel antes de escribir código. ¿Qué tablas necesita tu sistema de leads?

Schema mínimo:
```sql
CREATE TABLE leads (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  nombre VARCHAR(255),
  empresa VARCHAR(255),
  prioridad VARCHAR(50),
  fuente VARCHAR(100),
  estado VARCHAR(50) DEFAULT 'nuevo',
  datos_enriquecidos JSONB,
  creado_en TIMESTAMP DEFAULT NOW(),
  procesado_en TIMESTAMP
);

CREATE TABLE eventos_lead (
  id SERIAL PRIMARY KEY,
  lead_id INTEGER REFERENCES leads(id),
  tipo VARCHAR(100),
  descripcion TEXT,
  creado_en TIMESTAMP DEFAULT NOW()
);
```

Pregunta antes de crear: ¿qué datos son PII? ¿Cuánto tiempo los vas a guardar? ¿Cómo los eliminas si alguien lo pide? Documenta las respuestas — eso es tu data mapping GDPR para este sistema.

**Ejercicio 2 — N8N conectado a PostgreSQL**
Instala el nodo de PostgreSQL en N8N. Configura las credenciales. Ejecuta un INSERT simple desde un workflow:
```
[Manual Trigger] → [Set node: lead de prueba] → [PostgreSQL: INSERT en leads]
```
Verifica en pgAdmin que el registro apareció.

**Ejercicio 3 — Deduplicación**
El problema real: si el mismo email manda el formulario dos veces, no quieres procesarlo dos veces.

Implementa este patrón en tu workflow:
```
[nuevo lead llega]
→ [PostgreSQL: SELECT id FROM leads WHERE email = $email]
→ [IF node: ¿existe?]
  → SÍ: [actualiza estado, no crea duplicado]
  → NO: [INSERT nuevo lead, continúa procesamiento]
```

Este es el primer ejemplo de lógica de negocio real respaldada por una base de datos.

**Ejercicio 4 — Queries útiles**
Escribe estas queries directamente en pgAdmin, sin N8N, para entender tus datos:
```sql
-- ¿Cuántos leads por prioridad?
SELECT prioridad, COUNT(*) FROM leads GROUP BY prioridad;

-- ¿Leads de esta semana?
SELECT * FROM leads WHERE creado_en > NOW() - INTERVAL '7 days';

-- ¿Leads sin procesar?
SELECT * FROM leads WHERE estado = 'nuevo' ORDER BY creado_en ASC;
```

**Ejercicio 5 — Reporte automático**
Workflow semanal (Schedule trigger cada lunes a las 9am):
```
[Schedule Trigger]
→ [PostgreSQL: query de resumen semanal]
→ [Code node: formatea el reporte]
→ [notificación con el resumen]
```

### Condición de salida
Tienes N8N conectado a PostgreSQL en al menos un workflow productivo. La deduplicación funciona. Puedes escribir queries básicas sin consultar documentación para la sintaxis fundamental.

---

## BLOQUE 6 — DEPLOY EN PRODUCCIÓN
*Tu sistema vive sin ti*

**Condición de entrada**: Tienes al menos el Proyecto Ancla 1 funcionando localmente con N8N + PostgreSQL.

### Conceptos
- La diferencia entre "funciona en mi máquina" y "es un producto"
- Qué es un VPS y por qué Hetzner/DigitalOcean antes que AWS para Fase 1
- Docker Compose: definir tu stack completo como código
- Variables de entorno: la separación entre configuración y código
- SSH: cómo entrar a tu servidor y qué puedes hacer desde ahí
- HTTPS: por qué es obligatorio y cómo configurarlo con Certbot
- Reverse proxy: por qué N8N no debe estar expuesto directamente al internet
- Firewall básico: qué puertos abres y cuáles cierras

### Ejercicios

**Ejercicio 1 — Docker Compose local**
Antes de ir al servidor, levanta todo localmente con un solo archivo:

```yaml
version: '3.8'
services:
  n8n:
    image: n8nio/n8n
    ports:
      - "5678:5678"
    environment:
      - N8N_BASIC_AUTH_ACTIVE=true
      - N8N_BASIC_AUTH_USER=${N8N_USER}
      - N8N_BASIC_AUTH_PASSWORD=${N8N_PASSWORD}
      - DB_TYPE=postgresdb
      - DB_POSTGRESDB_HOST=postgres
      - DB_POSTGRESDB_DATABASE=n8n
      - DB_POSTGRESDB_USER=${POSTGRES_USER}
      - DB_POSTGRESDB_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - n8n_data:/home/node/.n8n
    depends_on:
      - postgres

  postgres:
    image: postgres:15
    environment:
      - POSTGRES_DB=n8n
      - POSTGRES_USER=${POSTGRES_USER}
      - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  n8n_data:
  postgres_data:
```

Variables en un archivo `.env` (nunca en el docker-compose directamente). Verifica que funciona: `docker compose up -d`.

**Ejercicio 2 — Primer servidor VPS**
Contrata un VPS en Hetzner (€4-6/mes). Ubuntu 22.04 LTS.

Primeras acciones en el servidor:
```bash
# Conectarte
ssh root@tu-ip

# Actualizar
apt update && apt upgrade -y

# Instalar Docker
curl -fsSL https://get.docker.com | sh

# Firewall básico
ufw allow 22    # SSH
ufw allow 80    # HTTP
ufw allow 443   # HTTPS
ufw enable

# Verificar
docker --version
ufw status
```

**Ejercicio 3 — Deploy del stack**
Copia tu docker-compose y .env al servidor:
```bash
scp docker-compose.yml .env root@tu-ip:/opt/n8n/
```
En el servidor:
```bash
cd /opt/n8n
docker compose up -d
```
Verifica que N8N está corriendo: `docker compose ps`

**Ejercicio 4 — HTTPS con Nginx + Certbot**
Sin HTTPS, no es un producto. Con HTTPS, lo es.

Instala Nginx como reverse proxy. Configura un subdominio (n8n.tupdominio.com o similar). Obtén certificado SSL con Certbot. Verifica que accedes por HTTPS desde el navegador.

**Ejercicio 5 — Backup automático**
Script semanal que hace backup de los volúmenes de Docker y los guarda en un lugar seguro:
```bash
#!/bin/bash
docker compose stop
tar -czf /backups/n8n-backup-$(date +%Y%m%d).tar.gz /opt/n8n/
docker compose start
```
Prográmalo con cron. Verifica que el backup se genera.

**Ejercicio 6 — Migración de workflows**
Exporta tus workflows del N8N local e impórtalos en el N8N del servidor. Verifica que funcionan igual. Reconecta las credenciales (las credenciales no se exportan por seguridad — es correcto).

### Condición de salida
Tienes N8N corriendo en un VPS con HTTPS, accesible desde cualquier lugar, con PostgreSQL en el mismo servidor, backup automático configurado y firewall básico activo. Puedes apagar tu laptop y el sistema sigue funcionando.

---

## BLOQUE 7 — SEGURIDAD + COMPLIANCE APLICADO
*Lo que hace que tu sistema sea entregable*

**Condición de entrada**: Tienes algo deployado en producción.

### Conceptos
- Por qué la seguridad no es una capa que se añade — es un diseño que se hace
- Superficie de ataque: qué partes de tu sistema son accesibles desde internet
- El principio de mínimo privilegio: usuarios, permisos, accesos
- Webhook security: cómo verificar que el webhook viene de quien dice venir
- Secrets management: la diferencia entre configuración y secretos
- El data mapping como herramienta de diseño, no de auditoría

### Ejercicios

**Ejercicio 1 — Audit de seguridad de tu stack**
Revisa tu sistema deployado y responde:
- ¿Está el puerto 5432 (PostgreSQL) expuesto al internet? No debería estarlo.
- ¿Están tus credenciales en el docker-compose? No deberían estarlo.
- ¿Tiene N8N basic auth o algo equivalente? Debería tenerlo.
- ¿Qué pasa si alguien encuentra tu URL de webhook? ¿Puede abusar de ella?

Documenta lo que encuentres y corrígelo.

**Ejercicio 2 — Usuario de PostgreSQL con mínimos privilegios**
Crea un usuario específico para N8N con solo los permisos que necesita:
```sql
CREATE USER n8n_user WITH PASSWORD 'password_seguro';
GRANT CONNECT ON DATABASE n8n TO n8n_user;
GRANT USAGE ON SCHEMA public TO n8n_user;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO n8n_user;
```
No uses el superuser para la conexión de N8N.

**Ejercicio 3 — Data mapping GDPR de tu sistema**
Para cada tabla de tu base de datos, completa esta ficha:

```markdown
## Tabla: leads

### Datos que contiene
- email (PII — identificador único)
- nombre (PII)
- empresa (no PII)
- datos_enriquecidos (puede contener PII según la API usada)

### Base legal para procesarlo
- Consentimiento explícito vía formulario de contacto

### Retención
- Leads activos: indefinido mientras haya relación comercial
- Leads sin respuesta en 12 meses: eliminar automáticamente

### Eliminación
- Endpoint manual: DELETE FROM leads WHERE email = $email
- Automático: job mensual que elimina leads inactivos >12 meses

### Acceso
- Solo Pablo Cuevas (propietario del sistema)
```

**Ejercicio 4 — Job de retención automática**
Workflow mensual que elimina registros según tu política de retención:
```
[Schedule: primer día del mes]
→ [PostgreSQL: DELETE leads inactivos >12 meses]
→ [PostgreSQL: registra cuántos se eliminaron en tabla de auditoría]
→ [notificación con resumen]
```

### Condición de salida
Puedes hacer el data mapping GDPR de cualquier sistema que construyas en menos de 30 minutos. Tu stack productivo no tiene vulnerabilidades obvias. Tienes documentado quién tiene acceso a qué datos y por cuánto tiempo.

---

## BLOQUE 8 — RESILIENCIA Y MONITOREO
*Tu sistema no falla silenciosamente*

**Condición de entrada**: Tienes un sistema en producción con datos reales.

### Conceptos
- Error workflows en N8N: qué hacer cuando un workflow falla
- La diferencia entre un error recuperable y uno que requiere intervención humana
- Idempotencia: diseñar operaciones que puedan repetirse sin efectos secundarios
- Health check: cómo saber si tu N8N está vivo
- Logging estructurado vs logs aleatorios

### Ejercicios

**Ejercicio 1 — Error workflow global**
En N8N puedes configurar un workflow que se ejecuta cuando cualquier otro workflow falla. Crea uno:
```
[Error Trigger]
→ [Code node: extrae información del error — workflow, nodo, mensaje, timestamp]
→ [notificación: Telegram o email con contexto completo]
```
Pruébalo: crea un workflow que falle intencionalmente y verifica que recibes la notificación.

**Ejercicio 2 — Retry y backoff**
Para workflows que llaman a APIs externas, configura retry con backoff:
- 3 reintentos máximo
- Espera entre reintentos: 1min, 5min, 15min
- Si los 3 fallan: notificación de intervención requerida

**Ejercicio 3 — Health check**
Crea un workflow simple con Schedule trigger cada 5 minutos:
```
[Schedule: cada 5min]
→ [HTTP Request: llama a una URL tuya o una API conocida]
→ [IF: respuesta OK?]
  → NO: [notificación urgente — el sistema puede estar caído]
```

**Ejercicio 4 — Test de idempotencia**
Toma tu workflow de procesamiento de leads. Envía el mismo lead dos veces. ¿Qué pasa?

Si crea dos registros → no es idempotente → arréglalo con la deduplicación del Bloque 5.
Si actualiza el existente → es idempotente → está bien diseñado.

Documenta cómo lograste la idempotencia. Ese razonamiento va a un ADR.

### Condición de salida
Cuando algo falla en tu sistema, te enteras antes de que lo haga un cliente. Tienes error handling explícito en todos tus workflows productivos.

---

## PROYECTO ANCLA 1 — SISTEMA DE GESTIÓN DE LEADS
*Integra Bloques 1 al 5*

**Cuándo construirlo**: Cuando hayas completado los Bloques 1-4 y tengas base de SQL (Bloque 5 en curso).

Este proyecto no es un ejercicio. Es un sistema real que resuelve un problema real tuyo: saber cuándo llegan leads, procesarlos automáticamente y tener historial.

**Spec completa**: Ver `fase-1-redefinida.md` → Proyecto Ancla 1

**Documentación obligatoria al terminar:**
- ADR: decisiones arquitectónicas del sistema (por qué este trigger, por qué estas APIs, por qué este schema)
- Data mapping GDPR completo
- README con diagrama de flujo
- Entrada en DAILY-LOG: qué aprendiste que no esperabas aprender

---

## PROYECTO ANCLA 2 — SISTEMA DEPLOYADO EN PRODUCCIÓN
*Integra Bloques 6 y 7*

**Cuándo construirlo**: Cuando el Proyecto Ancla 1 esté funcionando localmente y hayas completado el Bloque 6.

Este proyecto convierte el Ancla 1 en un producto entregable. El criterio real: ¿puedes apagar tu laptop y el sistema sigue procesando leads?

**Spec completa**: Ver `fase-1-redefinida.md` → Proyecto Ancla 2

**Documentación obligatoria:**
- Guía de deployment paso a paso (que otra persona pueda replicarlo)
- Runbook: qué hacer si algo falla
- Costos mensuales documentados (servidor + APIs + dominio)

---

## PROYECTO ANCLA 3 — SISTEMA CON FALLBACK Y RESILIENCIA
*Integra Bloque 8 + todo lo anterior*

**Cuándo construirlo**: Cuando el Ancla 2 esté en producción y hayas completado el Bloque 8.

**Spec completa**: Ver `fase-1-redefinida.md` → Proyecto Ancla 3

---

## OUTREACH EN PARALELO
*Esto no espera a que termines los bloques técnicos*

El primer cliente no llega cuando terminas de aprender. Llega cuando empiezas a hablar con el mercado. Estos dos flujos corren en paralelo.

### Cuándo empezar el outreach
Cuando tengas el Proyecto Ancla 1 funcionando — aunque sea localmente. Eso es evidencia suficiente para hablar con prospectos.

### Qué hacer antes de contactar a alguien
- Tener claro qué problema resuelves (no qué tecnología usas)
- Tener un caso de uso concreto que puedas mostrar
- Saber cuánto cobras y qué incluye

### La secuencia mínima
1. Lista de 10 empresas en Rovereto/Trento que podrían beneficiarse de automatización
2. Para cada una: identifica el problema probable (¿qué proceso manual hacen que podría automatizarse?)
3. Mensaje de contacto centrado en el problema, no en la tecnología
4. Audit gratuito de 30 minutos como entrada — en ese audit identificas oportunidades reales

### Lo que convierte una conversación en un proyecto
No es "tengo N8N". Es "identifico que pasas X horas semanales haciendo Y — puedo reducirlo a Z minutos".

---

## REGLAS DE ACTUALIZACIÓN DE ESTE DOCUMENTO

**Actualizar cuando:**
- Completas un bloque y ajustas la condición de salida según la experiencia real
- Descubres que un ejercicio no tiene el nivel correcto (muy fácil o muy bloqueante)
- Añades un ejercicio nuevo que surgió de un proyecto real

**No actualizar:**
- Para "mejorar el formato"
- Porque encontraste un tutorial interesante
- Por mantenerlo "perfecto"

---

**Documento creado**: 25 de febrero 2026  
**Referencia**: `fase-1-redefinida.md`  

