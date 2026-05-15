# Toolbox

Scripts personales para mi flujo de trabajo diario en Linux. Desarrollados sobre **Ubuntu**, aunque funcionan en cualquier sistema basado en Debian.

| Comando | Descripción |
| :--- | :--- |
| `sincronizar` | Respalda `~/Documentos` en Google Drive y en una unidad externa. |
| `contenedores` | Crea y gestiona entornos Linux aislados con Distrobox. |

---

## 1. Instalación

### Dependencias

Cada comando requiere únicamente sus propias dependencias. Instale solo las que correspondan a los scripts que vaya a usar:

**`sincronizar`** (`rclone` y `rsync`):
```bash
sudo apt update && sudo apt install rclone rsync -y
```

**`contenedores`** (`distrobox` y `podman`):
```bash
sudo apt update && sudo apt install distrobox podman -y
```

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
| Auto config | `y` (se abrirá el navegador para autorizar la cuenta) |
| Confirmaciones finales | `y`, luego `y` |
| Salir | `q` |

---

## 2. Comandos disponibles

### `sincronizar`

Hace una copia de seguridad de `~/Documentos` en dos lugares al mismo tiempo: Google Drive y una unidad externa. Si alguno de los dos no está disponible (por ejemplo, el disco no está conectado), respalda en el otro sin interrumpirse.

Tiene algunas protecciones incorporadas: no se ejecuta dos veces en paralelo, y si detecta que la carpeta de origen está vacía, cancela la operación para no borrar accidentalmente lo que ya estaba respaldado.

Al terminar muestra un resumen con el resultado de cada destino y el tiempo que tardó. El registro completo queda en `~/.cache/sincronizar.log`.

> **Nota:** en Google Drive se crea una carpeta llamada `thinkpad` que replica el contenido de `~/Documentos`. Ese nombre es arbitrario y puede cambiarse: edite la línea `rclone sync "$dir" "gdrive:thinkpad"` dentro del script y reemplace `thinkpad` por el nombre que prefiera.

> **Requisito: etiqueta de la unidad externa**
> El script localiza la unidad buscando el punto de montaje `/run/media/$USER/Respaldo`. Para que lo encuentre, la unidad debe tener asignada la etiqueta lógica `Respaldo` (exactamente con esa ortografía y mayúscula). Si la etiqueta es distinta (por ejemplo `respaldo`, `Backup` o `SSD`), el script no la detectará y omitirá el respaldo local sin avisar de error.
>
> Para verificar o cambiar la etiqueta de una partición ext4:
> ```bash
> # Ver etiqueta actual
> lsblk -o NAME,LABEL
>
> # Cambiar etiqueta (reemplaza sdXN con la partición correcta)
> sudo e2label /dev/sdXN Respaldo
> ```
>
> Si la unidad no está conectada al momento de ejecutar `sincronizar`, el respaldo local se omite sin interrumpir el respaldo en nube.

---

### `contenedores`

Gestiona entornos Linux aislados mediante **Distrobox**, con un `HOME` propio para cada contenedor en `~/Contenedores distrobox/`. Presenta un menú interactivo con las siguientes opciones:

| Opción | Descripción |
| :--- | :--- |
| `1` Crear contenedor | Elige una distribución del catálogo, le asigna un nombre y crea el contenedor con su propio HOME. Al terminar ofrece crear las carpetas estándar del usuario (`Documentos`, `Descargas`, etc.) y entrar directamente. |
| `2` Listar contenedores | Muestra los contenedores existentes y su estado actual. |
| `3` Entrar a un contenedor | Abre una sesión de terminal dentro del contenedor elegido. |
| `4` Apagar contenedor | Detiene un contenedor que esté corriendo en segundo plano. |
| `5` Añadir aplicación al menú | Hace visible en el menú de aplicaciones del sistema una app instalada dentro del contenedor, como si fuera nativa. |
| `6` Eliminar aplicación del menú | Quita del menú del sistema una aplicación que fue exportada desde Distrobox. |
| `7` Eliminar contenedor | Borra el contenedor, su HOME y sus accesos directos del menú. Pide escribir el nombre exacto antes de proceder. |
| `8` Salir | Cierra el script. |

**Catálogo de imágenes disponibles:**

- Ubuntu LTS (última versión estable)
- Debian Stable (última versión estable)
- Fedora (última versión)
- Arch Linux (Rolling Release)

---

## 3. Añadir nuevos scripts

Para agregar un script nuevo, basta con colocarlo en `~/toolbox/`. Se recomienda omitir la extensión (p. ej., `limpiar_temp` en lugar de `limpiar_temp.sh`). Luego:

1. Defina el intérprete en la primera línea:
   - Shell: `#!/bin/bash`
   - Python: `#!/usr/bin/env python3`
2. Otorgue permisos de ejecución: `chmod +x ~/toolbox/nombre_del_script`.

El comando quedará disponible en cualquier terminal nueva.

---

## 4. Desinstalación

Para eliminar la Toolbox y revertir el sistema a su estado original:

**1. Eliminar archivos del repositorio y caché de `sincronizar`:**

```bash
rm -rf ~/toolbox
rm -f ~/.cache/sincronizar.lock ~/.cache/sincronizar.log
```

**2. Limpiar datos de `contenedores` (si lo usó):**

Los HOME de cada contenedor quedan en `~/Contenedores distrobox/` y las imágenes descargadas se almacenan en Podman. Para eliminarlos:

```bash
rm -rf ~/Contenedores\ distrobox/
podman system prune --all --force
```

**3. Limpiar `~/.bashrc`:** abra el archivo (p. ej., con `nano ~/.bashrc`) y elimine el bloque `# --- TOOLBOX PERSONAL ---`.

**4. Recargar la configuración:**

```bash
source ~/.bashrc
```
