#### Ejercicio 1: Verificar el Espacio Disponible en el Sistema
    df -h
#### Ejercicio 2: Mostrar el Número de Inodos Utilizados y Disponibles
    df -i
#### Ejercicio 3: Ver el Espacio Ocupado por el directorio /var
    du -ch /var | grep '/var$'
    du -ch /var | grep 'total$'
    du -sh /var  
    Nota: -s muestra solamente el total para cada argumento
#### Ejercicio 4: Mostrar el Espacio Ocupado por Todos los Subdirectorios de /etc
    du -ch /etc | grep '/etc/'
    du -h --max-depth=1 /etc/
#### Ejercicio 5: Identificar los 10 Archivos más Grandes en /var/log
    du /var/log | sort -nr | head -n 10
    du -s /var/log/* | sort -nr | head -n 10
#### Ejercicio 6: Mostrar el Espacio Ocupado por todos los archivos '.log' en /var/log
    du -s /var/log/*.log | sort -nr
#### Ejercicio 7: Crear un Reporte del Espacio Ocupado por /var/log, sus subdirectorios, y los directorios de sus subdirectorios.