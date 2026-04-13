# Docker — Infraestructura Local
## sistema-leads-pablocuevas

Contiene la configuración de PostgreSQL y pgAdmin para desarrollo local.

---

## Requisitos
- Docker Desktop corriendo

---

## Primera vez

Copia el archivo de ejemplo y rellena tus valores reales:
```bash
cp .env.example .env
```

Edita `.env` con tus credenciales. Nunca subas `.env` a GitHub.

---

## Levantar los contenedores
```bash
docker compose up -d
```

La primera vez descarga las imágenes — tarda 2-3 minutos.

---

## Acceder a pgAdmin
Abre en el navegador: `http://localhost:5050`

- Email: el valor de `PGADMIN_EMAIL` en tu `.env`
- Contraseña: el valor de `PGADMIN_PASSWORD` en tu `.env`

Servidor a conectar:
- Host: `postgres`
- Puerto: `5432`
- Base de datos: el valor de `POSTGRES_DB`
- Usuario: `POSTGRES_USER`
- Contraseña: `POSTGRES_PASSWORD`

---

## Parar los contenedores
```bash
docker compose down
```

Los datos persisten en el volume `postgres_data` — no se pierden al parar.

---

## Verificar estado
```bash
docker ps
```

Deberías ver `postgres_local` y `pgadmin_local` con status `Up`.