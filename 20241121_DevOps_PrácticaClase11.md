# Practica 11 - Creación de scripts de Bash

## Introduccion

Este ejercicio integrador te permitirá aplicar varios conceptos clave de scripting en Bash, como el uso de variables, control de flujo, funciones, y el formateo de salida de texto, además de la interacción con comandos del sistema Unix/Linux.

## Requerimientos

1. Crea un script de Bash llamado informe_del_sistema.sh que realice las siguientes acciones y aplique los conceptos discutidos anteriormente:

2. Información del sistema: Usando echo, imprime un encabezado que diga "Informe del Sistema para: " seguido del nombre del host del sistema (puedes obtener el nombre del host usando el comando hostname).

3. Fecha y Hora: Imprime la fecha y hora actual en el formato "DD/MM/YYYY HH:MM:SS".

4. Uso del Disco: Utiliza el comando df para mostrar el uso del disco del sistema. Usa grep para filtrar y mostrar solo la línea correspondiente a la partición raíz /.

5. Usuarios Logueados: Muestra una lista de los usuarios actualmente logueados en el sistema. Usa el comando who.

6. Uso de Memoria: Muestra el uso de memoria total y disponible en el sistema. Puedes utilizar el comando free y formatear la salida para que sea legible, indicando los valores en Megabytes.

7. Procesos: Pide al usuario que ingrese un nombre o parte del nombre de un proceso, y utiliza ps -aux para mostrar los procesos que coincidan con ese nombre. Usa grep para filtrar la salida.

## Instrucciones Adicionales:

* Para la fecha y hora, utiliza el comando date y formatea la salida con + seguido del formato deseado.
* Implementa control de flujo con if para verificar si el usuario ingresó o no un nombre de proceso. Si no lo hizo, imprime un mensaje diciendo "No se especificó ningún nombre de proceso".
* Para mostrar el uso del disco y la memoria, asegúrate de usar printf para formatear la salida de manera que sea clara y legible.
* Añade comentarios explicativos en tu script que describan brevemente lo que hace cada parte del código.