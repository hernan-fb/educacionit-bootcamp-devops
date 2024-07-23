Eres un DevOps Engineer que recibio una alerta de que una aplicación crítica en una instancia EC2 (en AWS) está experimentando problemas de rendimiento y accesibilidad. Los usuarios han reportado que la aplicación Apache 2 está respondiendo lentamente y, en algunos casos, no está accesible.

### Para realizar este ejercicio, tenes que instalar Apache 2 en tu Multipass
*sudo apt-get install apache2*

Tarea:

#### Paso 1: Verificar el Estado del Sistema de Archivos y Dispositivos
- Lista los dispositivos de bloque y asegúrate de que todos los discos necesarios están disponibles y no presentan problemas.
- Lista los dispositivos de carácter y confirma que no hay problemas con los dispositivos de entrada/salida.
- Lista todas las particiones y asegúrate de que no hay particiones llenas o mal configuradas.
- Revisa la información de montajes para asegurarte de que todas las particiones necesarias están montadas correctamente.

#### Paso 2: Verificar el Rendimiento del Sistema
- Revisa la información del CPU para entender mejor la capacidad del sistema.
- Analiza la información de la memoria para identificar posibles cuellos de botella.
- Analiza los últimos 20 registros del log del sistema para confirmar que no haya nada mal con el SO.

#### Paso 3: Verificación y Reinicio de Servicios
- Verificar el estado del servicio Apache
- Reiniciar el servicio Apache
- Verificar los logs en /var/log/apache2/error.log para ver si todo funciona bien

