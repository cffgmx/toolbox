# Toolbox  
## Extensión Operativa para Ubuntu 24.04 LTS

Este repositorio define una **infraestructura modular de herramientas operativas** integradas de forma nativa en la terminal de **Ubuntu 24.04 LTS**. Su diseño prioriza la **estabilidad del sistema** y la **automatización no intrusiva**.

**Plataforma objetivo:**  
Optimizada específicamente para la arquitectura de la **Lenovo ThinkPad T14 Gen 1**, equipada con **AMD Ryzen 5 PRO 4650U**. El comportamiento térmico y los umbrales de carga están calibrados para este hardware concreto.

---

## 1. Arquitectura e Integración

La filosofía central de la toolbox es la **disponibilidad global de comandos** sin recurrir a alias frágiles ni a rutas absolutas manuales. La integración se basa exclusivamente en la inclusión del directorio de herramientas dentro de la variable de entorno `$PATH`, respetando el modelo estándar del sistema.

### Configuración del entorno

Añada el siguiente bloque a su archivo `~/.bashrc`:

    # TOOLBOX PERSONAL
    if [ -d "$HOME/toolbox" ]; then
        export PATH="$HOME/toolbox:$PATH"
    fi

Este enfoque garantiza que cualquier ejecutable contenido en el directorio `~/toolbox` sea reconocido como un comando nativo por la shell.

### Inicialización

1. Clonar el repositorio en el directorio personal:
       `git clone https://github.com/cffga/toolbox.git ~/toolbox`
2. Otorgar permisos de ejecución a las herramientas:
       `chmod +x ~/toolbox/*`
3. Refrescar la sesión de la shell:
       `source ~/.bashrc`

---

## 2. Dependencia Crítica: rclone

El subsistema de sincronización de datos depende de la utilidad `rclone`. Para preservar la coherencia con el ciclo de vida **LTS** del sistema, se recomienda utilizar exclusivamente la versión incluida en los repositorios oficiales de Ubuntu.

### Instalación

    sudo apt update && sudo apt install rclone -y

### Configuración del Remoto (gdrive)

Ejecuta en terminal lo siguiente:

    rclone config

Responde exactamente así en el asistente interactivo:

1. `n` → New remote  
2. **name:** `gdrive`  
3. **Storage:** Selecciona el número que corresponde a Google Drive
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

## 3. Catálogo Técnico de Comandos

| Comando        | Categoría | Descripción Técnica |
|---------------|-----------|---------------------|
| mantenimiento | SysAdmin  | Actualización controlada del sistema (preservando el kernel), purga de estados rc, rotación de logs (límite 100 MB) y ejecución de fstrim. |
| salud         | Hardware  | Diagnóstico de carga relativa (Lr) y análisis de correlación térmica basado en 12 hilos lógicos. |
| sincronizar   | Datos     | Espejo unidireccional estricto hacia la nube con soporte de auditoría previa. |

---

## 4. Mantenimiento del Repositorio

Para añadir nuevas herramientas a la toolbox:

1. Crear el archivo ejecutable en `~/toolbox/` sin extensión.
2. Incluir el shebang correspondiente (`#!/bin/bash` o `#!/usr/bin/env python3`).
3. Otorgar permisos de ejecución:
       chmod +x <archivo>
```
