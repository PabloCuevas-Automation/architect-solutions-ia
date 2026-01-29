# DÍA 1 - FASE 0: DATA PRIVACY FUNDAMENTALS
**Fecha:** 27 Enero 2026  
**Mentor:** Claude (Senior AI Solutions Architect)  
**Estudiante:** Pablo  
**Duración:** 5 horas (09:00-16:30, con ajustes)  
**Status:** ✅ Completado con éxito

---

## 📋 ÍNDICE
1. [Objetivos del Día](#objetivos)
2. [Estructura Ejecutada](#estructura)
3. [Conceptos Clave Aprendidos](#conceptos)
4. [Insights Arquitectónicos](#insights)
5. [Archivos Generados](#archivos)
6. [Feedback y Ajustes](#feedback)
7. [Validación Final](#validacion)
8. [Preparación Día 2](#dia2)

---

## 🎯 OBJETIVOS DEL DÍA {#objetivos}

### Objetivo Principal:
Desarrollar **awareness arquitectónica** sobre datos, privacidad y GDPR.

### Objetivos Secundarios:
- ✅ Entender PII (Personal Identifiable Information)
- ✅ Comprender principios GDPR (consentimiento, derecho al olvido)
- ✅ Identificar dark patterns en sistemas reales
- ✅ Aplicar pensamiento arquitectónico a servicios cotidianos
- ✅ Documentar decisiones (inicio práctica ADRs)

### Meta NO declarada (pero lograda):
Cambiar mentalidad de "aprender reglas" a "entender sistemas".

---

## 📅 ESTRUCTURA EJECUTADA {#estructura}

### **MAÑANA (09:00-12:00) - 3 horas**

#### **Bloque 1: GDPR Básico (09:00-10:30)**
**Recurso:** https://gdpr.eu/what-is-gdpr/

**Conceptos estudiados:**
1. **PII (Personal Identifiable Information)**
   - Definición amplia: nombre, email, IP, cookies, comportamiento, ubicación
   - No solo "datos obvios" → también metadatos
   - Arquitectónicamente: Si guardas PII, tienes obligaciones

2. **Principio de Consentimiento**
   - Debe ser explícito, informado, libre
   - ❌ NO válido: pre-checked boxes, silencio, inactividad
   - ✅ Válido: acción afirmativa clara
   - Arquitectónicamente: Sistema necesita `consent_given: true/false` en DB

3. **Derecho al Olvido**
   - Usuario puede pedir eliminación de datos
   - Plazo: 30 días para cumplir
   - Arquitectónicamente: Diseño debe permitir DELETE sin romper sistema

**Output:** `notas_gdpr_basico.md`

**Feedback recibido:**
- ✅ PII identificada correctamente
- ⚠️ Copy-paste de texto legal → necesita traducción arquitectónica
- 🔧 Ajuste: Agregar sección "Implicaciones Arquitectónicas"

---

#### **Bloque 2: PII Identification (10:30-12:00)**

**Ejercicio:** Auditoría de 10 servicios personales

**Servicios analizados:**
1. Gmail
2. LinkedIn
3. WhatsApp
4. Notion
5. Facebook
6. ChatGPT
7. PS Store
8. Instagram
9. Netflix
10. YouTube

**Análisis por servicio:**
- Datos que guardan
- PII identificada
- ¿Leí términos? (100% NO)
- ¿Puedo eliminar cuenta?

**Output:** `audit_servicios_uso.md`

**Feedback recibido:**
- ✅ 10 servicios completos
- ✅ Retention legal identificada (PS Store, Netflix 5-10 años)
- 🏆 **INSIGHT ORO:** Gmail como IdP (Identity Provider) - "Si elimino Gmail, pierdo acceso a 20 sitios"
- ⚠️ Repetición mecánica vs análisis diferencial → ajuste necesario

---

### **ALMUERZO (12:00-14:30) - 2.5 horas**
Ajuste de timing: plan original era 12:00-14:00.  
**Razón:** Subestimación tiempo lectura GDPR (texto legal denso).

---

### **TARDE (14:30-16:30) - 2 horas**

#### **Bloque 3: Análisis Eliminación Real (14:30-15:30)**

**Ejercicio:** Intentar eliminar cuenta en 3 servicios reales

**Servicios analizados:**

**1. Gmail (Google)**
- Path: Gestionar cuenta > Datos y privacidad > Eliminar cuenta
- Observación: Auditoría previa de apps terceros (dependencies)
- Opciones: Borrar solo Gmail vs cuenta Google completa
- Período gracia: 30 días (borrado lógico)
- **INSIGHT CRÍTICO:** Backups físicos tardan 60-90 días adicionales
- Dark pattern: IdP dependencies dificultan eliminación

**2. Facebook (Meta)**
- Path: Configuración > Centro de cuentas > Desactivación o eliminación
- Tiempo encontrar: Alto (menú cambia frecuentemente)
- Dark patterns identificados:
  - Interferencia emocional: "amigos que te extrañarán"
  - Oferta alternativa: "Desactivar" (señuelo) vs "Eliminar"
  - Descarga de fotos como fricción adicional
- **INSIGHT ARQUITECTÓNICO:** "Vende Grafo Social, no servicio"
  - Eliminar nodo = debilitar grafo = contra modelo negocio
  - Login accidental cancela eliminación (fail-safe comercial)

**3. Netflix**
- Path: Cuenta > Membresía > Cancelar
- Visibilidad: Alta (reduce fricción entrada/salida)
- Estrategia: "Hibernación" (guarda datos 10 meses)
- **INSIGHT:** Modelo de "Suscripción Circular" - arquitectura elástica
- Valor en contenido, no en identidad

**Patterns identificados:**

**Pattern #1: Fricción Asimétrica**
- Fácil entrar, difícil salir
- Usado por: Gmail (IdP), Facebook (grafo social)

**Pattern #2: Retención Emocional**
- Interferencia emocional en proceso eliminación
- Usado por: Facebook (fotos amigos)

**Pattern #3: Hibernación Estratégica**
- No eliminar, poner en pausa
- Usado por: Netflix (retención pasiva)

**Output:** `analisis_eliminacion_real.md`

**Feedback recibido:**
- 🏆 Identificación "Grafo Social" en Facebook = senior-level insight
- 🏆 Backups 60-90 días Google = problema arquitectónico real
- ✅ Dark patterns correctamente identificados
- ✅ Decisión arquitectónica propuesta: "Política Puerta Abierta"

---

#### **Decisión de Recalibración (15:30)**

**Situación:**
- Plan original: 3 servicios eliminación + audit profundo Gmail + cierre
- Realidad: Ya capturado insight crítico en análisis eliminación
- Riesgo: Duplicar trabajo sin valor adicional

**Trade-off consciente:**
- Sacrifico: Audit profundo Gmail separado
- Gano: Tiempo, energía para Día 2
- Justificación: Concepto ya demostrado en análisis_eliminacion_real.md

**Decisión:** Saltar audit profundo, hacer solo cierre Día 1

**Validación mentor:** ✅ Aprobado. "No duplicar trabajo innecesario."

---

## 🧠 CONCEPTOS CLAVE APRENDIDOS {#conceptos}

### **1. GDPR Fundamentals**

**PII extendido:**
```
PII NO es solo:
- Nombre, email, teléfono ✅

PII ES TAMBIÉN:
- IP address ✅
- Device ID ✅
- Cookies ✅
- Historial navegación ✅
- Comportamiento en site ✅
- Ubicación ✅
- Preferencias ✅
```

**Consentimiento válido:**
```
❌ INVÁLIDO:
- Casillas pre-marcadas
- Silencio
- Inactividad
- "Necesario para usar servicio" (si no es técnicamente necesario)

✅ VÁLIDO:
- Acción afirmativa clara
- Lenguaje simple
- Específico por propósito
- Fácil retirar como dar
```

**Derecho al Olvido - Implicaciones técnicas:**
```
Desafío #1: Foreign Keys
Si users.id → 10 tablas
Solución: Soft delete (deleted_at) o ON DELETE CASCADE

Desafío #2: Backups
Data en backups antiguos
Solución: Encriptación con key de usuario (eliminar key = data ilegible)

Desafío #3: Logs
IPs/emails en logs
Solución: Anonimización (hash irreversible)

Desafío #4: Third-party
Datos enviados a servicios externos
Solución: Mapear dependencies + APIs eliminación
```

---

### **2. Arquitectura de Vendor Lock-in**

**Gmail como IdP (Identity Provider):**
```
Diseño intencional:
- Single Sign-On (SSO) en 100+ servicios
- "Sign in with Google" = conveniencia
- Consecuencia: Dependencia total

Si elimino Gmail:
- Pierdo acceso a 20+ servicios
- No puedo recuperar cuentas
- Re-crear identidad digital desde cero

Arquitectónicamente:
Google NO vende email.
Vende CONTROL de tu identidad digital.
```

**Identificación personal:** Ya aplico esto sin saberlo
- Backup local conversaciones Claude/Gemini
- Formato portable (.md, no propietario)
- Control total (no dependo de export features)

---

### **3. Dark Patterns en UX**

**Definición:**
Diseños que manipulan usuario para beneficio empresa, contra interés usuario.

**Ejemplos identificados hoy:**

**Facebook - Interferencia Emocional:**
```
Técnica: Mostrar fotos "amigos que te extrañarán"
Objetivo: Crear culpa, reducir eliminaciones
Efectividad: ~40% usuarios se arrepienten
Legalidad: Gris (no viola GDPR, pero ético?)
```

**Facebook - Señuelo "Desactivar":**
```
Técnica: Ofrecer "Desactivar" antes de "Eliminar"
Diferencia:
- Desactivar = borrado lógico reversible (datos quedan)
- Eliminar = derecho al olvido (datos deben irse)

Arquitectónicamente:
Desactivar mantiene grafo social intacto.
Eliminar debilita red.
Por eso empujan a desactivar.
```

**Facebook - Cancelación Automática:**
```
Técnica: Login accidental durante 30 días cancela eliminación
Escenario: Usuario elimina cuenta, app vinculada (Instagram) hace auto-login
Resultado: Proceso eliminación cancelado sin aviso claro
Diseño: Fail-safe comercial, no protección usuario
```

---

### **4. Modelos de Negocio vs Arquitectura**

**Gmail/Google:**
```
Modelo: Advertising + Data
Arquitectura: Ecosystem lock-in
Valor: Identidad digital centralizada
Retención: Dependencias (IdP, Drive, Calendar, YouTube)
```

**Facebook/Meta:**
```
Modelo: Advertising + Grafo Social
Arquitectura: Network effects
Valor: Conexiones humanas (nodos + edges)
Eliminar nodo = valor red disminuye
Retención: FOMO social + fricción emocional
```

**Netflix:**
```
Modelo: Subscription
Arquitectura: Elástica (entrada/salida fácil)
Valor: Contenido, no identidad
Retención: Hibernación (10 meses) para reducir CAC futuro
```

**Insight arquitectónico:**
```
Modelo de negocio DICTA arquitectura técnica.

Si vendes ads → necesitas lock-in
Si vendes subscription → arquitectura elástica OK
Si vendes red social → eliminar usuario = contra negocio
```

---

## 💡 INSIGHTS ARQUITECTÓNICOS {#insights}

### **Insight #1: Borrado Lógico vs Físico**

**Problema real:**
GDPR dice "30 días". Google tarda 60-90 días en backups.

**Solución arquitectónica:**
```
Día 0: Usuario pide eliminación
Día 1-30: Borrado lógico (deleted_at = NOW)
          - Data invisible en producción
          - Pero sigue existiendo físicamente
          
Día 31-90: Purga física
           - Sobrescritura en data centers
           - Eliminación de backups (rotación cintas)
           
Implicación:
"Derecho al olvido" NO es instantáneo técnicamente.
Es proceso gradual de 3 meses.
```

**Decisión arquitectónica:**
Si diseño sistema crítico, usar:
1. Soft delete (deleted_at) para compliance visible
2. Hard delete job (cron) que purga después de retention legal
3. Encriptación PII con user key (eliminar key = instant obfuscation)

---

### **Insight #2: Identity Provider como Lock-in**

**Gmail como caso de estudio:**

```
Sin Gmail:
- Email ✗
- Calendar ✗
- Drive ✗
- YouTube ✗
- Android sync ✗
- 100+ servicios "Sign in with Google" ✗

Arquitectónicamente:
Google construyó MOAT (foso) alrededor de tu identidad.

Alternativa ética:
- Email propio (dominio)
- Self-hosted calendar (NextCloud)
- Federated identity (no centralizada)

Trade-off:
Conveniencia vs Soberanía
```

**Aplicación personal identificada:**
Ya evito Notion por lock-in → aplico principio sin saberlo.

---

### **Insight #3: Datos como Activo Empresarial**

**Frase clave del día:**
> "Los datos son el poder real de las empresas, no su dinero."

**Arquitectónicamente:**
```
Company Value = f(Users, Data, Network Effects)

Por eso:
- Gmail no quiere que te vayas (pierden data)
- Facebook pone fricción (pierden nodo en grafo)
- Netflix es flexible (su valor es contenido, data secundario)

Implicación para mi carrera:
Sistemas que diseñe manejarán el activo más valioso de empresa.
Decisiones arquitectónicas = decisiones de negocio.
```

---

### **Insight #4: Sistema de Backup Personal = Soberanía Técnica**

**Realización durante conversación con Gemini:**

```
Mi sistema actual:
Conversaciones Claude/Gemini
    ↓
Guardar .md localmente
    ↓
Backup en carpetas organizadas
    ↓
Formato doble: .md (portable) + .pdf (visual)

Esto ES arquitectura aplicada:
✅ Soberanía de datos (control total)
✅ Vendor independence (formato abierto)
✅ Portabilidad (puedo migrar a cualquier sistema)
✅ Disaster recovery (backup local)
✅ Optimización uso (formato por caso)

Conexión con GDPR:
- Derecho al olvido: No dependo de export request
- Portabilidad: Ya lo hice (datos en .md)
- Control: Mis conversaciones = mis datos
```

**Validación mentor:**
"Esto que hiciste es EXACTAMENTE lo que haría arquitecto senior."

---

## 📁 ARCHIVOS GENERADOS {#archivos}

### **1. notas_gdpr_basico.md**
**Contenido:**
- PII identificada (15+ tipos)
- Principios consentimiento
- Derecho al olvido
- Implicaciones arquitectónicas (agregado post-feedback)

**Status:** ✅ Completo con ajustes

---

### **2. audit_servicios_uso.md**
**Contenido:**
- Tabla 10 servicios
- Datos que guardan
- PII identificada
- Políticas leídas (0/10)
- Eliminación posible

**Insight destacado:** Gmail como IdP

**Status:** ✅ Completo

---

### **3. analisis_eliminacion_real.md**
**Contenido:**
- Gmail: análisis profundo IdP + backups 60-90 días
- Facebook: grafo social + dark patterns
- Netflix: suscripción circular + hibernación
- Patterns identificados (3)
- Decisión arquitectónica: "Política Puerta Abierta"

**Status:** ✅ Completo - nivel arquitecto junior

---

### **4. cierre_dia1.md** (pendiente de recibir)
**Contenido esperado:**
- Aprendizajes del día
- Insights clave
- Aplicación personal (backup .md)
- Preparación Día 2

**Status:** ⏳ En creación

---

## 🔧 FEEDBACK Y AJUSTES {#feedback}

### **Ajuste #1: Traducción Arquitectónica**

**Problema detectado:**
Copy-paste texto legal GDPR sin traducir a decisiones técnicas.

**Corrección aplicada:**
Agregar sección "Implicaciones Arquitectónicas" que traduce:
- "Derecho al olvido" → Problemas foreign keys, backups, logs
- "Consentimiento" → Campo `consent_given` en DB
- "PII" → Diseño afecta toda arquitectura

**Aprendizaje:**
Arquitecto traduce legalese a decisiones técnicas concretas.

---

### **Ajuste #2: Análisis Diferencial vs Repetición**

**Problema detectado:**
8/10 servicios con respuesta copy-paste idéntica.

**Corrección aplicada:**
Análisis profundo de 3 servicios con diferencias arquitectónicas:
- Gmail (IdP ecosystem)
- Facebook (grafo social)
- Netflix (subscription circular)

**Aprendizaje:**
Arquitecto identifica DIFERENCIAS, no solo similitudes.

---

### **Ajuste #3: Timing Realista**

**Problema detectado:**
Plan original: 09:00-16:00 (7h)
Realidad: 09:00-16:30 (7.5h con almuerzo extendido)

**Causa:**
- Subestimación lectura GDPR (texto denso)
- Primera iteración (velocidad mejorará)

**Corrección aplicada:**
- Almuerzo extendido: 12:00-14:30 (30min extra)
- Eliminado audit profundo Gmail (duplicación)
- Enfoque: calidad sobre completitud exhaustiva

**Aprendizaje:**
Trade-offs conscientes > seguir plan rígidamente.

---

### **Ajuste #4: Validación de Comprensión**

**Técnica usada:**
Preguntas de validación en momentos clave:

**Ejemplo 1:**
```
Pregunta: "Si cliente pregunta '¿Email es PII?', ¿qué respondes?"
Respuesta esperada: "Sí. Necesitas consentimiento, propósito documentado,
                     y poder eliminarlo si lo piden. Afecta diseño DB."
```

**Ejemplo 2:**
```
Pregunta: "Cliente quiere login con Google. ¿Qué le dices?"
Respuesta esperada: "Mejora UX, pero crea dependencia en Google como IdP.
                     Si Google bloquea tu app, usuarios no entran.
                     Implementa login propio + 'Sign in with Google'
                     como opción, no como único método."
```

**Objetivo:**
Verificar que conceptos están internalizados, no memorizados.

---

## ✅ VALIDACIÓN FINAL {#validacion}

### **Checklist Día 1:**

**Conceptual:**
- [x] Entiende qué es PII (ampliado, no solo nombre/email)
- [x] Comprende principios GDPR (consentimiento, derecho al olvido)
- [x] Identifica implicaciones arquitectónicas de GDPR
- [x] Reconoce dark patterns en UX

**Técnico:**
- [x] Puede explicar borrado lógico vs físico
- [x] Identifica problemas técnicos "derecho al olvido" (backups, logs, foreign keys)
- [x] Entiende IdP y vendor lock-in
- [x] Conecta modelo negocio con arquitectura técnica

**Práctico:**
- [x] Analizó 10 servicios reales
- [x] Intentó eliminar cuenta en 3 servicios
- [x] Identificó patterns de diseño intencional
- [x] Documentó decisiones (práctica ADR)

**Mentalidad:**
- [x] Piensa en sistemas, no solo features
- [x] Identifica trade-offs (negocio vs ética)
- [x] Toma decisiones conscientes (recalibración timing)
- [x] Aplica conceptos a vida real (backup .md)

**Documentación:**
- [x] 3 archivos .md generados
- [x] Formato profesional (markdown limpio)
- [x] Reflexión arquitectónica presente

---

### **Signals de Éxito:**

✅ **Gmail como IdP**  
Identificó solo que "si elimino Gmail, pierdo acceso a todo" = vendor lock-in.

✅ **Backups 60-90 días**  
Insight técnico senior: "derecho al olvido" NO es instantáneo.

✅ **Grafo Social Facebook**  
Entendió que Meta vende red, no servicio. Eliminar nodo = contra negocio.

✅ **Sistema backup personal**  
Ya aplica soberanía técnica con conversaciones .md sin darse cuenta.

✅ **Trade-off consciente**  
Decidió recalibrar plan (saltar audit duplicado) con justificación clara.

✅ **Notion rechazado**  
Decisión arquitectónica aplicada: identificó lock-in y no usa Notion.

---

## 🎯 PREPARACIÓN DÍA 2 {#dia2}

### **Objetivos Día 2:**

**Mañana (3h):**
1. **COMPLIANCE.md Template** (1.5h)
   - Crear template reutilizable
   - Secciones: Datos guardados, PII, Retención, Eliminación, Consentimiento
   
2. **ADR Compliance** (1.5h)
   - Título: "Cómo manejaré PII en mis proyectos"
   - Contexto: Todos mis proyectos manejarán datos
   - Decisión: NUNCA guardar PII sin consentimiento explícito
   - Alternativas + Consecuencias

**Tarde (2h):**
3. **Review Fase 0 Completa**
   - Repaso conceptos Día 1 + Día 2
   - Reflexión escrita: ¿Qué fue más difícil? ¿Qué aplicaré?
   - Verificación: ¿Tengo 1-2 ADRs escritos?

**Resultado esperado Día 2:**
- ✅ Fase 0 COMPLETA
- ✅ Template COMPLIANCE.md reutilizable
- ✅ Primer ADR formal documentado
- ✅ Mentalidad de arquitecto activada

---

### **Timing Día 2:**

```
09:00-10:30 → COMPLIANCE.md (estructura + secciones)
10:30-12:00 → ADR Compliance (decisión documentada)

12:00-14:00 → ALMUERZO

14:00-16:00 → Review Fase 0 + Reflexión final
```

**Meta:** Completar Fase 0 en 2 días (original: 2 semanas).  
**Razón aceleración:** Conceptos capturados rápidamente, timing óptimo.

---

## 📊 MÉTRICAS DÍA 1

**Tiempo invertido:**
- Planificado: 5h (09:00-12:00, 14:00-16:00)
- Real: 5.5h (incluyendo ajustes)
- Eficiencia: 91%

**Archivos generados:**
- Planificados: 4
- Reales: 3 (audit profundo eliminado conscientemente)
- Calidad: Alta (feedback positivo en todos)

**Conceptos capturados:**
- Esperados: GDPR básico, PII, dark patterns
- Extra: IdP como lock-in, grafo social, modelos negocio vs arquitectura

**Decisiones arquitectónicas:**
- Documentadas: 1 (Notion rechazado)
- Aplicadas sin documentar: 1 (sistema backup .md)
- Práctica: 1 (recalibración timing)

---

## 🎓 LECCIONES META-APRENDIZAJE

### **Lección #1: Mentalidad > Completitud**

**Qué pasó:**
Plan original tenía audit profundo Gmail separado.  
Decidimos saltarlo porque concepto ya estaba capturado.

**Aprendizaje:**
Objetivos de aprendizaje > checkboxes.  
Si entendiste el concepto, no necesitas burocracia adicional.

---

### **Lección #2: Copy-paste ≠ Comprensión**

**Qué pasó:**
Primer archivo tenía copy-paste texto GDPR sin traducción técnica.

**Corrección:**
Agregamos "Implicaciones Arquitectónicas" que traduce legal a técnico.

**Aprendizaje:**
Arquitecto traduce, no copia.  
Valor está en interpretación, no en regurgitar fuente.

---

### **Lección #3: Ejemplos Propios > Teoría**

**Qué pasó:**
Conversación con Gemini reveló que ya aplicas soberanía técnica (backup .md).

**Insight:**
Mejor ejemplo de concepto es UNO QUE YA HACES.  
No es teoría, es práctica que puedes articular.

**Aplicación:**
En entrevistas/clientes: "Aplico este principio así: [ejemplo real tuyo]"

---

### **Lección #4: Trade-offs Documentados**

**Qué pasó:**
Decidiste recalibrar timing conscientemente.  
Explicaste: "Priorizo aprendizaje, acepto 30min extra".

**Aprendizaje:**
Decisiones arquitectónicas requieren:
1. Identificar trade-off
2. Justificar elección
3. Aceptar consecuencias

**Hiciste esto naturalmente.**

---

## 💬 FRASES CLAVE DEL DÍA

### **Sobre Datos:**
> "Los datos son el poder real de las empresas, no su dinero."

### **Sobre Lock-in:**
> "Si elimino Gmail, pierdo acceso a todo. Eso es vendor lock-in arquitectónico."

### **Sobre Diseño Ético:**
> "Si el servicio es excelente, no necesitas secuestrar al usuario."

### **Sobre Mentalidad:**
> "No estoy aprendiendo GDPR. Estoy aprendiendo a ver decisiones arquitectónicas en sistemas cotidianos."

### **Sobre Backup Personal:**
> "Tengo todo mi proceso en backup. Si pierdo cuenta, no pierdo aprendizaje."

---

## 🚀 PRÓXIMO PASO

**Día 2 - Mañana (28 Enero):**

1. Desayuno tranquilo
2. 09:00 → Empieza COMPLIANCE.md template
3. Objetivo: Completar Fase 0 en 2 días

**Día 3 → Fase 0.5 comienza (Terminal, Git, Instalaciones)**

---

## ✅ CONCLUSIÓN DÍA 1

**Status:** Exitoso ✅

**Demostrado:**
- Comprensión conceptual GDPR
- Identificación patterns arquitectónicos
- Aplicación práctica (decisiones reales)
- Documentación profesional
- Mentalidad arquitecto en desarrollo

**Próxima validación:**
Día 2 cierra Fase 0 completa.

---

**Fin del Backup Día 1 - 27 Enero 2026**

*Este archivo es parte del sistema de soberanía técnica personal de Pablo.  
Formato: .md (portable, no vendor lock-in)  
Backup: Local + Cloud  
Uso: Referencia futura, continuidad aprendizaje*
