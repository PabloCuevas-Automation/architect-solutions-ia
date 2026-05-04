# TRIANGULACIÓN DE SEGURIDAD VPS — DOCUMENTO DE CIERRE
## Sistema: pablocuevas.it + pilotos (GeoLabor, Suanfarma)
## Stack: Hetzner CX22 + Ubuntu + Docker Compose + N8N + PostgreSQL + Nginx

**Propósito**: Este documento consolida cuatro rondas de triangulación entre  
Claude, Gemini, DeepSeek y ChatGPT sobre la arquitectura de seguridad completa  
del VPS. Se entrega a las tres IAs para validación final de convergencia.

**Pregunta de cierre**: ¿Hay algún gap estructural de seguridad que no esté  
cubierto en este documento? ¿Hay algo mal planteado o incompleto?

---

## CONTEXTO DEL SISTEMA

**Perfil del operador**: AI Solutions Architect en transición. Proyectos piloto  
gratuitos en producción real con datos personales de clientes bajo GDPR.

**Infraestructura decidida**: Hetzner CX22 (2 vCPU / 4GB RAM / 40GB SSD /  
Ubuntu 24.04). Empresa alemana. Datacenter Alemania o Finlandia. DPA firmable.  
Convergencia total de las cuatro fuentes sobre esta elección.

**Stack técnico**:
- N8N self-hosted (automatización de workflows)
- PostgreSQL (persistencia de datos)
- Nginx (reverse proxy)
- Docker Compose (orquestación de contenedores)
- Certbot (HTTPS)

**Proyectos en el mismo VPS** (pilotos gratuitos simultáneos):
- PA1: sistema de gestión de leads (pablocuevas.it)
- GeoLabor: automatización de laboratorio geotécnico
- Suanfarma: automatización de workflows (empresa farmacéutica)

**Clasificación de riesgo**:
- Riesgo operativo (workflow falla, API no responde): recuperable
- Riesgo existencial (brecha de datos PII): irrecuperable — notificación GDPR  
  72h, responsabilidad legal, pérdida de confianza del cliente. Todo el diseño  
  de seguridad está orientado a mitigar este segundo tipo.

---

## DECISIONES DE ARQUITECTURA TOMADAS

### D1 — VPS: Hetzner CX22
**Decisión**: Hetzner CX22, datacenter Alemania o Finlandia.  
**Razón**: Empresa alemana, GDPR sin ambigüedad, DPA firmable desde el panel,  
red estable para webhooks en tiempo real, mejor precio/rendimiento del mercado  
europeo, comunidad DevOps amplia con tutoriales para este stack exacto.  
**Descartados**: Contabo (overselling, latencia de red inconsistente),  
DigitalOcean (entidad legal USA, sujeta a CLOUD Act aunque datacenter sea UE).  
**Convergencia**: Claude + Gemini + DeepSeek + ChatGPT — unánime.

### D2 — DPA con Hetzner: necesario pero no suficiente
**Decisión**: Firmar DPA desde el panel de Hetzner inmediatamente tras contratar.  
**Importante**: El DPA cubre a Hetzner como procesador. No cubre la aplicación.  
El operador es el Data Controller — la responsabilidad legal de los datos es suya.  
El DPA sin medidas técnicas propias no constituye cumplimiento GDPR.  
**Acción**: Firmar + archivar copia como evidencia auditable.  
**Convergencia**: Claude + Gemini + DeepSeek + ChatGPT — unánime.

### D3 — Arquitectura multi-proyecto: silos por proyecto
**Decisión**: Cada proyecto tiene su propio docker-compose.yml, su propia red  
Docker interna, su propia base de datos PostgreSQL, sus propias credenciales.  
Nginx actúa como reverse proxy central con red compartida solo con los  
contenedores N8N de cada proyecto (no con las bases de datos).  
**Razón**: Blast radius mínimo. Si un atacante compromete el N8N de GeoLabor,  
no puede comunicarse a nivel de red con la base de datos de PA1 o Suanfarma.  
**Límite conocido**: El aislamiento Docker NO es equivalente a aislamiento de VM.  
Un escape de contenedor (baja probabilidad, existe) llega al host. Las medidas  
de mitigación están en las capas de OS y Docker.  
**Convergencia**: Claude + Gemini + DeepSeek + ChatGPT — unánime.

### D4 — Límite de multi-tenant: Suanfarma tiene fecha de caducidad
**Decisión**: VPS compartido es válido para pilotos gratuitos. Cuando Suanfarma  
pase a cliente de pago — especialmente siendo empresa farmacéutica — requiere  
VPS dedicado. El compliance farmacéutico no tolera datos en infraestructura  
compartida con otros proyectos.  
**Estado**: Deuda técnica consciente documentada. No es problema de hoy.  
**Convergencia**: Claude + DeepSeek — mencionado. Gemini + ChatGPT — implícito.

---

## MAPA COMPLETO DE SEGURIDAD POR CAPAS

### CAPA 1 — Cuenta Hetzner (antes de tocar el servidor)

| Medida | Descripción | Estado |
|--------|-------------|--------|
| 2FA en cuenta Hetzner | Si comprometen la cuenta, tienen acceso a la consola web sin SSH ni firewall. 2FA es la medida de mayor ROI de toda la lista | Pendiente |
| Datos reales en el registro | Hetzner tiene sistema antifraude agresivo — puede pedir foto de ID. Registrar sin VPN, con datos 100% reales | Pendiente |
| Firmar DPA | Panel de Hetzner → sección legal → DPA. Firmar y archivar copia | Pendiente |

---

### CAPA 2 — Firewall externo de Hetzner (primera línea de defensa)

El firewall externo de Hetzner filtra tráfico ANTES de que llegue al VPS.  
Es la primera línea. Si está bien configurado, un atacante ni toca el servidor.

| Regla | Puerto | Origen permitido |
|-------|--------|-----------------|
| SSH | 22/TCP | Solo tu IP pública fija de administración |
| HTTP | 80/TCP | 0.0.0.0/0 (cualquier IP) |
| HTTPS | 443/TCP | 0.0.0.0/0 (cualquier IP) |
| Todo lo demás | — | DENEGAR por defecto |

**Importante**: Restringir el puerto 22 a tu IP fija en esta capa es más seguro  
que hacerlo en UFW — el tráfico no llega ni al servidor.

---

### CAPA 3 — Sistema operativo (hardening inicial, antes de instalar Docker)

Secuencia de ejecución obligatoria nada más recibir la IP del servidor:

**3.1 Actualización del sistema**
```bash
sudo apt update && sudo apt upgrade -y
```

**3.2 Usuario no-root**
```bash
adduser pablo
usermod -aG sudo pablo
```
Nunca operar como root en producción.

**3.3 SSH key-only**
```bash
# Desde tu máquina local:
ssh-copy-id -i ~/.ssh/id_ed25519.pub pablo@IP_DEL_SERVIDOR
```
En `/etc/ssh/sshd_config`:
```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
```
```bash
sudo sshd -t && sudo systemctl restart sshd
# Verificar que puedes conectarte desde una nueva terminal ANTES de cerrar la sesión actual
```

**3.4 UFW — firewall de sistema operativo (segunda línea de defensa)**
```bash
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp
ufw allow 80/tcp
ufw allow 443/tcp
ufw enable
```

**3.5 Fail2Ban — protección contra fuerza bruta**
```bash
apt install fail2ban -y
```
Bloquea IPs con múltiples intentos fallidos en SSH.

**3.6 Parches automáticos de seguridad**
```bash
apt install unattended-upgrades -y
dpkg-reconfigure --priority=low unattended-upgrades
```
Parcheado automático del OS. Las imágenes Docker requieren estrategia separada.

**3.7 Actualizaciones de imágenes Docker (separado del OS)**
Las imágenes Docker (N8N, PostgreSQL, Nginx) tienen sus propias vulnerabilidades.  
Una imagen desactualizada sigue siendo vulnerable aunque Ubuntu esté parcheado.  
Estrategia: revisión mensual de imágenes + actualización programada.

---

### CAPA 4 — Fix crítico Docker/UFW (el engaño que nadie menciona)

**Problema**: Docker manipula iptables directamente, ignorando UFW. Si mapeas  
PostgreSQL como `5432:5432` en el docker-compose, el puerto está abierto a  
internet aunque UFW diga que está bloqueado.

**Solución**: Todo puerto del docker-compose debe bindearse a localhost:
```yaml
ports:
  - "127.0.0.1:5432:5432"  # ✅ Solo accesible desde el propio servidor
  # NO: "5432:5432"         # ❌ Expuesto a internet entero ignorando UFW
```
Esto aplica a PostgreSQL, pgAdmin, y cualquier servicio que no deba ser público.  
N8N sí debe ser accesible — pero a través de Nginx, no directamente.

**Convergencia**: Claude + Gemini + DeepSeek + ChatGPT — identificado como  
el gap más importante de toda la triangulación.

---

### CAPA 5 — Arquitectura Docker multi-proyecto

**Estructura de directorios**:
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

**Red central de Nginx**:
```bash
docker network create proxy_net
```

**docker-compose.yml por proyecto** (ejemplo PA1):
```yaml
networks:
  pa1_internal:       # Red interna del proyecto — aislada
    driver: bridge
  proxy_net:          # Red compartida solo con Nginx
    external: true

services:
  n8n:
    networks:
      - pa1_internal  # Se comunica con su propia DB
      - proxy_net     # Solo N8N tiene salida hacia Nginx
    security_opt:
      - no-new-privileges:true   # Impide escalada de privilegios

  postgres:
    networks:
      - pa1_internal  # Solo accesible desde N8N del mismo proyecto
    ports:
      - "127.0.0.1:5433:5432"   # Bind a localhost, puerto único por proyecto
    security_opt:
      - no-new-privileges:true
```

**Resultado del aislamiento**:
- N8N de GeoLabor no puede comunicarse con PostgreSQL de PA1 — redes distintas
- Un atacante que entra por N8N de GeoLabor no puede alcanzar datos de PA1
- El único punto de entrada externo es Nginx, que enruta a cada N8N por proxy_net

**Permisos de archivos .env**:
```bash
chmod 600 /opt/pa1/.env
chmod 600 /opt/geolabor/.env
chmod 600 /opt/suanfarma/.env
```
Solo el usuario propietario puede leer las credenciales.

---

### CAPA 6 — PostgreSQL: privilegios mínimos por proyecto

**Regla**: Un usuario único + una base de datos única por proyecto.  
Nunca usar el usuario `postgres` por defecto para las aplicaciones.

**Creación por proyecto**:
```sql
-- Crear base de datos y usuario
CREATE DATABASE pa1_db;
CREATE USER n8n_pa1 WITH ENCRYPTED PASSWORD 'password_muy_fuerte';

-- Revocar acceso público por defecto
REVOKE ALL ON DATABASE pa1_db FROM PUBLIC;

-- Dar acceso solo a su propia base de datos
GRANT CONNECT ON DATABASE pa1_db TO n8n_pa1;
GRANT USAGE ON SCHEMA public TO n8n_pa1;

-- Privilegios operativos (N8N necesita DDL para sus migraciones internas)
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO n8n_pa1;
GRANT CREATE, ALTER ON SCHEMA public TO n8n_pa1;
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA public TO n8n_pa1;

-- Privilegios para tablas futuras (migraciones de N8N)
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO n8n_pa1;
ALTER DEFAULT PRIVILEGES IN SCHEMA public
GRANT USAGE, SELECT ON SEQUENCES TO n8n_pa1;
```

**Bloqueo cruzado entre proyectos**:
```sql
-- El usuario de PA1 no puede conectarse a bases de datos de otros proyectos
REVOKE CONNECT ON DATABASE geolabor_db FROM n8n_pa1;
REVOKE CONNECT ON DATABASE suanfarma_db FROM n8n_pa1;
```

**Prohibiciones absolutas para usuarios de N8N**:
```sql
ALTER USER n8n_pa1 WITH NOSUPERUSER NOCREATEDB NOCREATEROLE;
REVOKE ALL ON SCHEMA pg_catalog FROM n8n_pa1;
```

**Verificación antes de producción**:
```sql
-- usesuper debe devolver 'false'
SELECT usename, usesuper FROM pg_user WHERE usename = 'n8n_pa1';
```

---

### CAPA 7 — Nginx: reverse proxy y protección del webhook

**7.1 Panel N8N restringido por IP**

El panel de administración de N8N contiene todos los workflows, credenciales  
y lógica de negocio. No debe ser accesible públicamente.

```nginx
location /n8n/ {
    allow TU_IP_PUBLICA;
    deny all;
    proxy_pass http://127.0.0.1:5678;
}
```

**7.2 Rate limiting en el webhook (única puerta pública)**

El webhook debe ser público por diseño. HMAC valida autenticidad pero no  
limita volumen. Rate limiting es lo que impide saturación.

```nginx
# En bloque http {}
limit_req_zone $binary_remote_addr zone=webhook_limit:10m rate=15r/m;

# En server block
location /webhook/ {
    limit_req zone=webhook_limit burst=10 nodelay;
    proxy_pass http://127.0.0.1:5678;
}
```
`burst=10` permite picos legítimos. `nodelay` los sirve sin latencia artificial.  
Umbral: 15 req/min por IP es generoso para formularios web humanos.

**7.3 Content-Type enforcement**
```nginx
location /webhook/ {
    if ($http_content_type !~ "^application/json") {
        return 415;
    }
    # ... resto de la configuración
}
```
Rechaza payloads con Content-Type incorrecto antes de que lleguen a N8N.

**7.4 Protección contra ataques de conexión lenta**
```nginx
client_body_timeout 10s;
client_header_timeout 10s;
client_max_body_size 1M;
```
`client_max_body_size 1M` evita payload bombs (JSON gigante para saturar memoria).  
Los timeouts cortan conexiones Slowloris antes de agotar workers.

---

### CAPA 8 — Webhook: vectores de ataque específicos

**8.1 Replay attacks (el gap más importante del webhook)**

HMAC valida que la petición es auténtica. NO impide que alguien capture una  
petición legítima y la reenvíe miles de veces — la firma sigue siendo válida.

**Solución**: Incluir timestamp en el payload + rechazar peticiones con más  
de 300 segundos de antigüedad.

```javascript
// En el payload que envía el formulario:
{
  "timestamp": Date.now(),
  "data": { ... },
  "hmac": hmac(timestamp + data)
}

// En N8N: rechazar si Date.now() - payload.timestamp > 300000
```
**Estado**: Documentado como deuda técnica — implementación en Bloque 7.

**8.2 Enumeración del endpoint**

Usar rutas no triviales: `/webhook/lead-a9f3k2` en lugar de `/webhook` genérico.  
Un endpoint no predecible no puede ser atacado en masa.

**8.3 webhook_entity bloat (separado de EXECUTIONS_DATA_MAX_AGE)**

N8N guarda datos en dos lugares: logs de ejecución (cubierto por  
`EXECUTIONS_DATA_MAX_AGE=30`) y la tabla `webhook_entity` (no cubierta).  
En alto volumen, `webhook_entity` crece indefinidamente y degrada PostgreSQL.  
**Estado**: Deuda técnica para Bloque 7 cuando haya datos reales de volumen.

---

### CAPA 9 — N8N: configuración de seguridad

**Variables de entorno críticas en .env**:
```bash
N8N_ENCRYPTION_KEY=clave_aleatoria_muy_larga   # CRÍTICA — ver backup
EXECUTIONS_DATA_MAX_AGE=30                      # TTL de logs — base legal GDPR
N8N_BASIC_AUTH_ACTIVE=true                      # Autenticación básica activada
N8N_BASIC_AUTH_USER=usuario
N8N_BASIC_AUTH_PASSWORD=password_fuerte
```

**Conexión GDPR entre EXECUTIONS_DATA_MAX_AGE y base legal**:  
Los logs de ejecución de N8N contienen el body completo del webhook — nombre,  
email, mensaje del lead. Son datos personales. Sin TTL definido, N8N los guarda  
indefinidamente violando el principio de minimización GDPR (Art. 5.1.e).  
`EXECUTIONS_DATA_MAX_AGE=30` cumple con retención mínima necesaria.

---

### CAPA 10 — Backup y recuperación

**10.1 Qué respaldar**

| Componente | Qué | Por qué crítico |
|------------|-----|-----------------|
| PostgreSQL | pg_dump por proyecto | Los datos de leads/clientes |
| N8N_ENCRYPTION_KEY | Archivo .env completo | Sin esta clave, los workflows arrancan pero todas las APIs fallan |
| docker-compose.yml | Por proyecto | Reconstrucción de infraestructura |
| Configuración Nginx | /etc/nginx/ | Reconstrucción de routing |

**CRÍTICO**: El backup de PostgreSQL sin la `N8N_ENCRYPTION_KEY` es  
inservible para reconstruir el sistema funcional. Van juntos siempre.

**10.2 Script de backup diario**
```bash
#!/bin/bash
BACKUP_DIR="/backups/postgres"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p $BACKUP_DIR

# Backup por proyecto
for PROJECT in pa1 geolabor suanfarma; do
    docker exec ${PROJECT}_postgres pg_dump -U n8n_${PROJECT} -d ${PROJECT}_db \
    | gzip > $BACKUP_DIR/${DATE}_${PROJECT}_dump.sql.gz
done

# Backup de .env (incluye N8N_ENCRYPTION_KEY)
cp /opt/pa1/.env $BACKUP_DIR/${DATE}_pa1.env
cp /opt/geolabor/.env $BACKUP_DIR/${DATE}_geolabor.env
cp /opt/suanfarma/.env $BACKUP_DIR/${DATE}_suanfarma.env
```

**10.3 Destino externo — Hetzner Storage Box**  
Los backups en el mismo VPS son riesgo existencial: si el servidor desaparece,  
el backup desaparece con él. Hetzner Storage Box: datacenter EU, precio mínimo,  
cumplimiento GDPR garantizado (mismo proveedor, misma jurisdicción).  
Sincronización via rclone o rsync desde el propio VPS.

**10.4 Rotación**
- Diarios: últimos 7 días
- Semanales: últimas 4 semanas

**10.5 Dry-run mensual obligatorio**  
Un backup no probado no existe. Procedimiento mensual:
1. Aprovisionar VPS temporal (o usar Docker local)
2. Bajar backup de Storage Box
3. Restaurar PostgreSQL + .env + docker-compose
4. Verificar que N8N arranca y las APIs conectan
5. Documentar resultado en runbook

**10.6 Snapshot post-hardening**  
Justo después de completar todo el hardening, antes de desplegar el primer  
proyecto: hacer snapshot del VPS en Hetzner.  
Nombre: `vps-base-hardened-YYYYMMDD`.  
Esto es el backup de la infraestructura configurada, separado del backup de datos.  
Si el OS se corrompe: restaurar snapshot + aplicar backup de datos = sistema  
operativo en minutos sin reinstalar y reconfigurar todo desde cero.

---

### CAPA 11 — Detección y respuesta ante incidentes

**11.1 Alertas de Hetzner Cloud (nivel 0 — cero coste adicional)**  
Configurar desde el panel de Hetzner:
- CPU > 80% durante más de 15 minutos → alerta por email/Telegram
- Tráfico de red saliente con pico anómalo → alerta

CPU al 100% sostenida sin carga conocida = señal típica de minería de  
criptomonedas tras compromiso. Tráfico saliente elevado = exfiltración de datos.

**11.2 Logwatch — reporte diario**
```bash
apt install logwatch -y
```
Resumen diario por email de actividad de SSH, Nginx y sistema.  
Detecta intentos anómalos sin infraestructura adicional.

**11.3 AIDE — integridad de binarios del sistema**
```bash
apt install aide -y
aide --init                    # Snapshot inicial del sistema
mv /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```
Cron semanal que compara el estado actual contra el snapshot.  
Si un atacante modifica un binario del sistema (para ocultar procesos),  
AIDE lo detecta. Es la diferencia entre enterarse de un compromiso y no  
enterarse nunca.

**11.4 auditd — detección de comportamiento en Docker**

Reglas en `/etc/audit/rules.d/docker.rules`:
```bash
-w /usr/bin/docker -p rwxa -k docker_binary
-w /var/run/docker.sock -p rwxa -k docker_socket
-w /var/lib/docker -p wa -k docker_data
-a always,exit -F arch=b64 -S setuid -F auid>=1000 -F auid!=4294967295 -k priv_esc
```
Detecta modificación de binarios Docker, acceso al socket (vector de escape  
de contenedor), y cualquier intento de escalada de privilegios.

**11.5 Señales concretas de compromiso**

| Señal | Qué significa | Herramienta |
|-------|--------------|-------------|
| CPU > 80% sostenida sin carga | Minería de criptomonedas | Hetzner alerts |
| Tráfico saliente anómalo | Exfiltración de datos | Hetzner alerts |
| Procesos desconocidos | `ps aux` | Manual |
| Conexiones salientes a IPs raras | Reverse shell / C2 | `netstat -tulpn` |
| Contenedores nuevos no creados por ti | Escape + persistencia | `docker ps` |
| Cambios en `~/.ssh/authorized_keys` | Backdoor SSH | auditd |
| Nuevos cron jobs | Persistencia de atacante | `crontab -l` |
| Binarios modificados | Rootkit | AIDE |

**11.6 Protocolo de respuesta ante incidente (orden obligatorio)**

```
1. AISLAR  → Panel Hetzner: quitar acceso público al firewall (dejar solo IP propia)
             Detiene exfiltración mientras se investiga.

2. SNAPSHOT → Hetzner: snapshot del estado actual del VPS
             Es la evidencia forense. Sin esto no hay prueba documental para GDPR.
             Nunca investigar antes de hacer el snapshot.

3. INVESTIGAR → /var/log/audit/audit.log
                docker logs [contenedor]
                /var/log/nginx/access.log
                ps aux
                netstat -tulpn

4. SI HAY BRECHA DE PII → Notificación a autoridad de control en 72h (GDPR Art. 33)
                           El snapshot es la documentación del incidente.
```

**Evolución futura (no urgente para fase actual)**:  
CrowdSec — alternativa moderna a Fail2Ban que entiende logs de Nginx, Docker y  
N8N, con inteligencia de amenazas compartida entre instalaciones.

---

### CAPA 12 — Deuda técnica documentada (consciente)

| Item | Descripción | Resolver en |
|------|-------------|-------------|
| HMAC Replay attacks | Timestamp + ventana de 300s en webhook | Bloque 7 |
| webhook_entity bloat | TTL para tabla de triggers de webhook en N8N | Bloque 7 |
| Rotación de claves API | Protocolo de rotación sin downtime | Antes de Suanfarma pago |
| Suanfarma VPS dedicado | Cuando pase de piloto a cliente de pago | Fase 2 |
| CrowdSec | Evolución de Fail2Ban con inteligencia de amenazas | Bloque 8+ |

---

## CHECKLIST DE CONVERGENCIA — PREGUNTA PARA LAS IAs

**Este documento representa el diseño completo de seguridad del sistema.  
Por favor responde las siguientes preguntas:**

1. ¿Hay algún gap estructural de seguridad que no esté cubierto en ninguna  
   de las 12 capas documentadas?

2. ¿Hay alguna decisión técnica que esté mal planteada o que introduzca  
   un riesgo que no se ha identificado?

3. ¿Hay alguna contradicción entre capas que debería resolverse?

4. Con este diseño, en el escenario de riesgo existencial definido (brecha  
   de datos PII, notificación GDPR 72h, responsabilidad legal):  
   ¿es este sistema razonablemente robusto para un arquitecto en fase inicial  
   con proyectos piloto, o falta alguna capa crítica?

**No busco perfección enterprise. Busco el mínimo viable que cubra el riesgo  
existencial de manera documentable y auditable ante una autoridad de control GDPR.**

---

**Generado por**: Pablo Cuevas — triangulación Claude + Gemini + DeepSeek + ChatGPT  
**Fecha**: 2 de mayo 2026  
**Versión**: 1.0 — para validación final de cierre
