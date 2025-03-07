Claro, aquí tienes una explicación de los comandos `awk`, `who` y `sed`, junto con ejemplos prácticos para cada uno.

### 1. Comando `awk`

`awk` es un poderoso lenguaje de programación orientado a la manipulación de texto y se usa principalmente para procesar y analizar archivos de texto. `awk` permite realizar operaciones complejas sobre registros que se separan por espacios o tabulaciones.

#### Ejemplos de uso de `awk`

- **Imprimir la primera columna de un archivo**:
    ```bash
    awk '{print $1}' archivo.txt
    ```

- **Sumar los valores de la segunda columna**:
    ```bash
    awk '{suma += $2} END {print suma}' archivo.txt
    ```

- **Filtrar líneas que contengan una palabra específica**:
    ```bash
    awk '/palabra/' archivo.txt
    ```

- **Imprimir solo líneas donde el valor de la tercera columna sea mayor que 50**:
    ```bash
    awk '$3 > 50' archivo.txt
    ```

### 2. Comando `who`

`who` se usa en sistemas Unix/Linux para mostrar quién está conectado al sistema. Muestra información sobre los usuarios que han iniciado sesión, incluyendo el nombre de usuario, el terminal en el que están, la hora de inicio de sesión y posiblemente el tiempo de inactividad.

#### Ejemplos de uso de `who`

- **Mostrar usuarios conectados**:
    ```bash
    who
    ```

- **Mostrar solo el número de usuarios conectados**:
    ```bash
    who | wc -l
    ```

- **Mostrar información más detallada**:
    ```bash
    who -H  # Muestra los nombres de las columnas
    ```

- **Ver usuarios y su última conexión**:
    ```bash
    last
    ```

### 3. Comando `sed`

`sed` (Stream Editor) es una herramienta de procesamiento de texto que se utiliza para realizar transformaciones sobre un flujo de texto (en archivos o entradas estándar). A continuación, se presentan varios usos comunes de `sed`, incluidos los que solicitaste.

#### Ejemplos de uso de `sed`

- **Buscar y reemplazar texto**:
    ```bash
    sed 's/patron1/patron2/g' archivo.txt
    ```
    Este comando reemplaza todas las ocurrencias del **`patron1`** por **`patron2`** en `archivo.txt`. La opción `g` significa "global", lo que implica que se sustituyan todas las instancias en cada línea.

- **Eliminar una línea que coincida con un patrón**:
    ```bash
    sed '/patron/d' archivo.txt
    ```
    Este comando elimina cualquier línea que contenga **`patron`**.

- **Añadir una línea después de una línea que coincide con un patrón**:
    ```bash
    sed '/patron/ a\Nueva línea que quiero añadir' archivo.txt
    ```
    Esto añade "Nueva línea que quiero añadir" `después` de cada línea que contiene **`patron`**. Si deseas agregar antes, usa `i\` en lugar de `a\`.

- **Eliminar un texto específico de todas las líneas**:
    ```bash
    sed 's/texto_a_eliminar//g' archivo.txt
    ```
    Reemplaza todas las instancias de **`texto_a_eliminar`** con una cadena vacía, eliminándolo de las líneas.

- **Eliminar un texto específico de todas las líneas (sin considerar mayúsculas y minúsculas)**:
    ```bash
    sed 's/[Tt][Ee][Xx][Tt][Oo]_[Aa][Ee][Ll][Ii][Mm][Ii][Nn][Aa][Rr]//g' archivo.txt
    ```
    Este comando elimina el texto específico sin considerar mayúsculas y minúsculas al utilizar una expresión regular con conjuntos de caracteres.

### Resumen

Estos comandos son herramientas poderosas y fundamentales en la línea de comandos de Unix/Linux:

- **`awk`**: Ideal para procesamiento y análisis de texto.
- **`who`**: Utilizado para mostrar usuarios conectados.
- **`sed`**: Excelente para manipulación de texto en archivos o flujos.


### 4. Comando `set`

El comando `set` en Bash se utiliza para modificar las propiedades del entorno de la shell. A continuación, te mostraré cómo funciona y ejemplos de su uso, incluyendo las opciones `-x` y `+x`, así como otras funcionalidades.

El comando `set` permite habilitar o deshabilitar características específicas de la shell. Algunas de las opciones más comunes son:

- **`-x`**: Activa el modo de depuración, lo que muestra cada comando y su expansión al ser ejecutado.
- **`+x`**: Desactiva el modo de depuración.
- **`-e`**: Hace que la shell salga inmediatamente si un comando falla (es decir, devuelve un estado distinto a cero).
- **`-u`**: Hace que la shell devuelva un error si intentas utilizar una variable que no ha sido inicializada.
- **`-o pipefail`**: Hace que una tubería devuelva un estado de fallo si cualquier comando en la tubería falla.

#### Ejemplos de Uso de `set`

**Ejemplo 1:** Modo de Depuración (`-x` y `+x`)
```bash
#!/bin/bash
DEBUG=$1
if $DEBUG == "DEBUG"; then
    set -x  # Activar modo de depuración
fi

echo "Iniciando el script"

# Definir una variable
nombre="Juan"
echo "Hola, $nombre"

# Desactivar modo de depuración
set +x

echo "Script completado"
```

**Ejecución**:
```bash
$ bash script.sh DEBUG
```

**Salida**:
```
+ echo 'Iniciando el script'
Iniciando el script
+ nombre=Juan
+ echo 'Hola, Juan'
Hola, Juan
Script completado
```

Aquí, cuando se activa `set -x`, todos los comandos se muestran en la salida estándar antes de su ejecución.

**Ejemplo 2:** Usar `-e` para Salir con Errores

```bash
#!/bin/bash

set -e  # Salir si un comando falla

echo "Comenzando el script..."
mkdir /ruta/que/no/existe  # Esto fallará y hará que el script termine aquí
echo "Este mensaje no se verá porque el comando anterior falló."
```

**Ejecución**:
```bash
$ bash script.sh
```

**Salida**:
```
Comenzando el script...
mkdir: cannot create directory ‘/ruta/que/no/existe’: No such file or directory
```

En este caso, el script se detiene inmediatamente cuando `mkdir` falla.

**Ejemplo 3:** Usar `-u` para Variables No Inicializadas

```bash
#!/bin/bash

set -u  # Activar error en variables no inicializadas

echo "Nombre: $nombre"  # Esto causará un error porque 'nombre' no está definido
```

**Ejecución**:
```bash
$ bash script.sh
```

**Salida**:
```
bash: nombre: unbound variable
```

Aquí, intentar usar una variable no definida provoca un error y el script termina.

**Ejemplo 4:** Usar `-o pipefail`

```bash
#!/bin/bash

set -o pipefail  # Hacer que una tubería falle si alguno de los comandos falla

false | true  # El primer comando falla
echo "Este mensaje no se verá porque la tubería falla."  # Este mensaje no se ejecuta
```

**Ejecución**:
```bash
$ bash script.sh
```

**Salida**:
```
bash: false: command not found  # La salida puede variar según la situación
```