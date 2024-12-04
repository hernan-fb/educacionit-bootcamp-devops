# Clase 09
(hasta el minuto 30 estuvo instalando y configurando jenkins y github de cero)


● Con que comando podemos listar y particionar una unidad?
● Es necesario formatear posteriormente a crear la partición?
● Es recomendable usar EXT2 para aplicaciones modernas? Donde lo podemos usar EXT2?
● En qué archivo se introduce la información para montar un dispositivo luego de cada reinicio?

## Volumen Lógico
Los Volúmenes Lógicos (LV) son parte de un método de administración de almacenamiento llamado Logical Volume Management (LVM), que permite una gestión más flexible del espacio en disco que la particionamiento tradicional.
### Que ofrecen los Volúmenes Lógicos
● Flexibilidad: puedo modificar la capacidad sin desmontar la unidad.
● Abstracción: permite un grado de abstracción de los dispositivos físicos.
### Para qué usamos Volúmenes Lógicos?
Gestión del Espacio: permite una gestión más eficiente y flexible del espacio en disco.
Snapshotting: facilita la creación de instantáneas (snapshots) de sistemas de archivos.
Alta Disponibilidad: al combinar múltiples discos en un solo volumen lógico, se puede mejorar la tolerancia a fallos.
### ¿Cómo se usan los Volúmenes Lógicos?
● Creación de un PV (volumen físico):
● Creación de un Grupo de Volúmenes (VG): Primero se crean grupos de volúmenes 
combinando uno o más dispositivos de almacenamiento físico.
● Creación de Volúmenes Lógicos (LV): Dentro de un grupo de volúmenes, se crean los volúmenes lógicos, asignándoles espacio del grupo según sea necesario.
● Formateo y Montaje: Los volúmenes lógicos se formatean con un sistema de archivos y se montan para ser utilizados por el sistema operativo y las aplicaciones.
● Gestión Dinámica: Se pueden ajustar los tamaños de los volúmenes lógicos, crear o eliminar volúmenes, y tomar snapshots, todo ello con comandos específicos del sistema de gestión de volúmenes lógicos.
