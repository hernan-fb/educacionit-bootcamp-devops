__Listar Dispositivos de Bloque__: ls -l /dev | grep “^b”
__Listar Dispositivos de Carácter__: ls -l /dev | grep “^c”
__Descartar salida de un comando__: ls / > /dev/null
__Verificar logs del sistema__: tail -n 20 /var/log/syslog
__Información del CPU__: cat /proc/cpuinfo
__Información de RAM (Memoria)__: cat /proc/meminfo
__Tiempo de actividad del sistema__: cat /proc/uptime
__Mostrar el estado de un proceso__: cat /proc/12/status
__Mostrar todas las particiones del sistema__: cat /proc/partitions
__Mostrar información de Dispositivos Montados__: cat /proc/mounts