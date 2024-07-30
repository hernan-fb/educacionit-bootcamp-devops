Mostrar en pantalla ‘Hola, Mundo!’
```bash
#!/bin/bash
echo "Hola, Mundo!"
```

Solicitar al usuario ingresar dos números y sumarlos

```bash
#!/bin/bash
echo "Ingrese dos números, así los puedo sumar"
echo "primer número: "
read NUMERO1
echo "segundo número: "
read NUMERO2
RESULTADO = $((NUMERO1 + $NUMERO2))
echo "la suma es: $RESULTADO"
```
Los paréntesis dobles $((...)) se utilizan para indicar que lo que está dentro de ellos es una expresión aritmética. Esto permite a Bash interpretar correctamente la operación de suma. Sin los paréntesis, Bash no interpretaría num1 + num2 como una operación matemática, sino que trataría de leerlo como una simple cadena de texto, lo que resultaría en un error.

Solicitar al usuario un numero, y verificar si es par o impar
```bash
#!/bin/bash
echo "Ingrese un número, así me fijo si es par o impar"
echo "número: "
read NUMERO1
if [ $((NUMERO1 % 2))==0 ]; then
    echo "Es un número par!!!"
else
    echo "No es un número par..."
fi 

```

Imprimir los números del 1 al 10 con ‘for’
```bash
#!/bin/bash
echo "A continuación, los nros del 1 al 10 con un for"
for i in {1..10}; do
    echo i+" "
done

```

Solicitar al usuario ingresar números hasta que el número ingresado sea 0. Una vez ingrese 0, sumar todos los números ingresados
```bash
#!/bin/bash
echo "Ingresar números a sumar. Al ingresar 0 se mostrará la suma total:"
NUMERITO=1
TOTAL=0
while [[ $NUMERITO != 0 ]]; do
    read NUMERITO
    TOTAL=$((TOTAL + NUMERITO))
done
echo "La suma es: $TOTAL"
```


Crear un archivo llamado ‘alumnos.txt’ que agregue una lista de nombres. Luego, leer e imprimir el contenido del archivo
```bash
#!/bin/bash
echo "Hernán" >> alumnos.txt
echo "Hernán" >> alumnos.txt
echo "Ahora se muestra en pantalla la lista de nombres del archivo alumnos.txt"
cat alumnos.txt
```

Definir una función que imprima 3 veces en pantalla ‘Hola, Mundo!’ 
```bash
#!/bin/bash
funcion_saludo() {
    for i in {1..3}; do
        echo "Hola, Mundo!"
    done
}
funcion_saludo
```

Aceptar dos argumentos, y al ejecutarse imprimir la suma
```bash
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Uso: $0 num1 num2"
    exit 1
fi

NUM1=$1
NUM2=$2
SUMA=$((NUM1+NUM2))
echo "La suma de los parámetros ingresados es: $SUMA"
```
Encontrar y contar todas las lineas un archivo de texto que contengan una palabra especifica
```bash
#!/bin/bash
if [ $# -ne 2 ]; then
    echo "Uso: $0 archivo palabra_a_contar"
    exit 1
fi

RESULTADO=$(grep -c $1 $2)

echo "Se buscó la palabra $2 en el archivo $1, y aparece $RESULTADO veces"
```
