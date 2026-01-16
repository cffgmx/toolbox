# Toolbox

Este repositorio define una **infraestructura modular de herramientas operativas** integradas de forma nativa en la terminal de **Ubuntu LTS** o **Debian Stable**. Su diseño prioriza la estabilidad del sistema y la automatización no intrusiva, optimizado específicamente para la arquitectura de la **Lenovo ThinkPad T14 Gen 1** (AMD Ryzen™ 5 PRO 4650U), aunque puede funcionar en cualquier computadora.

---

## 1. Instalación e Integración

### Requisitos Técnicos
Instale las dependencias necesarias para la gestión de red, sincronización y diagnóstico de hardware:

```bash
sudo apt update && sudo apt install git rclone rsync smartmontools lm-sensors -y
```

### Procedimiento de Implementación

1.  **Clonación:** Descargue el repositorio en su directorio personal:
    ```bash
    git clone [https://github.com/cffga/toolbox.git](https://github.com/cffga/toolbox.git) ~/toolbox
    ```
2.  **Permisos:** Otorgue capacidades de ejecución a todos los scripts:
    ```bash
    chmod +x ~/toolbox/*
    ```
3.  **Configuración del entorno ($PATH):** Para invocar las herramientas como comandos globales, añada este bloque al final de su archivo `~/.bashrc`:

> ```bash
> # --- TOOLBOX PERSONAL ---
> if [ -d "$HOME/toolbox" ]; then
>     export PATH="$HOME/toolbox:$PATH"
> fi
> # ------------------------
> ```

4.  **Activación:** Refresque la sesión actual de la shell para reconocer los comandos:
    ```bash
    source ~/.bashrc
    ```

Conectar Google Drive

Ejecuta en terminal lo siguiente:

    rclone config

Responde exactamente así en el asistente interactivo:

1. `n` → New remote  
2. **name:** `gdrive`  
3. **Storage:** Selecciona Google Drive (número 22, pero verifica) 
4. **Client ID:** Enter  
5. **Client Secret:** Enter  
6. **Scope:** opción `1` (Full access)
7. **service_account_file:** Enter
8. **Advanced config:** `n`  
9. **Auto config:** `y`  
   (Se abrirá el navegador, inicia sesión y autoriza)
10. Confirma con `y`
11. Confirma con `y`
12. Sal con `q`

---

## 2. Catálogo de Comandos

### `mantenimiento`
Ejecuta un saneamiento profundo del sistema en cuatro etapas controladas:

* **Actualización:** Sincroniza repositorios y descarga parches de seguridad.
* **Saneamiento:** Elimina dependencias huérfanas y configuraciones residuales (estados `rc`).
* **Gestión de Logs:** Limita el tamaño de `journalctl` a **100 MB** o **2 semanas** de antigüedad.
* **Optimización SSD:** Ejecuta `fstrim` en unidades compatibles para preservar el rendimiento.

### `salud` 
Genera un análisis de integridad basado en métricas del kernel y sensores físicos:

| Métrica | Umbral de Alerta | Descripción |
| :--- | :--- | :--- |
| **Temperatura (Tctl)** | > 88°C | Evaluación térmica de los 12 hilos lógicos del Ryzen 5. |
| **Desgaste SSD** | > 80% | Reporte de uso del NVMe mediante datos SMART. |
| **Batería** | Variable | Comparativa entre capacidad real vs. diseño y ciclos. |

### `sincronizar`
Implementa un protocolo de respaldo híbrido con seguridad industrial.

* **Exclusividad:** Utiliza un archivo de bloqueo (`flock`) para impedir ejecuciones concurrentes.
* **Protección Anti-Vacío:** El proceso se aborta si el origen está vacío para evitar borrar datos en el destino.
* **Nube (Remote):** Actualiza `gdrive:ubuntu` vía `rclone` con hasta 3 reintentos.
* **Local (Físico):** Copia incremental vía `rsync` hacia unidad externa.

> **REQUISITO CRÍTICO DE HARDWARE**
> La unidad externa **DEBE** tener la etiqueta lógica (**label**) exactamente como `Respaldo`. El sistema busca el punto de montaje en `/media/$USER/Respaldo`.

---

## 3. Extensión: Cómo añadir nuevos scripts

La Toolbox es una infraestructura abierta. Para integrar una nueva herramienta:

1.  **Creación:** Guarde su script en `~/toolbox/`. Se recomienda **omitir la extensión** (ej. `limpiar_temp` en lugar de `limpiar_temp.sh`).
2.  **Encabezado:** Defina el intérprete (Shebang) en la primera línea: `#!/bin/bash` o `#!/usr/bin/env python3`.
3.  **Habilitación:** Ejecute `chmod +x ~/toolbox/nombre_del_script`.
4.  **Disponibilidad:** El comando será reconocido automáticamente en cualquier terminal nueva.

---

## 4. Desinstalación y Reversión Total

Para eliminar la Toolbox y restaurar el sistema a su estado original:

1.  **Limpieza de archivos:**
    ```bash
    rm -rf ~/toolbox
    rm -f ~/.cache/sincronizar.lock ~/.cache/sincronizar.log
    ```
2.  **Edición de configuración:**
    * Abra `~/.bashrc` con su editor (ej. `nano ~/.bashrc`).
    * Elimine el bloque identificado como `# --- TOOLBOX PERSONAL ---`.
3.  **Finalización:** Ejecute `source ~/.bashrc`.
