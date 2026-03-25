## Tabla: leads
- id: número entero autogenerado — NO es PII (identificador interno del sistema)
- nome: texto — SÍ es PII
- cognome: texto — SÍ es PII
- email: texto — SÍ es PII
- azienda: texto — NO es PII
- servizio: texto — NO es PII
- messaggio: texto — SÍ es PII (puede contener datos personales)
- timestamp_roma: fecha y hora — NO es PII
- accion: texto — NO es PII
- razon: texto — NO es PII
- prioridad: texto — NO es PII

## Tabla: eventos
- procesado: verdadero/falso — NO es PII directo
- lead_id: número entero — NO es PII directo (referencia al ID del lead)
- tipoCuenta: texto — NO es PII directo
- nivelRiesgo: texto — NO es PII directo
- esEmailLibre: verdadero/falso — NO es PII directo
- esDesechable: verdadero/falso — NO es PII directo
- abstract_status: texto — NO es PII directo
- abstract_smtp_valid: verdadero/falso — NO es PII directo
- abstract_live_site: verdadero/falso — NO es PII directo

Nota: ningún campo es PII en aislamiento, pero al estar 
vinculados a un lead identificable se tratan con las mismas 
reglas de retención.

## Relación entre tablas

- La tabla leads y eventos están conectadas ya que ambas en su 
conjunto generan el registro completo de lo que recibimos, 
procesamos y notificamos. 

En leads viven los datos que llegan fijos desde el formulario 
más la decisión final del sistema. En eventos viven los datos 
que se construyeron durante el proceso.

La conexión técnica: cada lead tiene un ID único autogenerado. 
La tabla eventos guarda ese ID para indicar a qué lead 
pertenece cada registro.

## Política de retención
- Descartados — 60 días: necesito auditar si mi clasificador funciona correctamente
- En revisión — 6 meses: necesito tiempo suficiente para hacer seguimiento comercial
- Procesados — 12 meses: son leads activos con posibilidad de conversión