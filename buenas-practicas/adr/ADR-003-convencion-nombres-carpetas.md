# ADR-003: Naming Conventions / Convenciones de Nombres

**Date / Fecha**: 2026-01-29  
**Status / Estado**: Accepted / Aceptada  
**Type / Tipo**: Global Standard / Estándar Global

---

## Decision / Decisión

**EN**: Use kebab-case (lowercase + hyphens) for all folder and file names in technical projects.

**ES**: Usar kebab-case (minúsculas + guiones) para todos los nombres de carpetas y archivos en proyectos técnicos.

---

## Context / Contexto

**EN**: Spaces in names (`ARCHITECT SOLUTIONS IA`) cause terminal errors requiring quotes:
```powershell
cd ARCHITECT SOLUTIONS IA  # Error
cd "ARCHITECT SOLUTIONS IA"  # Requires quotes
```

**ES**: Los espacios en nombres (`ARCHITECT SOLUTIONS IA`) causan errores en terminal requiriendo comillas:
```powershell
cd ARCHITECT SOLUTIONS IA  # Error
cd "ARCHITECT SOLUTIONS IA"  # Requiere comillas
```

---

## Rules / Reglas

### ✅ DO / HACER
```
architect-solutions-ia
data-privacy-audit
tipos-datos.js
```

### ❌ DON'T / NO HACER
```
ARCHITECT SOLUTIONS IA  (spaces / espacios)
MiCarpeta  (inconsistent case / mayúsculas inconsistentes)
mi.carpeta  (dots in names / puntos en nombres)
mi-carpeta_final  (mixed separators / separadores mezclados)
```

### Exceptions / Excepciones
- Config files / Archivos de configuración: `.env`, `.gitignore`, `README.md`
- File extensions / Extensiones: `.js`, `.md`, `.json`

---

## Alternatives / Alternativas

| Option / Opción | Pros | Cons / Contras | Decision / Decisión |
|-----------------|------|----------------|---------------------|
| **Spaces + quotes / Espacios + comillas** | Natural for GUI / Natural para GUI | Requires quotes, error-prone / Requiere comillas, propenso a errores | ❌ Rejected / Rechazada |
| **snake_case** | Works without quotes / Funciona sin comillas | Less standard for web / Menos estándar para web | ❌ Rejected / Rechazada |
| **camelCase** | Works without quotes / Funciona sin comillas | Not standard for folders / No estándar para carpetas | ❌ Rejected / Rechazada |
| **kebab-case** | No quotes, standard, readable / Sin comillas, estándar, legible | Must rename existing / Debo renombrar existentes | ✅ **Accepted / Aceptada** |

---

## Consequences / Consecuencias

### Positive / Positivas ✅
- **EN**: Simpler commands (no quotes needed)
- **ES**: Comandos más simples (sin comillas)
- **EN**: Cross-platform compatible
- **ES**: Compatible cross-platform
- **EN**: Professional standard (GitHub/npm/open source)
- **ES**: Estándar profesional (GitHub/npm/open source)

### Negative / Negativas ⚠️
- **EN**: Must rename existing folders (one-time cost)
- **ES**: Debo renombrar carpetas existentes (costo único)
- **EN**: Requires discipline to remember convention
- **ES**: Requiere disciplina para recordar convención

---

## Validation / Validación

**EN**: This decision is correct if:
- ✅ Can execute commands without quotes in 100% of cases
- ✅ Scripts work without modifications on Windows/Linux/Mac
- ✅ No "path not found" errors due to spaces
- ✅ Collaborators understand convention without explanation

**ES**: Esta decisión es correcta si:
- ✅ Puedo ejecutar comandos sin comillas en 100% de casos
- ✅ Scripts funcionan sin modificaciones en Windows/Linux/Mac
- ✅ No hay errores "path not found" por espacios
- ✅ Colaboradores entienden convención sin explicación

---

**Created / Creado**: 2026-01-29  
**Last Updated / Última Actualización**: 2026-01-29