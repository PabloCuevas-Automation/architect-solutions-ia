# Cierre Día 1 - 27 Enero 2026
**Eje Central:** Soberanía del Dato y Auditoría de Sistemas

## ¿Qué aprendí hoy?
- **Identificación de PII**: Aprendí a distinguir entre datos de identidad (directos) e identificadores indirectos (IP, metadatos, comportamiento).
- **Derecho al Olvido (GDPR Art. 17)**: Comprendí que no es un borrado instantáneo, sino un proceso de purga que pasa por el borrado lógico, desvinculación y finalmente borrado físico.
- **Ciclo de Vida del Backup**: La importancia de la regla 3-2-1 (3 copias, 2 medios, 1 offsite) para evitar puntos únicos de fallo.
- **Fricción por Diseño**: Entendí cómo las empresas usan la arquitectura para retener usuarios, ya sea por dependencia técnica (SSO) o emocional.

## Insights arquitectónicos capturados:
- **Gmail**: Retención física en backups de 60-90 días; es el Punto Único de Fallo por ser el IdP (Identity Provider).
- **Facebook**: Su valor reside en el Grafo Social; borrar la cuenta debilita el activo de la empresa, de ahí su alta fricción.
- **Dark Patterns**: Uso de interferencia emocional y menús anidados para dificultar el ejercicio de derechos legales.
- **Lock-in**: El uso de formatos propietarios vs. formatos abiertos (Markdown) como mecanismo de soberanía o esclavitud técnica.
- **Metadatos vs. Contenido**: En servicios como WhatsApp, el valor para la empresa reside en el metadato (quién, cuándo, dónde) más que en el mensaje cifrado.

## Aplicación personal (soberanía técnica):
He validado mi flujo de trabajo actual bajo los lentes del arquitecto:
- **Backup local**: Utilizo conversaciones con Claude/Gemini exportadas a `.md`.
- **Formato portable**: Evito el vendor lock-in; si Claude desaparece, mi conocimiento estructurado permanece en mi disco.
- **Capa de Visualización**: Uso PDFs para lectura, pero mantengo el `.md` como la "fuente de verdad" para futuras IAs.

## ¿Qué me sorprendió más?
1. **La persistencia fiscal**: Que Netflix o PS Store deban guardar mis datos financieros por 5-10 años por ley, independientemente de mi derecho al olvido.
2. **La ilusión del borrado**: Descubrir que el botón "Eliminar" es, en realidad, un cambio de estado en una base de datos (`active: false`) y no una desaparición inmediata de los bits.

## Decisión clave que tomé:
*Priorizar el almacenamiento local y estructurado en Markdown para toda mi formación, limitando el uso de herramientas SaaS cerradas (como Notion) solo para visualización efímera, nunca como repositorio único de mi propiedad intelectual.*

## Validación de comprensión:
Si mañana un cliente pregunta "¿Es PII el email?", respondo:
> "Sí, es un identificador directo. Su tratamiento requiere consentimiento explícito, una finalidad documentada y la garantía técnica de que puede ser eliminado o anonimizado. Arquitectónicamente, esto implica diseñar flujos de borrado en cascada en todas nuestras bases de datos y backups."

## Preparación Día 2 (28 Enero):
- [ ] **Fase 0.5**: Iniciación en la Terminal (Línea de comandos).
- [ ] **ADR-002**: Decisión sobre estrategia de backups personales.
- [ ] **Práctica**: Manipulación de archivos `.md` mediante comandos `mkdir`, `cd` y `ls`.