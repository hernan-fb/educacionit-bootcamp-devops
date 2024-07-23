### Para realizar este ejercicio, tenes que instalar Apache 2 en tu Multipass
*sudo apt-get install apache2*

- __Verificar que init manager estamos usando:__ ps -p 1 -o comm=
- __Comprobemos el estado de un servicio (SysVinit):__ sudo service apache2 status
- __Listar todos los servicios en ejecución (SysVinit):__ sudo service --status-all
- __Reiniciar un servicio (SysVinit):__ sudo service apache2 stop AND sudo service apache2 start
- __Verifica los logs de un servicio:__ tail -f /var/log/apache2/error.log
- __Verificar si el proceso está en ejecución:__ ps aux | grep apache2
- __Comprobemos el estado de un servicio (systemD):__ sudo systemctl status apache2
- __Listar todos los servicios en ejecución (systemD):__ sudo systemctl list-units --type=service –-state=running
- __Verificar si el servicio está habilitado al arranque:__ sudo systemctl is-enabled apache2
- __Cambiar configuración del servicio sin reiniciarlo:__ sudo systemctl reload apache2