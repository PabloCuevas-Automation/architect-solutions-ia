# ADR-202: Patrón de Queries PostgreSQL en N8N — Expresiones Inline

**Fecha**: 25 de abril 2026  
**Estado**: ✅ Aceptada  
**Tipo**: Táctica — patrón de implementación obligatorio  
**Audiencia**: 🔒 Personal  
**Proyecto**: PA1 — sistema-leads-pablocuevas (aplica a todos los proyectos N8N + PostgreSQL)

---

## Contexto

N8N ofrece dos formas de pasar valores a queries PostgreSQL:

1. **Query Parameters**: un array `[$1, $2, $3]` donde cada elemento
   mapea a un placeholder en el SQL
2. **Expresiones inline**: `{{ $json.campo }}` directamente dentro
   del texto del SQL

Durante la construcción de PA1, el campo Query Parameters produjo errores
sistemáticos (`there is no parameter $3`, `invalid input syntax for type json`,
`Token "$" is invalid`) que consumieron múltiples iteraciones de debugging.

---

## El problema con Query Parameters

El campo Query Parameters parsea el contenido como texto antes de enviarlo
a PostgreSQL. Cualquier carácter especial dentro de un valor rompe ese
parsing:

- Una **coma** dentro de un string (ej: `"Siamo uno studio, vorremmo..."`)
  se interpreta como separador del array — el array se descompone mal
- **Comillas** dentro de un JSON serializado rompen la delimitación
  de strings en el array
- Un **timestamp** sin comillas (`2026-04-25T17:04:54.167Z`) no es
  JSON válido dentro del array
- En modo `fx` (expression), N8N envía el array entero como un solo
  parámetro `$1` en lugar de elementos separados

El resultado: el número de parámetros que recibe PostgreSQL no coincide
con los placeholders `$1...$N` del SQL, y la query falla.

---

## Decisión

**Usar expresiones `{{ $json.campo }}` directamente en el SQL.
Query Parameters siempre vacío.**

```sql
-- ✅ CORRECTO — expresiones inline
INSERT INTO leads (nome, email, messaggio)
VALUES (
  '{{ $json.body.nome }}',
  '{{ $json.body.email }}',
  '{{ $json.body.messaggio }}'
)

-- ❌ INCORRECTO — Query Parameters
INSERT INTO leads (nome, email, messaggio)
VALUES ($1, $2, $3)
-- Con Query Parameters: [ {{$json.body.nome}}, {{$json.body.email}}, {{$json.body.messaggio}} ]
```

**Por qué funciona:**
N8N evalúa las expresiones `{{ }}` antes de construir el SQL. PostgreSQL
recibe SQL ya formado con valores reales embebidos — sin arrays intermedios,
sin parsing ambiguo. Las comas dentro de strings quedan dentro de las
comillas simples y PostgreSQL las interpreta como texto.

---

## Regla operativa

> En nodos PostgreSQL de N8N: expresiones `{{ $json.campo }}` directamente
> en el SQL. Query Parameters siempre vacío. Sin excepciones.

**Para strings**: envolver en comillas simples `'{{ $json.campo }}'`  
**Para números**: sin comillas `{{ $json.campo }}`  
**Para JSONB con `json_build_object`**: los valores escalares van sin comillas
si son números, con comillas si son strings

---

## Consecuencias

### Positivas ✅
- Elimina la clase completa de errores de parsing de Query Parameters
- Funciona con cualquier contenido en los valores — comas, comillas,
  caracteres especiales, JSON anidado
- El SQL es legible — los valores están donde se usan

### Negativas ⚠️
- Técnicamente es interpolación de strings, no queries parametrizadas
- Para valores de usuario (PII del formulario) en un sistema de mayor
  exposición, evaluar si añadir sanitización explícita antes del nodo SQL
- N8N muestra advertencia "Consider using query parameters to prevent
  SQL injection" — la advertencia es válida en principio pero el campo
  Query Parameters está roto en la práctica para valores complejos

---

## Referencias

- PA1 Sesión 6 (25/04/2026) — descubierto durante construcción de nodos
  de registro de eventos
- Aplica a todos los nodos PostgreSQL del workflow de PA1 y a cualquier
  proyecto futuro con N8N + PostgreSQL

---

**Creado**: 25 de abril 2026  
**Autor**: Pablo Cuevas  
**Próxima revisión**: Si N8N lanza una versión que corrija el comportamiento
del campo Query Parameters
