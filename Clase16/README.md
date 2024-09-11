
# Ejercicios de Práctica de Python

Este repositorio contiene una serie de ejercicios para practicar el uso de Python. Los ejercicios están diseñados para ir desde lo más básico hasta conceptos más avanzados.

## Instrucciones

### Ejercicio 1: Imprimir en pantalla
- **Comando**: `print()`
- **Explicación**: Aprende a mostrar texto en la consola.
- **Instrucciones**:
  1. Escribe un programa que imprima "Hola, Mundo!".
- **Salida esperada**:
  ```python
  print("Hola, Mundo!")
  ```
  ```plaintext
  Hola, Mundo!
  ```

### Ejercicio 2: Operaciones matemáticas
- **Comando**: `+`, `-`, `*`, `/`, `//`, `%`, `**`
- **Explicación**: Realiza operaciones matemáticas básicas.
- **Instrucciones**:
  1. Escribe un programa que sume, reste, multiplique, y divida dos números, y muestre los resultados.
- **Salida esperada**:
  ```python
  a = 10
  b = 5
  print(a + b)  # Suma
  print(a - b)  # Resta
  print(a * b)  # Multiplicación
  print(a / b)  # División
  print(a // b) # División entera
  print(a % b)  # Módulo
  print(a ** b) # Exponente
  ```
  ```plaintext
  15
  5
  50
  2.0
  2
  0
  100000
  ```

### Ejercicio 3: Uso de variables
- **Comando**: Asignación de variables
- **Explicación**: Almacena valores en variables para su uso posterior.
- **Instrucciones**:
  1. Escribe un programa que guarde tu nombre en una variable y luego imprima "Hola, [nombre]".
- **Salida esperada**:
  ```python
  nombre = "Matias"
  print(f"Hola, {nombre}")
  ```
  ```plaintext
  Hola, Matias
  ```

### Ejercicio 4: Condicionales
- **Comando**: `if`, `else`, `elif`
- **Explicación**: Toma decisiones basadas en condiciones.
- **Instrucciones**:
  1. Escribe un programa que verifique si un número es positivo, negativo o cero.
- **Salida esperada**:
  ```python
  num = 10
  if num > 0:
      print("Positivo")
  elif num < 0:
      print("Negativo")
  else:
      print("Cero")
  ```
  ```plaintext
  Positivo
  ```

### Ejercicio 5: Bucles
- **Comando**: `for`, `while`
- **Explicación**: Repite acciones automáticamente.
- **Instrucciones**:
  1. Usa un bucle `for` para imprimir los números del 1 al 5.
  2. Usa un bucle `while` para imprimir los números del 5 al 1.
- **Salida esperada**:
  ```python
  for i in range(1, 6):
      print(i)

  i = 5
  while i >= 1:
      print(i)
      i -= 1
  ```
  ```plaintext
  1
  2
  3
  4
  5
  5
  4
  3
  2
  1
  ```

### Ejercicio 6: Listas y bucles
- **Comando**: `list`, `for`, `append()`
- **Explicación**: Manipula listas y recorre sus elementos.
- **Instrucciones**:
  1. Crea una lista de números del 1 al 5 y usa un bucle `for` para imprimir cada número.
  2. Añade un número a la lista y luego imprime la lista completa.
- **Salida esperada**:
  ```python
  numeros = [1, 2, 3, 4, 5]
  for num in numeros:
      print(num)

  numeros.append(6)
  print(numeros)
  ```
  ```plaintext
  1
  2
  3
  4
  5
  [1, 2, 3, 4, 5, 6]
  ```

### Ejercicio 7: Funciones
- **Comando**: Definición de funciones
- **Explicación**: Agrupa código en funciones reutilizables.
- **Instrucciones**:
  1. Define una función llamada `saludar` que tome un parámetro (nombre) y que imprima "Hola, [nombre]".
  2. Llama a la función con tu nombre.
- **Salida esperada**:
  ```python
  def saludar(nombre):
      print(f"Hola, {nombre}")

  saludar("Matias")
  ```
  ```plaintext
  Hola, Matias
  ```

### Ejercicio 8: Manejo de cadenas
- **Comando**: Métodos de cadenas (`upper()`, `lower()`, `split()`, etc.)
- **Explicación**: Manipula y procesa cadenas de texto.
- **Instrucciones**:
  1. Escribe un programa que convierta una cadena a mayúsculas y luego la divida en palabras.
- **Salida esperada**:
  ```python
  texto = "Hola mundo"
  texto_mayus = texto.upper()
  palabras = texto_mayus.split()
  print(texto_mayus)
  print(palabras)
  ```
  ```plaintext
  HOLA MUNDO
  ['HOLA', 'MUNDO']
  ```

### Ejercicio 9: Diccionarios
- **Comando**: Diccionarios, manipulación de pares clave-valor
- **Explicación**: Almacena y accede a datos en pares clave-valor.
- **Instrucciones**:
  1. Crea un diccionario con información de una persona (nombre, edad, ciudad) y muestra cada valor.
- **Salida esperada**:
  ```python
  persona = {
      "nombre": "Matias",
      "edad": 30,
      "ciudad": "Buenos Aires"
  }
  print(persona["nombre"])
  print(persona["edad"])
  print(persona["ciudad"])
  ```
  ```plaintext
  Matias
  30
  Buenos Aires
  ```

### Ejercicio 10: Manejo de archivos
- **Comando**: `open()`, `write()`, `read()`
- **Explicación**: Crea, lee y escribe archivos.
- **Instrucciones**:
  1. Escribe un programa que cree un archivo de texto, escriba una línea en él, y luego lea e imprima el contenido.
- **Salida esperada**:
  ```python
  with open("archivo.txt", "w") as file:
      file.write("Hola, este es un archivo de prueba.")

  with open("archivo.txt", "r") as file:
      contenido = file.read()
      print(contenido)
  ```
  ```plaintext
  Hola, este es un archivo de prueba.
  ```

## Requisitos

- Tener Python instalado en tu sistema.
- Conocimientos básicos de programación.

## Cómo usar este repositorio

1. Clona este repositorio:
   ```bash
   git clone git@github.com:yosoyfunes/bootcamp-devops-2024.git
   ```
2. Sigue los ejercicios en el orden sugerido.
3. Si tienes dudas, consulta la [documentación oficial de Python](https://docs.python.org/3/).

¡Buena suerte con tu práctica!
