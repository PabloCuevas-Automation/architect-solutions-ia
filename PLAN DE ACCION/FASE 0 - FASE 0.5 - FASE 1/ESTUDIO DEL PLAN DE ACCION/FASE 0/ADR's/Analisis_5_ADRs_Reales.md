# Análisis de 5 ADRs Reales - Día 2
**Fase 0 - Inmersión en ADRs**  
**Fecha**: 21 de enero, 2026  
**Estudiante**: Pablo  
**Objetivo**: Analizar 5 ADRs de proyectos open source reales

---

## Índice
1. [ADR-001: AWS - Adopción de GitFlow para desarrollo](#adr-001-aws)
2. [ADR-002: Spotify Backstage - Formato de archivo de catálogo](#adr-002-backstage)
3. [ADR-003: IBM Watson - Microservicios sin estado](#adr-003-ibm-watson)
4. [ADR-015: Proyecto Ejemplo - Deploy en AWS ECS Fargate](#adr-015-ecs-fargate)
5. [ADR-001: PMD Project - Usar Architecture Decision Records](#adr-001-pmd)

---

## ADR-001: AWS - Adopción de GitFlow para desarrollo {#adr-001-aws}

### Contexto del Proyecto
**Fuente**: AWS Prescriptive Guidance - Ejemplo real de ADR  
**Proyecto**: ABC Application (aplicación empaquetada para despliegue en entornos de clientes)  
**Fecha**: Documentado en AWS Prescriptive Guidance  
**Link**: https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/appendix.html

---

### 1. ¿QUÉ DECIDIERON?

**Decisión**: Adoptar GitFlow como proceso de desarrollo de software para la aplicación ABC.

**Detalles específicos**:
- Usar ramas `main` y `develop` como branches protegidas
- Implementar merge requests obligatorios para cambios en estas ramas
- Requerir al menos una aprobación para cada merge request
- Simplificación: NO usar ramas `hotfix/*` y `release/*` 

**Por qué es importante**: Define cómo todo el equipo trabajará con control de versiones durante toda la vida del proyecto.

---

### 2. ¿QUÉ ALTERNATIVAS CONSIDERARON?

El ADR menciona explícitamente estas alternativas:

| Alternativa | Descripción Breve |
|------------|-------------------|
| **Trunk-based development** | Desarrollo directo en rama principal con integraciones muy frecuentes |
| **GitHub Flow** | Flujo más simple con solo rama main y feature branches |
| **GitFlow (completo)** | Versión completa con todas las ramas incluyendo hotfix y release |

**Evaluación de alternativas**:
- Trunk-based: Muy simple pero requiere CI/CD muy maduro
- GitHub Flow: Simple pero no adecuado para releases empaquetados
- GitFlow completo: Más robusto pero con overhead innecesario para su caso

---

### 3. ¿POR QUÉ ELIGIERON ESTA OPCIÓN?

**Razones principales**:

1. **Control de features**: Necesitaban pipeline controlable para features, hotfixes y releases
2. **Despliegue empaquetado**: La aplicación se distribuye como paquete, no se despliega a un ambiente específico
3. **Simplicidad vs control**: GitFlow simplificado da control sin complejidad innecesaria
4. **Reacción rápida a bugs**: Sin ramas hotfix/* y release/* separadas pueden responder más rápido a bugs en producción

**Cita directa del ADR**:
> "For simplicity, we will not be using the hotfix/* and release/* branches, because ABC application will be packaged instead of being deployed to a specific environment. For this reason, there is no need for additional complexity that might prevent us from reacting quickly to fix bugs in production releases."

---

### 4. ¿QUÉ TRADE-OFFS ACEPTARON?

#### Ventajas aceptadas ✅
- **Estructura clara**: Separación entre desarrollo activo (`develop`) y código estable (`main`)
- **Control de calidad**: Merge requests obligatorios aseguran revisión
- **Trazabilidad**: Historial claro de qué cambios llegaron a producción y cuándo
- **Colaboración**: Aprobaciones fomentan revisión entre pares

#### Desventajas aceptadas ⚠️
- **Más complejo que GitHub Flow**: GitFlow simplificado sigue siendo más complejo que alternativas más simples
- **Overhead de proceso**: Merge requests y aprobaciones añaden tiempo al flujo de desarrollo
- **Curva de aprendizaje**: Desarrolladores nuevos necesitan aprender el modelo de branching

**Trade-off crítico**:
Aceptaron **mayor complejidad de proceso** a cambio de **mayor control sobre releases y calidad**.

---

### Reflexión Personal (Para tu análisis)

**Lo que me llama la atención**:
1. No eligieron la solución "por defecto" (GitFlow completo), sino que la adaptaron a sus necesidades
2. Justificaron explícitamente por qué NO usaron ciertas ramas del modelo
3. Consideraron el contexto específico (aplicación empaquetada vs desplegada)

**Aplicable a mi roadmap**:
- Cuando llegue a proyectos con Git, podría aplicar este mismo razonamiento
- Entiendo que debo adaptar metodologías a mi contexto, no seguirlas ciegamente
- Veo la importancia de documentar el "por qué NO" además del "por qué SÍ"

---

## ADR-002: Spotify Backstage - Formato de archivo de catálogo {#adr-002-backstage}

### Contexto del Proyecto
**Fuente**: Spotify Backstage (Open Source Developer Portal)  
**Proyecto**: Backstage Software Catalog  
**Fecha**: Temprano en el proyecto (está como ADR-002)  
**Link**: https://github.com/backstage/backstage/blob/master/docs/architecture-decisions/adr002-default-catalog-file-format.md

---

### 1. ¿QUÉ DECIDIERON?

**Decisión**: Usar YAML como formato por defecto para archivos descriptores del catálogo de software, siguiendo convenciones similares a Kubernetes.

**Detalles específicos**:
- Formato YAML con estructura similar a objetos de Kubernetes
- Campos obligatorios: `apiVersion`, `kind`, `metadata` (con `name`, `namespace`)
- Campos opcionales: `description`, `labels`, `annotations`
- Descriptores viven junto al código en control de versiones
- Sistema reacciona automáticamente a cambios en estos archivos

**Ejemplo de estructura decidida**:
```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: my-service
  description: "Mi servicio backend"
spec:
  type: service
  lifecycle: production
```

---

### 2. ¿QUÉ ALTERNATIVAS CONSIDERARON?

Aunque el ADR no lista alternativas explícitamente, el contexto menciona que:

| Alternativa implícita | Por qué NO la eligieron |
|----------------------|-------------------------|
| **Formato personalizado de Spotify** | Querían algo standard para open source |
| **JSON** | Menos legible para humanos que YAML |
| **TOML** | Menos familiar para comunidad Kubernetes |
| **Formato propietario UI-based** | Menos compatible con GitOps y control de versiones |

**Contexto histórico importante**:
> "Internally at Spotify, a homegrown software catalog system is used heavily and forms a core part of Backstage and other important pieces of the infrastructure. The user experience, learnings, and certain pieces of metadata from that catalog are being carried over to the open source effort."

Tuvieron que balancear entre:
- Mantener compatibilidad con su sistema interno
- Crear algo estándar para la comunidad open source

---

### 3. ¿POR QUÉ ELIGIERON ESTA OPCIÓN?

**Razones principales**:

1. **Familiaridad con Kubernetes**
   - YAML es el lenguaje de facto en el ecosistema Kubernetes
   - La comunidad DevOps ya conoce este formato
   - Sintaxis `apiVersion/kind/metadata` es reconocible

2. **GitOps-friendly**
   - Archivos de texto plano funcionan perfectamente con Git
   - Desarrolladores pueden mantenerlos junto al código
   - Revisiones usando el mismo proceso que code reviews

3. **Legibilidad humana**
   - YAML es más fácil de leer que JSON
   - Comentarios son posibles (vs JSON)
   - Menos verboso que XML

4. **Experiencia previa en Spotify**
   - Ya habían aprendido qué funciona y qué no en su sistema interno
   - Portaron las "lecciones aprendidas" al formato open source

---

### 4. ¿QUÉ TRADE-OFFS ACEPTARON?

#### Ventajas aceptadas ✅
- **Adopción rápida**: Comunidad Kubernetes no necesita aprender nuevo formato
- **Tooling existente**: Linters, validators, editores ya soportan YAML
- **Consistencia con ecosistema**: Backstage se siente "nativo" en entornos Kubernetes
- **Control de versiones natural**: Git diff funciona bien con YAML

#### Desventajas aceptadas ⚠️
- **Ambigüedades de YAML**: YAML tiene quirks conocidos (tabulaciones, tipos implícitos)
- **No es el formato interno de Spotify**: Tuvieron que "traducir" su conocimiento
- **Verbosidad moderada**: Más verboso que formatos más compactos
- **Curva de aprendizaje para no-DevOps**: Desarrolladores fuera del mundo Kubernetes necesitan aprender YAML

**Trade-off crítico**:
Sacrificaron **usar directamente su formato interno probado** a favor de **adopción más amplia en la comunidad open source**.

---

### Reflexión Personal (Para tu análisis)

**Lo que me llama la atención**:
1. Eligieron "familiaridad" sobre "lo que ya tenían funcionando internamente"
2. Pensaron en la experiencia del usuario final (desarrolladores que usarían Backstage)
3. Aprovecharon el "momentum" del ecosistema Kubernetes

**Aplicable a mi roadmap**:
- Cuando elija herramientas o formatos, considerar qué es familiar para mi "audiencia"
- No siempre lo "mejor técnicamente" es lo mejor en contexto real
- La adopción y facilidad de uso son consideraciones arquitectónicas válidas

---

## ADR-003: IBM Watson - Microservicios sin estado {#adr-003-ibm-watson}

### Contexto del Proyecto
**Fuente**: IBM Watson Discovery Service - WIRE Team  
**Proyecto**: Suite de microservicios cloud para Watson Discovery  
**Fecha**: Parte de más de 80 ADRs documentados a lo largo de 2 años  
**Link**: https://agilealliance.org/resources/experience-reports/distribute-design-authority-with-architecture-decision-records/

---

### 1. ¿QUÉ DECIDIERON?

**Decisión**: Implementar microservicios como servicios **sin estado (stateless)**.

**Detalles específicos**:
- Los microservicios no mantienen estado de sesión entre requests
- Todo el estado se almacena en bases de datos externas o servicios de caché
- Cada request contiene toda la información necesaria para procesarse
- Permite escalado horizontal sin coordinación entre instancias

**Nota sobre la fuente**: Este ADR se menciona en el reporte de experiencia del WIRE Team. Uno de los desarrolladores (Joe) reacciona: 
> "I was like, 'Oh man, not more documentation!' It seemed like documentation was all I was doing at the time. Here I was, staring at a page long exposition on why we were going to use a stateless microservice."

---

### 2. ¿QUÉ ALTERNATIVAS CONSIDERARON?

Según el contexto del proyecto de microservicios:

| Alternativa | Descripción |
|------------|-------------|
| **Servicios con estado (Stateful)** | Mantener estado de sesión en memoria del servicio |
| **Sticky sessions** | Enrutar requests del mismo usuario siempre a la misma instancia |
| **Estado compartido en memoria distribuida** | Usar herramientas como Redis Cluster para estado compartido |
| **Híbrido**: Stateless con caché local | Cachear datos localmente pero sin depender de ello |

---

### 3. ¿POR QUÉ ELIGIERON ESTA OPCIÓN?

**Razones principales**:

1. **Escalabilidad horizontal simple**
   - Pueden añadir o quitar instancias sin migrar estado
   - Load balancer puede distribuir requests libremente
   - No necesitan "sesiones pegajosas" (sticky sessions)

2. **Resiliencia ante fallos**
   - Si una instancia falla, otra puede tomar el siguiente request sin problema
   - No se pierde trabajo en progreso (porque no hay trabajo en progreso en memoria)
   - Recuperación más rápida ante crashes

3. **Despliegues más simples**
   - Rolling deployments son triviales
   - Blue-green deployments sin preocupación por migración de estado
   - Rollbacks inmediatos

4. **Cloud-native**
   - Filosofía de servicios cloud (AWS, GCP, Azure) favorece stateless
   - Compatible con auto-scaling
   - Aprovecha características de plataformas cloud

---

### 4. ¿QUÉ TRADE-OFFS ACEPTARON?

#### Ventajas aceptadas ✅
- **Escalabilidad ilimitada**: Pueden escalar horizontalmente sin límites prácticos
- **Fault tolerance**: Sistema sigue funcionando aunque fallen instancias
- **Deployment flexibility**: Despliegues, rollbacks, y updates son triviales
- **Cost efficiency**: Solo pagan por lo que usan, pueden escalar a cero

#### Desventajas aceptadas ⚠️
- **Latencia adicional**: Cada request necesita obtener contexto de DB/caché
- **Complejidad en gestión de estado**: El estado tiene que vivir en otro lado (DB, Redis, etc.)
- **Costos de infraestructura**: Necesitan mantener sistemas de storage externos
- **Sesiones de usuario**: Requieren tokens o cookies para mantener contexto entre requests

**Trade-off crítico**:
Aceptaron **latencia adicional en cada request** (para buscar contexto) a cambio de **escalabilidad y resiliencia masivas**.

---

### Reflexión Personal (Para tu análisis)

**Lo que me llama la atención**:
1. Esta decisión tiene consecuencias técnicas profundas en TODO el sistema
2. Afecta desde el código hasta la infraestructura
3. No es reversible fácilmente una vez que el sistema crece
4. La reacción de Joe ("oh man, not more documentation") muestra que documentar decisiones grandes es trabajo real

**Aplicable a mi roadmap**:
- Cuando aprenda sobre APIs y backends, recordar que stateless es el estándar moderno
- Entiendo que algunas decisiones tienen impacto en TODA la arquitectura
- Veo por qué es importante documentar esto: alguien nuevo en el equipo necesita entender por qué todo funciona sin estado

**Conexión con mi contexto**:
- Cuando construya automatizaciones con N8N, considerar si necesitan mantener estado
- En workflows simples, stateless será más fácil de mantener

---

## ADR-015: Proyecto Ejemplo - Deploy en AWS ECS Fargate {#adr-015-ecs-fargate}

### Contexto del Proyecto
**Fuente**: Ejemplo de ADR de mejores prácticas (citado en múltiples recursos)  
**Proyecto**: Aplicación web moderna con microservicios  
**Fecha**: Enero 2024  
**Tipo**: Decisión de infraestructura cloud

---

### 1. ¿QUÉ DECIDIERON?

**Decisión**: Desplegar la aplicación usando AWS ECS (Elastic Container Service) con Fargate como motor de ejecución.

**Detalles específicos**:
- Usar contenedores Docker para empaquetar aplicaciones
- ECS orquestará los contenedores
- Fargate gestionará la infraestructura subyacente (sin gestionar servidores EC2 directamente)
- Auto-scaling basado en métricas de uso

---

### 2. ¿QUÉ ALTERNATIVAS CONSIDERARON?

| Alternativa | Pros | Contras |
|------------|------|---------|
| **Self-managed Kubernetes** | Máxima flexibilidad y control | Overhead operacional enorme, requiere expertise especializado |
| **AWS EKS (Kubernetes gestionado)** | Kubernetes sin gestionar control plane | Más caro que ECS, curva de aprendizaje de K8s |
| **Google Cloud Run** | Muy simple, serverless containers | Vendor lock-in a GCP, menos familiar para equipo |
| **Heroku** | Extremadamente simple | Costoso a escala, menos control |
| **AWS EC2 directo** | Control total | Requiere gestión manual de servidores, no es cloud-native |

---

### 3. ¿POR QUÉ ELIGIERON ESTA OPCIÓN?

**Razones principales**:

1. **Balance complejidad-control**
   - ECS más simple que Kubernetes
   - Fargate abstrae gestión de servidores
   - Suficiente control para necesidades del proyecto

2. **Expertise del equipo**
   - Equipo ya familiar con AWS
   - No tienen experiencia con Kubernetes
   - Documentación de AWS ECS es excelente

3. **Costos predecibles**
   - Fargate tiene pricing por uso
   - No pagan por servidores infrautilizados
   - Costos competitivos comparado con EKS

4. **Tiempo de mercado**
   - Pueden desplegar en producción rápidamente
   - No necesitan contratar experto en Kubernetes
   - Integración nativa con otros servicios AWS

5. **Soporte y ecosistema**
   - AWS tiene soporte empresarial
   - Integración con CloudWatch, IAM, VPC
   - Comunidad activa

---

### 4. ¿QUÉ TRADE-OFFS ACEPTARON?

#### Ventajas aceptadas ✅
- **Simplicidad operacional**: No gestionan servidores ni clusters
- **Integración AWS**: Todo funciona nativamente con servicios AWS
- **Auto-scaling confiable**: AWS gestiona el escalado automáticamente
- **Menor costo operacional**: No necesitan equipo dedicado a infraestructura
- **Time-to-market rápido**: Desde decisión a producción en semanas, no meses

#### Desventajas aceptadas ⚠️
- **Vendor lock-in a AWS**: Migrar a otro cloud será costoso
- **Menos flexible que Kubernetes**: No pueden usar todo el ecosistema K8s
- **Limitaciones de Fargate**: Algunas configuraciones avanzadas no disponibles
- **Costos a largo plazo**: A muy alta escala, Kubernetes auto-gestionado podría ser más barato
- **Portabilidad limitada**: ECS no funciona fuera de AWS

**Trade-off crítico principal**:
Sacrificaron **portabilidad entre clouds y flexibilidad máxima** a favor de **simplicidad operacional y velocidad de implementación**.

**Trade-off secundario**:
Eligieron **lock-in con AWS** porque el equipo ya estaba comprometido con el ecosistema AWS.

---

### Reflexión Personal (Para tu análisis)

**Lo que me llama la atención**:
1. "Self-managed Kubernetes" fue rechazado explícitamente por overhead operacional
2. El expertise del equipo fue un factor decisivo (no solo factores técnicos)
3. Consideraron el costo a largo plazo pero lo pesaron contra velocidad de implementación
4. No eligieron la solución "más moderna" (Kubernetes), eligieron la apropiada para su contexto

**Aplicable a mi roadmap**:
- Cuando llegue a estudiar cloud computing, recordar que "más popular" ≠ "mejor para mi caso"
- El conocimiento del equipo es un constraint legítimo
- Simplicidad operacional es una consideración arquitectónica válida
- Vendor lock-in no siempre es malo si los beneficios lo justifican

**Lección sobre decisiones**:
Esta decisión muestra claramente el concepto de "architecturally significant": afecta costos, operaciones, velocidad de desarrollo, hiring, y capacidad de escalar. No es reversible sin costo significativo.

---

## ADR-001: PMD Project - Usar Architecture Decision Records {#adr-001-pmd}

### Contexto del Proyecto
**Fuente**: PMD (Programming Mistake Detector) - Herramienta open source de análisis estático de código  
**Proyecto**: PMD ha existido por más de 20 años  
**Fecha**: Septiembre 2022  
**Link**: https://github.com/pmd/pmd/blob/main/docs/pages/pmd/projectdocs/decisions/adr-1.md

---

### 1. ¿QUÉ DECIDIERON?

**Decisión**: Adoptar Architecture Decision Records como metodología para documentar decisiones del proyecto.

**Detalles específicos**:
- Usar el template simple propuesto por Michael Nygard
- Documentos en formato Markdown
- Almacenados junto al código fuente
- Integrados en el sitio de documentación generado
- Nuevos ADRs propuestos via Pull Request
- Estado inicial "Proposed", cambia a "Accepted" cuando se mergea el PR

**Nota meta**: ¡El primer ADR de un proyecto suele ser sobre adoptar ADRs! Es muy común.

---

### 2. ¿QUÉ ALTERNATIVAS CONSIDERARON?

El ADR lista explícitamente múltiples opciones:

| Alternativa | Descripción | Por qué NO |
|------------|-------------|-----------|
| **No documentar decisiones** | Seguir como hasta ahora | Pérdida de conocimiento, nuevos devs confundidos |
| **Wiki separada** | Confluence, GitHub Wiki | Separado del código, difícil de mantener sincronizado |
| **Comentarios en código** | Documentar en el código mismo | Solo visible para quien lea ese archivo específico |
| **Issues de GitHub** | Documentar en issues | Se pierden en el historial, no están organizados |
| **Documento único grande** | Un solo doc con todas las decisiones | Difícil de mantener, conflictos de merge |
| **Templates más complejos** | IBM UMF, Tyree & Akerman | Overhead excesivo para proyecto open source |

**Recursos que consultaron**:
- https://github.com/joelparkerhenderson/architecture-decision-record
- https://adr.github.io/
- Blog post original de Michael Nygard

---

### 3. ¿POR QUÉ ELIGIERON ESTA OPCIÓN?

**Razones principales**:

1. **Problema de conocimiento histórico**
   > "PMD has grown over 20 years as an open-source project. Along the way many decisions have been made, but they are not explicitly documented. PMD is also developed by many individuals and the original developers might not even be around anymore."

2. **Onboarding de nuevos desarrolladores**
   - Sin ADRs, nuevos devs no entienden por qué se tomaron decisiones
   - Pueden ignorar decisiones pasadas sin entender consecuencias
   - O pueden tener miedo de cambiar cosas sin entender el razonamiento original

3. **Simplicidad del formato**
   - Template de Nygard es minimalista pero suficiente
   - Markdown es familiar para todos los desarrolladores
   - No requiere herramientas especiales

4. **Integración con workflow existente**
   - Pull requests son el proceso de revisión que ya usan
   - ADRs viven junto al código
   - Aparecen en el sitio de documentación automáticamente

---

### 4. ¿QUÉ TRADE-OFFS ACEPTARON?

#### Ventajas aceptadas ✅
- **Conocimiento preservado**: Contexto y razonamiento no se pierde
- **Onboarding mejorado**: Nuevos devs entienden el "por qué"
- **Calidad de decisiones**: Escribir ADR fuerza a pensar claramente
- **Consenso explícito**: PR review asegura que todos estén alineados
- **Historial auditable**: Git guarda quién decidió qué y cuándo

#### Desventajas aceptadas ⚠️
- **Tiempo adicional**: Escribir ADR toma tiempo
- **Overhead de proceso**: No todas las decisiones necesitan ADR
- **Puede ser percibido como burocracia**: Algunos devs resistentes (como Joe en el ejemplo de IBM)
- **Requiere disciplina**: Fácil de olvidar escribir ADRs

**Cita clave del ADR**:
> "Explicitly documenting decisions has the benefit that new developers joining the projects know about the decisions and can read the context and consequences of the decisions. This will likely also improve the overall quality as the decisions need to be formulated and written down. Everybody is on the same page. However, this also adds additional tasks, and it takes time to write down and document the decisions."

**Trade-off crítico**:
Aceptaron **invertir tiempo en documentación** a cambio de **no perder 20 años de conocimiento arquitectónico**.

---

### Reflexión Personal (Para tu análisis)

**Lo que me llama la atención**:
1. ¡El primer ADR es sobre adoptar ADRs! Esto muestra que la metodología misma es una decisión arquitectónica
2. PMD tiene 20 años y recién están adoptando ADRs - nunca es "demasiado tarde"
3. Reconocen explícitamente que hay overhead, pero deciden que vale la pena
4. Mencionan el riesgo de que se convierta en burocracia

**Por qué es relevante para mí**:
- Este ADR es mi caso de uso EXACTO: Estoy en el punto donde decido si usar ADRs
- Veo que incluso proyectos maduros adoptan ADRs porque resuelven problemas reales
- El "Change History" al final muestra que los ADRs pueden evolucionar

**Aplicable a mi roadmap**:
- Mi "ADR-000" podría ser exactamente como este: "Por qué usar ADRs en mi roadmap de Solutions Architect"
- Entiendo que escribir ADRs es una inversión de tiempo con retorno a largo plazo
- Veo que puedo empezar simple (template de Nygard) y agregar complejidad si es necesario

---

## Síntesis: Patrones Comunes en los 5 ADRs

Después de analizar estos 5 ADRs reales, emergen patrones claros:

### Patrón 1: Contexto es Rey 👑
Todos los ADRs dedican espacio significativo a explicar **por qué** la decisión era necesaria. No asumen que el lector sabe el problema.

### Patrón 2: Alternativas Explícitas 🔄
Los mejores ADRs listan qué más consideraron y **por qué** rechazaron esas opciones. Esto previene que alguien más tarde pregunte "¿por qué no usamos X?".

### Patrón 3: Trade-offs Honestos ⚖️
Ningún ADR pretende que su decisión es perfecta. Todos documentan las desventajas aceptadas.

### Patrón 4: Decisiones Contextuales 🎯
Las decisiones "correctas" dependen del contexto:
- AWS eligió GitFlow porque distribuyen paquetes
- Backstage eligió YAML por familiaridad con Kubernetes  
- IBM Watson eligió stateless para escalabilidad cloud
- ECS Fargate ganó por simplicidad sobre Kubernetes
- PMD adoptó ADRs después de 20 años

### Patrón 5: No Siempre Eligen lo "Mejor" Técnicamente 🎓
- No eligieron Kubernetes (lo "mejor"), eligieron ECS (lo apropiado)
- No usaron su formato interno probado, usaron YAML por adopción
- No documentaban decisiones, ahora sí (cambio de proceso, no técnico)

---

## Ejercicio de Reflexión Final

Para cada ADR, pregúntate:

1. **¿Cuál fue la restricción más importante que influyó en la decisión?**
   - AWS: Modelo de distribución (paquete vs deployment)
   - Backstage: Adopción de la comunidad
   - IBM Watson: Necesidad de escalar horizontalmente
   - ECS Fargate: Expertise del equipo y velocidad
   - PMD: Historia de 20 años sin documentación

2. **¿Qué pasaría si en 2 años necesitan revertir la decisión?**
   - ¿Cuál sería más fácil de revertir?
   - ¿Cuál sería más costoso?
   - ¿Cuáles tienen "lock-in" y cuáles no?

3. **¿Cómo aplicaría cada lección a mi proyecto de N8N?**
   - GitFlow: ¿Necesito branches para mis workflows?
   - YAML: ¿Qué formato usar para configuración?
   - Stateless: ¿Mis workflows deben ser stateless?
   - Cloud: ¿Hosting local o cloud para N8N?
   - ADRs: ¿Documento mis decisiones desde el inicio?

---

## Próximos Pasos

Has completado el análisis de 5 ADRs reales. Ahora:

1. ✅ **Completaste**: Análisis de 5 ADRs según criterios establecidos
2. 📝 **Siguiente**: Crear tu propio ADR-000 sobre adoptar ADRs en tu roadmap
3. 🎯 **Meta Día 2**: Internalizar el pensamiento de "contexto → alternativas → decisión → trade-offs"

**Tiempo invertido**: ~2-3 horas de lectura y análisis profundo  
**Valor generado**: Comprensión real de cómo se toman decisiones arquitectónicas en proyectos reales

---

**Documento creado**: 21 de enero, 2026  
**Parte de**: Fase 0, Día 2 - Inmersión en ADRs  
**Student**: Pablo - Solutions Architect en formación  
**Fuentes**: AWS Prescriptive Guidance, Spotify Backstage, IBM Watson WIRE Team, PMD Project, y ejemplos de mejores prácticas
