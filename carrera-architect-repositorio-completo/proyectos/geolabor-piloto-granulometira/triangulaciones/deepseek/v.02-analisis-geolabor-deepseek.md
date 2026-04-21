# Respuesta a tus inquietudes sobre el piloto GeoLabor

**Fecha**: 16 de abril 2026  
**Para**: Pablo Cuevas  
**IA**: DeepSeek  

---

## 1. Tu idea de selección de plantilla manual

**Es excelente y resuelve el problema de raíz.**  
En lugar de intentar que el sistema "adivine" qué tipo de prueba es (lo cual era un riesgo alto), la interfaz local puede tener un selector desplegable:
[ ] Granulometría simple (0-30 mm)
[ ] Granulometría con sedimentación
[ ] Granulometría con cuarteo (sin sed.)
... (futuras pruebas)

text

**Ventajas:**
- **Escalabilidad inmediata**: cuando quieras agregar límites de Atterberg, compresión simple, etc., solo agregas una opción al selector y mapeas la plantilla correspondiente.
- **Cero ambigüedad**: el técnico sabe exactamente qué ensayo hizo.
- **Cumple GDPR por diseño**: sin necesidad de extraer metadatos del cliente (committente, località) del escaneo, solo los datos técnicos de la tabla de tamices.

**Sugerencia de implementación:**
1. Carpeta en el servidor con todas las plantillas Excel base nombradas claramente (ej. `plantilla_gran_simple.xlsx`, `plantilla_gran_sedimentacion.xlsx`).
2. Al seleccionar el tipo, el frontend envía un parámetro `tipo_prueba` junto con la imagen al webhook de N8N.
3. N8N usa ese parámetro para elegir qué archivo base copiar y rellenar.

---

## 2. Sobre "scope" (alcance)

**Scope** significa el **límite acordado de lo que incluye el proyecto**.  
En tu documento original ya lo definiste muy bien: solo granulometría estándar + triángulo simplificado. Lo demás (Atterberg, clasificación USCS completa, etc.) es **fuera del scope del piloto gratuito**.

Dices que ya lo has hablado con ellos. Perfecto. Asegúrate de que esté **por escrito** (un correo basta) para evitar que después digan "ah, pero pensé que también incluía...".

Si el piloto funciona, la versión de pago puede incluir las otras pruebas. Eso es una estrategia comercial sana.

---

## 3. Preocupación por el flujo en N8N con generación de Excel

Entiendo tu inquietud. N8N **no tiene nodos nativos potentes para manipular Excel con gráficos**. Pero se puede integrar fácilmente con un script Python que haga el trabajo pesado.

**Flujo propuesto dentro de N8N:**
[Webhook] → [Validación de token] → [Ejecutar script Python] → [Devolver archivo Excel]

text

### ¿Cómo se ejecuta Python desde N8N?

Tienes dos opciones en tu VPS con Docker:

**Opción A (más simple)**: Nodo **Execute Command** que llama a un script `.py` pasando los datos como argumento o archivo temporal.

```bash
python3 /app/scripts/generar_excel.py --tipo simple --datos '{"pesos": [62.6, 86.4,...]}'
Opción B (más limpia): Crear un microservicio simple con Flask dentro del mismo VPS, y N8N hace una petición HTTP a http://localhost:5000/generar-excel.

¿Y el gráfico triangular predefinido?
Tu idea es totalmente viable y la más inteligente:

Creas una vez una plantilla Excel (plantilla_triangulo.xlsx) que ya tenga:

El triángulo de fondo (como imagen PNG o dibujado con formas).

Un gráfico de dispersión (scatter) con una sola serie de un punto, cuyas coordenadas X e Y se calculan a partir de los porcentajes de ghiaia, sabbia y limo+argilla.

Celdas ocultas donde se escriben esos tres porcentajes, y el gráfico se actualiza solo.

El script Python, además de llenar Foglio2, escribe los tres porcentajes en las celdas correspondientes de la hoja del triángulo.

Cuando el técnico abre el Excel, el punto aparece en la zona correcta y la clasificación (ej. "Gr", "saGr") puede mostrarse con una fórmula BUSCARV o SI anidados.

Resultado: No necesitas generar el gráfico desde cero cada vez. Solo insertas datos en la plantilla pre-armada.

4. Si el OCR falla
Tienes razón: si la IA no lee bien, el técnico simplemente transcribe a mano como siempre. Eso es resiliencia real.

Para minimizar fallos:

Validación visual en la interfaz: Mostrar la imagen al lado de la tabla editable, con la zona de los pesos resaltada.

Botón "Reintentar OCR" por si el primer intento fue malo.

Log de errores para que tú puedas revisar qué imágenes fallaron y ajustar el prompt.

Si el servidor de Anthropic se cae (raro), el sistema puede mostrar un mensaje claro: "Servicio de OCR no disponible. Por favor, transcriba manualmente o intente más tarde."

5. Demo grabada
Una demo grabada es un video corto (2-3 minutos) que muestra:

El problema actual: técnico transcribiendo a mano.

La solución: subir escaneo, revisar datos en la web, descargar Excel generado.

Resultado: Excel abierto con curva granulométrica y triángulo clasificado.

Cómo hacerla:

Graba la pantalla con OBS Studio (gratuito).

Narra en italiano (o con subtítulos) para que sea entendible para clientes locales.

Súbela a YouTube como "no listado" o a Google Drive.

Esto sirve para:

Mostrar a tu jefe el valor del piloto.

Incluir en tu portfolio profesional.

Enseñar a potenciales clientes cómo funciona el sistema.

6. Interpolación logarítmica
Me alegra que quieras implementarla. Es un gran valor añadido que diferencia tu solución de una simple automatización.

Te dejo un pseudocódigo en Python para que lo tengas a mano:

python
import math

def interpolar_dx(passante_deseado, puntos):
    """
    puntos: lista de tuplas (diametro_mm, passante_%)
    ordenada por diámetro ascendente.
    """
    for i in range(len(puntos)-1):
        d1, p1 = puntos[i]
        d2, p2 = puntos[i+1]
        if p1 >= passante_deseado >= p2:
            # Interpolación logarítmica
            log_dx = math.log(d1) + (math.log(d2) - math.log(d1)) * (passante_deseado - p1) / (p2 - p1)
            return math.exp(log_dx)
    return None  # Fuera de rango
Luego calculas D10, D30, D60 y de ahí Cu y Cc.

7. Cálculo de pasante con cuarteo
Confirmo: en las pruebas con Foglio4, no se deben tocar los pesos de Foglio2 directamente. El script debe escribir los pesos brutos en las celdas correspondientes de Foglio4 (B21:B28) y dejar que Excel haga la magia.

Eso significa que el mapeo de celdas para el script cambia según el tipo de plantilla seleccionada. Lo tendrás documentado en una tabla de configuración.

8. Múltiples muestras
La interfaz puede preguntar después de la confirmación de datos:

"Se han detectado 3 muestras. ¿Desea generar un Excel por muestra o un solo archivo con hojas separadas?"

Y según la elección, el script genera uno o varios archivos.

Resumen ejecutivo para tu próximo paso
Diseñar la interfaz web local con:

Selector de tipo de prueba.

Zona de arrastrar/soltar imagen.

Tabla editable de confirmación.

Preparar las plantillas Excel base (simple, sedimentación, cuarteo) y la del triángulo.

Crear script Python que reciba JSON y genere Excel según plantilla seleccionada.

Montar el flujo en N8N que orqueste: recibir imagen → llamar a Claude Vision → mostrar confirmación (vía frontend) → ejecutar script Python → entregar archivo.

Probar con casos reales (las imágenes que tienes).

El proyecto está muy bien encaminado. Tienes claros los riesgos y las soluciones. Cualquier duda puntual sobre N8N o Python, puedes volver a preguntar.