# ADR-204: Write-Before-Processing — Guardado Antes de Procesar

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — principio de resiliencia del sistema  
**Audiencia**: 🔒 Personal  
**Proyecto**: PA1 — sistema-leads-pablocuevas

---

## Contexto

El sistema llama a servicios externos (Abstract API, Telegram) después de
recibir un lead. Cualquiera de esos servicios puede fallar: timeout, error
500, cuota agotada, servicio caído. Si el lead no está guardado antes de
esas llamadas, un fallo externo significa pérdida del lead.

---

## Decisión

**El lead se guarda en PostgreSQL con estado `pendiente` antes de cualquier
llamada a servicios externos.**

```
Webhook recibe lead
    ↓
PostgreSQL INSERT (status: 'pendiente')  ← primero
    ↓
Abstract API (enriquecimiento)           ← después
    ↓
Telegram (notificación)                  ← después
```

Si Abstract falla, el lead ya existe en la DB con estado `pendiente` —
Pablo puede revisarlo manualmente. Si Telegram falla, el lead ya está
clasificado en la DB — el health check lo detectará y reintentará.

**Un lead que falla en cualquier capa posterior nunca se pierde.**

---

## Consecuencias

### Positivas ✅
- Ningún lead se pierde por fallos de servicios externos
- El estado `pendiente` actúa como cola de trabajo — cualquier lead sin
  procesar es visible en la DB

### Negativas ⚠️
- La DB puede acumular leads en estado `pendiente` si hay fallos
  sistemáticos — requiere monitoreo (Bloque 8)

---

**Creado**: 25 de abril 2026  
**Autor**: Pablo Cuevas
