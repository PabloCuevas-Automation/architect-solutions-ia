# ADR-008: Arquitectura de Seguridad de Infraestructura VPS

**Fecha**: 2 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — arquitectura de seguridad universal  
**Serie**: 000 (principio universal — aplica a todos los proyectos en el VPS)  
**Audiencia**: 🔒 Personal  
**Fuentes**: Triangulación Claude + Gemini + DeepSeek + ChatGPT — 4 rondas + simulación de ataque

---

## Contexto

El sistema PA1 requiere un VPS para producción. Simultáneamente se desplegarán
pilotos gratuitos de GeoLabor y potencialmente Suanfarma en el mismo servidor.
Los tres proyectos manejan PII de terceros bajo GDPR.

La triangulación de seguridad identificó que el riesgo se divide en dos
categorías con consecuencias asimétricas:

- **Riesgo operativo**: workflow falla, API no responde, lead se pierde.
  Recuperable. Tiene segunda oportunidad.
- **Riesgo existencial**: brecha de datos PII. Irrecuperable. Notificación GDPR
  obligatoria en 72h (Art. 33), responsabilidad legal como Data Controller,
  pérdida de confianza irreversible del cliente.

Este ADR documenta las decisiones de seguridad de infraestructura orientadas
a mitigar el riesgo existencial. No busca perfección enterprise — busca el
mínimo viable auditable ante una autoridad de control GDPR.

---

## Decisiones por capa

### CAPA 0 — Cuenta Hetzner

**D0.1**: 2FA obligatorio en la cuenta Hetzner.  
*Razón*: sin 2FA, acceso a la consola web bypasea SSH y todos los firewalls.
Es la medida de mayor ROI de toda la arquitectura.

**D0.2**: DPA firmado antes de desplegar ningún proyecto.  
*Razón*: el DPA cubre a Hetzner como procesador bajo Art. 28 GDPR. Sin él,
el procesamiento de PII en la infraestructura carece de base legal.
El DPA no protege la aplicación — cubre la capa de infraestructura.

**D0.3**: Hetzner CX22, datacenter Alemania o Finlandia.  
*Razón*: empresa alemana, jurisdicción EU, sin ambigüedad legal.
DigitalOcean descartado por CLOUD Act (entidad legal USA).
Contabo descartado por overselling y latencia de red inconsistente.

---

### CAPA 1 — Firewall externo de Hetzner

**D1.1**: Política por defecto: denegar todo el tráfico entrante.

**D1.2**: Puerto 22 (SSH) restringido a IP fija de administración.  
*Razón*: restricción en esta capa significa que el tráfico no llega ni
al servidor. Superior a hacerlo solo en UFW.

**D1.3**: Puertos 80 y 443 abiertos a cualquier IP.

**D1.4**: Todo lo demás denegado sin excepción.

---

### CAPA 2 — Sistema operativo

**D2.1**: Usuario no-root `pablo` como único usuario operativo.

**D2.2**: SSH key-only. `PasswordAuthentication no`, `PermitRootLogin no`.  
*Razón*: las contraseñas pueden adivinarse. Las claves SSH no.

**D2.3**: UFW como segunda línea de defensa con las mismas reglas que Capa 1.  
Puerto 22 restringido a IP fija (no abierto a todos como riesgo si
el firewall de Hetzner falla). Redundancia defensiva — las capas no
se contradicen.

**D2.4**: Fail2Ban para bloquear fuerza bruta.

**D2.5**: `unattended-upgrades` para parches automáticos del OS.  
*Importante*: los parches del OS no actualizan imágenes Docker.
Estrategia de actualización de imágenes es separada y manual (mensual).

**D2.6**: AIDE para detección de integridad de binarios.  
*Razón*: si un atacante modifica un binario del sistema para ocultar
su presencia, AIDE lo detecta en el siguiente ciclo semanal.

**D2.7**: auditd con reglas específicas para Docker.  
Detecta escritura en `/var/run/docker.sock` (vector de escape),
modificación de binarios Docker, y escalada de privilegios (`setuid`).

**D2.8**: logwatch para reporte diario de actividad por email.

---

### CAPA 3 — Fix crítico Docker/UFW

**D3.1**: Docker manipula iptables directamente, ignorando UFW.
Si se mapea `5432:5432`, PostgreSQL está expuesto aunque UFW diga lo
contrario. Este es el gap más importante identificado en la triangulación.

**D3.2**: Para servicios que se comunican solo entre contenedores
(PostgreSQL con N8N en la misma red Docker), eliminar completamente
el bloque `ports` del docker-compose.yml.  
*Razón*: sin bloque `ports`, Docker no toca iptables para ese contenedor.
Queda 100% inaccesible desde el exterior y desde el host.

**D3.3**: Solo N8N tiene bloque `ports` con bind a localhost:
`"127.0.0.1:5678:5678"` — accesible solo desde Nginx en el mismo host.

**D3.4**: Límite de logs de Docker en `/etc/docker/daemon.json`:
```json
{
  "log-driver": "json-file",
  "log-opts": { "max-size": "10m", "max-file": "3" }
}
```
*Razón*: sin límite, los logs pueden llenar los 40GB del disco causando
fallo catastrófico del sistema (DoS accidental).

---

### CAPA 4 — Egress filtering (salida de contenedores)

**D4.1**: El blindaje de entrada no impide exfiltración de salida.
Un contenedor comprometido puede enviar datos a cualquier servidor externo
sin restricción si no hay egress filtering.

**D4.2**: `ufw default deny outgoing` NO es suficiente para Docker.
El tráfico de contenedores sale por la cadena FORWARD de iptables,
que Docker manipula ignorando UFW.

**D4.3**: Solución: cadena `DOCKER-USER` de iptables:
```bash
iptables -I DOCKER-USER -j DROP
iptables -I DOCKER-USER -o eth0 -p tcp --dport 443 -j ACCEPT
iptables -I DOCKER-USER -o eth0 -p tcp --dport 80 -j ACCEPT
iptables -I DOCKER-USER -o eth0 -p udp --dport 53 -j ACCEPT
iptables -I DOCKER-USER -o eth0 -p tcp --dport 53 -j ACCEPT
```

**D4.4**: Reglas persistentes con `iptables-persistent`.
Docker puede recrear cadenas en reinicios — verificar persistencia
post-reboot es obligatorio.

**D4.5**: Impacto en el riesgo existencial:
```
Sin egress:  atacante roba datos silenciosamente → breach GDPR total
Con egress:  atacante atrapado dentro → incidente contenible
```

---

### CAPA 5 — Arquitectura de silos multi-proyecto

**D5.1**: Un `docker-compose.yml` por proyecto en `/opt/{proyecto}/`.

**D5.2**: Una red Docker interna por proyecto (`pa1_internal`,
`geolabor_internal`, etc.). Contenedores de proyectos distintos
no pueden comunicarse entre sí por red.

**D5.3**: Red `proxy_net` compartida solo entre Nginx y los contenedores
N8N de cada proyecto. PostgreSQL nunca tiene acceso a `proxy_net`.

**D5.4**: Patrón de seguridad en docker-compose.yml:
```yaml
security_opt:
  - no-new-privileges:true   # Impide escalada de privilegios
user: "1000:1000"            # Proceso no-root dentro del contenedor
read_only: true              # Filesystem en solo lectura
volumes:
  - n8n_data:/home/node/.n8n # Excepción obligatoria para N8N
tmpfs:
  - /tmp
deploy:
  resources:
    limits:
      cpus: '0.5'
      memory: 512M           # Evita DoS interno
```

**D5.5**: `.env` con `chmod 600` en cada proyecto.
Solo el usuario propietario puede leer las credenciales.

**D5.6**: Límite de multi-tenant conocido: el aislamiento Docker no es
equivalente a aislamiento de VM. Un escape de contenedor (baja probabilidad)
llega al host. Esta limitación está documentada y aceptada para la fase
de pilotos gratuitos.

**D5.7**: Suanfarma requiere VPS dedicado cuando pase a cliente de pago.
Compliance farmacéutico no tolera infraestructura compartida.

---

### CAPA 6 — PostgreSQL: privilegios mínimos por proyecto

**D6.1**: Un usuario único + una base de datos única por proyecto.
Nunca usar el usuario `postgres` por defecto.

**D6.2**: N8N requiere permisos DDL (CREATE, ALTER) sobre su base de datos
porque TypeORM ejecuta migraciones en cada arranque.
No reducir estos permisos — rompe el arranque.

**D6.3**: REVOKE CONNECT entre bases de datos de proyectos distintos.
Bloqueo explícito de acceso cruzado aunque el usuario tenga credenciales.

**D6.4**: NOSUPERUSER + NOCREATEDB + NOCREATEROLE para todos los usuarios
de aplicación. Verificación: `usesuper` debe devolver `false`.

---

### CAPA 7 — Nginx: TLS, reverse proxy y protección del webhook

**D7.1**: TLS explícito — Certbot con defaults puede dejar protocolos
obsoletos. Configuración obligatoria:
```nginx
ssl_protocols TLSv1.2 TLSv1.3;
ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
add_header Strict-Transport-Security "max-age=63072000; includeSubDomains" always;
```

**D7.2**: Panel N8N restringido a IP de administración:
```nginx
location /n8n/ {
    allow TU_IP_PUBLICA;
    deny all;
}
```

**D7.3**: Rate limiting del webhook sin `nodelay`:
```nginx
limit_req_zone $binary_remote_addr zone=webhook_limit:10m rate=2r/s;
location /webhook/ {
    limit_req zone=webhook_limit burst=10;
}
```
`nodelay` eliminado — encolar es correcto para webhooks asíncronos
y protege N8N de picos instantáneos.

**D7.4**: Content-Type enforcement — rechazar requests no-JSON antes
de que lleguen a N8N.

**D7.5**: Protección contra Slowloris y payload bombs:
```nginx
client_body_timeout 10s;
client_header_timeout 10s;
client_max_body_size 1M;
```

**D7.6**: logrotate para logs de Nginx — 14 días de retención.
IPs en access.log son PII bajo GDPR. Sin TTL = violación Art. 5.1.e.

---

### CAPA 8 — Backup y recuperación

**D8.1**: Tres componentes obligatorios en cada backup:
pg_dump de cada proyecto + archivos .env completos (contienen
N8N_ENCRYPTION_KEY) + configuración de Nginx.

**D8.2**: N8N_ENCRYPTION_KEY es el componente más crítico del backup.
Sin ella, PostgreSQL se restaura pero todos los workflows fallan
al no poder descifrar las credenciales de APIs.

**D8.3**: Backups cifrados con GPG antes de salir del servidor.
La passphrase vive en el gestor de contraseñas local — nunca en el servidor.
En el servidor solo existe `/root/.backup_pass` con `chmod 400`
para permitir la automatización por cron.
*Razón*: si la Storage Box es comprometida, los archivos son ilegibles.

**D8.4**: Destino: Hetzner Storage Box. Misma jurisdicción EU, mismo DPA.
Garantiza que los backups no abandonan el EEE sin salvaguardas adicionales.

**D8.5**: Rotación: 7 días diarios + 4 semanas semanales.

**D8.6**: Snapshot post-hardening del VPS antes de desplegar proyectos.
Backup de infraestructura configurada — separado del backup de datos.

**D8.7**: Dry-run mensual de restauración completa. Un backup no probado
no existe. Documentar resultado en runbook.

---

### CAPA 9 — Detección y respuesta ante incidentes

**D9.1**: Alertas de Hetzner Cloud: CPU > 80% por 15 min + tráfico
saliente anómalo. Coste cero, detección de los dos indicadores más
comunes de compromiso (minería + exfiltración).

**D9.2**: Señales concretas de compromiso a revisar periódicamente:
`ps aux` (procesos desconocidos), `netstat -tulpn` (conexiones raras),
`docker ps` (contenedores no autorizados), `crontab -l` (persistencia).

**D9.3**: Protocolo de respuesta — orden inapelable:
```
1. AISLAR   → Panel Hetzner: quitar acceso público al firewall
2. SNAPSHOT → Estado actual del VPS antes de investigar
3. INVESTIGAR → logs, procesos, contenedores
4. NOTIFICAR → Si hay brecha PII: autoridad de control en 72h (Art. 33)
```
*Por qué este orden*: investigar antes de aislar permite al atacante
borrar evidencia. Investigar antes del snapshot destruye la evidencia forense.

---

## Reutilización para otros proyectos

Esta arquitectura es el estándar base para cualquier sistema con stack
N8N + Docker + PostgreSQL. El 80% se replica sin modificación.

**Lo que NO cambia entre proyectos**:
- Capas 0-4: hardening universal de cuenta, OS, Docker, egress
- Capa 5: plantilla docker-compose con silos y seguridad
- Capa 8: patrón de backup con GPG y Storage Box
- Capa 9: protocolo de incidente

**Lo que SÍ cambia por proyecto**:
- Puertos de egress según las APIs que usa ese proyecto
- TTLs según la sensibilidad de los datos
- Nivel de aislamiento: piloto vs cliente de pago
- Variables en .env

---

## Consecuencias

**✅ Positivas**:
- Sistema auditable ante autoridad de control GDPR
- Blast radius contenido: un proyecto comprometido no afecta a los otros
- Exfiltración bloqueada aunque el atacante entre en un contenedor
- Evidencia forense disponible si hay incidente

**⚠️ Negativas**:
- Configuración inicial más compleja que un deploy básico
- Egress filtering requiere pruebas post-deploy para verificar que
  las APIs necesarias (Abstract, Telegram) siguen funcionando
- AIDE y auditd generan alertas que deben interpretarse — riesgo
  de fatiga si no se configuran bien los umbrales

---

## Referencias

- ADR-009 — Seguridad de aplicación en workflows N8N (capa complementaria)
- ADR-010 — Secuencia de deploy: seguridad antes que producción
- Runbook-VPS-001 — Implementación práctica de este ADR
- Checklist pre-producción — verificación de implementación
- Triangulación seguridad v2.0 — evidencia documental de la decisión

---

**Creado**: 2 de mayo 2026  
**Última revisión**: 2 de mayo 2026  
**Próxima revisión**: Al completar Bloque 6 con primer proyecto en producción
