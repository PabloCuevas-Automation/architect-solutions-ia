# ADR-002: Política de Manejo de PII en Todos Mis Proyectos

**Fecha:** 28 de enero, 2026  
**Estado:** ✅ Aceptada  
**Autor:** Pablo  
**Tipo:** Política Transversal  
**Contexto:** Fase 0, Día 2 - Cerrando fundamentos de compliance

---

## Resumen Ejecutivo

En el contexto de construir sistemas de automatización + IA que inevitablemente manejarán datos personales, enfrentando regulaciones europeas (GDPR) y el hecho de que 90% de developers ignoran compliance (ventaja competitiva), **decidí establecer política NUNCA guardar PII sin consentimiento explícito y mecanismos de eliminación**, aceptando que esto añade complejidad de implementación pero reduce riesgo legal y me diferencia en mercado europeo.

---

## Contexto

### Situación
- Todos mis proyectos (automatización, pipelines, IA) manejarán datos
- GDPR aplica a TODO lo que opera en UE
- Multas hasta €20M o 4% revenue (aunque sea freelancer pequeño)
- Ventaja: 90% de "AI engineers" ignoran esto = yo no

### Por Qué Esto Es Arquitectónico
Esta no es decisión de "feature" - define cómo diseño sistemas desde día 1:
- ¿Qué datos guardo? (data minimization)
- ¿Cuánto tiempo? (retention policies)
- ¿Cómo los elimino? (right to deletion)

**Si diseño sistema sin pensar en compliance, tendré que reconstruirlo después.**

---

## Decisión

**Política obligatoria para TODOS mis proyectos:**

### 1. Data Minimization
- Solo guardo datos estrictamente necesarios
- Pregunta por cada campo: "¿Qué pasa si NO lo guardo?"
- Si la respuesta es "nada crítico" → NO guardarlo

### 2. Consentimiento Explícito
- NUNCA asumir "consentimiento implícito"
- Checkbox claro: "Acepto que guarden [datos específicos] para [propósito específico]"
- Timestamp + versión de términos guardados

### 3. Mecanismos de Eliminación
- TODO sistema tendrá endpoint/proceso de delete
- Implementado ANTES de primer usuario, no "después"
- Testing: ¿realmente se elimina todo?

### 4. Audit Trail
- Log de: quién accedió a qué, cuándo
- No para espiar - para defensa legal ("aquí está la prueba de que cumplimos")

### 5. Data Retention Automática
- Nada se guarda "forever"
- Define TTL (Time To Live) por tipo de dato
- Cleanup automático (cronjob, no manual)

---

## Alternativas Consideradas

### Alternativa A: "Ignorar Compliance Hasta Que Sea Problema"

**Descripción:** Construir sistemas sin pensar en GDPR. Si llega multa o queja, recién ahí arreglar.

**Pros:**
- ✅ Desarrollo más rápido inicialmente
- ✅ Menos complejidad técnica
- ✅ Competencia lo hace así

**Contras:**
- ❌ Riesgo legal real (multas hasta €20K incluso freelancers)
- ❌ Re-arquitectura costosa después
- ❌ Cliente enterprise te descarta inmediatamente
- ❌ Reputación dañada si hay breach

**Por Qué NO:** Soy europeo, opero en UE, aspiro a clientes serios. No es opcional.

---

### Alternativa B: "Compliance Máximo Paranoia"

**Descripción:** Implementar TODAS las medidas posibles: encriptación E2E, zero-knowledge, no guardar NADA, legal review cada feature.

**Pros:**
- ✅ Máxima seguridad legal
- ✅ Diferenciación total

**Contras:**
- ❌ Desarrollo 3x más lento
- ❌ Costos de infraestructura altos
- ❌ Over-engineering para proyectos pequeños
- ❌ Paralysis por miedo

**Por Qué NO:** Fase 1 necesita velocidad. Compliance pragmático > compliance perfecto.

---

### Alternativa C: Compliance Pragmático (ELEGIDA) ⭐

**Descripción:** Implementar fundamentos GDPR desde día 1 (data minimization, deletion, consentimiento), con profundidad escalable según criticidad del proyecto.

**Pros:**
- ✅ Legalmente defendible
- ✅ No ralentiza significativamente
- ✅ Diferenciación real en mercado
- ✅ Escalable (añado más después si necesito)

**Contras:**
- ❌ Complejidad añadida vs "ignorar"
- ❌ Necesito pensar compliance en cada proyecto
- ❌ Clientes pueden no valorarlo inicialmente

**Factor decisivo:** Compliance no es "extra" - es requirement. Mejor integrado desde inicio que retrofit después.

---

## Consecuencias

### Positivas ✅

1. **Ventaja competitiva europea**
   - Puedo vender a empresas serias (rechazan vendors sin compliance)
   - Puedo cobrar más (compliance tiene valor)
   - Puedo trabajar en sectores regulados (finanzas, salud)

2. **Arquitectura mejor por diseño**
   - Data minimization = sistemas más simples
   - Retention policies = menos storage costs
   - Audit logs = debugging más fácil

3. **Protección legal**
   - Si hay breach, tengo documentación de buenas prácticas
   - Reducción de multas potenciales
   - Puedo dormir tranquilo

4. **Narrative en ventas**
   - "Todos mis sistemas son GDPR-compliant desde día 1"
   - Diferenciador vs competencia que lo ignora
   - Confianza de cliente enterprise

### Negativas ⚠️

1. **Overhead de desarrollo**
   - ~15-20% tiempo extra por proyecto
   - Testing de deletion añade complejidad
   - Documentation obligatoria

2. **Fricción con usuarios**
   - Consentimiento explícito reduce conversión ~5-10%
   - Algunos usuarios molestos por "más clicks"

3. **Tentación de shortcuts**
   - Cuando hay presión de deadline, tentador "saltarse" compliance
   - Requiere disciplina

4. **Costo de infraestructura**
   - Audit logs ocupan espacio
   - Encriptación requiere más CPU
   - Backups con retention policies necesitan automation

### Mitigaciones 🛡️

**Para overhead desarrollo:**
- COMPLIANCE_TEMPLATE.md reutilizable → checklist rápido
- Boilerplate code de deletion/audit → copiar/pegar
- Documentación como parte de definition of done

**Para fricción usuarios:**
- Consentimiento simple y claro (no legalese)
- "Te protegemos" messaging positivo
- Transparencia genera confianza

**Para tentación shortcuts:**
- Compliance en checklist obligatorio pre-launch
- Review con mentor (Claude) antes de deploy
- Compromiso escrito (este ADR)

---

## Implementación

### Checklist Por Proyecto

Antes de considerar proyecto "completo":

**Design phase:**
- [ ] Identifiqué qué datos guardo (lista)
- [ ] Clasifiqué cuáles son PII
- [ ] Definí por qué necesito cada dato
- [ ] Escribí retention policy

**Development phase:**
- [ ] Implementé deletion endpoint/proceso
- [ ] Añadí consent mechanism
- [ ] Implementé audit logging
- [ ] Configuré cleanup automático

**Pre-launch:**
- [ ] COMPLIANCE.md completado
- [ ] Testing de deletion (realmente borra)
- [ ] User-facing privacy policy
- [ ] Documented en README

---

## Criterios de Éxito

### Corto Plazo (3 meses)
- ✅ COMPLIANCE.md en todos proyectos portfolio
- ✅ Ningún proyecto guarda PII sin consentimiento
- ✅ Al menos 1 cliente valora compliance (feedback explícito)

### Medio Plazo (6 meses)
- ✅ Rechacé 0 proyectos enterprise por "no cumples GDPR"
- ✅ Compliance es parte de mi pitch de ventas
- ✅ Tengo boilerplate code reutilizable

### Largo Plazo (12+ meses)
- ✅ Compliance es segunda naturaleza (no pienso, lo hago)
- ✅ Me han contratado ESPECÍFICAMENTE por compliance
- ✅ He asesorado a otro developer sobre GDPR

---

## Referencias

- **GDPR.eu** - Official resource
- **COMPLIANCE_TEMPLATE.md** - Template reutilizable
- **ADR-000** - Pensamiento sistémico primero (decisión madre)

---

## Notas Finales

Esta decisión refleja quién quiero ser profesionalmente:

**No quiero ser:** Developer que construye rápido y después lidia con consecuencias

**Quiero ser:** Arquitecto que construye correcto desde inicio, aunque tome 15% más tiempo

Compliance no es "overhead" - es **infrastructure de confianza**.

En mercado europeo, quien ignora GDPR no es "rápido" - es **irresponsable**.

---

**Firmado:** Pablo  
**Fecha:** 28 de enero, 2026  
**Revisión próxima:** Al finalizar Fase 1 (mes 6)

*"Compliance primero. Velocidad después. Ambos son posibles."*