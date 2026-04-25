# ADR-205: Nivel 2 Nunca Procesa Automáticamente

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Estratégica — decisión de negocio sobre clasificación  
**Audiencia**: 🔒 Personal  
**Proyecto**: PA1 — sistema-leads-pablocuevas

---

## Contexto

Abstract API puede fallar o no tener confianza suficiente para verificar
un email. En esos casos el sistema pasa a Nivel 2 — clasificación con
reglas propias. La pregunta: ¿el Nivel 2 puede decidir procesar
automáticamente un lead?

---

## Decisión

**El Nivel 2 nunca procesa automáticamente. Solo puede descartar o
escalar a revisión manual.**

```
Nivel 1 (Abstract con confianza suficiente):
    → procesar / revisar_manualmente / descartar

Nivel 2 (Abstract falló o sin confianza):
    → revisar_manualmente / descartar
    → NUNCA → procesar
```

**Por qué:**
Si `abstract_live_site === null`, Abstract no pudo verificar el dominio.
Procesar automáticamente un lead con información incompleta genera falsos
positivos — Pablo responde a leads que no son lo que parecen, dañando
la relación comercial. Un email que el sistema no puede verificar merece
revisión humana antes de cualquier acción.

La coherencia arquitectónica: si el Nivel 2 procesara automáticamente,
la condición `abstract_live_site === null` nunca podría cumplirse para
activar el procesamiento — el nodo de clasificación sería inalcanzable.
Nivel 2 con procesamiento automático es arquitectónicamente incoherente.

---

## Consecuencias

### Positivas ✅
- Sin falsos positivos por leads no verificados
- Pablo mantiene control sobre cualquier lead que el sistema no puede
  clasificar con confianza

### Negativas ⚠️
- Más carga manual cuando Abstract falla sistemáticamente
- Leads legítimos con dominios poco conocidos van a revisión manual

---

**Creado**: 25 de abril 2026  
**Autor**: Pablo Cuevas
