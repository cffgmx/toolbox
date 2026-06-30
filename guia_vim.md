Vim no es un editor tradicional basado en atajos de teclado inconexos; funciona mediante una gramática. Para dominarlo, no debes memorizar combinaciones, sino aprender a construir comandos como si formaras oraciones simples: uniendo una acción (verbo) con un destino (objeto o movimiento).

### 1. El Flujo de Trabajo (Cuándo usar cada modo)

Vim tiene distintos modos, y la regla de oro para ser veloz es comprender qué tarea pertenece a qué estado.

*   **Modo Normal (`Esc`):** Es el estado por defecto. **Debes pasar el 90 % de tu tiempo aquí.** Se usa para leer, desplazarte, borrar, copiar o alterar texto existente. Piensa en este modo como si tuvieras el texto en tus manos para manipular su estructura, pero no un bolígrafo para escribir.
*   **Modo Insertar (`i`, `a`, `o`, `c`):** **Solo se usa para redactar texto nuevo.** Entras, tecleas la palabra o línea que necesitas, e *inmediatamente* presionas `Esc` para volver al modo Normal. Si usas las flechas para corregir un error anterior mientras estás aquí, estás perdiendo el tiempo; sal al modo Normal y muévete desde ahí.
*   **Modo Visual (`v`, `V`, `<C-v>`):** Se usa **únicamente cuando el texto que quieres afectar es irregular** y no encaja en una definición simple (como "una palabra" o "un párrafo"). Te permite resaltar el texto de forma manual para estar seguro de tu selección antes de borrarlo o copiarlo.
*   **Modo Comando (`:`):** Se usa para tareas de administración del editor, no para editar el texto en sí. Aquí es donde guardas (`:w`), sales (`:q`) o aplicas sustituciones en todo el documento.

### 2. Los Movimientos (Vectores de desplazamiento)

Los movimientos definen una dirección y una distancia a partir de donde se encuentra tu cursor en este instante. 

**Movimiento por carácter:**
*   `h`, `j`, `k`, `l`: Izquierda, abajo, arriba, derecha.

**Movimiento por palabras:**
*   `w`: Salta al inicio de la siguiente palabra.
*   `b`: Salta al inicio de la palabra anterior.
*   `e`: Salta al final de la palabra actual.

**Límites de la línea y el archivo:**
*   `0`: Salta al inicio absoluto de la línea (columna cero).
*   `^`: Salta al primer carácter visible de la línea (ignora los espacios en blanco).
*   `$`: Salta al final de la línea.
*   `gg`: Salta a la primera línea del documento.
*   `G`: Salta a la última línea del documento.

**Búsqueda en la misma línea:**
*   `f{carácter}` (find): Salta hacia la derecha hasta quedar exactamente sobre el carácter indicado.
*   `t{carácter}` (till): Salta hacia la derecha hasta quedar un espacio antes del carácter indicado.

### 3. Las Acciones (Operadores)

Son las acciones que alteran el texto. Si las presionas solas, no hacen nada; entran en un estado de "espera" hasta que les indiques sobre qué movimiento o bloque van a operar.

*   `d` (delete): Corta el texto.
*   `c` (change): Corta el texto y te cambia automáticamente al modo Insertar para que escribas un reemplazo.
*   `y` (yank): Copia el texto.

**Acciones inmediatas:**
*   `p` (put): Pega el texto cortado o copiado. Actúa de inmediato donde está el cursor, por lo que no espera ningún movimiento adicional.

*(Regla de duplicación)*: Si presionas el mismo operador dos veces seguidas (`dd`, `cc`, `yy`), la acción se aplica a toda la línea actual de extremo a extremo.

### 4. Objetos de Texto vs. Movimientos (El Alcance)

Esta es la diferencia más importante para ser eficiente:
*   Un **Movimiento** (ej. `w`, `$`) actúa *desde donde está el cursor* hacia adelante o hacia atrás. Si estás a la mitad de una palabra y borras un movimiento (`dw`), borrarás solo desde esa letra hasta el inicio de la siguiente palabra.
*   Un **Objeto de texto** actúa sobre un *bloque completo* (una palabra entera, un párrafo, o todo lo que está entre comillas), sin importar si tu cursor está al inicio, en medio o al final de ese bloque.

Para usar un Objeto de texto, primero escribes la acción (`d`, `c`, `y`), luego un **Modificador** y finalmente el **Bloque**.

**Modificadores:**
*   `i` (dentro): Aplica la acción estrictamente al contenido, dejando intactas las comillas, paréntesis o espacios que lo rodean.
*   `a` (alrededor): Aplica la acción al contenido y también borra o copia los delimitadores y espacios que lo rodean.

**Bloques comunes:**
*   De texto: `w` (palabra), `p` (párrafo).
*   Delimitadores: `"`, `'`, `` ` `` , `(`, `{`, `[`.

### 5. La Gramática en Acción (Operador $\circ$ Destino)

Al combinar los elementos anteriores, creas los comandos finales.

| Fórmula | Comando | Resultado Lógico |
| :--- | :--- | :--- |
| $d \circ \$ $ | `d$` | (Movimiento) Corta desde el cursor hasta el final de la línea. |
| $d \circ i \circ w$ | `diw` | (Objeto) Corta la palabra entera bajo el cursor, respetando los espacios adyacentes, sin importar en qué letra estés. |
| $c \circ a \circ "$ | `ca"` | (Objeto) Borra todo el texto junto con sus comillas `""`, y te pone en modo Insertar. |
| $y \circ i \circ \{$ | `yi{` | (Objeto) Copia todo el bloque de código que esté dentro de las llaves `{}`. |

> **La anomalía de `cw`:** Por lógica matemática, $c \circ w$ debería borrar hasta el inicio de la siguiente palabra (como lo hace `dw`). Sin embargo, por razones históricas, Vim trata a `cw` como si fuera `ce` (cambia solo hasta el final de la palabra actual, dejando el espacio en blanco intacto). Es la excepción más notable del editor.

### 6. La Magia de la Iteración: El Operador Punto (`.`)

El comando `.` (punto) es la herramienta más poderosa de Vim. Su función es repetir exactamente la última alteración de texto que realizaste.

**¿Qué es una alteración?**
Vim graba todo lo que haces desde el instante en que aplicas una acción o entras al modo Insertar, hasta el momento en que presionas `Esc`.

**Ejemplo práctico:**
Supongamos que quieres cambiar la variable `x` por `velocidad` en cinco lugares distintos.
1. Pones el cursor sobre la primera `x`.
2. Presionas `ciw` (Cambiar dentro de la palabra). La `x` desaparece y entras al modo Insertar.
3. Tecleas `velocidad`.
4. Presionas `Esc`. (Aquí termina tu grabación).

Para las siguientes cuatro variables, ya no tienes que escribir el comando ni la palabra. Solo navegas hasta la siguiente `x` y presionas `.`. Vim ejecutará automáticamente toda la secuencia por ti.

### 7. La Regla de Simetría (Minúsculas vs. Mayúsculas)

El uso de la tecla Shift (mayúsculas) no es aleatorio; siempre aplica una de tres transformaciones predecibles sobre la versión en minúscula de esa misma tecla.

**I. Invertir la dirección**
Hace exactamente lo mismo, pero hacia el lado contrario.
*   $p \leftrightarrow P$ : Pega después del cursor $\leftrightarrow$ Pega antes del cursor.
*   $o \leftrightarrow O$ : Abre una línea debajo $\leftrightarrow$ Abre una línea arriba.
*   $f \leftrightarrow F$ : Busca un carácter hacia la derecha $\leftrightarrow$ Busca hacia la izquierda (aplica igual para $t \leftrightarrow T$).

**II. Ir a los extremos**
Toma una acción pequeña y la estira hasta los límites absolutos de la línea.
*   $i \leftrightarrow I$ : Inserta donde estás $\leftrightarrow$ Inserta al inicio de la línea (`^`).
*   $a \leftrightarrow A$ : Añade un espacio a la derecha $\leftrightarrow$ Añade al final de la línea (`$`).
*   $c \leftrightarrow C$ : Requiere que le des un movimiento $\leftrightarrow$ Cambia todo hasta el final de la línea ($c \circ \$ $).
*   $d \leftrightarrow D$ : Requiere que le des un movimiento $\leftrightarrow$ Corta todo hasta el final de la línea ($d \circ \$ $).
*   *(Excepción)*: Por razones históricas, $Y$ no copia hasta el final de la línea ($y \circ \$ $), sino que copia la línea entera (equivale a `yy`).

**III. Saltar bloques más grandes**
*   $w, b, e \leftrightarrow W, B, E$ : En minúsculas, Vim se detiene en cada punto, coma o guion. En mayúsculas, Vim ignora por completo los signos de puntuación y solo se detiene cuando encuentra un espacio en blanco. Es ideal para saltar rápidamente sobre fragmentos de código o enlaces largos.

### 8. Memoria a largo plazo: Registros y Macros

**Registros (Múltiples portapapeles):**
Vim tiene distintos espacios de memoria. Si quieres copiar algo y evitar que se sobrescriba cuando borres otro texto, guárdalo en un registro específico usando `"` seguido de una letra (de la `a` a la `z`).
*   `"ayiw`: Copia la palabra actual y la guarda en el registro `a`.
*   `"ap`: Pega el contenido guardado en el registro `a`.

**Macros (Automatización de tareas complejas):**
Mientras que el punto (`.`) repite un solo cambio, los macros te permiten grabar secuencias enteras de navegación, edición y formato, para luego ejecutarlas de golpe.
*   `q{letra}`: Inicia la grabación del macro y lo guarda en la `{letra}` que elijas.
*   `q`: Detiene la grabación.
*   `@{letra}`: Ejecuta todas las operaciones guardadas en esa `{letra}`. (Ej: `10@a` ejecutará el macro `a` diez veces seguidas).

### 9. Disciplina Muscular y Advertencias

Para que el uso de Vim se convierta en un reflejo veloz, obedece estas tres reglas durante tu aprendizaje:

1. **Evita presionar la misma tecla varias veces:** Si presionas `j` (abajo) o `l` (derecha) cinco veces seguidas, lo estás haciendo mal. Detente, presiona `Esc` y piensa qué movimiento directo te lleva allí (quizá saltar una palabra con `w` o buscar una letra con `f`).
2. **Abandona las flechas direccionales y el ratón:** Usarlos destruye la principal ventaja de Vim, que es mantener las manos fijas sobre el teclado. Navega exclusivamente con `h`, `j`, `k`, `l`.
3. **Piensa antes de actuar:** La elegancia de tu edición se mide por tu capacidad de usar el punto (`.`). Si mientras escribes en el modo Insertar te equivocas, usas flechas para corregir, borras y vuelves a escribir, el punto intentará repetir todo ese desastre y fallará. Piensa tu comando desde el modo Normal y ejecútalo limpiamente.