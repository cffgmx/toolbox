# Toolbox  
## Extensión Operativa para Ubuntu 24.04 LTS

Este repositorio define una **infraestructura modular de herramientas operativas** integradas de forma nativa en la terminal de **Ubuntu 24.04 LTS**. Su diseño prioriza la **estabilidad del sistema**, la **automatización no intrusiva** y la **observabilidad del estado del hardware**, evitando dependencias externas innecesarias o configuraciones frágiles.

La toolbox está concebida como una **extensión funcional del entorno Unix**, no como una colección de scripts aislados.

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
       git clone https://github.com/cffga/toolbox.git ~/toolbox
2. Otorgar permisos de ejecución a las herramientas:
       chmod +x ~/toolbox/*
3. Refrescar la sesión de la shell:
       source ~/.bashrc

---

## 2. Dependencia Crítica: rclone

El subsistema de sincronización de datos depende de la utilidad `rclone`. Para preservar la coherencia con el ciclo de vida **LTS** del sistema, se recomienda utilizar exclusivamente la versión incluida en los repositorios oficiales de Ubuntu.

### Instalación

    sudo apt update && sudo apt install rclone -y

### Configuración del Remoto (gdrive)

El comando `sincronizar` asume la existencia de un remoto configurado **exactamente** con el nombre `gdrive`.

Procedimiento resumido:

1. Ejecutar:
       rclone config
2. Crear un nuevo remoto (`n`) llamado `gdrive`.
3. Seleccionar **Google Drive** como tipo de almacenamiento.
4. En *Scope*, elegir la opción **1 — Acceso total**.
5. Completar el flujo de autenticación mediante el navegador web.

---

## 3. Catálogo Técnico de Comandos

| Comando        | Categoría | Descripción Técnica |
|---------------|-----------|---------------------|
| mantenimiento | SysAdmin  | Actualización controlada del sistema (preservando el kernel), purga de estados rc, rotación de logs (límite 100 MB) y ejecución de fstrim. |
| salud         | Hardware  | Diagnóstico de carga relativa (Lr) y análisis de correlación térmica basado en 12 hilos lógicos. |
| sincronizar   | Datos     | Espejo unidireccional estricto hacia la nube con soporte de auditoría previa. |

---

## 4. Manual de Operación: sincronizar

El comando `sincronizar` implementa una **lógica de espejo unidireccional estricto**. La **fuente de verdad** reside exclusivamente en el almacenamiento local; el estado en la nube es derivado y subordinado.

### Lógica de sincronización (R ≡ L)

Sea:
- L: el conjunto de archivos en el directorio local  
- R: el conjunto de archivos en el remoto  

La operación garantiza que, tras su ejecución:

    R_post = { x | x ∈ L }

Bajo este modelo:

- **Sobrescritura:**  
  Si un archivo existe tanto en L como en R pero difiere, el remoto se sobrescribe con la versión local, independientemente de la marca de tiempo del archivo en la nube.
- **Eliminación:**  
  Cualquier objeto y ∈ R tal que y ∉ L será movido a la papelera del servidor (`--drive-use-trash`).
- **Integridad del espejo:**  
  Archivos añadidos directamente en la nube desde otros dispositivos serán eliminados en la siguiente ejecución para preservar la paridad con el estado local.

### Modos de uso

- Ejecución estándar:
      sincronizar
- Modo auditoría (simulación):
      sincronizar --dry
  Muestra las operaciones que se ejecutarían sin realizar cambios reales en los datos.

---

## 5. Análisis de Hardware: salud

El comando `salud` realiza un análisis contextual del rendimiento del sistema, definiendo la **carga relativa** Lr mediante:

    Lr = L_avg(1) / N

donde:
- L_avg(1) es el promedio de carga del último minuto,
- N = 12 corresponde al número de hilos lógicos del Ryzen 5 PRO 4650U.

### Umbrales de evaluación

- **Óptimo:** Lr < 0.70 con temperaturas congruentes.
- **Saturación:** Lr ≥ 1.0, indicando carga superior a la capacidad nominal de procesamiento paralelo.
- **Anomalía térmica:**  
  Carga baja acompañada de temperatura elevada, indicativa de obstrucción física, acumulación de polvo o degradación del material térmico.

---

## 6. Mantenimiento del Repositorio

Para añadir nuevas herramientas a la toolbox:

1. Crear el archivo ejecutable en `~/toolbox/` sin extensión.
2. Incluir el shebang correspondiente (`#!/bin/bash` o `#!/usr/bin/env python3`).
3. Otorgar permisos de ejecución:
       chmod +x <archivo>
```
