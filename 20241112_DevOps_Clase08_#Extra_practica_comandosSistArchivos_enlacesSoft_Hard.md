- Actividad Práctica: Creación de una partición a partir de un archivo en ubuntu
- Comandos para Sistema de Archivos

---
**EXTRA DE CLASE**

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

Si ahora hacemos `df -h` va a mostrar todos los file systems, todos lso dispositivos y el recién creado no aparece porqeu todavía no lo montamos, ni tampoco formateamos.
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




## Comandos para Sistema de Archivos
desde 1h clase 8.
Es básicamente un índice mediante el cual se puede acceder a cada archivo.

### Resumen de comandos relacionados a sistema de archivos:
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

#### sudo find / -printf '%h\n' | sort | uniq -c | sort -nr | head
  1. find /:
find / significa que comenzará a buscar desde la raíz del sistema de archivos (/), lo que implica que buscara en todo el sistema.
  2. -printf '%h\n':
-printf es una opción de find que permite formatear la salida.
`'%h\n'` significa que queremos imprimir el directorio que contiene cada archivo o directorio encontrado.
`%h` representa el directorio padre del archivo encontrado (el nombre del directorio).
`\n` es un salto de línea, lo que asegura que cada directorio se imprima en una línea separada.
3. sort:
sort ordena las líneas de texto en orden alfabético. Así, todos los nombres de directorios se ordenarán.
4. uniq -c:
uniq elimina líneas duplicadas consecutivas, pero en este caso, dado que las líneas fueron ordenadas anteriormente, todas las duplicadas serán contiguas.
La opción -c cuenta cuántas veces aparece cada línea (en este caso, cada directorio) y muestra esa cuenta junto con el nombre del directorio.
5. sort -nr:
La opción -n indica que se debe realizar una comparación numérica (en lugar de lexicográfica) y -r indica que debe ordenarse en orden inverso (de mayor a menor).
Esto significa que los directorios que tienen mayor cantidad de archivos aparecerán primero en la salida.

#### Comando ls -l
La salida del comando ls -l comienza con una serie de caracteres que indican el tipo de archivo. 
1. El primer carácter de esta cadena representa el tipo de archivo. Aquí están los más comunes:
  **-:** Indica un archivo regular (es decir, un archivo normal de datos, texto, etc.).
  **d:** Indica un directorio.
  **b:** Indica un dispositivo de bloque (block device). Los dispositivos de bloque, como discos duros y sistemas de archivos, son aquellos que pueden manejar bloques de datos y permiten acceso aleatorio. Por eso, están etiquetados con la letra b.
  **c:** Indica un dispositivo de carácter (character device). Los dispositivos de carácter, que manejan flujos de datos secuenciales, como terminales o dispositivos serie, están etiquetados con la letra c.
  **l:** Indica un enlace simbólico (symlink).
  **s:** Indica un socket.
  **p:** Indica un FIFO (named pipe).
2. Siguientes Nueve Caracteres (Permisos):
Estos caracteres se dividen en tres grupos de tres, que representan los permisos para el propietario, el grupo y otros.
    * Primer grupo (Propietario):
      **r:** Permiso de lectura.
      **w:** Permiso de escritura.
      **x:** Permiso de ejecución.
      **-:** Sin permiso (es decir, no tiene ese permiso específico).
    * Segundo grupo (Grupo) y Tercer grupo (Otros):
      Similar al primer grupo, y muestra lo mismo que los permisos del propietario.


---

### Mantenimiento de Sistemas de Archivos

#### Dump 
Es una herramienta en sistemas Unix y Linux que se utiliza para hacer copias de seguridad de sistemas de archivos. Su función principal es crear un volcado (dump) de los datos contenida en un sistema de archivos y almacenarlos en un medio de respaldo, generalmente en un archivo de respaldo o en un dispositivo de almacenamiento.
Su objetivo es permitir la recuperación de datos en caso de fallos del sistema, pérdidas de datos, o corrupción de archivos.

#### fsck (File System Consistency Check)
Es una herramienta utilizada en sistemas Unix y Linux para verificar y reparar sistemas de archivos. Su función principal es asegurarse de que el sistema de archivos esté en un estado consistente y libre de errores.
fsck puede intentar corregirlos automáticamente o solicitar la intervención del usuario.

no incluye una etiqueta (LABEL) porque estás especificando un dispositivo en formato de archivo de dispositivo, en este caso, `/dev/loop0p1`

#### LABELS: Utilización de etiquetas y UUID para identificar sistemas de archivos
En ocasiones es más sencillo y descriptivo referirse a una partición por su etiqueta (LABEL). Tanto ext4, xfs como btrfs tienen herramientas para cambiar y/o asignar una etiqueta.
Además, puede resultar ventajoso usar el UUID (identificador único universal), cuando vamos a utilizar discos que estaban en una máquina en otra, ya que podemos abstraernos del archivo del nodo al dispositivo

- Al usar `/dev/loop0p1`, estás haciendo referencia directa a un dispositivo de bloque específico en tu sistema. 
- Las etiquetas (LABEL) permiten identificar sistemas de archivos de una manera más legible. Por ejemplo, en lugar de usar `/dev/loop0p1`, podrías etiquetar tu sistema de archivos como data_volume y luego usar LABEL=data_volume en tu archivo `/etc/fstab`.
- Usar etiquetas proporciona más flexibilidad, ya que la etiqueta seguirá siendo la misma incluso si el dispositivo cambia su nombre (por ejemplo, si `/dev/loop0p1` se convierte en `/dev/loop1p1`, si hay múltiples dispositivos o si el sistema se cambia a otro entorno donde el orden de los dispositivos es diferente).
- para asignar una etiqueta a tu sistema de archivos en ext4:
	`sudo e2label /dev/loop0p1 my_label`

Luego, en tu archivo `/etc/fstab`, podrías agregar la entrada así:
	`LABEL=my_label /mnt/my-volume ext4 defaults 0 0`


Si al hacer el cambio del archivo fstab no hacemos reboot y cerramos el multipass arruinamos la instancia. Creo.

No necesitas reiniciar el sistema para aplicar los cambios que hiciste en /etc/fstab. Puedes usar el comando `mount -a` o montar un sistema de archivos específico según tus necesidades.


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


### Herramientas de mantenimiento y optimización en xfs

#### xfs_metadump

Solo debería ser usado para copiar sistemas de archivos desmontados, de solo lectura o congelados (xfs_freeze).
Podemos copiar los metadatos hacia un archivo:

```shell
root@foo:/home/ubuntu# xfs_metadump -g /dev/sdb6 prueba.img
Copied 347520 of 1141120 inodes (1 of 4 AGs)
# ls -lh prueba.img
-rw-r--r-- 1 root root 123M nov 24 23:22 prueba.img
```

Uso el comando file para que reconozca el tipo de archivo:

```shell
root@foo:/home/ubuntu# file prueba.img
rino: XFS filesystem metadump image
```
#### xfs_db

Este programa sirve para hacer tareas de depuración y para obtener información de bajo nivel del sistema, por ejemplo:

```shell
root@foo:/home/ubuntu# xfs_db -r /dev/sdb6
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
root@foo:/home/ubuntu# xfs_fsr /dev/sdb6
```
### Herramientas avanzadas para ext2/ext3/ext4

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


### Material extra brindado en la clase: 

 - Podcast:
    Inglés: The DevOps Paradox
    Español: Enchiladas DevOps

 - Youtube: 
    DevOpsToolkit
    Es de una de las personas que hace DevOps Paradox.








