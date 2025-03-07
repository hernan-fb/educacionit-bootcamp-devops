# Clase 11

### Desafío 02
- instalar plugin de nodejs
- correr un proyecto con cada push o pullrequest mediante jenkins y un hook de github.

(1h24m)
## Bash Scripting
Bash es básicamente una versión mejorada de .sh scripting.
Bash (Bourne Again Shell) es un intérpreta de comandos para sistemas basados en Unix/Linux. Permite ejecutar comandos, automatizar tareas y escribir scripts, gestionar sistemas, procesar archivos. Generalmente cada sistema operativo tiene asociado un lenguaje de scripting que nos puede ayudar a llevar a cabo este tipo de tareas. En Windows, por ejemplo, el lenguaje es PowerShell.
Sus usos comunes suelen ser la automatización de tareas repetitivas, gestión de sistemas y el procesamiento de archivos.
Una buena práctica es poner la extensión `.sh` a los scripts de bash.
Para ejecutar el archivo creado hay que cambiarlo y hacerlo ejecutable con `chmod +x archivo.sh` y luego ejecutarlo `./archivo.sh`.

### Elementos de un script
#### Shebang:
`#!/bin/bash`: Indica al sistema que interprete usar. En este caso, bash. Se escribe en la primera linea de un script bash.
#### Comentarios:
`‘#’`: Usamos el carácter ‘#’ para agregar comentarios a nuestro codigo.
#### Declaración de Variables:
`‘[NOMBRE]=[VALOR]’`: 
Ejemplo: 
`num1=10`
#### Uso de Variables:
`‘ echo “El primer número es igual a $num1” ’`

#### Leer Input del Usuario
`‘read’`: Usamos la keyword `‘read’` para leer input del usuario. Ejemplo:
```bash
#!/bin/bash
echo "Introduce tu nombre:"
read NOMBRE
echo "Hola, $NOMBRE"
```

#### 1. Comando `echo`

El comando `echo` se utiliza para imprimir texto o variables en la salida estándar (generalmente la terminal).

##### Sintaxis Básica

```bash
echo [opciones] [texto]
```

##### Ejemplos

- Imprimir un texto simple:

    ```bash
    echo "Hola, mundo!"
    ```

- Imprimir el valor de una variable:

    ```bash
    nombre="Juan"
    echo "Hola, $nombre!"
    ```

- **Opciones comunes**:

    - `-n`: No imprime una nueva línea al final.
    
        ```bash
        echo -n "Hola sin salto"
        ```

    - `-e`: Permite interpretar caracteres de escape (como `\n`).
    
        ```bash
        echo -e "Primera línea\nSegunda línea"
        ```

#### 2. Comando `printf`

El comando `printf` es más avanzado y flexible que `echo`. Se basa en la función `printf` de C, lo que permite formatear la salida de manera más precisa.

##### Sintaxis Básica

```bash
printf "formato" [argumentos...]
```

##### Ejemplos

- Imprimir un texto simple:

    ```bash
    printf "Hola, mundo!\n"
    ```

- Imprimir el valor de una variable con formato:

    ```bash
    nombre="Juan"
    printf "Hola, %s!\n" "$nombre"
    ```

    Aquí, `%s` es un especificador de formato para una cadena de caracteres.

- Imprimir múltiples variables:

    ```bash
    nombre="Juan"
    edad=25
    printf "Nombre: %s, Edad: %d\n" "$nombre" "$edad"
    ```

    En este caso, `%d` es un especificador para enteros.

- Formatear números:

    ```bash
    printf "Valor con dos decimales: %.2f\n" 3.14159
    ```

    Aquí, `%.2f` formatea un número de punto flotante con dos decimales.

#### Variables
##### Variables locales
```bash
var_local="Soy una variable local"
echo $var_local
```
cuando la terminal o el script se cierra, esa variable deja de estar disponible. Ni tampoco va a estar disponible en otra terminal.

##### Variables globales
```bash
export GLOBAL_VAR="Soy una variable global"
echo $GLOBAL_VAR
```
Cuando utilizas `export`, haces que la variable esté disponible en todos los subshells o procesos hijos (es decir, cualquier comando o script que se ejecute desde ese script). Esta variable también puede ser accedida por cualquier script o comando que se ejecute tras iniciar el shell desde el que se llamaron.

Pero si ejecutas un script (por ejemplo, script B) desde otro script (por ejemplo, script A), script B se ejecuta en un subshell. Esto significa que todo lo que se declare o modifique (incluidas las variables de entorno) dentro de script B no afectará el entorno de script A.
Si en script B exportas una variable HOLA, esa variable estará disponible en los subshells que pueda generar script B (en caso de que contenga otros comandos o scripts). Sin embargo, no será accesible en script A.
Si deseas que la variable HOLA sea accesible en script A, necesitas ejecutar script B en el mismo contexto, utilizando source o .:
```bash
# scriptA.sh  
#!/bin/bash  
echo "Ejecutando script A"  
source scriptB.sh   # O simplemente . scriptB.sh  
echo "Desde script A, HOLA es: $HOLA"
```
Con este cambio, la variable HOLA estará disponible en script A después de que se ejecute script B.


##### Variables de posicion
```bash
echo "ejecutándolo dentro de un script"
echo "This is the Script name: $0"
echo "This is the First argument: $1"
```
##### Variables especiales
```bash
echo "Último estado de salida: $?"
echo "PID del script: $$"
echo "Número de argumentos: $#"
```
##### Cadenas (string)
```bash
name="Mundo"
greeting="Hola, $name"
echo $greeting
```
##### Importante:
* Las comillas dobles (") permiten la expansión de variables dentro de la cadena.
* Las comillas simples (') tratan el contenido literalmente, sin expansión de variables.

```bash
variable="mundo"
echo "Hola, $variable"  # Hola, mundo
echo 'Hola, $variable'  # Hola, $variable
```

##### Variables aritmeticas

```bash
let a=5+4
echo $a  # 9

b=$((3+2))
echo $b  # 5

c=$(expr 2 \* 3)
echo $c  # 6
```

si yo usara la forma usual de declarar variables `a=5+4` al hacer `echo $a` obtendría `5+4`. Con `let`, `$(())`, y `$(expr ..... )` se procesa la cuenta en cuestión.

### Corchetes en Bash
En Bash, los corchetes se utilizan para evaluar condiciones en estructuras de control como if, while, y until. Existen tres formas principales de corchetes que se utilizan con diferentes propósitos:

1. Corchetes simples (`[]`)
**Denominación:** Este es conocido como el comando test.
**Uso:** 
- Se utiliza para evaluar expresiones condicionales, como comparaciones de cadenas, números, y comprobaciones de archivos.
- Es necesario colocar espacios antes y después de los corchetes. No hacerlo provocará un error de sintaxis. 
- Se pueden usar operadores como `-eq`, `-ne`, `-lt`, `-gt`, `=`, `!=`, etc.
**Ejemplo:**
```bash
if [ -e "archivo.txt" ]; then  
    echo "El archivo existe."  
fi
```  

2. Doble corchete (`[[ ]]`)
**Denominación:** Este es un comando de evaluación condicional mejorado en Bash.
**Uso:** 
- Proporciona más funcionalidad y es más seguro que los corchetes simples. Permite operaciones más complejas y evita la necesidad de escapar caracteres como >, <, y &&.
- se requiere que haya espacios antes y después de los corchetes dobles. Si no se incluyen los espacios, también se producirá un error de sintaxis
- 
**Ejemplo:**
```bash
if [[ "$a" == "texto" ]]; then  
    echo "La variable es igual a 'texto'."  
fi
```
Características:
Permite más opciones de comparación de cadenas (p.ej., `==`, `!=`).
Permite el uso de operadores lógicos directamente (`&&`, `||`) sin necesidad de usar `-a` o `-o`.
Es útil para realizar comparaciones de cadenas y patrones más avanzadas.
3. Corchetes de llave ({})
Denominación: Este tipo se utiliza para agrupamientos o expansión de variables.
Uso: Se utilizan para agrupar comandos y en la expansión de secuencias y conjuntos. También se utilizan en bucles y funciones.
Ejemplo:
for i in {1..5}; do  
    echo "Número: $i"  
done  
Características:
Permiten la expansión de secuencias y conjuntos.
Son útiles en scripts para definir bloque de tareas o funciones.
Resumen de Usos
[]: Comando test para comparaciones básicas y pruebas de condición.
[[ ]]: Evaluación de condiciones más robusta y flexible, ideal para Bash.
{}: Agrupaciones y expansiones de secuencias; también se usa para definir funciones o agrupar comandos.


### Sintaxis en Bash Scripting
En Bash, la sintaxis tiene ciertas reglas sobre el uso de espacios que son importantes para la correcta interpretación de los comandos. Aquí te explico cuándo no deben usarse espacios y dónde sí:

1. Espacios en asignación de variables
Al asignar un valor a una variable, no debes incluir espacios alrededor del signo igual (=). Por ejemplo:
```bash
resultado=5  # Correcto  
resultado = 5  # Incorrecto, esto no funcionará  
```
Si colocas espacios, Bash intentará interpretar la línea como si estuvieras intentando ejecutar un comando llamado resultado con argumentos = y 5, lo que no tiene sentido.

2. Expresiones aritméticas
Los paréntesis dobles $((...)) se utilizan para indicar que lo que está dentro de ellos es una expresión aritmética. Esto permite a Bash interpretar correctamente la operación de suma. Sin los paréntesis, Bash no interpretaría num1 + num2 como una operación matemática, sino que trataría de leerlo como una simple cadena de texto.
En cuanto a la sintaxis, cuando usas la sintaxis de expresiones aritméticas, como $(( ... )), no necesitas espacios entre las variables y el operador, pero los puedes usar dentro de las expresiones si lo deseas. Por ejemplo, se puede usar:
```bash
resultado=$((num1 + num2))  # Correcto  
resultado=$(( num1 + num2 ))  # También correcto, pero no es necesario  
resultado=$((num1 + num2 ))  # Correcto, con espacio solo al final  
resultado=$(( num1 + num2 ))  # Correcto, pero no es necesario tener espacios  
```
No obstante, los espacios alrededor de los operadores dentro de las expresiones aritméticas son opcionales.

3. Comandos y parámetros
En general, para la ejecución de comandos y la transmisión de parámetros, sí debes usar espacios. Por ejemplo:
```bash
echo "Hola mundo"  # Correcto, hay un espacio después de 'echo'  
```
4. Comillas simples sin variables a imprimir
Si usamos comillas simples, no podemos poner variables dentro, para hacer esto si o si debemos usar comillas dobles.
Ej. si `NOMBRE = "Juan"`
- `echo "hola $NOMBRE"` imprime `hola Juan`
- `echo 'hola $NOMBRE'` imprime `hola $NOMBRE` porque se está usando comillas simples y no reconoce que tenga que leer la variable NOMBRE.

5. Espacio alrededor de las expresiones entre corchetes simples, corchetes dobles, y llaves:

En Bash, cuando usas corchetes ([ ]), debes dejar espacios antes y después del operador. Por ejemplo, debe ser [ expresión ] y no [expresión].

### Estructuras de subproceso

#### 1. Comillas simples (`'...'`)
**Función:** Se utilizan para definir cadenas literales. Todo lo que se encuentra dentro de los corchetes simples se considera una cadena y no se interpretan variables ni caracteres especiales.

**Características:**
- No se realiza la expansión de variables.
- No se realizan interpretaciones de caracteres especiales, como `\n` (nueva línea).

**Ejemplo:**
```
VAR='Hola, $USER'  
echo $VAR  # Salida: Hola, $USER (sin interpretar $USER)  
```
#### 2. Comillas dobles (`"..."`)
**Función:** Se utilizan para definir cadenas de texto donde se quiere permitir la expansión de variables e interpretación de caracteres especiales.

**Características:**
- Se expande el contenido de las variables.
- Se interpreta caracteres especiales, como `\n`.

**Ejemplo:**
```
VAR="Hola, $USER"  
echo $VAR  # Salida: Hola, <nombre_de_usuario>  
```
#### 3. Paréntesis (`(...)`)
**Función:** Se utilizan para crear subshells. Los comandos dentro de los paréntesis se ejecutan en una subinstancia del shell actual.

**Características:**
- El entorno de la subshell es separado del entorno del shell padre.
- Cambios en el entorno (como cambios de directorio o variables) no se ven reflejados en el shell padre.

**Ejemplo:**
```
(cd /tmp && ls)  # Cambia al directorio /tmp y lista su contenido  
echo $PWD       # Salida: <directorio_original> (no cambia el directorio del shell padre)  
```
#### 4. Paréntesis de dólar (`$()`)
**Función:** Se utiliza para la sustitución de comandos. Permite capturar la salida de un comando y usarla dentro de otra instrucción.

**Características:**
- Más moderno que los backticks **\`...`**, permite anidamiento más fácil.
- Elimina la necesidad de escapar ciertos caracteres.

**Ejemplo:**
```
CURRENT_DATE=$(date)  
echo "La fecha actual es: $CURRENT_DATE"  
```
#### 5. Backticks (`...`)
Función: También se utilizan para la sustitución de comandos, aunque son considerados como una forma más antigua y menos legible.

**Características:**
La sustitución se hace igual que con `$()`, pero se requiere escapar más caracteres si hay anidamientos.
**Ejemplo:**
```
CURRENT_DATE=`date`  
echo "La fecha actual es: $CURRENT_DATE"  
```
#### 6. Comando `test` y `[]` (corchetes simples)
**Función:** Se utilizan para evaluar expresiones condicionales dentro de scripts.

**Características:**
- `test` es un comando y `[]` es un alias del comando `test`.
- Devuelven un código de salida que se puede usar para controlar la lógica del script.

**Ejemplo con `test`:**
```
if test -f "archivo.txt"; then  
    echo "El archivo existe."  
fi  
```
**Ejemplo con `[]`:**
```
if [ -f "archivo.txt" ]; then  
    echo "El archivo existe."  
fi  
```
#### 7. Comparativa de uso
**Características comunes:**
- Substitución de comandos:
`$()` y **\`...`** sirven para capturar la salida de comandos, aunque el primero es preferido por su legibilidad.


### Estucrturas de Control

#### Condicionales

```bash
if [ $NOMBRE == "Juan" ]; then
    echo "Hola, Juan"
else
    echo "No sos Juan"
fi
```

#### Bucle 'for'
for: permite ejecutar un bucle hasta que se cumpla una condición.

## Ejemplo:
```bash
# C style

for (( i = 1; i <= 5; i++ )); do
    echo "Iteración $i"
done

# lista

for color in rojo verde azul; do
    echo "El color es $color"
done
```

#### Bucle 'While'

```bash
COUNTER=1
while [ $COUNTER -le 5 ]; do
    echo "Contador: $COUNTER"
    ((COUNTER++))
done
```
### Case

case: Evalúa una variable y ejecuta el bloque de código correspondiente al patrón que coincida.

```bash
case $1 in
    start)
        echo "Iniciando servicio..."
        ;;
    stop)
        echo "Deteniendo servicio..."
        ;;
    restart)
        echo "Reiniciando servicio..."
        ;;
    *)
        echo "Opción no válida."
        ;;
esac
```

#### Funciones

```bash
funcion_saludo() {
    echo "Hola $1"
}
funcion_saludo "Juan"
```

#### Parametros para un script Bash

##### $0 es el nombre del script

##### $1 es el primer parámetro después del nombre del script

##### $* devuelve todos los argumentos como una única cadena string.
Si se usa entre comillas, se trata como una sola cadena.

##### $# Representa el número total de argumentos pasados al script

##### $@ devuelve todos los argumentos como un iterable
Similar a $*, pero si se usa entre comillas, cada argumento se mantiene como una palabra separada. Esto es útil cuando se quiere iterar sobre los argumentos.

#### Comparadores en Bash

##### Comparadores de Cadenas de corchetes simples y dobles
* `=` Comprueba si dos cadenas son iguales.
```bash
if [ "$a" = "$b" ]; then  
    echo "Las cadenas son iguales"  
fi
```

* `!=` Comprueba si dos cadenas son diferentes.
```bash
if [ "$a" != "$b" ]; then  
    echo "Las cadenas son diferentes"  
fi
```

* `-z` Comprueba si una cadena está vacía (longitud cero).
```bash
if [ -z "$a" ]; then  
    echo "La cadena está vacía"  
fi
```

* `-n` Comprueba si una cadena no está vacía (longitud mayor que cero).
```bash
if [ -n "$a" ]; then  
    echo "La cadena no está vacía"  
fi
```

* `<` y `>` Compara dos cadenas lexicográficamente. Se suele usar dentro de dobles corchetes ([[ ... ]]).
```bash
if [[ "$a" < "$b" ]]; then  
    echo "$a es menor que $b"  
fi
```

##### Comparadores Numéricos de corchetes simples y dobles
* `-eq` Compara si dos números son iguales.
```bash
if [ $a -eq $b ]; then  
    echo "Los números son iguales"  
fi
```

* `-ne` Compara si dos números son diferentes.
```bash
if [ $a -ne $b ]; then  
    echo "Los números son diferentes"  
fi
```

* `-lt` Comprueba si el primer número es menor que el segundo.
```bash
if [ $a -lt $b ]; then  
    echo "$a es menor que $b"  
fi
```

* `-le` Comprueba si el primer número es menor o igual que el segundo.
```bash
if [ $a -le $b ]; then  
    echo "$a es menor o igual que $b"  
fi
```

* `-gt` Comprueba si el primer número es mayor que el segundo.
```bash
if [ $a -gt $b ]; then  
    echo "$a es mayor que $b"  
fi
```

* `-ge` Comprueba si el primer número es mayor o igual que el segundo.
```bash
if [ $a -ge $b ]; then  
    echo "$a es mayor o igual que $b"  
fi
```

##### Comparadores de Archivos de corchetes simples y dobles
* `-e` Comprueba si un archivo existe.
```bash
if [ -e "archivo.txt" ]; then  
    echo "El archivo existe"  
fi
```

* `-f` Comprueba si un archivo es un archivo regular.
```bash
if [ -f "archivo.txt" ]; then  
    echo "Es un archivo regular"  
fi
```

* `-d` Comprueba si un archivo es un directorio.
```bash
if [ -d "directorio" ]; then  
    echo "Es un directorio"  
fi
```

* `-r` Comprueba si un archivo es legible.
```bash
if [ -r "archivo.txt" ]; then  
    echo "El archivo es legible"  
fi
```

* `-w` Comprueba si un archivo es escribible.
```bash
if [ -w "archivo.txt" ]; then  
    echo "El archivo es escribible"  
fi
```

* `-x` Comprueba si un archivo es ejecutable.
```bash
if [ -x "archivo.txt" ]; then  
    echo "El archivo es ejecutable"  
fi
```

* `-s` Comprueba si un archivo no está vacío.
```bash
if [ -s "archivo.txt" ]; then  
    echo "El archivo no está vacío"  
fi
```
##### Comparaciones en corchetes dobles `[[]]`

* &&: AND lógico.
* ||: OR lógico.
* Expresiones Regulares: Permite verificar si una cadena coincide con una expresión regular.
Ejemplo:
`[[ $string =~ regex ]]` 


#### Combinación de Condiciones
Puedes combinar condiciones utilizando los operadores lógicos:

Ejemplo de uso combinado:

```bash
if [ -e "archivo.txt" ] && [ -r "archivo.txt" ]; then  
    echo "El archivo existe y es legible"  
fi
```
