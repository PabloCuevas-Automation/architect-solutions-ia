# Terminal Basics / Fundamentos de Terminal

**Date / Fecha**: 2026-01-29  
**Context / Contexto**: Fase 0.5 - Pre-requisitos técnicos

---

## Why Terminal Matters / Por Qué Importa el Terminal

**EN**: 
- **Control**: You know EXACTLY what command executed
- **Automation**: Commands can be scripted and repeated
- **Professional**: Standard tool for architects and engineers
- **Scalability**: 1 command can affect 100 files

**ES**:
- **Control**: Sabes EXACTAMENTE qué comando se ejecutó
- **Automatización**: Los comandos se pueden script y repetir
- **Profesional**: Herramienta estándar para arquitectos e ingenieros
- **Escalabilidad**: 1 comando puede afectar 100 archivos

---

## Essential Commands / Comandos Esenciales

### Navigation / Navegación

| Command / Comando | What it Does / Qué Hace | Example / Ejemplo |
|-------------------|--------------------------|-------------------|
| `pwd` | Print Working Directory / Muestra dónde estás | `pwd` → `C:\Users\pablo` |
| `cd` | Change Directory / Cambiar directorio | `cd architect-solutions-ia` |
| `cd ..` | Go up one level / Subir un nivel | `cd ..` |
| `cd ~` | Go to home / Ir a carpeta principal | `cd ~` (Linux/Mac) |
| `ls` | List files / Listar archivos | `ls` |
| `ls -Force` | List all (including hidden) / Listar todo (incluye ocultos) | `ls -Force` |

### File Operations / Operaciones de Archivos

| Command / Comando | What it Does / Qué Hace | Example / Ejemplo |
|-------------------|--------------------------|-------------------|
| `New-Item` | Create file / Crear archivo | `New-Item app.js -ItemType File` |
| `mkdir` | Create folder / Crear carpeta | `mkdir ejercicios-js` |
| `cat` | Show file content / Mostrar contenido | `cat app.js` |
| `cp` | Copy / Copiar | `cp app.js backup.js` |
| `mv` | Move or rename / Mover o renombrar | `mv app.js main.js` |
| `rm` | Delete / Eliminar | `rm test.js` |
| `rm -r` | Delete folder / Eliminar carpeta | `rm -r old-folder` |

### Useful Shortcuts / Atajos Útiles

| Shortcut / Atajo | What it Does / Qué Hace |
|------------------|--------------------------|
| `Tab` | Autocomplete / Autocompletar |
| `Ctrl + C` | Cancel command / Cancelar comando |
| `clear` | Clear screen / Limpiar pantalla |
| `explorer .` | Open current folder in GUI / Abrir carpeta en GUI (Windows) |

---

## Important Rules / Reglas Importantes

### 1. Spaces in Names / Espacios en Nombres

**EN**: If a folder/file has spaces, use quotes:
```powershell
cd "My Folder"  # With quotes / Con comillas
```

**ES**: Si una carpeta/archivo tiene espacios, usa comillas:
```powershell
cd "Mi Carpeta"  # Con comillas
```

**Best practice / Mejor práctica**: Use `kebab-case` to avoid this:
```powershell
cd my-folder  # No quotes needed / Sin comillas
```

### 2. Current Directory / Directorio Actual

**EN**: Always know where you are:
```powershell
pwd  # Shows: C:\Users\pablo\Desktop\PROGRAMACION
```

**ES**: Siempre sabe dónde estás:
```powershell
pwd  # Muestra: C:\Users\pablo\Desktop\PROGRAMACION
```

### 3. Relative vs Absolute Paths / Rutas Relativas vs Absolutas

**Absolute / Absoluta**:
```powershell
cd C:\Users\pablo\Desktop\PROGRAMACION\architect-solutions-ia
```

**Relative / Relativa**:
```powershell
cd architect-solutions-ia  # From current location / Desde ubicación actual
cd ..  # Go up / Subir
cd ../other-folder  # Go up then into other / Subir y entrar a otra
```

---

## Workflow Example / Ejemplo de Flujo de Trabajo
```powershell
# 1. Check where you are / Verificar dónde estás
pwd

# 2. List files / Listar archivos
ls

# 3. Create new folder / Crear nueva carpeta
mkdir new-project

# 4. Enter folder / Entrar a carpeta
cd new-project

# 5. Create file / Crear archivo
New-Item index.js -ItemType File

# 6. Verify / Verificar
ls

# 7. Go back / Volver
cd ..
```

---

## Terminal vs GUI / Terminal vs Interfaz Gráfica

### When to Use Terminal / Cuándo Usar Terminal

**EN**:
- ✅ Repetitive tasks (creating 10 folders)
- ✅ Working with Git
- ✅ Installing software (npm, pip, Docker)
- ✅ Debugging (viewing logs)
- ✅ Automation scripts

**ES**:
- ✅ Tareas repetitivas (crear 10 carpetas)
- ✅ Trabajar con Git
- ✅ Instalar software (npm, pip, Docker)
- ✅ Debugging (ver logs)
- ✅ Scripts de automatización

### When to Use GUI / Cuándo Usar Interfaz Gráfica

**EN**:
- ✅ One-time reorganization (large restructure)
- ✅ Visual file comparison
- ✅ Exploring unfamiliar folders

**ES**:
- ✅ Reorganización única (reestructuración grande)
- ✅ Comparación visual de archivos
- ✅ Explorar carpetas desconocidas

---

## Key Concept: Terminal as Mental Model / Concepto Clave: Terminal como Modelo Mental

**EN**:
Terminal is your "mental interface" to the file system:
- `pwd` = "Where am I?"
- `ls` = "What's here?"
- `cd` = "Go to X"

**ES**:
Terminal es tu "interfaz mental" del sistema de archivos:
- `pwd` = "¿Dónde estoy?"
- `ls` = "¿Qué hay aquí?"
- `cd` = "Ir a X"

---

## Common Mistakes / Errores Comunes

### 1. Forgetting Quotes with Spaces / Olvidar Comillas con Espacios
```powershell
❌ cd My Folder  # Error
✅ cd "My Folder"  # Correct / Correcto
```

### 2. Wrong Slash Direction / Dirección de Barra Incorrecta

**EN**: Windows uses backslash `\`, but forward slash `/` also works in PowerShell.

**ES**: Windows usa barra invertida `\`, pero barra normal `/` también funciona en PowerShell.
```powershell
✅ cd C:\Users\pablo
✅ cd C:/Users/pablo  # Also works / También funciona
```

### 3. Not Verifying Location / No Verificar Ubicación
```powershell
# Always check where you are before creating files
# Siempre verifica dónde estás antes de crear archivos
pwd
ls
New-Item important-file.js -ItemType File
```

---

## Next Steps / Próximos Pasos

**EN**:
1. Practice: Create 5 folders with terminal
2. Practice: Navigate between them without GUI
3. Learn Git: Version control with terminal

**ES**:
1. Practica: Crea 5 carpetas con terminal
2. Practica: Navega entre ellas sin GUI
3. Aprende Git: Control de versiones con terminal

---

## Resources / Recursos

**EN**:
- [Codecademy: Learn the Command Line](https://www.codecademy.com/learn/learn-the-command-line)
- [PowerShell Documentation](https://docs.microsoft.com/en-us/powershell/)

**ES**:
- [Tutorial PowerShell en español](https://docs.microsoft.com/es-es/powershell/)

---

**Created / Creado**: 2026-01-29  
**Last Updated / Última Actualización**: 2026-01-29
```

---

## 📋 Checklist Final

Antes de continuar con Git + JavaScript:
```
✅ Estructura reorganizada en kebab-case (GUI)
✅ ADR-003 creado (decidir: español, inglés, o bilingüe)
✅ terminal-basics.md guardado en fase-0.5/terminal/
✅ Decisión de idiomas tomada conscientemente