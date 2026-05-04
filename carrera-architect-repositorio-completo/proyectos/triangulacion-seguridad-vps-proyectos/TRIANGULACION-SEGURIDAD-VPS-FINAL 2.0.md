# ARQUITECTURA DE SEGURIDAD VPS — DOCUMENTO DE CIERRE v2.0
## Stack: Hetzner CX22 + Ubuntu 24.04 + Docker Compose + N8N + PostgreSQL + Nginx
## Proyectos: PA1 (leads pablocuevas.it) + GeoLabor + Suanfarma

**Versión**: 2.0  
**Fecha**: 2 de mayo 2026  
**Fuentes**: Triangulación Claude + Gemini + DeepSeek + ChatGPT — 4 rondas  
**Propósito**: Validación final de cierre — ¿hay alguna fisura estructural restante?  
**Reutilización**: Esta arquitectura es el estándar base para cualquier sistema  
con stack N8N + Docker + PostgreSQL. El 80% se replica sin modificación.  
Lo que cambia por proyecto: puertos de egress, TTLs, nombres de DB y .env.

---

## CLASIFICACIÓN DE RIESGO

**Riesgo operativo** (workflow falla, API no responde, lead se pierde):  
Recuperable. Tiene segunda oportunidad. No es el foco de este documento.

**Riesgo existencial** (brecha de datos PII):  
Irrecuperable. Notificación GDPR obligatoria en 72h (Art. 33), responsabilidad  
legal como Data Controller, pérdida de confianza del cliente. Todo el diseño  
de seguridad está orientado a mitigar este escenario.

---

## DECISIONES ESTRATÉGICAS TOMADAS

### VPS: Hetzner CX22
Empresa alemana. Datacenter Alemania o Finlandia (EEE). DPA firmable desde  
el panel. Red estable para webhooks en tiempo real. ~4€/mes.  
**Descartados**: Contabo (overselling, latencia inconsistente), DigitalOcean  
(entidad legal USA — sujeta a CLOUD Act aunque el datacenter sea Frankfurt).  
**Convergencia**: Claude + Gemini + DeepSeek + ChatGPT — unánime.

### Arquitectura multi-proyecto: silos por proyecto
Cada proyecto tiene docker-compose.yml propio, red Docker interna propia,  
base de datos PostgreSQL propia, credenciales propias.  
Nginx actúa como reverse proxy central — único punto de entrada exterior.  
**Límite conocido y documentado**: aislamiento Docker ≠ aislamiento VM.  
Un escape de contenedor llega al host. Mitigado por capas OS + Docker hardening.

### Límite de multi-tenant: Suanfarma
VPS compartido válido para pilotos gratuitos. Cuando Suanfarma pase a cliente  
de pago — especialmente siendo empresa farmacéutica — requiere VPS dedicado.  
Compliance farmacéutico no tolera datos en infraestructura compartida.  
**Estado**: deuda técnica consciente documentada. No es problema de hoy.

---

## CAPAS DE SEGURIDAD — SECUENCIA COMPLETA

---

### CAPA 0 — Cuenta Hetzner (antes de tocar el servidor)

| Acción | Detalle | Prioridad |
|--------|---------|-----------|
| 2FA en cuenta Hetzner | Sin 2FA, acceso a consola web sin SSH ni firewall. Mayor ROI de toda la lista. | 🔴 Crítico |
| Registro con datos reales | Sistema antifraude agresivo — puede pedir foto de ID. Sin VPN. | 🔴 Crítico |
| Firmar DPA | Panel Hetzner → sección legal → DPA. Firmar + archivar copia como evidencia auditable. | 🔴 Crítico |

**Sobre el DPA**: Cubre a Hetzner como procesador. NO cubre la aplicación.  
El operador es el Data Controller — la responsabilidad de los datos es suya.  
DPA sin medidas técnicas propias no constituye cumplimiento GDPR.

---

### CAPA 1 — Firewall externo de Hetzner (primera línea)

Filtra tráfico ANTES de que llegue al VPS. Si está bien configurado,  
un atacante ni toca el servidor.

| Regla | Puerto | Origen |
|-------|--------|--------|
| SSH | 22/TCP | **Solo tu IP pública fija** — nunca 0.0.0.0/0 |
| HTTP | 80/TCP | 0.0.0.0/0 |
| HTTPS | 443/TCP | 0.0.0.0/0 |
| Todo lo demás | — | DENEGAR por defecto |

Restringir el 22 a tu IP fija aquí es más seguro que en UFW:  
el tráfico no llega ni al servidor.

---

### CAPA 2 — Sistema operativo (hardening inicial)

**Ejecutar en este orden exacto, antes de instalar Docker.**

#### 2.1 Actualización del sistema
```bash
apt update && apt upgrade -y
```

#### 2.2 Usuario no-root
```bash
adduser pablo
usermod -aG sudo pablo
# Nunca operar como root en producción
```

#### 2.3 SSH key-only
```bash
# Desde tu máquina local:
ssh-copy-id -i ~/.ssh/id_ed25519.pub pablo@IP_DEL_SERVIDOR
```

`/etc/ssh/sshd_config`:
```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```
```bash
sshd -t && systemctl restart sshd
# CRÍTICO: verificar acceso desde nueva terminal ANTES de cerrar la sesión actual
```

#### 2.4 UFW — segunda línea de defensa
```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow from TU_IP_PUBLICA to any port 22    # Alineado con Hetzner — no 22/tcp abierto
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable
```

**Importante**: La regla del 22 debe ser restrictiva por IP, no abierta.  
Redundancia correcta con la Capa 1 — las capas no deben contradecirse.

#### 2.5 Fail2Ban
```bash
apt install fail2ban -y
# Configuración por defecto en Ubuntu 24.04 es un buen punto de partida
# Evolución futura: añadir jails para logs de Nginx
```

#### 2.6 Parches automáticos del OS
```bash
apt install unattended-upgrades -y
dpkg-reconfigure --priority=low unattended-upgrades
```
Los parches del OS y las actualizaciones de imágenes Docker son estrategias  
separadas. Una imagen Docker desactualizada sigue siendo vulnerable aunque  
Ubuntu esté parcheado. Revisión mensual de imágenes programada.

#### 2.7 Detección de integridad — AIDE
```bash
apt install aide -y
aide --init
mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
# Cron semanal para comparar estado actual vs snapshot
```
Si un atacante modifica un binario del sistema para ocultar su presencia,  
AIDE lo detecta. Es la diferencia entre enterarse de un compromiso y no  
enterarse nunca.

#### 2.8 Auditoría de comportamiento — auditd

`/etc/audit/rules.d/docker.rules`:
```bash
-w /usr/bin/docker -p rwxa -k docker_binary
-w /var/run/docker.sock -p rwxa -k docker_socket
-w /var/lib/docker -p wa -k docker_data
-a always,exit -F arch=b64 -S setuid -F auid>=1000 -F auid!=4294967295 -k priv_esc
```
Detecta modificación de binarios Docker, acceso al socket (vector de escape)  
y cualquier intento de escalada de privilegios.

#### 2.9 Logwatch — reporte diario
```bash
apt install logwatch -y
# Resumen diario por email de SSH, Nginx y sistema
```

---

### CAPA 3 — Fix crítico Docker/UFW

**El gap más importante de toda la triangulación — nadie lo menciona.**

Docker manipula iptables directamente, ignorando UFW. Si mapeas PostgreSQL  
como `5432:5432`, el puerto está abierto a internet aunque UFW diga lo contrario.

**Solución en docker-compose.yml**:
```yaml
# ✅ CORRECTO — bind a localhost
ports:
  - "127.0.0.1:5433:5432"

# ❌ INCORRECTO — expuesto a internet ignorando UFW
ports:
  - "5432:5432"
```

**Mejor solución**: Para servicios que solo se comunican entre contenedores  
(PostgreSQL con N8N en la misma red Docker), **eliminar completamente el  
bloque `ports`**. Si no hay mapeo de puertos, Docker no toca iptables para  
ese contenedor. Queda 100% inaccesible desde el exterior y desde el host.

```yaml
# PostgreSQL sin ports — accesible solo por contenedores en pa1_internal
postgres:
  image: postgres:16
  networks:
    - pa1_internal
  # Sin bloque ports — aislamiento total
```

---

### CAPA 4 — Egress filtering (salida de contenedores)

**Gap identificado en ronda 3 — crítico.**

Tu blindaje actual es solo de entrada. Un contenedor comprometido puede  
exfiltrar datos o abrir reverse shells a cualquier servidor externo sin  
obstáculos — ninguna capa anterior lo bloquea.

**Solución**: Cadena `DOCKER-USER` de iptables.

⚠️ `ufw default deny outgoing` NO es suficiente para Docker.  
El tráfico de contenedores sale por la cadena FORWARD de iptables, que Docker  
manipula directamente ignorando UFW. Solución falsa = falsa seguridad.

```bash
# Bloquear todo el tráfico saliente de contenedores por defecto
iptables -I DOCKER-USER -j DROP

# Permitir solo lo imprescindible
iptables -I DOCKER-USER -o eth0 -p tcp --dport 443 -j ACCEPT  # HTTPS APIs
iptables -I DOCKER-USER -o eth0 -p tcp --dport 80 -j ACCEPT   # HTTP
iptables -I DOCKER-USER -o eth0 -p udp --dport 53 -j ACCEPT   # DNS
iptables -I DOCKER-USER -o eth0 -p tcp --dport 53 -j ACCEPT   # DNS TCP

# Hacer persistente con iptables-persistent
apt install iptables-persistent -y
netfilter-persistent save
```

**Impacto**: Convierte una intrusión silenciosa en un incidente contenible.  
Un contenedor comprometido no puede exfiltrar datos ni abrir shells inversos.

---

### CAPA 5 — Límites de logs de Docker

**Gap identificado en ronda 3 — riesgo DoS accidental.**

Sin límites, los logs de contenedores Docker crecen indefinidamente  
y pueden llenar los 40GB del disco, causando fallo catastrófico del sistema.

`/etc/docker/daemon.json`:
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```
```bash
systemctl restart docker
```

---

### CAPA 6 — Arquitectura de silos multi-proyecto

#### Estructura de directorios
```
/opt/
  pa1/
    docker-compose.yml
    .env                  # chmod 600 obligatorio
  geolabor/
    docker-compose.yml
    .env                  # chmod 600 obligatorio
  suanfarma/
    docker-compose.yml
    .env                  # chmod 600 obligatorio
```

#### Red central de Nginx
```bash
docker network create proxy_net
```

#### docker-compose.yml por proyecto (plantilla base)
```yaml
networks:
  pa1_internal:           # Red interna — aislada por proyecto
    driver: bridge
  proxy_net:              # Red compartida solo con Nginx
    external: true

services:
  n8n:
    image: n8nio/n8n:latest
    networks:
      - pa1_internal      # Se comunica con su propia DB
      - proxy_net         # Solo N8N tiene salida hacia Nginx
    env_file:
      - .env              # Nunca variables inline en compose
    user: "1000:1000"     # N8N corre como node — compatible con imagen oficial
    read_only: true       # Sistema de archivos en solo lectura
    volumes:
      - n8n_data:/home/node/.n8n   # Excepción obligatoria — N8N escribe aquí
    tmpfs:
      - /tmp              # Escritura temporal permitida en memoria
    security_opt:
      - no-new-privileges:true
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M    # Evita DoS interno por contenedor descontrolado

  postgres:
    image: postgres:16
    networks:
      - pa1_internal      # Solo accesible desde N8N del mismo proyecto
    # Sin bloque ports — aislamiento total del exterior
    env_file:
      - .env
    user: "999:999"       # Usuario postgres no-root
    security_opt:
      - no-new-privileges:true
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  n8n_data:
  postgres_data:
```

#### Permisos de archivos .env
```bash
chmod 600 /opt/pa1/.env
chmod 600 /opt/geolabor/.env
chmod 600 /opt/suanfarma/.env
# Solo el usuario propietario puede leer las credenciales
```

---

### CAPA 7 — PostgreSQL: privilegios mínimos por proyecto

**Regla**: Un usuario + una base de datos por proyecto.  
Nunca usar el usuario `postgres` por defecto para las aplicaciones.

#### Creación por proyecto
```sql
-- Crear base de datos y usuario
CREATE DATABASE pa1_db;
CREATE USER n8n_pa1 WITH ENCRYPTED PASSWORD 'password_muy_fuerte';

-- Revocar acceso público por defecto
REVOKE ALL ON DATABASE pa1_db FROM PUBLIC;

-- Dar acceso solo a su propia base de datos
GRANT CONNECT ON DATABASE pa1_db TO n8n_pa1;
GRANT USAGE ON SCHEMA public TO n8n_pa1;

-- N8N necesita DDL para sus migraciones internas (TypeORM)
-- CRÍTICO: no reducir estos permisos — rompe el arranque de N8N
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO n8n_pa1;
GRANT CREATE, ALTER ON SCHEMA public TO n8n_pa1;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO n8n_pa1;

-- Privilegios para tablas futuras creadas por migraciones
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO n8n_pa1;
ALTER DEFAULT PRIVILEGES IN SCHEMA public
  GRANT USAGE, SELECT ON SEQUENCES TO n8n_pa1;
```

#### Bloqueo cruzado entre proyectos
```sql
-- El usuario de PA1 no puede conectarse a bases de datos de otros proyectos
REVOKE CONNECT ON DATABASE geolabor_db FROM n8n_pa1;
REVOKE CONNECT ON DATABASE suanfarma_db FROM n8n_pa1;
```

#### Prohibiciones absolutas
```sql
ALTER USER n8n_pa1 WITH NOSUPERUSER NOCREATEDB NOCREATEROLE;
REVOKE ALL ON SCHEMA pg_catalog FROM n8n_pa1;
```

#### Verificación antes de producción
```sql
-- usesuper debe devolver 'false' — si devuelve 'true', hay un error crítico
SELECT usename, usesuper FROM pg_user WHERE usename = 'n8n_pa1';
```

**Nota sobre schema public vs schema propio**:  
N8N asume `schema public` durante sus migraciones internas. Usar un schema  
propio por proyecto es arquitectónicamente más limpio, pero rompe N8N.  
El aislamiento real se garantiza con bases de datos separadas + REVOKE CONNECT,  
no cambiando el nombre del schema.

---

### CAPA 8 — Nginx: TLS, reverse proxy y protección del webhook

#### 8.1 TLS explícito — gap identificado en ronda 3
Certbot con defaults puede dejar protocolos obsoletos activos.  
Esta configuración debe estar explícita — no asumirse.

```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
ssl_prefer_server_ciphers on;
ssl_session_cache shared:SSL:10m;
ssl_session_timeout 1d;
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;
```
Deshabilita SSLv3, TLSv1.0, TLSv1.1 y fuerza HSTS para prevenir  
downgrade attacks. Sin esto el sistema no está blindado en transporte.

#### 8.2 Panel N8N restringido por IP
El panel contiene todos los workflows, credenciales y lógica de negocio.  
No debe ser accesible públicamente.

```nginx
location /n8n/ {
    allow TU_IP_PUBLICA;
    deny all;
    proxy_pass http://127.0.0.1:5678;
}
```

#### 8.3 Rate limiting del webhook
```nginx
# En bloque http {}
limit_req_zone $binary_remote_addr zone=webhook_limit:10m rate=2r/s;

# En server block — SIN nodelay para encolar en lugar de servir instantáneo
location /webhook/ {
    limit_req zone=webhook_limit burst=10;
    proxy_pass http://127.0.0.1:5678;
}
```
`nodelay` eliminado: Nginx encola las peticiones burst — para un webhook  
asíncrono es correcto y protege N8N de picos instantáneos.  
`2r/s con burst=10`: para formularios web humanos de bajo volumen,  
más de 10 peticiones por segundo desde la misma IP es un ataque, no un usuario.

#### 8.4 Content-Type enforcement
```nginx
location /webhook/ {
    if ($http_content_type !~ "^application/json") {
        return 415;
    }
    # ... resto de configuración
}
```

#### 8.5 Protección contra ataques de conexión lenta
```nginx
client_body_timeout 10s;
client_header_timeout 10s;
client_max_body_size 1M;    # Evita payload bombs (JSON gigante)
```

#### 8.6 TTL de logs de Nginx — GDPR
`nginx access.log` guarda IPs de origen (PII bajo GDPR) indefinidamente  
por defecto. Configurar logrotate:

`/etc/logrotate.d/nginx`:
```
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
```
14 días de retención. Alineado con principio de minimización GDPR (Art. 5.1.e).

---

### CAPA 9 — N8N: configuración de seguridad y GDPR

#### Variables críticas en .env
```bash
N8N_ENCRYPTION_KEY=clave_aleatoria_minimo_32_chars   # CRÍTICA — ver backup
EXECUTIONS_DATA_MAX_AGE=30                            # TTL logs — base legal GDPR
N8N_BASIC_AUTH_ACTIVE=true
N8N_BASIC_AUTH_USER=usuario_admin
N8N_BASIC_AUTH_PASSWORD=password_muy_fuerte
DB_TYPE=postgresdb
DB_POSTGRESDB_HOST=postgres                           # Nombre del servicio Docker
DB_POSTGRESDB_PORT=5432
DB_POSTGRESDB_DATABASE=pa1_db
DB_POSTGRESDB_USER=n8n_pa1
DB_POSTGRESDB_PASSWORD=password_db_fuerte
```

#### Conexión GDPR de EXECUTIONS_DATA_MAX_AGE
Los logs de ejecución de N8N contienen el body completo del webhook:  
nombre, email, mensaje del lead — datos personales. Sin TTL, N8N los guarda  
indefinidamente violando el principio de minimización GDPR (Art. 5.1.e).  
`EXECUTIONS_DATA_MAX_AGE=30` establece retención de 30 días.

#### webhook_entity — deuda técnica
N8N guarda datos en dos lugares: logs de ejecución (cubierto por el TTL)  
y la tabla `webhook_entity` (no cubierta). En alto volumen, esta tabla  
crece indefinidamente. Evaluar TTL cuando haya datos reales de producción.

---

### CAPA 10 — Vectores de ataque específicos del webhook

#### Replay attacks — deuda técnica documentada
HMAC valida que la petición es auténtica. NO impide que alguien capture  
una petición legítima y la reenvíe miles de veces — la firma sigue siendo válida.

**Solución**: Incluir timestamp en el payload + rechazar peticiones  
con más de 300 segundos de antigüedad.

```javascript
// Payload del formulario incluye:
{
  "timestamp": Date.now(),
  "data": { ... },
  "hmac": hmac(timestamp + JSON.stringify(data))
}
// N8N rechaza si: Date.now() - payload.timestamp > 300000
```
**Estado**: Implementar en Bloque 7 (post-producción).

#### Enumeración del endpoint
Usar rutas no triviales: `/webhook/leads-a9f3k2` en lugar de `/webhook`.  
Un endpoint no predecible no puede ser atacado en masa.

---

### CAPA 11 — Backup y recuperación

#### 11.1 Qué respaldar y por qué

| Componente | Por qué crítico |
|------------|-----------------|
| pg_dump por proyecto | Los datos de clientes |
| Archivos .env completos | Contienen N8N_ENCRYPTION_KEY — sin ella los workflows arrancan pero todas las APIs fallan |
| docker-compose.yml | Reconstrucción de infraestructura |
| /etc/nginx/ | Reconstrucción de routing |

**CRÍTICO**: El backup de PostgreSQL sin la `N8N_ENCRYPTION_KEY` es  
inservible para reconstruir el sistema funcional. Van siempre juntos.

#### 11.2 Script de backup con cifrado GPG

**Gap identificado en ronda 3 — convergencia total de las cuatro fuentes.**  
Los dumps SQL y los .env en Hetzner Storage Box sin cifrar son riesgo existencial:  
si la Storage Box es comprometida, el atacante tiene datos Y la llave de encriptación.

```bash
#!/bin/bash
BACKUP_DIR="/tmp/backups_$(date +%Y%m%d_%H%M%S)"
DATE=$(date +%Y%m%d_%H%M%S)
mkdir -p $BACKUP_DIR

# Backup de bases de datos por proyecto
for PROJECT in pa1 geolabor suanfarma; do
    docker exec ${PROJECT}_postgres pg_dump \
        -U n8n_${PROJECT} -d ${PROJECT}_db \
    | gzip > $BACKUP_DIR/${DATE}_${PROJECT}_dump.sql.gz
done

# Backup de configuración
for PROJECT in pa1 geolabor suanfarma; do
    cp /opt/${PROJECT}/.env $BACKUP_DIR/${DATE}_${PROJECT}.env
    cp /opt/${PROJECT}/docker-compose.yml $BACKUP_DIR/${DATE}_${PROJECT}_compose.yml
done
cp -r /etc/nginx/ $BACKUP_DIR/${DATE}_nginx/

# CIFRADO con GPG antes de subir
# BACKUP_PASSPHRASE vive en tu gestor de contraseñas local — nunca en el servidor
tar -czf $BACKUP_DIR.tar.gz $BACKUP_DIR
gpg -c --batch --passphrase "$BACKUP_PASSPHRASE" $BACKUP_DIR.tar.gz

# Subir cifrado a Hetzner Storage Box via rclone
rclone copy $BACKUP_DIR.tar.gz.gpg hetzner_storage:backups/

# Limpiar temporales
rm -rf $BACKUP_DIR $BACKUP_DIR.tar.gz
```

Cron diario a las 03:00 AM:
```bash
0 3 * * * /opt/scripts/backup.sh
```

#### 11.3 Destino: Hetzner Storage Box
Mismo proveedor, misma jurisdicción EU, bajo el mismo DPA.  
Garantiza que los backups no abandonan el EEE — requisito GDPR.  
Sin esto, backups en AWS S3 us-east requieren mecanismos adicionales  
(Standard Contractual Clauses) para ser legales bajo GDPR.

#### 11.4 Rotación
- Diarios: últimos 7 días
- Semanales: últimas 4 semanas

#### 11.5 Snapshot del VPS post-hardening
Una vez completado todo el hardening, antes de desplegar el primer proyecto:  
snapshot en Hetzner → nombrar `vps-base-hardened-YYYYMMDD`.

Diferencia con el backup de datos: este es el backup de la infraestructura  
configurada. Si el OS se corrompe: restaurar snapshot + aplicar backup de datos  
= sistema operativo en minutos sin reinstalar y reconfigurar desde cero.

#### 11.6 Dry-run mensual obligatorio
Un backup no probado no existe.

1. Aprovisionar VPS temporal (o Docker local)
2. Descargar backup cifrado de Storage Box
3. Descifrar con GPG: `gpg -d backup.tar.gz.gpg > backup.tar.gz`
4. Restaurar PostgreSQL + .env + docker-compose
5. Verificar que N8N arranca y las APIs conectan
6. Documentar resultado en runbook

---

### CAPA 12 — Detección y respuesta ante incidentes

#### 12.1 Alertas de Hetzner Cloud (coste cero)
Configurar desde el panel:
- CPU > 80% durante más de 15 minutos → email/Telegram  
  *(señal típica de minería de criptomonedas tras compromiso)*
- Pico anómalo de tráfico de red saliente  
  *(señal de exfiltración de datos)*

#### 12.2 Señales concretas de compromiso

| Señal | Significado | Cómo detectar |
|-------|-------------|---------------|
| CPU > 80% sostenida sin carga conocida | Minería de criptomonedas | Hetzner alerts |
| Tráfico saliente anómalo | Exfiltración de datos | Hetzner alerts |
| Procesos desconocidos | Malware activo | `ps aux` |
| Conexiones salientes a IPs raras | Reverse shell / C2 | `netstat -tulpn` |
| Contenedores nuevos no creados por ti | Escape + persistencia | `docker ps` |
| Cambios en `~/.ssh/authorized_keys` | Backdoor SSH instalado | auditd |
| Nuevos cron jobs | Persistencia del atacante | `crontab -l` |
| Binarios del sistema modificados | Rootkit | AIDE |
| Escritura en `/var/run/docker.sock` | Intento de escape de contenedor | auditd |

#### 12.3 Protocolo de respuesta — orden obligatorio e inapelable

```
1. AISLAR   → Panel Hetzner: quitar acceso público al firewall (dejar solo tu IP)
              Detiene la exfiltración mientras se investiga.
              NO investigar antes de aislar.

2. SNAPSHOT → Hetzner: snapshot del estado actual del VPS
              Es la evidencia forense. Sin esto no hay prueba documental para GDPR.
              NO investigar antes de hacer el snapshot.

3. INVESTIGAR → /var/log/audit/audit.log
                docker logs [contenedor]
                /var/log/nginx/access.log
                ps aux
                netstat -tulpn
                docker ps
                crontab -l

4. SI HAY BRECHA DE PII:
   Notificación a autoridad de control en 72 horas (GDPR Art. 33)
   El snapshot es la documentación del incidente.
   Notificar a los afectados si la brecha puede causarles daño (GDPR Art. 34)
```

**Por qué este orden**: Si investigas antes de aislar, el atacante puede detectar  
tu actividad y borrar evidencia o acelerar la exfiltración. Si investigas  
antes del snapshot, cada comando que ejecutas modifica el estado forense.

---

## DEUDA TÉCNICA DOCUMENTADA — CONSCIENTE Y ACEPTADA

| Item | Descripción | Resolver en |
|------|-------------|-------------|
| HMAC Replay attacks | Timestamp + ventana 300s en webhook | Bloque 7 |
| webhook_entity bloat | TTL para triggers de webhook en N8N | Bloque 7 con datos reales |
| Rotación de claves API | Protocolo de rotación sin downtime | Antes de Suanfarma pago |
| Suanfarma VPS dedicado | Cuando pase de piloto a cliente de pago | Fase 2 |
| CrowdSec | Evolución de Fail2Ban con inteligencia de amenazas compartida | Bloque 8+ |
| Cifrado de backups en reposo | gpg en script de backup | Bloque 6 — antes de producción |

---

## REUTILIZACIÓN PARA OTROS SISTEMAS

Esta arquitectura es el estándar base para cualquier sistema  
con stack N8N + Docker + PostgreSQL. El 80% se replica sin modificación.

**Lo que NO cambia entre sistemas**:
- Capas 0-5: hardening de cuenta, firewall, OS, Docker/UFW fix, egress filtering
- Capa 6: plantilla docker-compose con silos, user, read_only, límites
- Capa 7: patrón de privilegios PostgreSQL por proyecto
- Capas 11-12: backup con GPG, snapshot, protocolo de incidente

**Lo que SÍ cambia por sistema**:
- Puertos de egress permitidos (si el sistema llama a APIs distintas)
- TTLs de retención según sensibilidad de los datos del cliente
- Nivel de aislamiento: piloto compartido vs cliente de pago en VPS dedicado
- Variables en .env (credenciales, nombres de DB)
- Endpoints de Nginx (rutas de webhook no triviales por proyecto)

**Documentar en el ADR de cada proyecto**:
- Qué APIs externas necesita (define los puertos de egress)
- Qué datos personales maneja (define los TTLs)
- Si es piloto o producción de pago (define el nivel de aislamiento)

---

## PREGUNTAS PARA VALIDACIÓN FINAL

1. ¿Hay algún gap estructural de seguridad que no esté cubierto en este documento?

2. ¿Hay alguna decisión técnica mal planteada o que introduzca un riesgo  
   no identificado? ¿Alguna contradicción entre capas?

3. Con este diseño, en el escenario de riesgo existencial definido  
   (brecha PII, notificación GDPR 72h, responsabilidad legal):  
   ¿es este sistema razonablemente robusto y auditable ante una autoridad  
   de control GDPR para proyectos piloto con datos reales?

4. ¿Hay algo que esté sobreingenieriado para la fase actual y que  
   introduzca complejidad innecesaria sin reducir riesgo real?

---

**Generado por**: Pablo Cuevas — triangulación Claude + Gemini + DeepSeek + ChatGPT  
**Rondas**: 4 rondas de triangulación + síntesis final  
**Fecha**: 2 de mayo 2026  
**Versión**: 2.0 — para validación final de cierre
