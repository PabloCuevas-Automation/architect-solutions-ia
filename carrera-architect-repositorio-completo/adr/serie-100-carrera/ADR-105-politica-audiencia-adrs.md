# ADR-105: Política de Audiencia de ADRs — Internos vs Entregables

**Serie**: 100 — Decisiones de aprendizaje y carrera  
**Estado**: ✅ Aceptada  
**Fecha**: 16 de abril 2026  
**Tipo**: Estratégica — afecta cómo se clasifica y entrega documentación en todos los proyectos

---

## Contexto

El sistema de ADRs de Pablo cubre decisiones de naturaleza muy distinta: algunas
existen porque Pablo está en Fase 1 de un roadmap de aprendizaje personal (como
posponer Python), otras existen porque el cliente necesita entender por qué el
sistema está construido así para poder confiar en él (como el almacén de evidencia
en GeoLabor). Hasta ahora todos los ADRs viven en el mismo espacio sin distinguir
a quién están dirigidos. Eso genera dos problemas: entregar ADRs personales a un
cliente expone información interna innecesaria, y no entregar ADRs relevantes al
cliente reduce la confianza en el sistema.

---

## La Decisión

Cada ADR tiene un campo de **audiencia** que determina si forma parte del
paquete de documentación entregable al cliente o si permanece en el repositorio
personal de Pablo.

---

## Los dos tipos y su criterio de clasificación

Un ADR es **entregable** cuando el cliente tiene un interés legítimo en entender
esa decisión para confiar en el sistema que recibe o para poder operarlo sin
depender de Pablo. La pregunta de clasificación es: *¿necesita el cliente entender
esta decisión para confiar en el sistema o para poder mantenerlo?* Si la respuesta
es sí, el ADR es entregable.

Un ADR es **personal** cuando la decisión existe por razones internas de Pablo:
su roadmap de aprendizaje, las limitaciones de su stack actual en Fase 1, sus
trade-offs de tiempo y recursos como profesional en formación. El cliente no gana
nada leyendo estos ADRs y puede incluso perder confianza si percibe que las
decisiones del sistema dependen de circunstancias personales de quien lo construyó.

---

## Estructura de carpetas en cada proyecto

Dentro de la carpeta `adr/` de cada proyecto, dos subcarpetas.

La carpeta `adr/entregable/` contiene los ADRs que forman parte del paquete
de documentación al cliente. Están redactados en el idioma del cliente y en
lenguaje de negocio, no de aprendizaje. Explican decisiones del sistema, no
decisiones del arquitecto sobre su propio stack.

La carpeta `adr/personal/` contiene los ADRs de decisiones internas. Están
redactados en español (idioma de trabajo de Pablo durante Fase 1) y pueden
hacer referencia al roadmap de Fase 1, a ADRs de serie 100, y a trade-offs
de aprendizaje. Nunca se entregan al cliente.

---

## Regla sobre el idioma

Los ADRs entregables se redactan en el idioma del cliente al momento de la
entrega final del proyecto, no durante la construcción. Cambiar el idioma
durante la construcción genera fricción cognitiva innecesaria. El flujo
correcto es: construir y documentar en español, traducir al idioma del cliente
como último paso antes de la entrega.

---

## Aplicación a las series existentes

La serie 000 (principios universales) y la serie 100 (aprendizaje y carrera)
son siempre personales. Nunca se entregan a clientes.

Las series 200, 300 y siguientes (proyectos específicos) pueden contener ADRs
de ambos tipos. La clasificación se decide ADR por ADR en el momento de crearlo,
siguiendo el criterio de audiencia descrito arriba.

---

## Consecuencias

Como consecuencia positiva, la documentación entregable al cliente es siempre
limpia, relevante y profesional. No contiene información sobre el roadmap de
aprendizaje de Pablo ni sobre las limitaciones de su stack actual. El cliente
recibe exactamente lo que necesita para confiar en el sistema y para poder
operarlo.

Como deuda técnica consciente, los proyectos anteriores (PA1 incluido) no
tienen esta clasificación aplicada retroactivamente. Se aplica desde GeoLabor
en adelante. PA1 se clasifica cuando llegue a producción real.

---

**Autor**: Pablo Cuevas  
**Fecha**: 16 de abril 2026  
**Relacionado con**: ADR-001 (política de documentación de decisiones), ADR-104 (Arc42 + C4)
