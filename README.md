## Descripción general

Este repositorio funciona como una **extensión del sistema operativo**, proporcionando un conjunto de herramientas personalizadas que se integran de forma nativa en la terminal. Su propósito es **abstraer tareas complejas o repetitivas** en comandos simples, reproducibles y seguros, con el objetivo de preservar la **estabilidad y usabilidad a largo plazo** de la estación de trabajo.  

Está diseñado y probado específicamente para **Ubuntu**, siguiendo prácticas conservadoras y no intrusivas.

---

## Arquitectura e integración

La filosofía central de esta *toolbox* es su **integración directa en el `$PATH` del usuario**.

En lugar de manejar scripts aislados con extensiones `.sh` o ubicaciones arbitrarias, todos los comandos viven en un único directorio que el sistema reconoce como fuente de ejecutables.

### Implicaciones clave

- **Ejecución nativa:**  
  Los scripts se invocan directamente por su nombre (por ejemplo, `mantenimiento`) desde cualquier directorio, de la misma forma que comandos estándar como `ls`, `grep` o `apt`.

- **Portabilidad inmediata:**  
  Al clonar este repositorio en una nueva máquina y añadir el directorio al `PATH`, todo el entorno personal de herramientas queda restaurado de manera instantánea, sin configuraciones adicionales.

- **Estética y coherencia:**  
  Los comandos no utilizan extensiones visibles, reforzando la sensación de estar trabajando con utilidades propias del sistema.

---

## Catálogo de comandos

| Comando | Categoría | Descripción técnica |
| :--- | :--- | :--- |
| **`mantenimiento`** | SysAdmin | **Rutina mensual de saneamiento del sistema.**<br>• Actualización segura del sistema (`apt update / upgrade`).<br>• Eliminación de dependencias huérfanas y configuraciones residuales (`rc`).<br>• Rotación y limpieza de logs (`journalctl`, máx. 2 semanas o 100 MB).<br>• Optimización del almacenamiento Snap (`retention=2`).<br>• Mantenimiento físico del SSD mediante TRIM (`fstrim`). |

*(Los futuros scripts —Python, respaldos, gestión de bases de datos u otras utilidades— se documentarán aquí.)*

---

## Instalación y configuración

### 1. Clonar el repositorio

Se recomienda clonar la *toolbox* directamente en el directorio personal para mantener una estructura simple y predecible.

    cd ~
    git clone https://github.com/cffga/toolbox.git

### 2. Asignar permisos de ejecución

Para que el sistema trate los archivos como comandos ejecutables:

    chmod +x ~/toolbox/*

### 3. Integración permanente en Bash

Añade el siguiente bloque a tu archivo `~/.bashrc`.  
Este fragmento verifica la existencia del directorio antes de modificar el `PATH`, evitando errores si el repositorio se elimina en el futuro.

    # --- TOOLBOX PERSONAL ---
    if [ -d "$HOME/toolbox" ]; then
        export PATH="$HOME/toolbox:$PATH"
    fi

### 4. Aplicar los cambios

Recarga la configuración de la shell:

    source ~/.bashrc

---

## Flujo de trabajo: cómo añadir nuevos comandos

Para incorporar una nueva herramienta (por ejemplo, un script en Python para limpieza de datos):

1. **Crear el archivo**  
   Ubícalo dentro de `~/toolbox` **sin extensión** y añade el *shebang* correspondiente.

        nano ~/toolbox/limpiar-data
        #!/usr/bin/env python3

2. **Hacerlo ejecutable**

        chmod +x ~/toolbox/limpiar-data

3. **Versionar (opcional)**

        cd ~/toolbox
        git add limpiar-data
        git commit -m "feat: add data cleaning utility"
        git push

4. **Uso inmediato**  
   El comando `limpiar-data` estará disponible desde cualquier terminal, sin reiniciar sesión.

---

## Desinstalación

Para eliminar completamente la *toolbox* y revertir los cambios realizados en el entorno:

### 1. Eliminar el directorio local

**Advertencia:** esta acción es irreversible.

    rm -rf ~/toolbox

### 2. Limpiar la configuración de la shell

1. Abre el archivo de configuración:

        nano ~/.bashrc

2. Localiza y elimina el bloque `TOOLBOX PERSONAL`.
3. Guarda (`Ctrl+O`) y cierra (`Ctrl+X`).

### 3. Refrescar la sesión

    source ~/.bashrc
