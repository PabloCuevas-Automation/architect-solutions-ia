# Análisis Real de Eliminación de Cuentas - 27 Enero 2026

## Gmail (Google)

### ¿Dónde está el botón?
- **Path**: [Gestionar tu cuenta de Google > Datos y privacidad > Más opciones > Eliminar tu cuenta de Google.]
- **Tiempo para encontrar**: [Pocos minutos, muchas decisiones previas]
- **¿Escondido o visible?**: [Relativamente visible]

### ¿Qué me pregunta antes?
- **Paso 1 (Auditoría de Apps de Terceros)** Primero me manda a cambiar las depedencias de las aplicaciones conectadas a esta cuenta.
- Puedes borrar solo Gmail **(mantienes Drive, YouTube y el login)**. O borrar la Cuenta de Google completa **(lo borra todo)**.
- Pasa a los 30 días de "Arrepentimiento" **(Borrado Lógico)**
- **El periodo de "Purga Física" (Derecho al Olvido)** Una vez superado el periodo de gracia inicial "arrepentimiento":
  Eliminació Definitiva: Los sistemas de Google comienzan a sobrescribir los datos en sus centros de datos
Backups: Aquí viene el dato de arquitecto: aunque tu cuenta desaparezca de la "vista" de producción, tus datos pueden permanecer en las cintas de backup de Google por un periodo de hasta 60 a 90 días.

### Observaciones de diseño
[Veo que arquitectonicamente Gmail, estuvo pensada primeramente para la recopilacion de datos, por ende esos datos, que crean metadatos, y que son de gran valor para Google, no quiere perderlos. Por ende ha creado una red de arquitectura para que en cierta parte mis propios datos estan en un ambiente lock-in, por ende que sean dificil de desaparecer, porque buscan fomentar muchas depedencias con Gmail. Ya de por si se busca que sea tu IdP (Identity Provider). Google utiliza un sistema de Single Sign-On (SSO) ]

---

## Facebook (Meta)

### ¿Dónde está el botón?
- **Path**: [Configuración y privacidad > Centro de cuentas > Datos personales > Propiedad y control de la cuenta > Desactivación o eliminación.]
- **Tiempo para encontrar**: [Alto. Meta suele mover este menú con frecuencia para generar desorientación].
- **¿Escondido o visible?**: [Muy escondido tras capas de "Centro de Cuentas"].

### ¿Qué me pregunta antes?
- **Interferencia emocional**: Te muestra fotos de "amigos que te extrañarán".
- **Ofrecimiento de alternativa**: Te empuja a "Desactivar" (borrado lógico reversible) en lugar de "Eliminar" (Derecho al olvido).
- **Descarga de información**: Te advierte que perderás tus fotos y mensajes si no los descargas primero.

### Observaciones de diseño
[**Arquitectónicamente**, Meta no vende un servicio, vende un Grafo Social. El sistema está diseñado para que tu identidad sea un "nodo" conectado a otros. Si borras el nodo, debilitas el grafo. Por eso usan el periodo de 30 días de arrepentimiento como un fail-safe comercial: cualquier login accidental desde una app vinculada (como Instagram o un juego) restaura el sistema automáticamente, cancelando el proceso de borrado. Precisamente con esto busca ser un lock-in en las depedencias que tengamos con sus servicios.]

---

## Netflix

### ¿Dónde está el botón?
- **Path**: [Cuenta > Membresía y facturación > Cancelar membresía.] *(Nota: El borrado total del dato PII requiere email a privacy@netflix.com)*.
- **Tiempo para encontrar**: [Bajo]
- **¿Escondido o visible?**: [Visible (interesa reducir la fricción de entrada/salida)]

### ¿Qué me pregunta antes?
- **Degradación de plan**: Intenta evitar el *churn* ofreciendo planes más baratos.
- **Encuesta de salida**: Recolecta datos sobre el motivo del abandono.
- **Promesa de "Hielo"**: Notifica que guardarán perfiles y recomendaciones por 10 meses.

### Observaciones de diseño
[Modelo de **Suscripción Circular**. Su arquitectura es "elástica": permite entrar y salir con facilidad porque su valor es el consumo, no la identidad. Aplican **retención pasiva**: el sistema no muere, se pone en pausa para reducir el costo de adquisición futuro cuando el usuario decida regresar.]

---

## Pattern Identificado

### ¿Qué tienen en común los servicios que DIFICULTAN eliminar cuenta?
 - [ Que estan arquitectonicamente pensados para que recaigas sobre regresar a aquello que te ofrecen, desde un nivel emocional, hasta un nivel de cierta presion social (Gmail porque te da facilidad y se usa incluso a nivel lavorativo en muchos ambitos). (Facebook porque todos lo usan y si no estas fuera de un ambito socio-tecnologico). (Un ejemplo burdo para Netflix es que sale una nueva temporada de una serie que todo tu entorno habla, decides volver para ser parte).]
 - Las advertencias, los 3 las tienen, algunos con mayor o menor friccion, pero se te advierte de aquello que estas queriendo hacer.
 - Tus datos se mantienen, no entran rapido en el derecho del olvido, por ende al minimo contacto, se renueva el recuento para la eliminacion final.
 - Hay mas ejemplos estos son 3 que se me ocurrieron a grandes rasgos.

### ¿Por qué lo hacen?
[Te necesitan que seas parte, en Netflix hay una subscripcion de por medio que lo demuestra a mas claro ejemplo. Pero todos necesitan tus datos, ya que son parte fundamental de su modelo de negocio. Los datos son el poder real de las empresas, no su dinero.]

Posicionamiento como Arquitecto (Ética vs. Negocio)

### A favor del diseño actual (Visión Negocio):
1. **Reducción de Churn accidental**: Evita pérdidas de datos críticas por errores de usuario.
2. **Valor del Dato Agregado**: Los datos inactivos sirven para entrenar modelos de recomendación y retención.

### En contra del diseño actual (Visión Ética/Soberanía):
1. **Dark Patterns**: La dificultad de borrado en Meta viola el principio de transparencia.
2. **Deuda de Seguridad**: Mantener datos de usuarios inactivos crea un "Honeypot" de PII innecesario en caso de brecha de seguridad.

### Mi Decisión Arquitectónica:
"Implementaría una **Política de Puerta Abierta**. Si el servicio es excelente, no necesitas secuestrar al usuario. Diseñaría el borrado con una **Exportación Obligatoria en formato estructurado (JSON/MD)**. Facilitar la salida genera una confianza de marca que reduce el costo de adquisición a largo plazo y elimina riesgos legales de GDPR de raíz."

