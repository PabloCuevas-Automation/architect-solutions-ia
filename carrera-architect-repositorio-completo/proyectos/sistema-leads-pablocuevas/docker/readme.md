# Docker Stack — sistema-leads-pablocuevas

## Servicios
- **PostgreSQL** `localhost:5432` — base de datos del sistema
- **pgAdmin** `localhost:5050` — interfaz visual de PostgreSQL
- **N8N** `localhost:5678` — motor de automatización

## Requisitos
- Docker Desktop corriendo
- Archivo `.env` en esta carpeta (ver `.env.example`)

## Comandos

**Levantar todo:**
```bash
cd C:\Users\pablo\Desktop\architect-solutions\carrera-architect-repositorio-completo\proyectos\sistema-leads-pablocuevas\docker
docker compose up -d
```

**Parar todo:**
```bash
docker compose down
```

**Ver estado:**
```bash
docker ps
```

## Notas
- Los datos persisten en volúmenes Docker aunque pares los contenedores
- El `.env` nunca va a Git — contiene credenciales reales
- Ver `.env.example` para saber qué variables configurar