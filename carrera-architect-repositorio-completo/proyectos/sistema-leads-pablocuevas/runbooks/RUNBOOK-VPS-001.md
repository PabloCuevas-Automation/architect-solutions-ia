# RUNBOOK-VPS-001: Guía de Ejecución del VPS
## Hetzner CX22 + Ubuntu 24.04 + Docker + N8N + PostgreSQL

**Versión**: 1.0  
**Fecha**: 2 de mayo 2026  
**Referencia**: ADR-207 — Arquitectura de seguridad VPS  
**Prerequisito**: Cuenta Hetzner con 2FA y DPA firmado  
**Tiempo estimado**: 3-4 horas para primera ejecución completa  
**Convención**: Los comandos se ejecutan como usuario `pablo` con `sudo`
salvo que se indique explícitamente `root`.

---

## FASE 0 — Antes de tocar el servidor

### 0.1 Cuenta Hetzner
```
☐ 2FA activado en hetzner.com → Security → Two-factor authentication
☐ DPA firmado: hetzner.com → Legal → Data Processing Agreement
☐ Copia del DPA descargada y archivada en /docs/legal/
```

### 0.2 Servidor contratado
```
Producto: CX22
SO: Ubuntu 24.04 LTS
Datacenter: nbg1 (Nuremberg, Alemania) o hel1 (Helsinki, Finlandia)
SSH key: añadir tu clave pública durante el proceso de creación
```

### 0.3 Firewall externo de Hetzner (configurar ANTES de conectar)
```
Panel Hetzner → Firewalls → Create Firewall

Reglas de entrada (Inbound):
  TCP 22   → Source: TU_IP_PUBLICA/32   (solo tu IP)
  TCP 80   → Source: 0.0.0.0/0
  TCP 443  → Source: 0.0.0.0/0
  Todo lo demás: DENEGAR (por defecto)

Asignar el firewall al servidor antes del primer login.
```

---

## FASE 1 — Hardening del sistema operativo

**Conectar al servidor:**
```bash
ssh root@IP_DEL_SERVIDOR
```

### 1.1 Actualización inicial
```bash
apt update && apt upgrade -y
```

### 1.2 Usuario no-root
```bash
adduser pablo
usermod -aG sudo pablo
# Cuando pida contraseña: usar una fuerte, guardar en gestor de contraseñas
```

### 1.3 Copiar SSH key al nuevo usuario
```bash
# Desde tu máquina LOCAL (nueva terminal, sin cerrar la sesión root):
ssh-copy-id -i ~/.ssh/id_ed25519.pub pablo@IP_DEL_SERVIDOR

# Verificar que funciona:
ssh pablo@IP_DEL_SERVIDOR
# Si conecta: perfecto. No cerrar la sesión root todavía.
```

### 1.4 Hardening SSH
```bash
# Como root:
nano /etc/ssh/sshd_config

# Cambiar/añadir estas líneas:
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes

# Verificar sintaxis y reiniciar:
sshd -t && systemctl restart sshd

# CRÍTICO: Abrir nueva terminal y verificar acceso como pablo ANTES de cerrar root
ssh pablo@IP_DEL_SERVIDOR
```

### 1.5 UFW
```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow from TU_IP_PUBLICA to any port 22    # Tu IP fija, no 22/tcp abierto
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable
ufw status verbose
```

### 1.6 Fail2Ban
```bash
apt install fail2ban -y
systemctl enable fail2ban
systemctl start fail2ban
systemctl status fail2ban
```

### 1.7 Parches automáticos
```bash
apt install unattended-upgrades -y
dpkg-reconfigure --priority=low unattended-upgrades
```

### 1.8 AIDE — detección de integridad
```bash
apt install aide -y
aide --init
mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db

# Cron semanal (domingos a las 4am):
echo "0 4 * * 0 root aide --check | mail -s 'AIDE Report' tu@email.com" >> /etc/crontab
```

### 1.9 auditd — auditoría de comportamiento Docker
```bash
apt install auditd -y

cat > /etc/audit/rules.d/docker.rules << 'EOF'
-w /usr/bin/docker -p rwxa -k docker_binary
-w /var/run/docker.sock -p rwxa -k docker_socket
-w /var/lib/docker -p wa -k docker_data
-a always,exit -F arch=b64 -S setuid -F auid>=1000 -F auid!=4294967295 -k priv_esc
EOF

systemctl restart auditd
auditctl -l   # Verificar que las reglas están activas
```

### 1.10 logwatch
```bash
apt install logwatch -y
# Configurar email en /etc/logwatch/conf/logwatch.conf si es necesario
```

---

## FASE 2 — Instalación de Docker

```bash
# Dependencias
apt install ca-certificates curl gnupg -y

# Repositorio oficial de Docker
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) \
  signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null

apt update
apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# Añadir usuario pablo al grupo docker
usermod -aG docker pablo

# Verificar
docker --version
docker compose version
```

### 2.1 Configuración de Docker daemon
```bash
cat > /etc/docker/daemon.json << 'EOF'
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
EOF

systemctl restart docker
```

---

## FASE 3 — Egress filtering

```bash
# Instalar iptables-persistent
apt install iptables-persistent -y

# Aplicar reglas de egress para contenedores Docker
iptables -I DOCKER-USER -j DROP
iptables -I DOCKER-USER -o eth0 -p tcp --dport 443 -j ACCEPT   # HTTPS APIs
iptables -I DOCKER-USER -o eth0 -p tcp --dport 80 -j ACCEPT    # HTTP
iptables -I DOCKER-USER -o eth0 -p udp --dport 53 -j ACCEPT    # DNS
iptables -I DOCKER-USER -o eth0 -p tcp --dport 53 -j ACCEPT    # DNS TCP

# Guardar para persistencia
netfilter-persistent save

# Verificar que las reglas están activas
iptables -L DOCKER-USER

# VERIFICACIÓN CRÍTICA tras reboot:
# reboot
# iptables -L DOCKER-USER   ← debe seguir mostrando las reglas
```

### 3.1 Verificar egress desde contenedor
```bash
# Lanzar contenedor de prueba
docker run --rm alpine sh -c "apk add curl && curl https://api.abstract.io"
# Debe funcionar (puerto 443 permitido)

docker run --rm alpine sh -c "apk add curl && curl http://evil.com:8888"
# Debe fallar (puerto 8888 no permitido)
```

---

## FASE 4 — Estructura de proyectos

```bash
# Crear estructura de directorios
mkdir -p /opt/pa1
mkdir -p /opt/geolabor
mkdir -p /opt/suanfarma
mkdir -p /opt/scripts
mkdir -p /backups

# Red central de Nginx
docker network create proxy_net
```

---

## FASE 5 — PostgreSQL central

```bash
# Crear directorio para PostgreSQL
mkdir -p /opt/postgres

cat > /opt/postgres/docker-compose.yml << 'EOF'
networks:
  pa1_internal:
    driver: bridge
  geolabor_internal:
    driver: bridge

services:
  postgres:
    image: postgres:16
    restart: always
    networks:
      - pa1_internal
      - geolabor_internal
    env_file:
      - .env
    volumes:
      - postgres_data:/var/lib/postgresql/data
    security_opt:
      - no-new-privileges:true

volumes:
  postgres_data:
EOF

# .env de PostgreSQL
cat > /opt/postgres/.env << 'EOF'
POSTGRES_PASSWORD=CAMBIAR_PASSWORD_FUERTE_AQUI
EOF

chmod 600 /opt/postgres/.env

# Arrancar PostgreSQL
cd /opt/postgres && docker compose up -d

# Crear bases de datos y usuarios
docker exec -it postgres_postgres_1 psql -U postgres << 'EOF'
-- PA1
CREATE DATABASE pa1_db;
CREATE USER n8n_pa1 WITH ENCRYPTED PASSWORD 'CAMBIAR_PASSWORD_PA1';
REVOKE ALL ON DATABASE pa1_db FROM PUBLIC;
GRANT CONNECT ON DATABASE pa1_db TO n8n_pa1;
GRANT USAGE ON SCHEMA public TO n8n_pa1;
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO n8n_pa1;
GRANT CREATE, ALTER ON SCHEMA public TO n8n_pa1;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO n8n_pa1;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO n8n_pa1;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT USAGE, SELECT ON SEQUENCES TO n8n_pa1;
ALTER USER n8n_pa1 WITH NOSUPERUSER NOCREATEDB NOCREATEROLE;

-- Verificar
SELECT usename, usesuper FROM pg_user WHERE usename = 'n8n_pa1';
-- usesuper debe ser 'false'
EOF
```

---

## FASE 6 — Nginx con TLS

```bash
# Instalar Nginx y Certbot
apt install nginx certbot python3-certbot-nginx -y

# Configuración base de Nginx
cat > /etc/nginx/sites-available/n8n << 'EOF'
# Zona de rate limiting
limit_req_zone $binary_remote_addr zone=webhook_limit:10m rate=2r/s;

server {
    listen 80;
    server_name n8n.pablocuevas.it admin.pablocuevas.it;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name n8n.pablocuevas.it;

    # TLS explícito
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;

    # Protección contra ataques de conexión lenta
    client_body_timeout 10s;
    client_header_timeout 10s;
    client_max_body_size 1M;

    # Panel N8N restringido a IP de administración
    location / {
        allow TU_IP_PUBLICA;
        deny all;
        proxy_pass http://127.0.0.1:5678;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
    }

    # Webhook público con rate limiting
    location /webhook/ {
        # Content-Type enforcement
        if ($http_content_type !~ "^application/json") {
            return 415;
        }
        limit_req zone=webhook_limit burst=10;
        proxy_pass http://127.0.0.1:5678;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
EOF

ln -s /etc/nginx/sites-available/n8n /etc/nginx/sites-enabled/
nginx -t
systemctl reload nginx

# Obtener certificado SSL
certbot --nginx -d n8n.pablocuevas.it -d admin.pablocuevas.it

# Configurar logrotate para Nginx
cat > /etc/logrotate.d/nginx << 'EOF'
/var/log/nginx/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    sharedscripts
    postrotate
        nginx -s reopen
    endscript
}
EOF
```

---

## FASE 7 — Deploy de PA1

```bash
cat > /opt/pa1/docker-compose.yml << 'EOF'
networks:
  pa1_internal:
    external: true      # Red creada en PostgreSQL compose
  proxy_net:
    external: true      # Red creada en Fase 4

services:
  n8n:
    image: n8nio/n8n:latest
    restart: always
    networks:
      - pa1_internal
      - proxy_net
    env_file:
      - .env
    user: "1000:1000"
    read_only: true
    volumes:
      - n8n_data:/home/node/.n8n
    tmpfs:
      - /tmp
    security_opt:
      - no-new-privileges:true
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M

volumes:
  n8n_data:
EOF

# Crear .env de PA1
cat > /opt/pa1/.env << 'EOF'
N8N_ENCRYPTION_KEY=GENERAR_CLAVE_ALEATORIA_32_CHARS
EXECUTIONS_DATA_MAX_AGE=30
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=pablo
N8N_BASIC_AUTH_PASSWORD=CAMBIAR_PASSWORD_N8N
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=postgres
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=pa1_db
DB_POSTGRESDB_USER=n8n_pa1
DB_POSTGRESDB_PASSWORD=CAMBIAR_PASSWORD_PA1
WEBHOOK_URL=https://n8n.pablocuevas.it
EOF

chmod 600 /opt/pa1/.env

# Arrancar N8N
cd /opt/pa1 && docker compose up -d
docker logs pa1_n8n_1 --follow   # Verificar arranque sin errores
```

---

## FASE 8 — Script de backup

```bash
# Crear passphrase de backup
echo "GENERAR_PASSPHRASE_LARGA_Y_SEGURA" > /root/.backup_pass
chmod 400 /root/.backup_pass
# IMPORTANTE: copiar esta passphrase al gestor de contraseñas local

# Instalar rclone
curl https://rclone.org/install.sh | sudo bash

# Configurar rclone para Hetzner Storage Box
# rclone config  → seguir el asistente para SFTP con credenciales de Storage Box

# Script de backup
cat > /opt/scripts/backup.sh << 'SCRIPT'
#!/bin/bash
set -e
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_DIR="/tmp/backup_${DATE}"
mkdir -p "$BACKUP_DIR"
PASSPHRASE=$(cat /root/.backup_pass)

# Backup de bases de datos
for PROJECT in pa1; do
    docker exec postgres_postgres_1 pg_dump \
        -U n8n_${PROJECT} -d ${PROJECT}_db \
    | gzip > "${BACKUP_DIR}/${DATE}_${PROJECT}_dump.sql.gz"
done

# Backup de configuración
for PROJECT in pa1; do
    cp /opt/${PROJECT}/.env "${BACKUP_DIR}/${DATE}_${PROJECT}.env"
    cp /opt/${PROJECT}/docker-compose.yml "${BACKUP_DIR}/${DATE}_${PROJECT}_compose.yml"
done
cp -r /etc/nginx/ "${BACKUP_DIR}/${DATE}_nginx"

# Comprimir y cifrar
tar -czf "${BACKUP_DIR}.tar.gz" "$BACKUP_DIR"
gpg -c --batch --passphrase "$PASSPHRASE" "${BACKUP_DIR}.tar.gz"

# Subir a Storage Box
rclone copy "${BACKUP_DIR}.tar.gz.gpg" hetzner_storage:backups/

# Limpiar
rm -rf "$BACKUP_DIR" "${BACKUP_DIR}.tar.gz" "${BACKUP_DIR}.tar.gz.gpg"

echo "Backup completado: ${DATE}"
SCRIPT

chmod +x /opt/scripts/backup.sh

# Cron diario a las 3am
echo "0 3 * * * root /opt/scripts/backup.sh >> /var/log/backup.log 2>&1" >> /etc/crontab

# Ejecutar backup manual para verificar
/opt/scripts/backup.sh
```

---

## FASE 9 — Snapshot post-hardening

```bash
# Antes del snapshot: verificar que todo está corriendo
docker ps
systemctl status nginx
ufw status
iptables -L DOCKER-USER
```

```
Panel Hetzner → Server → Snapshots → Take Snapshot
Nombre: vps-base-hardened-YYYYMMDD

Este snapshot es el punto de restauración ante cualquier
corrupción del OS o compromiso del host.
```

---

## VERIFICACIÓN FINAL

```bash
# Sistema operativo
ufw status verbose
systemctl status fail2ban
systemctl status auditd

# Docker
docker ps
docker network ls   # debe mostrar proxy_net, pa1_internal, geolabor_internal

# Egress (desde contenedor)
docker run --rm alpine sh -c "apk add curl && curl -s https://api.abstract.io && echo OK"
docker run --rm alpine sh -c "apk add curl && curl http://example.com:8888" || echo "Bloqueado OK"

# TLS
curl -I https://n8n.pablocuevas.it   # debe devolver 200 con HTTPS

# Verificar checklist pre-producción completo antes del primer lead real
```

---

## PROCEDIMIENTO DE RECUPERACIÓN ANTE DESASTRE

```
TIEMPO OBJETIVO: < 30 minutos

1. Contratar nuevo Hetzner CX22 (misma región)
2. Ejecutar Fases 0-3 de este runbook (hardening base)
3. Instalar Docker (Fase 2)
4. Descargar último backup de Storage Box:
   rclone copy hetzner_storage:backups/[ultimo_backup].tar.gz.gpg .
5. Descifrar:
   gpg -d [backup].tar.gz.gpg > backup.tar.gz
   tar -xzf backup.tar.gz
6. Restaurar PostgreSQL:
   docker exec -i postgres_postgres_1 psql -U n8n_pa1 pa1_db < dump.sql
7. Restaurar .env con N8N_ENCRYPTION_KEY
8. docker compose up -d en cada proyecto
9. Verificar que N8N arranca y APIs conectan
10. Actualizar DNS si la IP cambió
```

---

**Documento creado**: 2 de mayo 2026  
**Última revisión**: 2 de mayo 2026  
**Próxima revisión**: Después de primera ejecución completa — documentar desvíos
