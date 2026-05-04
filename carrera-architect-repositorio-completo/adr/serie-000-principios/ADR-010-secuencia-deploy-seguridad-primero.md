# ADR-010: Secuencia de Deploy — Seguridad Antes que Producción

**Fecha**: 2 de mayo 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — principio de despliegue universal  
**Serie**: 000 (principio universal — aplica a todos los proyectos)  
**Audiencia**: 🔒 Personal  
**Tipo de decisión**: Type 1 — difícil de revertir si se ignora

---

## Contexto

Al finalizar la triangulación de seguridad del VPS (cuatro rondas, cuatro IAs,
una simulación de ataque), el sistema PA1 está listo para desplegarse en
producción en Hetzner CX22. Sin embargo, el análisis de vulnerabilidades reales
en el workflow actual reveló que el sistema tiene vulnerabilidades conocidas
activas en este momento:

- **Capa 1 (HMAC)**: placeholder — cualquier petición HTTP es procesada
- **Capa 4 (Telegram)**: envía PII completa violando ADR-206 ya aprobado
- **SQL injection**: documentada en tabla de deuda técnica — riesgo con inputs
  controlados por atacante
- **messaggio sin sanitizar**: campo de texto libre que irá a interfaz web

La tentación natural es: desplegar al VPS primero, y resolver las
vulnerabilidades después en producción. Esa secuencia es incorrecta.

---

## El problema con "deploy primero, seguridad después"

Una vez que datos reales de clientes entran al sistema, el contexto cambia
irreversiblemente:

**Con HMAC como placeholder en producción**:
Cualquier bot que descubra la URL del webhook puede inyectar datos falsos,
agotar créditos de Abstract API, y llenar PostgreSQL con basura. Un atacante
con acceso al formulario puede intentar SQL injection con datos controlados.

**Con Capa 4 enviando PII a Telegram**:
Desde el primer lead real, nombre, email y mensaje viajan a servidores de
Telegram fuera de la UE sin DPA. Eso no es deuda técnica — es una violación
GDPR activa desde el primer segundo en producción.

**Con messaggio sin sanitizar**:
Cuando la interfaz de administración (ADR-206) se construya, cualquier dato
en `messaggio` que contenga HTML o JavaScript se ejecutará en el navegador.
Un atacante que haya enviado un payload en ese campo antes de que existiera
la sanitización tiene un XSS persistente en la interfaz de administración.

Ninguno de estos problemas es recuperable de la misma manera que un bug
de lógica de negocio. Afectan datos ya almacenados, leads ya procesados,
y posiblemente clientes ya notificados.

---

## Decisión

**El sistema no se despliega en el VPS de producción hasta que las
vulnerabilidades conocidas de Capa 1 y Capa 4 estén resueltas y verificadas.**

El orden correcto es:

```
FASE A — Resolver vulnerabilidades (entorno local actual)
  1. Implementar HMAC real en Capa 1 (Bloque 7 adelantado)
  2. Reconstruir Capa 4 según ADR-206 (notificación sin PII + link)
  3. Sanitizar campo messaggio en procesador-body1
  4. Verificar los tres puntos con Thunder Client en local

FASE B — Preparar infraestructura VPS
  5. Contratar Hetzner CX22
  6. Ejecutar Runbook-VPS-001 completo (hardening + Docker + silos)
  7. Snapshot post-hardening

FASE C — Deploy
  8. Completar checklist pre-producción (todos los ítems en ✅)
  9. Deploy del sistema con vulnerabilidades ya resueltas
  10. Primer lead real
```

---

## Lo que este orden garantiza

**Para el cliente**: ningún dato real entra a un sistema con vulnerabilidades
conocidas activas. El primer lead real es procesado por un sistema donde
la seguridad está implementada, no planificada.

**Para el arquitecto**: si hay un incidente de seguridad, no podrá atribuirse
a vulnerabilidades que se conocían antes del deploy y no se resolvieron.
La diferencia entre negligencia y riesgo residual documentado es exactamente
esta secuencia.

**Para el GDPR**: el Art. 25 exige "Privacy and Security by Design" —
diseñar la seguridad antes de procesar datos, no añadirla después.
Esta secuencia es la implementación práctica de ese principio.

---

## Consecuencias

**✅ Positivas**:
- El primer dato real de cliente entra a un sistema seguro
- Elimina la categoría de incidentes "vulnerabilidad conocida no resuelta"
- Cumple con el principio de Privacy by Design del GDPR
- Documentable y auditable ante cualquier autoridad de control

**⚠️ Negativas**:
- El deploy se retrasa hasta resolver Capa 1 y Capa 4
- Requiere adelantar trabajo de Bloque 7 (HMAC) antes de Bloque 6 (VPS)
- Más sesiones de trabajo antes de tener el sistema en producción

**Trade-off aceptado**: el retraso es medible en sesiones de trabajo.
Una brecha GDPR con datos reales de clientes no tiene coste medible —
tiene coste existencial para el proyecto y para la confianza del cliente.

---

## Referencias

- ADR-206 — Minimización PII en Notificaciones Telegram (viola la implementación actual de Capa 4)
- ADR-207 — Reconstrucción de Capa 4 (trabajo técnico derivado de esta decisión)
- ADR-008 — Arquitectura de seguridad VPS (infraestructura que se prepara en Fase B)
- Runbook-VPS-001 — Guía de ejecución del VPS (Fase B)
- Checklist pre-producción — criterios de "listo para deploy" (Fase C)
- Triangulación de seguridad — 4 rondas, 4 IAs, simulación de ataque (evidencia)

---

**Creado**: 2 de mayo 2026  
**Última revisión**: 2 de mayo 2026
