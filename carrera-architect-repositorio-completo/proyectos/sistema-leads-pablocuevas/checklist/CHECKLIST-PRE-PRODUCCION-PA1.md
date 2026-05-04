# CHECKLIST PRE-PRODUCCIÓN — Sistema PA1
## Criterios de "Listo para Producción"

**Versión**: 1.0  
**Fecha**: 2 de mayo 2026  
**Uso**: Completar íntegramente antes del primer lead real  
**Referencia**: ADR-209 — Secuencia de deploy  
**Instrucción**: Cada ítem debe ser ✅ verificado, no solo asumido.  
Un ítem sin verificar práctica es ❌ aunque esté implementado.

---

## BLOQUE A — Cuenta y Acceso Hetzner

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| A1 | 2FA activado en cuenta Hetzner | Intentar login desde modo incógnito — debe pedir segundo factor | ☐ |
| A2 | DPA firmado con Hetzner | Descargar copia firmada del panel y archivarla | ☐ |
| A3 | Firewall externo configurado: 22 solo tu IP, 80 y 443 abiertos | Intentar SSH desde IP diferente — debe rechazar | ☐ |
| A4 | Snapshot post-hardening creado | Panel Hetzner → Snapshots → nombre `vps-base-hardened-YYYYMMDD` | ☐ |

---

## BLOQUE B — Sistema Operativo

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| B1 | Usuario `pablo` creado, root deshabilitado | `ssh root@IP` debe rechazar | ☐ |
| B2 | SSH key-only activo, contraseña deshabilitada | Intentar `ssh pablo@IP` sin clave — debe rechazar | ☐ |
| B3 | UFW activo con reglas correctas | `ufw status verbose` — confirmar reglas y puerto 22 restringido a IP propia | ☐ |
| B4 | Fail2Ban activo | `systemctl status fail2ban` — debe estar `active (running)` | ☐ |
| B5 | unattended-upgrades activo | `systemctl status unattended-upgrades` | ☐ |
| B6 | AIDE inicializado | `aide --check` — primera ejecución crea baseline | ☐ |
| B7 | auditd con reglas Docker activo | `auditctl -l` — debe mostrar reglas de docker.rules | ☐ |
| B8 | logwatch instalado y configurado | Verificar que llega email de reporte al día siguiente | ☐ |

---

## BLOQUE C — Docker y Redes

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| C1 | Fix Docker/UFW: límite de logs en daemon.json | `cat /etc/docker/daemon.json` — confirmar max-size y max-file | ☐ |
| C2 | Egress filtering activo en DOCKER-USER | Desde contenedor: `curl http://example.com` — debe fallar. `curl https://api.anthropic.com` — debe funcionar | ☐ |
| C3 | Reglas DOCKER-USER persistentes tras reboot | Reiniciar servidor, verificar que las reglas siguen activas con `iptables -L DOCKER-USER` | ☐ |
| C4 | Red `proxy_net` creada | `docker network ls` — debe mostrar `proxy_net` | ☐ |
| C5 | PostgreSQL sin bloque `ports` en compose | `docker inspect [postgres_container]` — no debe escuchar en interfaces externas | ☐ |
| C6 | `.env` con permisos 600 en cada proyecto | `ls -la /opt/pa1/.env` — debe mostrar `-rw-------` | ☐ |

---

## BLOQUE D — PostgreSQL

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| D1 | Base de datos `pa1_db` creada con usuario `n8n_pa1` | `psql -U postgres -c "\l"` — debe mostrar pa1_db | ☐ |
| D2 | Usuario `n8n_pa1` sin privilegios de superusuario | `SELECT usename, usesuper FROM pg_user WHERE usename = 'n8n_pa1';` — usesuper debe ser `false` | ☐ |
| D3 | REVOKE CONNECT entre bases de datos de proyectos | Intentar `psql -U n8n_pa1 -d geolabor_db` — debe rechazar | ☐ |
| D4 | Schema migrado correctamente por N8N al arrancar | N8N arranca sin errores de migración en los logs | ☐ |

---

## BLOQUE E — Nginx y TLS

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| E1 | HTTPS activo con certificado válido | `curl -I https://n8n.pablocuevas.it` — debe devolver 200 con certificado válido | ☐ |
| E2 | TLS 1.2/1.3 únicamente | Verificar con SSL Labs (ssllabs.com/ssltest) — grado A mínimo | ☐ |
| E3 | HSTS activo | En cabeceras de respuesta: `Strict-Transport-Security` presente | ☐ |
| E4 | Panel N8N restringido por IP | Intentar acceder al panel desde IP diferente — debe devolver 403 | ☐ |
| E5 | Rate limiting activo en webhook | Enviar 20 peticiones rápidas al webhook — debe empezar a rechazar | ☐ |
| E6 | Content-Type enforcement activo | `curl -X POST -H "Content-Type: text/plain" [webhook_url]` — debe devolver 415 | ☐ |
| E7 | client_max_body_size configurado | Intentar enviar payload > 1MB — debe rechazar | ☐ |
| E8 | logrotate configurado para logs Nginx | `cat /etc/logrotate.d/nginx` — debe mostrar configuración con rotate 14 | ☐ |

---

## BLOQUE F — Seguridad del Workflow (Pre-producción obligatorio)

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| F1 | HMAC implementado en Capa 1 (no placeholder) | Enviar request sin header `X-Webhook-Signature` — debe rechazar con 401 | ☐ |
| F2 | HMAC implementado con firma inválida | Enviar request con firma incorrecta — debe rechazar | ☐ |
| F3 | HMAC implementado con firma válida | Enviar request válido desde Thunder Client con firma correcta — debe procesar | ☐ |
| F4 | Capa 4 reconstruida — sin PII en Telegram | Procesar lead de prueba y verificar que el mensaje de Telegram NO contiene nombre, email ni messaggio | ☐ |
| F5 | Link de la notificación Telegram resuelve | Hacer clic en el link de la notificación — debe abrir la interfaz de administración con autenticación | ☐ |
| F6 | Interfaz de administración con autenticación | Acceder sin credenciales — debe rechazar | ☐ |
| F7 | messaggio sanitizado contra XSS | Enviar lead con `messaggio: "<script>alert('xss')</script>"` y verificar que se almacena sanitizado en PostgreSQL | ☐ |
| F8 | Workflow de reporte semanal auditado | Completar audit template — adjuntar al proyecto | ☐ |
| F9 | Workflow de limpieza GDPR auditado | Completar audit template — adjuntar al proyecto | ☐ |

---

## BLOQUE G — Backup y Recuperación

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| G1 | Script de backup creado en `/opt/scripts/backup.sh` | `cat /opt/scripts/backup.sh` — debe mostrar script completo con GPG | ☐ |
| G2 | Backup passphrase en archivo `/root/.backup_pass` con permisos 400 | `ls -la /root/.backup_pass` — debe mostrar `-r--------` | ☐ |
| G3 | Hetzner Storage Box configurada en rclone | `rclone lsd hetzner_storage:` — debe listar sin errores | ☐ |
| G4 | Cron de backup activo | `crontab -l` — debe mostrar línea para backup a las 03:00 | ☐ |
| G5 | Backup manual ejecutado y verificado | Ejecutar script manualmente — verificar que el archivo .gpg aparece en Storage Box | ☐ |
| G6 | Dry-run de restauración completado | Restaurar en entorno local — N8N arranca, APIs conectan, datos visibles | ☐ |
| G7 | N8N_ENCRYPTION_KEY incluida en backup | Verificar que el .env del backup contiene la clave antes del cifrado | ☐ |

---

## BLOQUE H — Alertas y Monitoreo

| # | Ítem | Verificación | Estado |
|---|------|-------------|--------|
| H1 | Alertas de CPU configuradas en Hetzner | Panel Hetzner → Monitoring → Alert rules → CPU > 80% por 15min | ☐ |
| H2 | Alertas de red configuradas en Hetzner | Alerta de tráfico saliente anómalo configurada | ☐ |
| H3 | Health check de N8N activo | `curl https://n8n.pablocuevas.it/healthz` — debe devolver 200 | ☐ |
| H4 | Health check de PostgreSQL activo | Workflow de health check disparando cada 5 min sin errores | ☐ |

---

## FIRMA DE APROBACIÓN

| Campo | Valor |
|-------|-------|
| **Bloques completados** | A: ☐ B: ☐ C: ☐ D: ☐ E: ☐ F: ☐ G: ☐ H: ☐ |
| **Ítems totales** | 40 |
| **Ítems en ✅** | ___ / 40 |
| **Aprobado para producción** | ☐ SÍ (todos en ✅) ☐ NO |
| **Fecha de aprobación** | _______________ |
| **Firmado por** | Pablo Cuevas |

**Regla**: el checklist es binario. No existe "casi listo".  
Si un solo ítem está en ❌, el sistema no va a producción.

---

**Documento creado**: 2 de mayo 2026  
**Próxima revisión**: Al completar Bloque 6 + resolver Bloque F
