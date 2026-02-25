# Plan de Acción — Días 28 al 36
**Desde**: Miércoles 25/02/2026  
**Hasta**: Jueves 05/03/2026 — instalación de N8N  
**Fuente**: `21-02-recalibracion_fase_1.md` — Semanas 5 y 6

---

## Día 28 — Miércoles 25/02 · APIs: El modelo mental

**Objetivo**: Entender qué es una API y cómo funciona la comunicación cliente-servidor antes de escribir una sola línea de código.

### Concepto 1 — Cliente y Servidor
Cuando abres pablocuevas.it, tu navegador es el **cliente** — pide cosas. El servidor es quien responde. Una API es la puerta por donde el cliente le habla al servidor.

En N8N esto es constante: un nodo llama a una API externa (Gmail, Telegram, Firebase) y recibe una respuesta JSON. Si no entiendes el modelo cliente-servidor, no entenderás por qué los nodos fallan.

### Concepto 2 — URL structure
Cada parte de una URL tiene significado:
```
https://api.example.com/leads/123?estado=activo
  │         │              │    │       │
  │         │              │    │       └─ parámetro de filtro
  │         │              │    └─ ID específico del recurso
  │         │              └─ recurso (colección)
  │         └─ dominio de la API
  └─ protocolo seguro
```

### Concepto 3 — Métodos HTTP
El método le dice al servidor qué quieres hacer:

| Método | Acción | Ejemplo real |
|--------|--------|--------------|
| `GET` | Pedir datos | Ver leads en Firebase |
| `POST` | Enviar datos nuevos | Guardar un lead nuevo |
| `PUT` | Reemplazar datos | Actualizar todo un perfil |
| `PATCH` | Modificar parcialmente | Cambiar solo el estado del lead |
| `DELETE` | Eliminar | Borrar un lead |

### Ejercicio del día — Sin código
Abre el navegador y visita estas dos URLs. Observa la respuesta JSON:
```
https://jsonplaceholder.typicode.com/users/1
https://jsonplaceholder.typicode.com/users
```
La primera devuelve 1 usuario. La segunda devuelve todos. Analiza la diferencia en la URL y en la respuesta. Eso es REST.

**Pregunta de validación al cerrar**: ¿Qué método HTTP usarías para que N8N guarde un lead nuevo en una base de datos externa?

---

## Día 29 — Jueves 26/02 · Status Codes + Thunder Client

**Objetivo**: Entender cómo el servidor te comunica si algo fue bien o mal, e instalar la herramienta para hacer requests desde VS Code.

### Concepto 1 — Status Codes
Cada respuesta de una API lleva un número. Ese número es el diagnóstico:

| Rango | Significa | Ejemplos clave |
|-------|-----------|----------------|
| `2xx` | Todo bien | `200` OK, `201` Creado |
| `4xx` | Error tuyo | `400` Request malo, `401` Sin autenticación, `404` No existe |
| `5xx` | Error del servidor | `500` Error interno |

En N8N cuando un nodo falla, lo primero que miras es el status code. `401` significa que tus credenciales están mal. `404` significa que la URL está mal. `500` significa que el problema es del servicio externo, no tuyo.

### Concepto 2 — Headers
Los headers son metadata que viajan con cada request — información sobre el request, no el contenido:
- `Content-Type: application/json` → le dices al servidor que mandas JSON
- `Authorization: Bearer tu_token` → te identificas

### Ejercicio del día — Instalar Thunder Client
1. En VS Code: `Ctrl+Shift+X`
2. Buscar "Thunder Client"
3. Instalar
4. En el panel izquierdo aparece un ícono de rayo — abrirlo
5. Hacer un GET a `https://jsonplaceholder.typicode.com/posts`
6. Observar el status code `200` y la respuesta JSON

**Pregunta de validación**: Recibes un `401` de la API de Gmail en N8N. ¿Qué es lo primero que revisas?

---

## Día 30 — Viernes 27/02 · Fetch + Async/Await

**Objetivo**: Llamar a una API real desde JavaScript y procesar la respuesta.

### Concepto 1 — Por qué async/await
`fetch()` es asíncrono — JavaScript no espera la respuesta, sigue ejecutando el código. Eso rompe el flujo si no lo manejas.

Sin `async/await`:
```javascript
const respuesta = fetch("https://api.example.com/data");
console.log(respuesta); // Promise {pending} — no tienes los datos todavía
```

Con `async/await`:
```javascript
async function obtenerDatos() {
  const respuesta = await fetch("https://api.example.com/data");
  const datos = await respuesta.json();
  console.log(datos); // aquí sí tienes los datos
}
```

`await` le dice a JavaScript: "espera aquí hasta tener la respuesta antes de continuar".

### Concepto 2 — Try/Catch en APIs
Las APIs fallan. La red falla. El servidor falla. `try/catch` es tu red de seguridad:

```javascript
async function obtenerDatos() {
  try {
    const respuesta = await fetch("https://api.example.com/data");
    const datos = await respuesta.json();
    console.log(datos);
  } catch (error) {
    console.log("La API falló:", error.message);
  }
}
```

### Ejercicio del día
Script que llama a `https://restcountries.com/v3.1/name/italy`, extrae nombre, capital y población, y los muestra en consola con template literals. Lo construimos juntos.

---

## Día 31 — Sábado 28/02 · Autenticación

**Objetivo**: Entender cómo identificarte ante APIs que requieren credenciales.

### Concepto 1 — API Key
La forma más simple. La API te da una clave única y la mandas en cada request:

```javascript
// En el header
fetch("https://api.example.com/data", {
  headers: {
    "Authorization": "Bearer tu_api_key_aqui"
  }
});

// En la URL (menos seguro)
fetch("https://api.example.com/data?api_key=tu_api_key_aqui");
```

### Concepto 2 — Cómo lo gestiona N8N
N8N tiene un sistema de **Credentials** — guardas la API key una vez, encriptada, y la referencias en los nodos. Nunca escribes la key en el código directamente. Eso es seguridad y es GDPR-compatible.

### Concepto 3 — OAuth (solo entender el flujo)
Es el sistema del botón "Conectar con Google". No lo implementas — N8N lo hace por ti. Solo necesitas entender que existe y que N8N lo maneja automáticamente cuando conectas Gmail o Google Sheets.

### Ejercicio del día
Leer la documentación de una API pública con autenticación — por ejemplo `https://openweathermap.org/api`. Identificar dónde va la key, qué tipo de autenticación usa, y cómo se vería en un nodo de N8N.

---

## Día 32 — Domingo 01/03 · Proyecto Integrador APIs

**Objetivo**: Primer script completo autónomo que combina todo lo de la semana.

### Lo que construyes
Un script que:
1. Llama a una API real con `fetch` y `async/await`
2. Transforma el JSON recibido usando `map` y `filter`
3. Produce un output estructurado en consola
4. Tiene manejo de errores con `try/catch`
5. Tiene un README explicando qué hace y por qué

Este script es tu primer entregable de la semana de APIs y va al repositorio de GitHub.

### La conexión con N8N
Cada paso de ese script es un nodo:
```
[HTTP Request node] → [Code node: transforma] → [Set node: estructura output]
```
Ya sabes hacer los tres en JS. En N8N solo cambias la sintaxis.

---

## Días 33-34 — Lunes 02/03 y Martes 03/03 · Consolidación

**Objetivo**: Verificar que puedes operar sin consultar documentación básica.

### La pregunta que valida estos dos días
¿Puedes construir desde cero un script que llame a una API, transforme el JSON y muestre un output útil sin mirar apuntes para la sintaxis básica?

Si sí → Día 35 es repaso rápido y el 36 instalas N8N.  
Si hay gaps → los identificas aquí y los cierras. No avanzas con deuda oculta.

### Ejercicio de validación
Sin ayuda, sin apuntes: script que llama a `https://jsonplaceholder.typicode.com/todos` y muestra solo los todos completados (`completed: true`) con su título y el ID del usuario.

---

## Día 35 — Miércoles 04/03 · Consolidación JS completa

**Objetivo**: Repaso final de todos los conceptos que usarás en el Code node de N8N.

### Checklist de conceptos N8N-críticos

Repasa cada uno. Si tienes dudas en alguno, ese es el gap que cierras hoy:

- `map` — transformar cada item del array
- `filter` — quedarte con los items que cumplen una condición
- `find` — encontrar el primer item que cumple una condición
- Objetos anidados — `item.json.datos.campo`
- Optional chaining `?.` — `item.json.datos?.campo` (no rompe si `datos` no existe)
- Template literals — `` `texto ${variable}` ``
- `async/await` — llamadas a APIs sin bloquear
- `try/catch` — manejo de errores
- Spread operator `...` — enriquecer items sin perder datos originales

### Al cerrar el día
Si puedes usar cada uno de estos sin consultar, estás listo. Mañana instalas N8N.

---

## Día 36 — Jueves 05/03 · 🎯 N8N Instalado

**Objetivo**: N8N corriendo en local y accesible desde el navegador.

### Instalación via Docker (recomendada — Docker ya instalado)

Abre el terminal y ejecuta:
```bash
docker run -p 5678:5678 n8nio/n8n
```

Luego abre el navegador en:
```
http://localhost:5678
```

Si ves la interfaz de N8N, el día está completado.

### Si Docker falla — alternativa npm
```bash
npm install n8n -g
n8n
```

### Lo que haces después de instalarlo
1. Crear una cuenta local (no necesitas internet)
2. Explorar la interfaz — canvas, panel de nodos, execution log
3. Leer "Getting Started" en `https://docs.n8n.io`
4. No construir nada todavía — solo familiarizarte

### Por qué Docker y no npm
Docker crea un contenedor aislado — N8N corre en su propio entorno sin interferir con tu sistema. Cuando llegue el momento de ponerlo en producción para un cliente, el proceso es el mismo. Aprendes el stack correcto desde el inicio.

---

## Resumen del camino

| Día | Fecha | Hito |
|-----|-------|------|
| 28 | 25/02 | APIs — modelo mental y HTTP |
| 29 | 26/02 | Status codes + Thunder Client instalado |
| 30 | 27/02 | Fetch + async/await funcionando |
| 31 | 28/02 | Autenticación — API keys y OAuth conceptual |
| 32 | 01/03 | Primer script completo con API real |
| 33-34 | 02-03/03 | Consolidación — validación sin apuntes |
| 35 | 04/03 | Repaso JS N8N-crítico |
| **36** | **05/03** | **🎯 N8N instalado y corriendo** |

Lo que viene después del 36 — Días 37 al 39 — es N8N en serio: interfaz, Code node, y el Día 39 la automatización real Firebase → notificación leads.
