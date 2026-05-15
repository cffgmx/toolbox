# Toolbox

Infraestructura modular de herramientas operativas integradas de forma nativa en la terminal de **Ubuntu LTS** o **Debian Stable**. Diseñada con énfasis en la automatización no intrusiva y la estabilidad del sistema; optimizada para la **Lenovo ThinkPad T14 Gen 1 (AMD Ryzen 5 PRO 4650U)**, aunque funciona en cualquier equipo compatible.

---

## 1. Instalación

### Dependencias

Cada comando requiere únicamente sus propias dependencias. Instale solo las que correspondan a los scripts que vaya a usar:

**`sincronizar`** — `rclone` y `rsync`:
```bash
sudo apt update && sudo apt install rclone rsync -y
```

**`contenedores`** — `distrobox` y `podman`:
```bash
sudo apt update && sudo apt install distrobox podman -y
```

> Los comandos `flock`, `xdg-user-dir`, `mountpoint`, `du` y `df` que usa `sincronizar` son utilidades estándar presentes en cualquier instalación de Ubuntu o Debian.

### Procedimiento

**1. Clonar el repositorio:**

```bash
git clone https://github.com/cffga/toolbox.git ~/toolbox
```

**2. Otorgar permisos de ejecución:**

```bash
chmod +x ~/toolbox/*
```

**3. Registrar en el `PATH`.** Añada el siguiente bloque al final de `~/.bashrc`:

```bash
# --- TOOLBOX PERSONAL ---
if [ -d "$HOME/toolbox" ]; then
    export PATH="$HOME/toolbox:$PATH"
fi
# ------------------------
```

**4. Activar en la sesión actual:**

```bash
source ~/.bashrc
```

A partir de este punto, todos los scripts del directorio `~/toolbox` estarán disponibles como comandos globales.

---

### Configurar Google Drive (requisito de `sincronizar`)

Ejecute el asistente interactivo de rclone:

```bash
rclone config
```

Siga estos pasos cuando el asistente lo solicite:

| Paso | Entrada |
| :--- | :--- |
| Tipo de operación | `n` (New remote) |
| Nombre | `gdrive` |
| Tipo de almacenamiento | Google Drive (verifique el número en su versión de rclone) |
| Client ID / Secret | Enter (dejar vacío) |
| Scope | `1` (Full access) |
| Service account file | Enter (dejar vacío) |
| Advanced config | `n` |
| Auto config | `y` — se abrirá el navegador para autorizar la cuenta |
| Confirmaciones finales | `y`, luego `y` |
| Salir | `q` |

---

## 2. Comandos disponibles

### `sincronizar`

Realiza un respaldo híbrido (nube + unidad local) con protecciones de integridad:

- **Exclusividad de ejecución:** usa un archivo de bloqueo (`flock`) para impedir ejecuciones concurrentes.
- **Protección anti-vacío:** aborta el proceso si el directorio de origen (`~/Documentos`) está vacío, evitando sobrescribir el destino con nada.
- **Respaldo en nube:** sincroniza con `gdrive:thinkpad` vía `rclone`. Los archivos eliminados localmente van a la papelera de Drive en lugar de borrarse de forma permanente.
- **Respaldo local:** copia incremental hacia unidad externa mediante `rsync`, con verificación de espacio disponible antes de iniciar.

Al terminar, imprime un resumen con el estado de cada destino (Exitoso / Fallido / Omitido) y el tiempo total transcurrido. El log completo se guarda en `~/.cache/sincronizar.log`.

> **Requisito de hardware**
> La unidad externa debe tener la etiqueta lógica exactamente como `Respaldo`. El script busca el punto de montaje en `/run/media/$USER/Respaldo`. Si la unidad no está conectada, el respaldo local se omite sin interrumpir el respaldo en nube.

---

### `contenedores`

Gestiona entornos Linux aislados mediante **Distrobox**, con un `HOME` propio para cada contenedor en `~/Contenedores distrobox/`. Presenta un menú interactivo con las siguientes opciones:

| Opción | Descripción |
| :--- | :--- |
| `1` Crear contenedor | Crea un nuevo contenedor con imagen seleccionada del catálogo, HOME aislado y nombre validado. Ofrece crear carpetas estándar (`Documentos`, `Descargas`, etc.) y entrar al entorno al terminar. |
| `2` Listar contenedores | Muestra todos los contenedores existentes con su estado actual. |
| `3` Entrar a un contenedor | Abre una sesión interactiva dentro del contenedor seleccionado. |
| `4` Apagar contenedor | Detiene un contenedor en ejecución. |
| `5` Añadir aplicación al menú | Exporta una aplicación instalada en el contenedor al menú de aplicaciones del sistema anfitrión. |
| `6` Eliminar aplicación del menú | Retira del menú del sistema una aplicación exportada desde Distrobox. |
| `7` Eliminar contenedor | Elimina el contenedor, su HOME y sus accesos directos. Requiere escribir el nombre exacto para confirmar. |
| `8` Salir | Cierra el script. |

**Catálogo de imágenes disponibles:**

- Ubuntu LTS (última versión estable)
- Debian Stable (última versión estable)
- Fedora (última versión)
- Arch Linux (Rolling Release)

---

## 3. Añadir nuevos scripts

La Toolbox es una infraestructura abierta. Para integrar una herramienta nueva:

1. Guarde el script en `~/toolbox/`. Se recomienda omitir la extensión (p. ej., `limpiar_temp` en lugar de `limpiar_temp.sh`).
2. Defina el intérprete en la primera línea del archivo:
   - Shell: `#!/bin/bash`
   - Python: `#!/usr/bin/env python3`
3. Otorgue permisos de ejecución: `chmod +x ~/toolbox/nombre_del_script`.
4. El comando quedará disponible de forma inmediata en cualquier terminal nueva.

---

## 4. Desinstalación

Para eliminar la Toolbox y revertir el sistema a su estado original:

**1. Eliminar archivos:**

```bash
rm -rf ~/toolbox
rm -f ~/.cache/sincronizar.lock ~/.cache/sincronizar.log
```

**2. Limpiar `~/.bashrc`:** abra el archivo (p. ej., con `nano ~/.bashrc`) y elimine el bloque `# --- TOOLBOX PERSONAL ---`.

**3. Recargar la configuración:**

```bash
source ~/.bashrc
```
