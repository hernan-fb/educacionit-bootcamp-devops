Modulo 02 - Clase 03
### Resumen de comandos explicados en este archivo:
#### Ver el tipo de sistema de archivos: 
* file -sL /dev/sda1

#### Mostrar inodos libres: 
* df -i

#### Mostrar espacio: 
* df -h

#### Mostrar listado de particiones, espacio ocupado y punto de montaje: 
* df -h | grep '^/dev' | tr -s ' ' | cut -d' ' -f1,2,6

#### Espacio total ocupado por una carpeta (ej. /etc): 
* du -sch /etc

#### Espacio total ocupado por distintos directorios: 
* du -sch /*
#### Espacio total ocupado por distintos directorios en distinta profundidad: 
* du -h -max-depth=2 /home
#### Traer los 10 archivos más grandes en el directorio actual: 
* du -sm * | sort -nr | head


### Sistema de Archivos
Un conjunto cualquiera de páginas apiladas no necesariamente es un libro. Otras características como los índices y los números de páginas son los que lo convierten en un libro. Con un sistema de archivos pasa algo parecido. Un montón de información apilada no hace a un sistema de archivos.

Un sistema de archivos sirve para poder localizar la información de manera coherente. A diferencia 
de un libro necesitamos coherencia, no 
solamente para “leer” sino para “escribir”, es 
decir para modificar la información almacenada.

Ext2, ext3 y ext4 son sistemas de archivos creados para Linux.

#### Ext2
Ext2 es sinónimo de second extended filesystem  o "segundo sistema de archivos extendido". Fue introducido en 1993 y desarrollado por Rémy Card. Este fue desarrollado para superar la limitación del sistema de archivos original ext. 
Ext2 no tiene característica de journaling (no lleva registro de los movimientos de archivos).
● Se recomienda en las unidades flash, unidades USB, ext2, ya que no tiene que utilizar journaling.
● El tamaño máximo de archivo individual puede ser de 2 TB. En general el tamaño del sistema de archivos ext2 puede ser de hasta 32 TB.


#### Ext3
Ext3 es sinónimo de third extended filesystem o "tercer sistema de archivos extendido". Fue introducido en 2001. Desarrollado por Stephen Tweedie, está disponible a partir del kernel Linux 2.4.15.
● La principal ventaja de ext3 es que permite journaling. En journaling tiene un área dedicada en el sistema de archivos, donde se registran todos los cambios. Cuando el 
sistema se cuelga, la posibilidad de corrupción del sistema de archivos es menor debido al journaling.
● El tamaño máximo de archivo individual puede ser de hasta 2 TB. En general el tamaño del sistema de archivos ext3 puede ser de hasta 32 TB.
● Puedes convertir un sistema de archivos ext2 
al sistema de archivos ext3 directamente (sin 
copia de seguridad / restauración).

● Hay tres tipos de journaling disponibles en 
el sistema de archivos ext3:
 - Journal: Los metadatos y el contenido se guardan en el journaling.
 - Ordered: Los metadatos sólo se salvan en el journaling. Los metadatos son volcados al journaling sólo después de escribir el contenido en el disco. Este es el valor predeterminado.
 - Writeback: Los metadatos sólo se salvan en el journaling. Los metadatos pueden estar en el journaling, ya sea antes o después de que el contenido se grabe en el disco

#### Ext4
Es el más usado por AWS. AWS ofrece una variedad de sistemas de archivos para sus instancias EC2 y volúmenes de EBS, pero EXT4 es el predeterminado y más comunmente usado debido a su balance entre rendimiento estabilidad y características.
● Ext4 es sinónimo de fourth extended filesystem o “cuarto sistema de archivos extendido”. Fue introducido en 2008, a partir del kernel Linux 2.6.19 ext4.
● Lleva muchos años, fue probado extensamente, lo que lo hace confiable, es una versión mejorada de ext3.
● El tamaño máximo de archivo individual puede ser de hasta 16 TB.
● El tamaño promedio global del sistema de archivos ext4 es 1 EB (Exabyte). 1 EB = 1024 PB (petabytes). 1 PB = 1024 TB (terabyte).
● Un directorio puede contener un máximo de 64,000 subdirectorios (en comparación con 32.000 en ext3).
● También puede montar una fs ext3 existente como un fs ext4 (sin tener que actualizar).
● Otras nuevas características son introducidas en ext4: multiblock allocation, delayed allocation, journal checksum. fast fsck, etc. 
Estas nuevas características han mejorado el rendimiento y la fiabilidad del sistema de archivos cuando se compara con ext3.
● El journaling es un sistema que ayuda a proteger la integridad de los datos en caso de fallos inesperados del sistema, mejorando la durabilidad y la seguridad de los datos.
No tendría mucho sentido que un solo servidor de aws tenga mucha capacidad utilizada pero es un plus el poder tener archivos voluminosos o sistemas de archivos de mucho volumen.

Cuando en AWS creás un EDS (algo asicomo un pendrive) cuando ese pendrive vos lo conectes o montes en el servidor, qeu en AWS se dice instancia, si usás los settings automáticos, va a estar en EXT4.

#### EFS (Elastic File System)
 Un sistema de archivos administrado por AWS que se escala automáticamente y permite el acceso concurrente desde múltiples instancias EC2. Es ideal para aplicaciones que necesitan almacenamiento compartido y escalable.

#### EBS (Elastic Block Store)
Aunque no es un sistema de archivos per se, los volúmenes de EBS se utilizan para almacenar datos y pueden ser formateados con cualquier sistema de archivos soportado por Linux, como EXT4, XFS o BTRFS. Automático está EXT4.

#### Conversión de un Sistema de Archivos

- De ext2 a ext3
Para convertir un sistema de archivos de ext2 a ext3 se utiliza el comando tune2fs.
Desmontar la unidad (en este caso no es obligatorio, pero sí recomendable): 
	`# umount /dev/sda2`

Ejecutar la conversión:
	`# tune2fs -j /dev/sda2`

Montar la partición nuevamente:
	`# mount /dev/sda2 /home`
	
- Convertir ext3 a ext4
Para convertir un sistema de archivos de ext3 a ext4 también se utiliza el comando tune2fs.

- Desmontar la partición	
	`# umount /dev/sda2`

- Ejecutar la conversión:
	`# tune2fs -O extents,uninit_bg,dir_index /dev/sda2`
	
- Verificar el sistema de archivos:
	`# e2fsck -pf /dev/sda2`
	
- Montar la partición nuevamente: 
	`# mount /dev/sda2 /home`

#### XFS
El sistema de archivos xfs fue creado por Silicon 
Graphic Inc. y se agregó al kernel Linux en la 
versión 2.4. 
● Alta escalabilidad, es capaz de crear particiones de unos 109 GB.
● Uso eficiente del espacio.
● Sistema transaccional de alto rendimiento.
● Rápida recuperación.
● Capacidad para establecer límite de ocupación 
por directorios.

Supongamos que queremos formatear la 
partición /dev/sdb6 como xfs, haríamos lo 
siguiente:
	`# make.xfs /dev/sdb6`

- xfs_info
	Muestra información del sistema de archivos.

#### BTRFS
El proyecto btrfs es relativamente nuevo y activo, sin embargo distribuciones como CentOS consideran que aún no es maduro de forma suficiente para usarlo en producción. 
La distribución openSUSE tiene una posición diametralmente opuesta: lo usa como sistema de archivos predeterminado. Fedora a partir de su versión 33 lo usa como predeterminado para su edición Workstation.
Una de las características interesantes es que puede guardar y restaurar el estado del sistema de archivos (snapshots) y usar subvolumenes (raíces alternativas).

Para crear una partición con btrfs se usa el 
archivo correspondiente al disco entero:

	# mkfs.btrfs /dev/sdb

En btrfs podemos pensar en él como un 
determinado espacio disponible que incluso 
puede abarcar más de un disco. Por ejemplo:

	# mkfs.btrfs -L “Mi Espacio” /dev/sdb /dev/sdc

Luego se puede montar:
	# mount /dev/sdb /mnt

Para ver información del sistema de archivos:
	# btrfs filesystem show
	Label: 'Mi Pool' uuid: 8de75101-81da-4dfe-a97f-c9f77622d19c
	 Total devices 2 FS bytes used 256.00KiB
	 devid 1 size 1.00GiB used 212.75MiB path /dev/sdb
	 devid 2 size 1.00GiB used 212.75MiB path /dev/sdc

De manera predeterminada los datos se distribuyen entre 
los dos discos, mientras que los metadatos se espejan en 
cada disco.

Nota: en el sistema btrfs se puede formatear sin particionar, no obstante en la gran mayoría de los casos conviene crear las particiones necesarias para poder aprovechar en su totalidad, las funcionalidades del sistema de archivos. 

#### Sistema de Archivos VFAT

Es el sistema de archivos MS DOS (es decir, no es 
nativo de Linux) con soporte de nombres largos 
de archivos. El comando mkfs.vfat crea una 
partición vfat. 

	# mkfs.vfat /dev/sda1

#### Sistema de archivos exFAT

El sistema de archivos exFAT fue creado por Microsoft posee similitudes con FAT32, pero brinda mayores funcionalidades (por ejemplo, tamaño de archivos más grandes que 4GiB)
En Linux existe una implementación libre, se trata de un módulo de sistema de archivos en espacio de usuario. En el caso de Debian deben estar instalados los paquetes exfat-fuse (para montar particiones en exFAT) y exfat-utils (herramientas para manipular particiones en exFAT). En CentOS se puede usar el repositorio adicional LiFTeR

Para formatear, por ejemplo:

	# mkfs.exfat /dev/sda1
	
Una vez creada la partición, el sistema de 
archivos debe ser añadido para que Linux pueda 
hacer uso de este espacio. La utilidad mkfs se usa 
para crear sistemas de archivos en particiones 
vacías. La utilidad mkfs se utiliza con muchas 
opciones distintas, como las siguientes:	

- -t fstype		Especifica el tipo de sistema de ficheros a crear. Por defecto se usa ext2.

- fs -options		Opciones específicas de sistema de ficheros para ser pasados al sistema real de ficheros que vamos a crear.

- -c				Comprueba el dispositivo en busca de bloques defectuosos antes de crear el sistema de ficheros.

- -l fichero 		Lee los bloques defectuosos del fichero.

- -v				Produce una salida con más información,
incluyendo todas las órdenes específicas del sistema de ficheros concreto que se ejecutan. Ésto es realmente sólo útil para comprobaciones


Las opciones usadas por mkfs están seguidas 
por un argumento especificando la partición que 
debe ser formateada. La sintaxis correcta para 
este comando es la siguiente:

	# mkfs [opciones] unidadAFormatear → /dev/sd[abcd][1234]
	
Algunas otras utilidades del comando mkfs son 
las siguientes:

| Opciones | Descripción |
|----------| -------------|
|mkfs.ext2 	| Crea un sistema de ficheros ext2.|
|mke2f 		| Crea un sistema de ficheros ext2.|
|mkfs.ext3 	| Crea un sistema de ficheros ext3.|
|mke2fs –j 	| Crea un sistema de ficheros ext3.|
|mkfs.ext4 	| Crea un sistema de ficheros ext4.|
|mke2fs -t ext4 | Crea un sistema de ficheros ext4.|

Nota: estos comandos no piden confirmación.

Otros comandos (contradicciones):

Crear sistema de archivos en ext2:

	#mke2fs /dev/sda1
	
Crear sistema de archivos en ext3:

	# mkfs.ext3 /dev/sda1
	# mke2fs –j /dev/sda1

Crear sistema de archivos en ext3:

	#mkfs.ext4 /dev/sda1
	#mke2fs -t ext4 /dev/sda1

#### Creando Swap

Muestra el uso de las particiones swap:

	# swapon -s
	Filename Type Size Used Priority
	/dev/sda3 partition 4199420 0 -1
	
Crea el swap en la partición /dev/sda3:

	# mkswap /dev/sda3
	
Habilitar el swap

	# swapon /dev/sda3

Deshabilitar el swap

	# swapoff /dev/sda3


### Fuentes y Más recursos

#### *En libros*
● “CompTIA Linux+ / LPIC-1 Cert Guide: (Exams LX0-103 & 
LX0-104/101-400 & 102-400)”, Ross Brunson & Sean 
Walberg.
● “CompTIA Linux+ /LPIC-1 Certification All-In-One Exam 
Guide, Premium Second Edition With Online Practice 
Labs (Exams LX0-103 & LX0-104/101-400 & 102-400)”, 
Robb Tracy.
● “LPIC-1 Certification All-in-One Exam Guide Exams”, 
Robb Tracy.

#### _En Internet_
● Introducción a los sistemas de archivos.
https://smr.iesharia.org/wiki/doku.php/ssv:ut3:intro_sis_arch
● ext4 - Wikipedia.
https://es.wikipedia.org/wiki/Ext4
● Understanding Linux filesystems: ext4 and beyond.
https://es.wikipedia.org/wiki/Ext4
● A high-level discussion of Linux filesystem concepts
https://opensource.com/life/16/10/introduction-linux-filesystems
● Comparison of file systems - Wikipedia
https://en.m.wikipedia.org/wiki/Comparison_of_file_systems


### Introducción a Discos Duros

#### Tipos de Discos Rígidos

Es importante tener una noción acerca de los tipos de discos rígidos (IDE; SCSI; SATA).
Un disco duro es un dispositivo de almacenamiento masivo de datos que puede tener instalado algún Sistema Operativo. Así mismo, funciona como memoria no volátil, es decir, cuando se interrumpe el suministro de energía eléctrica, la información queda almacenada. 
Un disco rígido convencional (Hard Disk) emplea un sistema de grabación magnética, el cual es aplicado a una serie de platos metálicos apilados girando a gran velocidad. Sobre estos platos se sitúan los cabezales encargados de leer o escribir los impulsos magnéticos.

#### Discos Rígidos IDE-ATA
Los discos rígidos con esta denominación hacen uso de una interfaz llamada IDE (Integrated Device Electronics) ATA (Advanced Technology Attachment), que es la encargada de comunicar al Disco Duro con la tarjeta madre. El estándar IDE-ATA fue diseñado originalmente para conectar discos duros; sin embargo, se desarrolló una extensión llamada ATAPI que permite interconectar otros periféricos de almacenamiento como unidades de CD o unidades de DVD en una interfaz IDE-ATA. 

Habitualmente, un disco duro IDE-ATA puede estar configurado de tres maneras diferentes:
● Maestro. Los discos rígidos con esta configuración indican a la placa madre que él debe ser el primero en ser leído, y, por ende, el primero en arrancar
● Esclavo. Los discos rígidos con este tipo de configuración no son tomados en cuenta al momento de arrancar el sistema, por lo que el disco rígido maestro puede disponer de los demás discos duros configurados como discos esclavos.
● Selección por cable.El dispositivo será maestro o esclavo en función de su posición en el cable. Si hay otro dispositivo, también debe estar configurado como cable select. Si el dispositivo es el único en el cable, debe estar situado en la posición de maestro. 

El diseño IDE-ATA tiene el inconveniente de que mientras se accede a un dispositivo, el otro dispositivo del mismo conector IDE no se puede usar. Este inconveniente está resuelto en discos duros como los SATA y en SCSI, que pueden usar dos dispositivos por canal.


#### Discos Rígidos SATA
Los discos rígidos con esta denominación hacen uso de una interfaz llamada SATA (Serial Advanced Technology Attachment), que es la encargada de comunicar los datos del Disco rígido con la placa madre. Estos discos duros sustituyen a los tradicionales IDE-ATA, además de proporcionar mayores velocidades, mejor aprovechamiento cuando hay varios discos, mayor longitud del cable de transmisión de datos y capacidad para conectar discos en caliente (con la computadora encendida). 

El estándar Serial ATA se basa en una comunicación en serie. Se utiliza una ruta de datos para transmitir los datos y otra ruta para transmitir las confirmaciones de recepción. 

**_Características_**
El estándar Serial ATA brinda una velocidad de 187.5 MB/s ó 1.5 Gb/s. Cada octeto se transmite con un bit de arranque y un bit de parada, con una velocidad efectiva teórica de 150 MB/s ó 1,2 Gb/s. El estándar Serial ATA II debe contribuir a alcanzar 375 MB/s ó 3 Gb/s, es decir, una velocidad efectiva teórica de 300 MB/s, y finalmente 750 MB/s ó 6 Gb/s, es decir, una velocidad efectiva teórica de 600 MB/s. Los cables del estándar Serial ATA pueden medir hasta 1 metro de longitud (en comparación con los 45 cm que miden los cables IDE). 
Además, la baja cantidad de hilos en una envoltura redonda permite una mayor flexibilidad y una mejor circulación del aire dentro de la carcasa que la de los cables IDE(incluso si existieran los cables IDE redondeados). 
A diferencia de los periféricos del estándar ATA, los del Serial ATA se encuentran solos en cada cable y ya no es necesario diferenciar los discos duros maestros de los discos duros esclavos. 
Otra de las ventajas con este tipo de disco es que permiten la conexión en caliente o en pocas palabras, mientras el equipo está encendido.

#### Discos Duros SCSI
El estándar SCSI (Small Computers System Interface) es una interfaz que se utiliza para permitir la conexión de distintos tipos de periféricos a un ordenador, vía una tarjeta llamada adaptador SCSI o controlador SCSI. 
El número de periféricos que se pueden conectar depende del ancho del bus SCSI. 

Los periféricos se direccionan vía números de identificación. El primer número es el ID, número que designa al controlador que se encuentra dentro de cada periférico (definido a través de los jumpers posicionados en cada periférico SCSI o por el software). El periférico puede tener hasta 8 unidades lógicas (por ejemplo, una unidad de CD-ROM con varios cajones). Las unidades lógicas se identifican mediante un LUN (Número de unidad lógica). Por último, un ordenador puede contener diversas tarjetas SCSI y, por lo tanto, a cada una le corresponde un número diferente.

##### Existen Dos tipos de bus SCSI
● Bus asimétrico: SE (Single-Ended) (Terminación única). Basado en una arquitectura paralela en la que cada canal circula en un alambre sensible a las interferencias. Los cables SCSI en modo SE poseen 8 alambres para una transmisión de 8 bits (que se denominan limitados) o 16 alambres para cables de 16 bits (conocidos como extendidos). Este es el tipo de bus SCSI más común. 

● Bus diferencial: Transporta señales a un par de alambres. Modo LVD (Voltaje bajo diferencial). Modo HVD (Voltaje Alto Diferencial).

Los conectores para las dos categorías de periféricos son los mismos, pero las señales eléctricas son diferentes. Por lo tanto, los periféricos necesitan ser identificados (mediante los símbolos creados para tal fin) para no dañarlos.
En general, existen dos modos: el modo LVD (Voltaje bajo diferencial), basado en señales de 3,3 V y el modo HVD (Voltaje Alto Diferencial), que utiliza señales de 5V.


---
**EXTRA DE CLASE**

Esto vamos a ver con cada herramienta del bootcamp:

Terraform para infraestructura como código. Lo desarrolló una empresa que empezó como código abierto, y ahora hace poco locompró ibm y fue cambiando la licencia de sus productos y pronto va a dejar de ser open.

Y ahora apareció un fork específico (copia del mismo repo) Open Tofu, que es mejor porque va a seguir siendo opensource, no hay mucha documentación, y entonces muchos devops están con Terraform, porque van a aparecer más resultados al googlear por un problema. En cambio con Open Tofu van a haber menos resultados.

No siempre vamos a usar la última herramienta del mercado, especialmente las empresas más robustas van a usar herramientas que capáz sean antiguas pero mucho más estables.

---

### Actividad Práctica: Creación de una partición a partir de un archivo en ubuntu

```shell
ubuntu@foo:~$ sudo dd if=/dev/zero of=/mnt/data.img bs=1M count=1024
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 0.542861 s, 2.0 GB/s
```
Con este comando vamos a crear una imagen que pesa 1gb y vamos a usarla para montar como si fuese un disco extra. Es equivalente a alquilar un volumen en AWS.

- dd: Es una utilidad en Unix y Linux que se utiliza para convertir y copiar archivos. En este caso, se utiliza para crear un archivo.
- if=/dev/zero: Especifica el archivo de entrada (input file) que, en este caso, es /dev/zero. Este es un dispositivo especial en Unix que produce una secuencia continua de ceros.
- of=/mnt/data.img: Especifica el archivo de salida (output file). Aquí, se está creando un archivo llamado data.img en el directorio /mnt. Este archivo será el que contenga los datos.
- bs=1M: Establece el tamaño del bloque (block size) a 1 megabyte. Esto significa que dd leerá y escribirá datos en bloques de 1 MB. Bloque de lectura y escritura es de 1mb.
- count=1024: Indica el número de bloques que se van a copiar. En este caso, se están copiando 1024 bloques de 1 MB cada uno.

En resumen, el comando sudo dd if=/dev/zero of=/mnt/data.img bs=1M count=1024 crea un archivo data.img de 1 GB en el directorio /mnt, lleno de ceros. Este tipo de operación es común cuando se necesita crear un archivo de respaldo para propósitos como pruebas, almacenamiento virtual, o preparación para la creación de sistemas de archivos.

```shell
ubuntu@foo:~$ ls /mnt
data.img
```
data.img es lo único que tenemos. Ahora tenemos que asociar ese archivo a un dispositivo de bloque con losetup (lo cual AWS hace automático al alquilar un volumen). En linux cada /dev es un dispositivo de bloques.

```shell
ubuntu@foo:~$ sudo losetup -fP /mnt/data.img
```

Asocia el archivo de imagen (data.img) a un dispositivo loop, en este caso, el primer dispositivo loop disponible (/dev/loop0).
Detalles:
-f: Encuentra el primer dispositivo loop libre.
-P: Permite que losetup busque automáticamente las particiones en el archivo de imagen.

Un dispositivo loop es un tipo de dispositivo en sistemas operativos Linux que permite acceder a archivos como si fueran bloques de dispositivos de almacenamiento físico, como discos duros o particiones. Esto es especialmente útil para trabajar con imágenes de disco, que son archivos que contienen una copia completa de un sistema de archivos.

Características de un dispositivo loop:
1. Virtualización del acceso a archivos: Permite que un archivo sea tratado como un dispositivo de bloque. Esto significa que puedes montar un archivo de imagen como si fuera un disco físico, lo que te permite acceder a su contenido de manera similar a cómo lo harías con un disco duro real.

2. Facilidad de uso: Es común usar dispositivos loop para probar sistemas de archivos, para tareas de recuperación de datos, o para montar imágenes de disco que contienen sistemas operativos o aplicaciones. Puedes crear una imagen de disco, hacer cambios en ella y luego desmontarla sin afectar el sistema de archivos del host.

3. Soporte para particiones: Los dispositivos loop también pueden trabajar con imágenes de disco que contienen particiones múltiples. Al usar la opción -P en losetup, se pueden identificar y acceder a las particiones específicas dentro de la imagen.

4. No requiere hardware adicional: Como son dispositivos virtuales, no se necesita hardware físico adicional para usar un dispositivo loop. Esto los hace muy útiles en entornos de desarrollo y pruebas.

```shell
ubuntu@foo:~$ losetup -a
/dev/loop0: []: (/mnt/data.img)
```

losetup: Es una utilidad en Linux utilizada para configurar y gestionar dispositivos de bucle (loop devices).
- '-a': Esta opción significa "mostrar todos los dispositivos de bucle activos". Cuando utilizas esto, losetup lista todos los dispositivos de bucle que están actualmente configurados en tu sistema y sus respectivas imágenes asociadas.
Los dispositivos de bucle se numeran incrementalmente
- []: Este campo normalmente mostraría información sobre el uso actual del dispositivo de bucle, como el tipo de sistema de archivos que se está utilizando o si hay alguna partición montada. En este caso, está vacío, lo que indica que no hay información adicional asociada con ese dispositivo de bucle.
- (/mnt/data.img): Este es el archivo de imagen asociado con el dispositivo de bucle. En tu caso, significa que el dispositivo de bucle /dev/loop0 está configurado para utilizar el archivo de imagen data.img, que se encuentra en el directorio /mnt/

```shell
ubuntu@foo:~$ lsblk
NAME      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0       7:0    0    1G  0 loop
sda         8:0    0    5G  0 disk
├─sda1      8:1    0    4G  0 part /
├─sda14     8:14   0    4M  0 part
├─sda15     8:15   0  106M  0 part /boot/efi
└─sda16   259:0    0  913M  0 part /boot
sr0        11:0    1   52K  0 rom
ubuntu@foo:/$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            90M  948K   89M   2% /run
efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
/dev/sda1       3.9G  2.9G  955M  76% /
tmpfs           449M     0  449M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/sda16      881M  112M  708M  14% /boot
/dev/sda15      105M  6.1M   99M   6% /boot/efi
tmpfs            90M   12K   90M   1% /run/user/1000
```

Haciendo lsblk en este momento muestra los dispositivos de bloque. Muestra el loop0 que tiene 1gb de espacio. Pero todavía no lo podemos usar, no podemos poner archivos. Para usarlo hay que crear una partición, como a continuación:

```shell
ubuntu@foo:~$ sudo fdisk /dev/loop0
```

Abre la utilidad fdisk para gestionar particiones en el dispositivo loop `/dev/loop0`. Las opciones que se ingresan a continuación son las siguientes:
   - **n**   add a new partition
   - **w**   Save & Exit, write table to disk and exit

```shell
Welcome to fdisk (util-linux 2.39.3).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS (MBR) disklabel with disk identifier 0xdaad05f7.

Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-2097151, default 2048):
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-2097151, default 2097151):

Created a new partition 1 of type 'Linux' and of size 1023 MiB.

Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

Si ahora hacemos df -h va a mostrar todos los file systems, todos lso dispositivos y el recién creado no aparece porqeu todavía no lo montamos, ni tampoco formateamos.
Cuando hacemos la partición elegimos que sistema de archivos usar, es lo que AWS puede hacer automáticamente como EXT4.

```shell
ubuntu@foo:~$ lsblk
NAME      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0       7:0    0    1G  0 loop
└─loop0p1 259:1    0 1023M  0 part
sda         8:0    0    5G  0 disk
├─sda1      8:1    0    4G  0 part /
├─sda14     8:14   0    4M  0 part
├─sda15     8:15   0  106M  0 part /boot/efi
└─sda16   259:0    0  913M  0 part /boot
sr0        11:0    1   52K  0 rom
ubuntu@foo:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            90M  948K   89M   2% /run
efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
/dev/sda1       3.9G  2.9G  923M  77% /
tmpfs           449M     0  449M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/sda16      881M  112M  707M  14% /boot
/dev/sda15      105M  6.1M   99M   6% /boot/efi
tmpfs            90M   12K   90M   1% /run/user/1000

ubuntu@foo:~$ sudo mkfs.ext4 /dev/loop0p1
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 261888 4k blocks and 65536 inodes
Filesystem UUID: 53c02f6a-e2b0-4cc7-9492-2b1b614d4d4c
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376
Allocating group tables: done
Writing inode tables: done
Creating journal (4096 blocks): done
Writing superblocks and filesystem accounting information: done
```

Fdisk formatea la partición (`/dev/loop0p1`) con el sistema de archivos **ext4**.
Le asignó un Universal Unique Identifier, y una vez formateado vamos a poder montarlo, para eso creamos la carpeta donde vamos a montar la unidad, la partición.
Normalmente se usa `/mnt` , pero eventualmente podriá ser cualquier directorio.

```shell
ubuntu@foo:~$ sudo mkdir /mnt/data
ubuntu@foo:~$ ls /mnt
data  data.img
ubuntu@foo:~$ sudo mount /dev/loop0p1 /mnt/data
ubuntu@foo:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            90M  948K   89M   2% /run
efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
/dev/sda1       3.9G  2.0G  1.9G  51% /
tmpfs           449M     0  449M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/sda16      881M  112M  707M  14% /boot
/dev/sda15      105M  6.1M   99M   6% /boot/efi
tmpfs            90M   12K   90M   1% /run/user/1000
/dev/loop0p1    989M   24K  922M   1% /mnt/data
```

Crea un nuevo directorio llamado data en el sistema de archivos de Linux.
Este directorio servirá como punto de montaje para la nueva partición.
Luego monta la partición (`/dev/loop0p1`) en el directorio `/mnt/data`.
Esto permite que el contenido del sistema de archivos en la partición sea accesible desde el directorio montado.


```shell
ubuntu@foo:~$ vim /mnt/data/archivo_de_texto.txt
ubuntu@foo:~$ sudo vim /mnt/data/archivo_de_texto.txt
ubuntu@foo:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
tmpfs            90M  948K   89M   2% /run
efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
/dev/sda1       3.9G  2.0G  1.9G  51% /
tmpfs           449M     0  449M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
/dev/sda16      881M  112M  707M  14% /boot
/dev/sda15      105M  6.1M   99M   6% /boot/efi
tmpfs            90M   12K   90M   1% /run/user/1000
/dev/loop0p1    989M   28K  922M   1% /mnt/data
ubuntu@foo:~$ lsblk
NAME      MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
loop0       7:0    0    1G  0 loop
└─loop0p1 259:1    0 1023M  0 part /mnt/data
sda         8:0    0    5G  0 disk
├─sda1      8:1    0    4G  0 part /
├─sda14     8:14   0    4M  0 part
├─sda15     8:15   0  106M  0 part /boot/efi
└─sda16   259:0    0  913M  0 part /boot
sr0        11:0    1   52K  0 rom
```

Es un proceso un poco largo, que no tendría sentido hacerlo para cada servidor. Y para cada volumen extra que se agregue.

Existe un archivo
`cat /etc/fstab`

 Este es el archivo de configuración del sistema que contiene información sobre los sistemas de archivos y las particiones que deben montarse automáticamente al inicio del sistema. "fstab" significa "file system table" (tabla de sistemas de archivos).
A la hora de que el kernel se levanta, se fija los volúmenes de este archivo y los ejecuta y asigna en startup
Podemos entonces editar ese archivo y agregar el dispositivo que acabamos de crear al final de ese archivo:  `/dev/loop0p1 /mnt/data.img ext4 defaults 0 0`

```shell
ubuntu@foo:~$ cat /etc/fstab
LABEL=cloudimg-rootfs   /        ext4    discard,commit=30,errors=remount-ro    0 1
LABEL=BOOT      /boot   ext4    defaults        0 2
LABEL=UEFI      /boot/efi       vfat    umask=0077      0 1
```
Las etiquetas son una forma de identificar particiones sin depender de la asignación de los dispositivos, que puede cambiar. Ver más adelante sección LABEL.
Sigue el punto de montaje, es decir, la ubicación en el sistema de archivos.
Sigue  el sistema de archivos.
Siguen opciones de montaje, a saber:
	- discard: Activa la recolección de basura en el sistema de archivos, lo que permite gestionar mejor el espacio libre.
	- commit=30: indica que los datos se escribirán en el disco cada 30 segundos en lugar de esperar a que el bufer esté lleno.
	- errors=remount-ro: si hay un error en el sistema de archivos, se volverá a montar como solo lectura (ro), lo que ayuda a proteger los datos.
	
Por último hay dos números que son parámetros relacionados con el sistema de archivos, con el programa fsck (comprobar el sistema de archivos) durante el inicio, a saber:
	- el primer bit indica si la parrtición debe ser ignorada por dump (Un 0 significa que no se debe respaldar mediante dump, mientras que un 1 significa que sí se respaldará.)
	- el segund número indica el orden en el que fsck debe verificar el sistema de archivos (indica el orden en el que fsck verifica los sistemas de archivos durante el arranque:
	0: Significa que fsck ignorará este sistema de archivos y no lo verificará.
	1: Significa que fsck lo verificará primero. Esto generalmente se asigna al sistema de archivos raíz (/), ya que es fundamental para el funcionamiento del sistema.
	2 y números más altos)

#### Dump 
Es una herramienta en sistemas Unix y Linux que se utiliza para hacer copias de seguridad de sistemas de archivos. Su función principal es crear un volcado (dump) de los datos contenida en un sistema de archivos y almacenarlos en un medio de respaldo, generalmente en un archivo de respaldo o en un dispositivo de almacenamiento.
Su objetivo es permitir la recuperación de datos en caso de fallos del sistema, pérdidas de datos, o corrupción de archivos.

#### fsck (File System Consistency Check)
Es una herramienta utilizada en sistemas Unix y Linux para verificar y reparar sistemas de archivos. Su función principal es asegurarse de que el sistema de archivos esté en un estado consistente y libre de errores.
fsck puede intentar corregirlos automáticamente o solicitar la intervención del usuario.

no incluye una etiqueta (LABEL) porque estás especificando un dispositivo en formato de archivo de dispositivo, en este caso, `/dev/loop0p1`

#### LABEL
- Al usar `/dev/loop0p1`, estás haciendo referencia directa a un dispositivo de bloque específico en tu sistema. 
- Las etiquetas (LABEL) permiten identificar sistemas de archivos de una manera más legible. Por ejemplo, en lugar de usar `/dev/loop0p1`, podrías etiquetar tu sistema de archivos como data_volume y luego usar LABEL=data_volume en tu archivo `/etc/fstab`.
- Usar etiquetas proporciona más flexibilidad, ya que la etiqueta seguirá siendo la misma incluso si el dispositivo cambia su nombre (por ejemplo, si `/dev/loop0p1` se convierte en `/dev/loop1p1`, si hay múltiples dispositivos o si el sistema se cambia a otro entorno donde el orden de los dispositivos es diferente).
- para asignar una etiqueta a tu sistema de archivos en ext4:
	`sudo e2label /dev/loop0p1 my_label`

Luego, en tu archivo `/etc/fstab`, podrías agregar la entrada así:
	`LABEL=my_label /mnt/my-volume ext4 defaults 0 0`


Si al hacer el cambio del archivo fstab no hacemos reboot y cerramos el multipass arruinamos la instancia. Creo.

No necesitas reiniciar el sistema para aplicar los cambios que hiciste en /etc/fstab. Puedes usar el comando `mount -a` o montar un sistema de archivos específico según tus necesidades.


### Mantenimiento de Sistemas de Archivos

#### Espacio de los inodos libres en particiones
Un sistema de lectura/escritura no sirve de mucho si crece hasta el punto en que no pueda admitir nuevos ficheros. Esto puede ocurrir si nuestro sistema de ficheros se llena o si se queda sin inodos libres. Los inodos son las estructuras de datos dentro del sistema de archivos que describen los archivos (valga la redundancia) en el disco. Cada sistema de archivos contiene un número finito de inodos que se establecen en el momento de creación del sistema de archivos. 
Este número es, a su vez, el máximo número de archivos que un sistema de archivos puede acomodar. Como se crean con un número de inodos enorme, probablemente nunca crearemos tantos archivos como para agotar este número. No obstante, es posible quedarse sin inodos libres en particiones que contengan muchos ficheros pequeños.

#### Comando df
El comando df (disk free) muestra información sobre el uso del espacio en disco de los **sistemas de archivos** montados. Es muy importante prevenir la escasez de inodos libres en las particiones del sistema. El comando df brinda información necesaria, tanto sobre el uso del espacio en disco, como de los inodos libres. Además, nos muestra información general sobre el uso del disco en los sistemas de ficheros montados en directorios.
Normalmente, en los directorios, indicamos ficheros de dispositivos de particiones, como por ejemplo, /dev/hda1, pero si indicamos otro tipo de nombre de fichero o directorio, obtendremos información sobre la partición donde está ubicado dicho fichero o directorio. 
Si omitimos directorios, se mostrará la información relativa a los sistemas de ficheros montados en los dispositivos incluidos en /etc/fstab. La forma correcta de utilizar el comando df es la siguiente:

`# df [opciones] /dev/sd[a|b|c|d][1,2,3,4]`

Opciones 			Descripción
-h 					Muestra los resultados en un formato legible para las
					personas, incluyendo sufijos como M (megabytes)
					y G (gigabytes).
-i					Muestra información sobre los inodos libres en lugar
					de la información por defecto sobre el espacio libre
					en disco.
```shell
`ubuntu@foo:~$ df`
`Filesystem     1K-blocks    Used Available Use% Mounted on`
`tmpfs              91784     940     90844   2% /run`
`efivarfs          131072      26    131042   1% /sys/firmware/efi/efivars`
`/dev/sda1        3988172 1719192   2252596  44% /`
`tmpfs             458908       0    458908   0% /dev/shm`
`tmpfs               5120       0      5120   0% /run/lock`
`/dev/sda16        901520   62312    776080   8% /boot`
`/dev/sda15        106832    6246    100586   6% /boot/efi`
`tmpfs              91780      12     91768   1% /run/user/1000`

`ubuntu@foo:~$ df -h`
`Filesystem      Size  Used Avail Use% Mounted on`
`tmpfs            90M  936K   89M   2% /run`
`efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars`
`/dev/sda1       3.9G  1.7G  2.2G  44% /`
`tmpfs           449M     0  449M   0% /dev/shm`
`tmpfs           5.0M     0  5.0M   0% /run/lock`
`/dev/sda16      881M   61M  758M   8% /boot`
`/dev/sda15      105M  6.1M   99M   6% /boot/efi`
`tmpfs            90M   12K   90M   1% /run/user/1000`

`ubuntu@foo:~$ df -i`
`Filesystem     Inodes IUsed  IFree IUse% Mounted on`
`tmpfs          114727   688 114039    1% /run`
`efivarfs            0     0      0     - /sys/firmware/efi/efivars`
`/dev/sda1      524288 81966 442322   16% /`
`tmpfs          114727     1 114726    1% /dev/shm`
`tmpfs          114727     3 114724    1% /run/lock`
`/dev/sda16      58496   600  57896    2% /boot`
`/dev/sda15          0     0      0     - /boot/efi`
`tmpfs           22945    32  22913    1% /run/user/1000`

`ubuntu@foo:~$ df -i -h` o bien `ubuntu@foo:~$ df -i -h`
`Filesystem     Inodes IUsed IFree IUse% Mounted on`
`tmpfs            113K   688  112K    1% /run`
`efivarfs            0     0     0     - /sys/firmware/efi/efivars`
`/dev/sda1        512K   81K  432K   16% /`
`tmpfs            113K     1  113K    1% /dev/shm`
`tmpfs            113K     3  113K    1% /run/lock`
`/dev/sda16        58K   600   57K    2% /boot`
`/dev/sda15          0     0     0     - /boot/efi`
`tmpfs             23K    32   23K    1% /run/user/1000`
```

El sistema de archivos btrfs tiene su propia herramienta para comprobar el espacio libre y el ocupado, con mucho detalle:
	`# btrfs filesystem usage /`

#### Comando file

La información que obtienes con file sobre un sistema de archivos puede no ser tan detallada como otros comandos específicos que se utilizan para gestionar sistemas de archivos, como `df`, `lsblk`, o `mount`. Sin embargo, file es útil para obtener una descripción rápida y puede ser muy útil en soluciones de problemas.
El comando file en Unix es una herramienta utilizada para determinar el tipo de un archivo. Este comando examina el archivo y proporciona información sobre su contenido y formato, basándose en las características internas del archivo en lugar de su extensión. 
__*Opciones Comunes*__
- -b (brief): Muestra solo el tipo de archivo sin el nombre del archivo.
- -i (mime): Muestra el tipo MIME del archivo.
- -f (file): Lee los nombres de los archivos desde un archivo en lugar de la línea de comandos.
- -s le dice al comando file que examine el contenido del dispositivo en lugar de solo su nombre. Puedes necesitar privilegios de superusuario (usando sudo) para acceder a ciertos dispositivos de bloque.

Esta opción se usa con el comando file dirigiéndolo a la partición, por ejemplo, `/dev/sda1` o cualquier otro dispositivo de bloque, para verificar el tipo de sistema de archivos de una partición o dispositivo. No sigue enlaces simbólicos. Si especificas un enlace simbólico, file devolverá información sobre el enlace simbólico en sí, no sobre el archivo o dispositivo al que apunta.

- -L la opción -L le indica a file que siga los enlaces simbólicos. Si se encuentra un enlace simbólico, file examinará el archivo o dispositivo al que apunta el enlace simbólico en lugar de mostrar información solo del enlace.

ejemplo: **_Ver el tipo de sistema de archivos_**

```shell
ubuntu@foo:~$ sudo file -sL /dev/sda1
/dev/sda1: Linux rev 1.0 ext4 filesystem data, UUID=7b1c29f0-7159-4456-9ca8-db40f35bc6ff, volume name "cloudimg-rootfs" (needs journal recovery) (extents) (64bit) (large files) (huge files)
```
dice el tipo de sistema de archivos, el uuid, 
ejemplo: **_Mostrar un listado de particiones, espacio ocupado y punto de montaje_**

```shell 
# df -h | grep '^/dev' | tr -s ' '| cut -d' ' -f1,2,6 /
dev/mapper/fedora-root 49G /
/dev/sda3 108G /mnt/win10
/dev/mapper/fedora-home 305G /home
/dev/sda5 976M /boot
```

1. El comando **df** (disk free) muestra información sobre el uso del espacio en disco de los sistemas de archivos montados.

2. **'^/dev'**: Este patrón especifica que solo queremos líneas que empiecen (el símbolo ^ indica inicio de línea) con /dev. Esto selecciona solo las líneas que representan sistemas de archivos que están en dispositivos de bloque

3. **tr** es el comando "translate" que se usa para traducir o eliminar caracteres. -s: La opción -s (squeeze) indica que tr debe reemplazar secuencias de espacios en blanco consecutivos con un solo espacio. Esto es útil para normalizar el espacio entre columnas y hacer que la salida sea más manejable.

4. **cut** es un comando utilizado para dividir líneas en secciones (o "campos") y tomar solo aquellas que nos interesan. 
	* **-d' '**: Esto establece un delimitador. En este caso, el delimitador es un espacio simple (' '), lo que significa que cut dividirá cada línea usando espacios como separadores.
	* **-f1,2,6**: Esto especifica que queremos extraer los campos 1, 2 y 6 de la salida. Normalmente, estos campos representan:
		- Campo 1: Nombre del sistema de archivos (por ejemplo, /dev/sda1).
		- Campo 2: Tamaño total del sistema de archivos.
		- Campo 6: Punto de montaje (donde está montado el sistema de archivos).
		**_Los descartados son los siguientes:_**
		- Campo 3: Used Espacio utilizado en el sistema de archivos.
		- Campo 4: Available Espacio disponible en el sistema de archivos.
		- Campo 5: Use% Porcentaje de espacio utilizado.

#### Comando du

El comando du nos puede ayudar, al mostrar directorio por directorio, el uso del espacio en disco. Así mismo, examina los directorios recursivamente y muestra información detallada o resumida sobre el espacio en disco consumido.
La forma correcta de utilizar el comando du es la siguiente:

`# du [opciones] [directorio]`

|Opciones|Descripción|
|:---:|---|
|-a| Muestra todos los ficheros, no solo los directorios.|
|-c| Genera un gran total de todos los elementos listados.|
|-h| Muestra los resultados en un formato legible para las personas, incluyendo sufijos como M (megabytes) y G (gigabytes).|
|-s| Visualiza un sumario para cada uno de los directorios especificados, en lugar de los totales encontrados recursivamente en cada subdirectorio.|
|-S| Excluye los subdirectorios de las sumas y los totales, limitándose a totalizar los directorios.|

* Espacio total ocupado por /etc:
```shell
ubuntu@foo:~$ sudo du -sch /etc
6.1M    /etc
6.1M    total
```
* Espacio total ocupado por distintos directorios:
```shell
ubuntu@foo:~$ sudo du -sch /*
0       /bin
4.0K    /bin.usr-is-merged
67M     /boot
0       /dev
6.1M    /etc
32K     /home
0       /lib
4.0K    /lib.usr-is-merged
16K     /lost+found
4.0K    /media
4.0K    /mnt
4.0K    /opt
20K     /root
948K    /run
4.0K    /sbin.usr-is-merged
8.0K    /snap
4.0K    /srv
64K     /tmp
1.4G    /usr
380M    /var
1.8G    total
```
* Si queremos saber cuáles son los diez archivos
más grandes en el directorio actual
```shell
ubuntu@foo:/$ sudo du -s * | sort -nr | head
1410772 usr
388416  var
68550   boot
6176    etc
952     run
72      tmp
32      home
20      root
16      lost+found
8       snap
```

1. du -s *
 * du (Disk Usage): Este comando se utiliza para estimar y mostrar el uso de espacio en disco de archivos y directorios.
 * -s (summarize): Esta opción indica a du que debe mostrar solo un resumen del tamaño total para cada argumento (en este caso, cada archivo o directorio), en lugar de mostrar el tamaño de cada subdirectorio y archivo dentro de cada uno.
 * *: Este símbolo se usa como un comodín que representa todos los archivos y directorios en el directorio actual. Por lo tanto, du -s * calculará el tamaño total de cada archivo y directorio presente en el directorio actual y mostrará ese tamaño.
2. | sort -nr
 * sort: Este comando se utiliza para ordenar líneas de texto.
 * -n: Esta opción le dice a sort que ordene numéricamente en lugar de alfabéticamente. Esto es importante porque queremos ordenar por tamaño.
 * -r: Esta opción significa "reverse" (invertido), lo que hará que los resultados se ordenen en orden descendente, es decir, de mayor a menor.
3. | head
 * head: Este comando muestra las primeras líneas de su entrada.
 * Sin opciones: Por defecto, head muestra las primeras 10 líneas de la entrada que recibe.

#### Comando tune2fs en sistemas extendidos

El comando tune2fs, se utiliza para ajustar y modificar parámetros de sistemas de archivos ext2, ext3 y ext4. Este comando permite a los administradores del sistema realizar varias tareas relacionadas con el mantenimiento y la optimización de los sistemas de archivos.

```shell
tune2fs [opciones] <dispositivo>
```
Donde <dispositivo> es la ruta al dispositivo de bloque que contiene la partición ext (por ejemplo, /dev/sda1).

|Opción corta| Opción larga | Descripción|
|:---:|---|---|
|-c [número] | --max-mount-counts [número] | Establece el número máximo de montajes antes de que se realice una comprobación del sistema de archivos. Un número de montajes es el número de veces que el sistema de archivos ha sido montado. `tune2fs -c 20 /dev/sda1`
|-C [número] | |Define el número de veces que se montó.
|-f | |Fuerza la operación.|
|-i [tiempo]| --interval-between-checks [tiempo]| Define el tiempo máximo que puede transcurrir entre las comprobaciones del sistema de archivos. El tiempo se puede especificar en días (d), horas (h), semanas (w), `tune2fs -i 2w /dev/sda1`.|
|-j | |Agrega journaling (convierte de ext2 a ext3).|
|-m [número] | |Ajusta el porcentaje de bloques reservados para el uso del sistema (generalmente para root). Por defecto, es un 5% del total de bloques. `tune2fs -m 10 /dev/sda1`|
|-o [opciones]| --o [opciones]| Permite ajustar las opciones del sistema de archivos. Por ejemplo, puedes establecer que sea "read-only". `tune2fs -o journal_data /dev/sda1`|
|-l | | Muestra información del sistema de archivos `tune2fs -l /dev/sda1`|
|-L [nombre]| --label [nombre]| Cambia la etiqueta del sistema de archivos. La etiqueta es un nombre que se le puede dar al sistema de archivos para una mejor identificación.`tune2fs -L MiEtiqueta /dev/sda1 `|
|-e [modo]| --errors=[modo]|Define cómo manejar los errores en el sistema de archivos. Los modos pueden ser continue, remount-ro, panic, entre otros.`tune2fs -e remount-ro /dev/sda1`|
|-j|--init-journal| Crea un nuevo diario para un sistema de archivos ext3/4 si no existe.`tune2fs -j /dev/sda1  `|

Ejemplo que usa opción que está en la tabla de recién:
**Espacio en particiones sin montar en ext4**

```shell
tune2fs -l /dev/sda5 | egrep 'Free|(Inode|Block) count|^Block size|Reserved'Inode count:
65536
Block count: 262144
Reserved block count: 13107
Free blocks: 111889
Free inodes: 65396
Block size: 4096
Reserved GDT blocks: 127
Reserved blocks uid: 0 (user root)
Reserved blocks gid: 0 (group root)
```

Ejemplo que muestra toda la información del sistema de archivos

```shell
ubuntu@foo:/$ sudo tune2fs -l /dev/sda1
tune2fs 1.47.0 (5-Feb-2023)
Filesystem volume name:   cloudimg-rootfs
Last mounted on:          /
Filesystem UUID:          7b1c29f0-7159-4456-9ca8-db40f35bc6ff
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super large_file huge_file dir_nlink extra_isize metadata_csum
Filesystem flags:         signed_directory_hash
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              524288
Block count:              1048315
Reserved block count:     0
Overhead clusters:        51272
Free blocks:              545652
Free inodes:              442444
First block:              0
Block size:               4096
Fragment size:            4096
Group descriptor size:    64
Reserved GDT blocks:      255
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         16384
Inode blocks per group:   1024
Flex block group size:    16
Filesystem created:       Wed Jul 10 08:38:05 2024
Last mount time:          Sat Jul 20 16:54:30 2024
Last write time:          Sat Jul 20 19:55:09 2024
Mount count:              3
Maximum mount count:      -1
Last checked:             Wed Jul 10 08:38:05 2024
Check interval:           0 (<none>)
Lifetime writes:          2498 MB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               256
Required extra isize:     32
Desired extra isize:      32
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      7b1152e0-c019-4159-9991-8cdbf33ad686
Journal backup:           inode blocks
Checksum type:            crc32c
Checksum:                 0x9aed1bd0
```

### Herramientas de mantenimiento y optimización en xfs

#### xfs_metadump

Solo debería ser usado para copiar sistemas de archivos desmontados, de solo lectura o congelados (xfs_freeze).
Podemos copiar los metadatos hacia un archivo:

```shell
# xfs_metadump -g /dev/sdb6 prueba.img
Copied 347520 of 1141120 inodes (1 of 4 AGs)
# ls -lh prueba.img
-rw-r--r-- 1 root root 123M nov 24 23:22 prueba.img
```

Uso el comando file para que reconozca el tipo de archivo:

```shell
# file prueba.img
rino: XFS filesystem metadump image
```
#### xfs_db

Este programa sirve para hacer tareas de depuración y para obtener información de bajo nivel del sistema, por ejemplo:

```shell
# xfs_db -r /dev/sdb6
xfs_db> frag
actual 92517, ideal 91628, fragmentation factor 0,96%
Note, this number is largely meaningless.
Files on this filesystem average 1,01 extents per file
xfs_db> quit
```
La opción -r es para operar en modo sólo lectura.

#### xfs_fsr
Esta herramienta sirve para reorganizar el sistema de archivos:
```shell
# xfs_fsr /dev/sdb6
```
### Herramientas avanzadas para ext2/ext3/ext4

#### Comando debugfs
Herramienta interactiva para tener información
de depuración o reparar el sistema de archivos
ext2/ext3/ext4 en una partición específica:
Si quisiéramos, podríamos marcar el sistema
como dirty para modificar algún inodo,
limpiarlo, testear bloques, borrar links, crear
directorios, estadísticas, buscar archivos, etc

```shell
ubuntu@foo:/$ sudo  debugfs /dev/sda1
debugfs 1.47.0 (5-Feb-2023)
debugfs:  asdf
debugfs: Unknown request "ds".  Type "?" for a request list.
debugfs:  ?
Available debugfs requests: (....)
```

#### Comando dumpe2fs

Muestra la información del bloque y grupo de bloques de un sistema de archivos presente en un dispositivo: 

```shell
ubuntu@foo:/$ sudo dumpe2fs /dev/sda1
dumpe2fs 1.47.0 (5-Feb-2023)
Filesystem volume name:   cloudimg-rootfs
Last mounted on:          /
Filesystem UUID:          7b1c29f0-7159-4456-9ca8-db40f35bc6ff
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype needs_recovery extent 64bit flex_bg sparse_super large_file huge_file dir_nlink extra_isize metadata_csum
Filesystem flags:         signed_directory_hash
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              524288
Block count:              1048315
Reserved block count:     0
Overhead clusters:        51272
Free blocks:              545652
Free inodes:              442444
First block:              0
Block size:               4096
Fragment size:            4096
Group descriptor size:    64
Reserved GDT blocks:      255
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         16384
Inode blocks per group:   1024
Flex block group size:    16
Filesystem created:       Wed Jul 10 08:38:05 2024
Last mount time:          Sat Jul 20 16:54:30 2024
Last write time:          Sat Jul 20 19:55:09 2024
Mount count:              3
Maximum mount count:      -1
Last checked:             Wed Jul 10 08:38:05 2024
Check interval:           0 (<none>)
Lifetime writes:          2498 MB
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:               256
Required extra isize:     32
Desired extra isize:      32
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      7b1152e0-c019-4159-9991-8cdbf33ad686
Journal backup:           inode blocks
Checksum type:            crc32c
Checksum:                 0x9aed1bd0
Journal features:         journal_incompat_revoke journal_64bit journal_checksum_v3
Total journal size:       64M
Total journal blocks:     16384
Max transaction length:   16384
Fast commit length:       0
Journal sequence:         0x000001f8
Journal start:            5903
Journal checksum type:    crc32c
Journal checksum:         0x504c1d09


Group 0: (Blocks 0-32767) csum 0x428e [ITABLE_ZEROED]
  Primary superblock at 0, Group descriptors at 1-1
  Reserved GDT blocks at 2-256
  Block bitmap at 257 (+257), csum 0x7e75b32e
  Inode bitmap at 273 (+273), csum 0x0d225aa6
  Inode table at 289-1312 (+289)
  3937 free blocks, 0 free inodes, 1571 directories
  Free blocks: 25234-25344, 25374-25388, 28957-32767
  Free inodes:
Group 1: (Blocks 32768-65535) csum 0x5604 [ITABLE_ZEROED]
  Backup superblock at 32768, Group descriptors at 32769-32769
  Reserved GDT blocks at 32770-33024
  Block bitmap at 258 (bg #0 + 258), csum 0xd39e18b2
  Inode bitmap at 274 (bg #0 + 274), csum 0x0d225aa6
  Inode table at 1313-2336 (bg #0 + 1313)
  9191 free blocks, 0 free inodes, 1322 directories
  Free blocks: 33034-33035, 33040-33055, 33078-33087, 33278-33279, 33282, 33310-33311, 34043-34045, 34048, 34053-34055, 34057, 34078-34080, 34088, 34102-34103, 34578-34637, 34694, 34727-34728, 34795, 34896-34911, 35838-35839, 35866-35925, 36012, 36064, 36071-36078, 36083-36084, 36087-36088, 36093, 36124-36125, 36128-36129, 36133, 36137-36139, 36142-36351, 36483-38911, 38946-38947, 38952-38953, 38957-38958, 39081-39098, 39234-39245, 39254-39261, 39264-39265, 39272, 39277, 39291-39294, 39299-39301, 39303-39310, 39322, 39328-39329, 39331, 39333-39347, 39350-39354, 39379-39401, 39421, 39936, 39938-39943, 40053-40059, 40451-40510, 40516, 40538, 40541, 40960-47103, 47192-47197, 47201-47202
  Free inodes:
Group 2: (Blocks 65536-98303) csum 0xf168 [ITABLE_ZEROED]
  Block bitmap at 259 (bg #0 + 259), csum 0x0cf9937c
  Inode bitmap at 275 (bg #0 + 275), csum 0x0d225aa6
  Inode table at 2337-3360 (bg #0 + 2337)
  0 free blocks, 0 free inodes, 1640 directories
  Free blocks:
  Free inodes:
```
### Fuentes y Mas recursos

#### *En libros*
● “CompTIA Linux+ / LPIC-1 Cert Guide: (Exams LX0-103 &
LX0-104/101-400 & 102-400)”, Ross Brunson & Sean
Walberg.
● “CompTIA Linux+ /LPIC-1 Certification All-In-One Exam
Guide, Premium Second Edition With Online Practice
Labs (Exams LX0-103 & LX0-104/101-400 & 102-400)”,
Robb Tracy.
● “LPIC-1 Certification All-in-One Exam Guide Exams”,
Robb Tracy.

#### *En Internet*
- 
● [How to check free disk space in Linux.](https://opensource.com/article/18/7/how-check-free-disk-space-linux)
● [Particiones y sistemas de ficheros.](http://persoal.citius.usc.es/tf.pena/ASR/Tema_3html/node12.html)
● [Comprobar y reparar sistemas de archivos con fsck.](https://www.ochobitshacenunbyte.com/2015/04/29/comprobar-reparar-sistemas-archivos-fsck/)
● [Ict-innovation/LPI/104.2.](https://en.wikibooks.org/wiki/Ict-innovation/LPI/104.2)
● [How to fix (fsck) a root file system that you have
to boot into on Linux.](http://bitsofmymind.com/2014/03/14/how-to-fix-fsck-your-root-file-system-that-you-have-to-boot-into-on-linux)
● [Slackbook:working_with_filesystems](https://docs.slackware.com/slackbook:working_with_filesystems)


### Montaje y Desmontaje de FileSystem

#### Puntos de Montaje

Un punto de montaje es en general un directorio mediante el cual accedemos a un sistema de archivos. Cuando hablamos de montar un dispositivo en realidad lo que hacemos es establecer un puente entre el sistema de archivos principal y el del dispositivo al cual queremos acceder. Esto nos puede resultar algo complejo en primera instancia, pero el mismo principio para acceder a un CDROM, por ejemplo, es el que se aplica para acceder a una carpeta de un servidor remoto.
De acuerdo a la FHS el punto de montaje para dispositivos removibles es `/media`. No obstante podría ser utilizado cualquier directorio, siempre que este tenga sentido

#### Comando mount
	# mount [opciones] [dispositivo|directorio]

El comando mount admite dos tipos de opciones:
● unos para el propio comando, 
● y otros para especificar opciones del sistema de ficheros.

|Opciones | Descripción |
|:---:| --- |
|-a | Monta todos los filesystems especificados en el `/etc/fstab`, menos los que tengan la opción "noauto".|
|-h | Ayuda del comando mount.|
|-o | Especifica las opciones del mount en la línea de comandos.|
|-r | Monta filesystems en modo de solo lectura.|
|-t [fstype] | Especifica un tipo de filesystem.|
|-v | Salida interactiva.|
|-w | Monta el sistema de archivos de lectura/escritura.|

El comando mount, por si solo, nos devuelve lo que está 
montado en el equipo, por ejemplo:

```shell
ubuntu@foo:/mnt$ mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=441520k,nr_inodes=110380,mode=755,inode64)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,noexec,relatime,size=91784k,mode=755,inode64)
efivarfs on /sys/firmware/efi/efivars type efivarfs (rw,nosuid,nodev,noexec,relatime)
/dev/sda1 on / type ext4 (rw,relatime,discard,errors=remount-ro,commit=30)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,inode64)
tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k,inode64)
cgroup2 on /sys/fs/cgroup type cgroup2 (rw,nosuid,nodev,noexec,relatime,nsdelegate,memory_recursiveprot)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
bpf on /sys/fs/bpf type bpf (rw,nosuid,nodev,noexec,relatime,mode=700)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=32,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=2210)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,nosuid,nodev,relatime,pagesize=2M)
mqueue on /dev/mqueue type mqueue (rw,nosuid,nodev,noexec,relatime)
tracefs on /sys/kernel/tracing type tracefs (rw,nosuid,nodev,noexec,relatime)
debugfs on /sys/kernel/debug type debugfs (rw,nosuid,nodev,noexec,relatime)
fusectl on /sys/fs/fuse/connections type fusectl (rw,nosuid,nodev,noexec,relatime)
configfs on /sys/kernel/config type configfs (rw,nosuid,nodev,noexec,relatime)
/dev/sda16 on /boot type ext4 (rw,relatime)
/dev/sda15 on /boot/efi type vfat (rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro)
binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,nosuid,nodev,noexec,relatime)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=91780k,nr_inodes=22945,mode=700,uid=1000,gid=1000,inode64)
tracefs on /sys/kernel/debug/tracing type tracefs (rw,nosuid,nodev,noexec,relatime)
/dev/loop0p1 on /mnt/data type ext4 (rw,relatime)
```

El archivo `/etc/mtab` (es un enlace simbólico a `/proc/self/mounts`) tiene un contenido similar:

```shell
ubuntu@foo:/mnt$ cat /etc/mtab
sysfs /sys sysfs rw,nosuid,nodev,noexec,relatime 0 0
proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
udev /dev devtmpfs rw,nosuid,relatime,size=441520k,nr_inodes=110380,mode=755,inode64 0 0
devpts /dev/pts devpts rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 0 0
tmpfs /run tmpfs rw,nosuid,nodev,noexec,relatime,size=91784k,mode=755,inode64 0 0
efivarfs /sys/firmware/efi/efivars efivarfs rw,nosuid,nodev,noexec,relatime 0 0
/dev/sda1 / ext4 rw,relatime,discard,errors=remount-ro,commit=30 0 0
securityfs /sys/kernel/security securityfs rw,nosuid,nodev,noexec,relatime 0 0
tmpfs /dev/shm tmpfs rw,nosuid,nodev,inode64 0 0
tmpfs /run/lock tmpfs rw,nosuid,nodev,noexec,relatime,size=5120k,inode64 0 0
cgroup2 /sys/fs/cgroup cgroup2 rw,nosuid,nodev,noexec,relatime,nsdelegate,memory_recursiveprot 0 0
pstore /sys/fs/pstore pstore rw,nosuid,nodev,noexec,relatime 0 0
bpf /sys/fs/bpf bpf rw,nosuid,nodev,noexec,relatime,mode=700 0 0
systemd-1 /proc/sys/fs/binfmt_misc autofs rw,relatime,fd=32,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=2210 0 0
hugetlbfs /dev/hugepages hugetlbfs rw,nosuid,nodev,relatime,pagesize=2M 0 0
mqueue /dev/mqueue mqueue rw,nosuid,nodev,noexec,relatime 0 0
tracefs /sys/kernel/tracing tracefs rw,nosuid,nodev,noexec,relatime 0 0
debugfs /sys/kernel/debug debugfs rw,nosuid,nodev,noexec,relatime 0 0
fusectl /sys/fs/fuse/connections fusectl rw,nosuid,nodev,noexec,relatime 0 0
configfs /sys/kernel/config configfs rw,nosuid,nodev,noexec,relatime 0 0
/dev/sda16 /boot ext4 rw,relatime 0 0
/dev/sda15 /boot/efi vfat rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro 0 0
binfmt_misc /proc/sys/fs/binfmt_misc binfmt_misc rw,nosuid,nodev,noexec,relatime 0 0
tmpfs /run/user/1000 tmpfs rw,nosuid,nodev,relatime,size=91780k,nr_inodes=22945,mode=700,uid=1000,gid=1000,inode64 0 0
tracefs /sys/kernel/debug/tracing tracefs rw,nosuid,nodev,noexec,relatime 0 0
/dev/loop0p1 /mnt/data ext4 rw,relatime 0 0
```

##### Para montar una partición en un directorio
```shell
# mount -t ext4 /dev/sdb6 /mnt
# mount |grep mnt
/dev/sdb6 on /mnt type ext4 (rw,relatime)
```

##### montar el cdrom:

```shell
# ls -l /dev/cdrom
lrwxrwxrwx 1 root root 3 nov 25 00:47 /dev/cdrom -> sr0
# mount -t iso9660 /dev/cdrom /media
mount: dispositivo de bloques /dev/sr0 está protegido contra escritura; se monta como sólo lectura
# mount |grep -i media
/dev/sr0 on /media type iso9660 (ro,relatime)
```

##### De este modo podemos montar una partición o volumen lógico usando etiqueta:
```shell
# mount -v -L SYSTEM
mount: /dev/sda1 montado en /boot/efi.
```

##### De este modo podemos montar una partición o volumen lógico usando UUID:
```shell
# mount -v -U B4A4-9276
mount: /dev/sda1 montado en /boot/efi.
```

#### Comando umount

Los sistemas de ficheros pueden ser desmontados usando el comando umount. Cuando un sistema de ficheros es desmontado, los contenidos del árbol principal se actualizan, no pudiéndose usar el umount si el sistema de ficheros que se quiere desmontar está en uso. Si el sistema de ficheros está en uso, el comando umount dará un error.
Esto puede ocurrir, por ejemplo, cuando tenemos abierto un fichero de un DVD o un proceso está haciendo uso del mismo. Incluso estar dentro del directorio del sistema a desmontar. Otros errores pueden surgir si quitamos dispositivos removibles sin antes desmontarlos

	# umount [opciones] [dispositivo|directorio]

|Opciones | Descripción |
|:---:| --- |
|-a | Desmonta todos los filesystems descritos en `/etc/mtab`. Este fichero está mantenido por los comando mount y umount en tiempo real, se usa normalmente cuando se apaga/reinicia el PC.|
|-t [fstype] | Desmonta sólo los filesystems del tipo especificado.|


#### Utilización de etiquetas y UUID para identificar sistemas de archivos
En ocasiones es más sencillo y descriptivo referirse a una partición por su etiqueta (LABEL). Tanto ext4, xfs como btrfs tienen herramientas para cambiar y/o asignar una etiqueta.
Además, puede resultar ventajoso usar el UUID (identificador único universal), cuando vamos a utilizar discos que estaban en una máquina en otra, ya que podemos abstraernos del archivo del nodo al dispositivo

#### Comando lsblk
El comando lsblk ("list block devices") se utiliza para mostrar información sobre todos los dispositivos de bloques en el sistema, incluyendo discos duros, particiones y dispositivos de almacenamiento extraíbles.

Uso básico:

	lsblk  

**Salida:** Muestra una lista de dispositivos de bloques en un formato de árbol que incluye la siguiente información, entre otros datos:

- Nombre del dispositivo (por ejemplo, sda, sda1, loop0, etc.)
- Tamaño de cada dispositivo
- Tipo (por ejemplo, disk, part, loop, etc.)
- Puntos de montaje (dónde está montado cada dispositivo, si aplica)

Para ver las etiquetas y los UUID podemos usar el comando `lsblk` con la opción -o que permite indicar cuáles columnas de datos mostrar.

```shell
ubuntu@foo:/mnt$ lsblk -o NAME,LABEL,UUID
NAME      LABEL           UUID
loop0
└─loop0p1                 0e8124c3-dc0e-4cd1-99e7-bc7b763817d0
sda
├─sda1    cloudimg-rootfs 7b1c29f0-7159-4456-9ca8-db40f35bc6ff
├─sda14
├─sda15   UEFI            87D6-168F
└─sda16   BOOT            667a6fa0-2a65-4800-8c8a-74a9ecf3fe58
sr0       cidata

ubuntu@foo:/mnt$ lsblk --help

Usage:
 lsblk [options] [<device> ...]

List information about block devices.

Options:
 -A, --noempty        don't print empty devices
 -D, --discard        print discard capabilities
 -E, --dedup <column> de-duplicate output by <column>
 -I, --include <list> show only devices with specified major numbers
 -J, --json           use JSON output format
 -M, --merge          group parents of sub-trees (usable for RAIDs, Multi-path)
 -O, --output-all     output all columns
 -P, --pairs          use key="value" output format
 -S, --scsi           output info about SCSI devices
 -N, --nvme           output info about NVMe devices
 -v, --virtio         output info about virtio devices
 -T, --tree[=<column>] use tree format output
 -a, --all            print all devices
 -b, --bytes          print SIZE in bytes rather than in human readable format
 -d, --nodeps         don't print slaves or holders
 -e, --exclude <list> exclude devices by major number (default: RAM disks)
 -f, --fs             output info about filesystems
 -i, --ascii          use ascii characters only
 -l, --list           use list format output
 -m, --perms          output info about permissions
 -n, --noheadings     don't print headings
 -o, --output <list>  output columns
 -p, --paths          print complete device path
 -r, --raw            use raw output format
 -s, --inverse        inverse dependencies
 -t, --topology       output info about topology
 -w, --width <num>    specifies output width as number of characters
 -x, --sort <column>  sort output by <column>
 -y, --shell          use column names to be usable as shell variable identifiers
 -z, --zoned          print zone related information
     --sysroot <dir>  use specified directory as system root

 -h, --help           display this help
 -V, --version        display version

Available output columns:
    ALIGNMENT  alignment offset
      ID-LINK  the shortest udev /dev/disk/by-id link name
           ID  udev ID (based on ID-LINK)
     DISC-ALN  discard alignment offset
          DAX  dax-capable device
    DISC-GRAN  discard granularity
     DISK-SEQ  disk sequence number
     DISC-MAX  discard max bytes
    DISC-ZERO  discard zeroes data
      FSAVAIL  filesystem size available
      FSROOTS  mounted filesystem roots
       FSSIZE  filesystem size
       FSTYPE  filesystem type
       FSUSED  filesystem size used
       FSUSE%  filesystem use percentage
        FSVER  filesystem version
        GROUP  group name
         HCTL  Host:Channel:Target:Lun for SCSI
      HOTPLUG  removable or hotplug device (usb, pcmcia, ...)
        KNAME  internal kernel device name
        LABEL  filesystem LABEL
      LOG-SEC  logical sector size
      MAJ:MIN  major:minor device number
       MIN-IO  minimum I/O size
         MODE  device node permissions
        MODEL  device identifier
           MQ  device queues
         NAME  device name
       OPT-IO  optimal I/O size
        OWNER  user name
    PARTFLAGS  partition flags
    PARTLABEL  partition LABEL
        PARTN  partition number as read from the partition table
     PARTTYPE  partition type code or UUID
 PARTTYPENAME  partition type name
     PARTUUID  partition UUID
         PATH  path to the device node
      PHY-SEC  physical sector size
       PKNAME  internal parent kernel device name
       PTTYPE  partition table type
       PTUUID  partition table identifier (usually UUID)
           RA  read-ahead of the device
         RAND  adds randomness
          REV  device revision
           RM  removable device
           RO  read-only device
         ROTA  rotational device
      RQ-SIZE  request queue size
        SCHED  I/O scheduler name
       SERIAL  disk serial number
         SIZE  size of the device
        START  partition start offset
        STATE  state of the device
   SUBSYSTEMS  de-duplicated chain of subsystems
   MOUNTPOINT  where the device is mounted
  MOUNTPOINTS  all locations where device is mounted
         TRAN  device transport type
         TYPE  device type
         UUID  filesystem UUID
       VENDOR  device vendor
        WSAME  write same max bytes
          WWN  unique storage identifier
        ZONED  zone model
      ZONE-SZ  zone size
   ZONE-WGRAN  zone write granularity
     ZONE-APP  zone append max bytes
      ZONE-NR  number of zones
    ZONE-OMAX  maximum number of open zones
    ZONE-AMAX  maximum number of active zones

For more details see lsblk(8).
```
#### Comando blkid

 El comando blkid se usa para buscar o mostrar atributos de dispositivos de bloques (como el UUID, el tipo de sistema de archivos, etc.).

Uso básico:

	blkid  

**Salida:** Muestra una lista de dispositivos con sus atributos, por ejemplo:

- UUID (Identificador único universal)
- Tipo de sistema de archivos (por ejemplo, ext4, xfs, vfat, etc.)
- Etiquetas (si las hay)

```shell
ubuntu@foo:/mnt$ blkid
/dev/sr0: BLOCK_SIZE="2048" LABEL="cidata" TYPE="iso9660"
/dev/sda16: LABEL="BOOT" UUID="667a6fa0-2a65-4800-8c8a-74a9ecf3fe58" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="7a7be01d-8b0b-47a9-90c0-a8681d5ec0db"
/dev/sda15: LABEL_FATBOOT="UEFI" LABEL="UEFI" UUID="87D6-168F" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="3cf2cdaa-9d59-4843-b641-296c11b7418e"
/dev/sda1: LABEL="cloudimg-rootfs" UUID="7b1c29f0-7159-4456-9ca8-db40f35bc6ff" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="3d48b694-8656-49c1-83cb-f5ba0386e6b3"
```

### Tipos de enlaces con filesystems

#### Tipos
Los enlaces se dividen en dos clases: 
● Duros. Los enlaces duros son indistinguibles del original y tienen sus propias restricciones.
Estos enlaces comparten el inodo del fichero original. De hecho, un hard link es indistinguible del original, por eso, los cambios en el link afectan al fichero original, excepto en el borrado. Borrar el link no elimina al original ni a la inversa. Este tipo de enlace conserva los permisos del original y marcas de tiempo. 
Por contra, no se pueden usar para hacer enlaces a directorios, ni pueden extenderse a otros sistemas de ficheros. 
Por otro lado, a diferencia de los enlaces 
simbólicos, los enlaces duros nunca se pueden 
romper moviendo uno de los archivos (entradas 
de directorio) a otra ubicación.
Su sintaxis es la siguiente:

	# ln /ruta/completa/fichero nombre_enlace

Los enlaces duros son implementados en los sistemas de archivos estilo Unix (ext2/3/4, xfs, etc), como diferentes entradas en bloques de directorio que apuntan al mismo inodo. Cambiando de nombre o borrando una de esas entradas no borramos los datos, solamente disminuye la cuenta del enlace. 

● Simbólicos o Blandos o Symlinks. 
Los enlaces simbólicos (symlinks) son los más utilizados debido a su flexibilidad y la capacidad para trabajar a través de diferentes particiones. 
Estos enlaces pueden extenderse a otros sistemas de ficheros. También pueden hacer referencia a directorios, de hecho, pueden referenciar hasta ficheros inexistentes. La lectura y escritura, así como la copia del enlace, afectan al archivo al que apuntan, mientras que el borrado afecta al propio enlace. Borrar el archivo apuntado tampoco elimina el enlace automáticamente. 
Su sintaxis es la siguiente:

	# ln -s /ruta/completa/fichero nombre_enlace

Son implementados en Linux como un archivo separado conteniendo una referencia en el archivo al archivo original, en términos más técnicos, un inodo conteniendo la ubicación 
“real” del archivo.
Los enlaces simbólicos usan una ruta que puede ser, o absoluta (comenzando con /), o bien relativa a la ubicación del enlace. 
Si un enlace relativo se crea y luego se mueve, se romperá, mientras que los enlaces absolutos generalmente no. A causa de su diseño, un enlace simbólico puede apuntar a cualquier 
directorio o archivo, o aun, apuntar a una ubicación inexistente (en cuyo caso es llamado “roto”).
Un programa llamado symlinks se escribió para hacer el proceso de ubicar y limpiar los enlaces simbólicos rotos. A diferencia de un enlace duro, borrar el archivo original provocará que el archivo sea borrado y los enlaces simbólicos apuntando a él se convierten en colgantes.

Los enlaces simbólicos no tienen permisos o estado por su cuenta. En su lugar, usan los permisos del archivo al que apuntan, aunque los permisos del enlace mismo son rwxrwxrwx. El tamaño de un enlace simbólico es informado como la longitud de la ruta que contiene (más el carácter nulo al final)

##### Creación Enlace Duro

El ejemplo siguiente muestra dos archivos hola y hard_hola que comparten el mismo inodo, por lo cual son enlaces duros.

```shell
ubuntu@foo:~$ sudo vim hola.txt
ubuntu@foo:~$ sudo ln hola.txt hard_hola.txt
ubuntu@foo:~$ ls -li hola.txt
294021 -rw-r--r-- 2 root root 28 Jul 21 11:00 hola.txt
ubuntu@foo:~$ ls -li hard_hola.txt
294021 -rw-r--r-- 2 root root 28 Jul 21 11:00 hard_hola.txt
```
Con los enlaces duros, se mantiene un contador de enlaces en el inodo de cada archivo. Cuando el contador de enlaces cae a 0, se borra el inodo y los bloques de datos son liberados para reusarlos. De esta manera, una vez que un archivo es “enlazado duramente”, cualquier “copia” del archivo se puede borrar, y los otros quedarán intactos.

```shell
ubuntu@foo:~$ rm hola.txt
rm: remove write-protected regular file 'hola.txt'? y
ubuntu@foo:~$ cat hard_hola.txt
Esto es un texto de prueba!
ubuntu@foo:~$ ls
hard_hola.txt  shola.txt
ubuntu@foo:~$ cat shola.txt
cat: shola.txt: No such file or directory
```
Los enlaces duros se usan además, en ciertas soluciones de backups y en software para distribución de paquetes.

##### Creación Enlace Simbólico o Symlink
Este shola tiene un número diferente de inodo pero apunta al mismo archivo hola.

```shell
ubuntu@foo:~$ ln -s hola.txt shola.txt
ubuntu@foo:~$ ls -li shola.txt
294020 lrwxrwxrwx 1 ubuntu ubuntu 8 Jul 21 11:02 shola.txt -> hola.txt
ubuntu@foo:~$ ls -li hola.txt
294021 -rw-r--r-- 2 root root 28 Jul 21 11:00 hola.txt
```

### Componentes de la secuencia de arranque

#### BIOS
BIOS significa `Basic Input Output System`. Se trata de un firmware embebido en la placa madre encargado el hardware cuando se enciende la computadora y un proceso de diagnóstico llamado `POST (Power On Self Test)`. Su función principal es realizar la autocomprobación del hardware (POST - Power-On Self-Test) y preparar el sistema para cargar el sistema operativo.
Después de la verificación, busca un dispositivo de arranque (como un disco duro o una unidad USB) y transfiere el control al bootloader (cargador de arranque) de esa unidad.
Se encuentra almacenado en una memoria ROM (Read-Only Memory) o en una memoria flash.
#### UEFI
Las computadoras más nuevas cuentan con UEFI `Unified Extended Firmware Interface`, que es un reemplazo moderno del BIOS. Este proporciona una interfaz más avanzada y flexible entre el sistema operativo y el firmware de la computadora.
Ofrece soporte para discos grandes (más de 2 TB), tiempos de arranque más rápidos, interfaces gráficas y más funcionalidades de seguridad.
UEFI puede operar en modo de compatibilidad con BIOS, lo que significa que se puede usar para arrancar sistemas operativos diseñados para BIOS.

 Las principales diferencias son:
● UEFI proporciona una serie de estándares técnicos para una interfaz, en lugar de aplicarse a una única implementación de un firmware.
● UEFI entiende los conceptos de “cargador de arranque”, particiones y sistemas operativos. 
● UEFI es capaz de saltear GRUB y lanzar el kernel directamente (aunque este modo de uso no es habitual).
● No se basa en un sector de arranque, definiendo en cambio un gestor de arranque como parte de la especificación UEFI. La configuración (que incluye rutas del sistema de archivos a cargadores y núcleos de sistemas operativos) está almacenada en memoria no volátil.

#### El kernel
El kernel es el núcleo del sistema operativo. Se trata de un programa que administra los recursos de hardware y los procesos del sistema.
El kernel es la parte central del sistema operativo que se encarga de gestionar el hardware y proporcionar servicios esenciales al software.
Incluye gestión de procesos, memoria, controladores de dispositivos, y administración del sistema de archivos. El kernel actúa como intermediario entre el software y el hardware.
Tipos: Existen diferentes tipos de kernels, como kernels monolíticos, microkernels, y kernels híbridos.

#### Initramfs
Initramfs es un sistema de archivos temporal que se utiliza para el arranque del sistema durante el proceso de carga del kernel. Es una imagen inicial utilizada por el kernel para precargar los módulos de los dispositivos de bloques (tales como IDE, SCSI o RAID) que se necesitan para acceder al sistema de archivos raíz, montar el sistema de archivos raíz, e iniciar el sistema real. Esta operación se realiza en espacio de usuario.
Proporciona los controladores necesarios y las herramientas para montar el sistema de archivos raíz (root filesystem) del sistema operativo. Contiene el entorno mínimo necesario para que el kernel pueda arrancar.
Ubicación: Se almacena como una imagen comprimida y se descomprime en la memoria durante el inicio.

#### MBR (Master Boot Record)
MBR es un esquema de particionamiento y un área de arranque que se encuentra al comienzo de un disco duro.
Funciones: Contiene la tabla de particiones del disco y un pequeño programa (bootloader) que se ejecuta al inicio y que carga el sistema operativo. Solo permite hasta 4 particiones primarias y es limitado a discos de hasta 2 TB.
Limitaciones: En comparación con GPT (GUID Partition Table), MBR tiene límites en cuanto a tamaño y cantidad de particiones.

#### Bootloader
Definición: El bootloader es un programa que se encarga de cargar el sistema operativo en la memoria cuando se inicia la computadora.
Funciones: Localiza el sistema operativo en el disco de arranque y lo carga en la memoria para su ejecución. Puede ofrecer un menú para seleccionar entre varios sistemas operativos instalados.
Ejemplos: GRUB (Grand Unified Bootloader) y LILO (Linux Loader).

#### GRUB (Grand Unified Bootloader)
GRUB es un bootloader muy utilizado en sistemas Linux que permite la carga de múltiples sistemas operativos.
Funciones: Soporta diferentes sistemas de archivos, permite seleccionar entre diferentes kernels y configuraciones, y puede mostrar un menú de inicio.
Configuración: La configuración de GRUB se encuentra generalmente en /boot/grub/grub.cfg. Permite agregar o editar entradas para diferentes sistemas operativos o versiones del kernel.

#### Secuencia de arranque
La secuencia de arranque es el conjunto de operaciones, desde que iniciamos el equipo, hasta que inicia el primer proceso del sistema.
Cuando iniciamos el equipo, se ejecuta el BIOS o UEFI. Dentro de las opciones de configuración de la BIOS/UEFI, podemos definir los dispositivos físicos de arranque del sistema (disco rígido, USB, CD-ROM, etc). 
El dispositivo utilizado para el arranque debe tener instalado en el primer sector, conocido como `MBR (Master Boot Record)`, el código de arranque, la definición de la tabla de particiones y el código de comprobación.
El código de arranque inicia el bootloader o cargador de arranque (GRUB en Linux), donde podemos elegir a través de un menú, el Sistema Operativo a iniciar.
El sistema UEFI mantiene por compatibilidad el inicio de MBR. En caso de no utilizar el modo de compatibilidad intentará usar una partición GPT (Guid Partition Table) para cargar los archivos de inicio de los distintos sistemas operativos.
Estos archivos tienen extensión `.EFI`. UEFI puede arrancar directamente su propio bootloader o bien usar GRUB. Este cargará el kernel. Luego montará (si es que existe) el `initramfs`. Continuará el inicio para detectar el tipo de CPU, el manejo de memoria, planificador de tareas, entradas y salidas, comunicación interprocesos, y demás sistemas de control.
Una vez que el sistema de archivos raíces está localizado y montado el `initramfs` le cede el control al gestor del sistema de la máquina. Llegado este punto se ejecuta el primer proceso llamado `init` o `systemd` que es el encargado de iniciar los distintos servicios del sistema, realizar configuraciones y montar unidades entre otras cosas. También va a ser el responsable del apagado del sistema.
##### Pasos típicos:
1. Encendido: El usuario presiona el botón de encendido.
2. POST: El BIOS/UEFI realiza la verificación inicial del hardware.
3. Dispositivo de Arranque: Se determina el dispositivo de arranque (uso de MBR o UEFI).
4. Bootloader: Se ejecuta el bootloader que carga el kernel.
5. Kernel: El kernel se inicia y carga initramfs.
6. Sistema de Archivos: Initramfs monta el sistema de archivos raíz.
7. Init: El proceso init (o systemd en muchos sistemas modernos) se inicia, creando los procesos del usuario y iniciando los servicios del sistema.


### Material extra brindado en la clase: 

 - Podcast:
    Inglés: The DevOps Paradox
    Español: Enchiladas DevOps

 - Youtube: 
    DevOpsToolkit
    Es de una de las personas que hace DevOps Paradox.








