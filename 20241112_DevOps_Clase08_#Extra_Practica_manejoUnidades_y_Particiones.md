# Clase 8 - Instalacion del sistema, manejo de unidades y particiones
(esto está en la clase 8 y en la 9 desde 1h15m)
## Instalación del Sistema

En este apartado vamos a ver una instalacion generica de un sistema operativo, en este caso vamos a utilizar Ubuntu, pero los pasos son similares para cualquier distribución de Linux.

Por favor vean la grabacion.

## Manejo de Unidades y Particiones

En esta practica vamos a ver como manejar unidades y particiones en Linux, el objetivo es que podamos montar la carpeta home de un usuario en una unidad especifica, esto puede ser de utilidad si sabemos que el usuario va a utilizar mucho almacenamiento y queremos que este en una unidad diferente a la del sistema operativo.

1. Identificar el disco.
   ```bash
    sudo fdisk -l
    ```
2. Crear una partición, ten en cuenta que tipo de unidad utilizas, dependiendo de esta vas a tener un identificador diferente, en este caso vamos a utilizar un disco ssd que se identifica como `sdb`. Lean
   ```bash
    sudo fdisk /dev/sdb
    ```
    1. `n` - Crear una nueva partición.
    2. `p` - Partición primaria.
    3. `1` - Número de partición.
    4. `+1G` - Tamaño de la partición.
    5. `w` - Escribir los cambios.
3. Formatear una partición.
   ```bash
    sudo mkfs.ext4 /dev/sdb1
    ```
4. Obtener UUID de una partición.
   ```bash
    sudo blkid /dev/sdb1
    ```
5. Montar una partición.
   ```bash
    sudo mkdir /mnt/disco1
    sudo mount -U 1234-5678-9ABC-DEF0 /mnt/disco1
    ```
    Copiar el contenido de un directorio sin perder los permisos.
    ```bash
    sudo cp -r /home/user/data /mnt/disco1
    ```
6. Configurar para un montaje automatico
   ```bash
    sudo vi /etc/fstab
    ```
    Escribir lo siguiente dentro del archivo, cambia el UUID por el que obtuviste en el paso 4.
    ```bash
    UUID=1234-5678-9ABC-DEF0 /mnt/disco1 ext4 defaults 0 0
    ```
    Escribe el archivo, si usamos vi presiona `ESC` y luego `:wq`.
    
    Montar la unidad.
    ```bash
    sudo mount -a
    ```
7. Verificar que la unidad se monte automaticamente.
   ```bash
    df -h
    ```

### Explicación de Cada Columna

Cada línea en `/etc/fstab` sigue este formato general:

```plaintext
<dispositivo> <punto_de_montaje> <sistema_de_archivos> <opciones> <dump> <pass>
```
## Dispositivo
•	Usando el nombre del dispositivo (como /dev/sda1).
•	Usando el UUID, que se obtiene con blkid (por ejemplo, UUID=1234-5678-9ABC-DEF0).
•	Usando la etiqueta (LABEL), si la partición tiene una (por ejemplo, LABEL=mi_particion).
•	Nota: Utilizar el UUID es recomendado, ya que es único y permanece constante, incluso si el sistema cambia la designación de dispositivo.

## Punto de Montaje

	•	Especifica la ubicación en el sistema de archivos donde se montará la partición o dispositivo, como /, /home, /mnt/disco1, etc.
	•	Debe ser un directorio existente en el sistema.

## Sistema de Archivos

	•	Define el tipo de sistema de archivos del dispositivo, como ext4, xfs, ntfs, vfat, etc.
	•	También puedes usar valores especiales como swap para indicar que el dispositivo se utilizará como memoria de intercambio.

## Opciones

	•	Define las opciones de montaje que se usarán para el sistema de archivos. Algunas opciones comunes son:
	•	defaults: Usa las opciones por defecto (rw, suid, dev, exec, auto, nouser, async).
	•	ro/rw: Monta el sistema de archivos en modo de solo lectura (ro) o de lectura/escritura (rw).
	•	noauto: Evita el montaje automático en el arranque. El usuario deberá montarlo manualmente.
	•	user/nouser: Permite o prohíbe que usuarios no privilegiados monten el sistema.
	•	noexec: Prohíbe la ejecución de archivos en la partición.
	•	Estas opciones pueden ser combinadas separándolas con comas (por ejemplo, defaults,noexec).

## Dump

Se trata de una herramienta de respaldo, hoy en dia esto fue remplazado por otras herramientas, pero en algunos sistemas se sigue utilizando.

	•	Indica si se debe respaldar (hacer dump) el sistema de archivos con el comando dump.
	•	Usualmente tiene el valor 0 (no hacer dump) o 1 (hacer dump). La mayoría de las veces se deja en 0.

## Pass

Define el orden de chequeo del sistema de archivos al inicio con el comando fsck.
•	Los valores posibles son:
•	0: No verifica la partición durante el arranque.
•	1: La partición raíz (/) generalmente tiene 1 para asegurar que se verifique primero.
•	2: Las otras particiones tienen 2, indicando que se deben verificar después de la raíz.
•	Las particiones con pass mayor que 0 se verifican en paralelo.

Ejemplo de Línea en /etc/fstab

```plaintext
UUID=1234-5678-9ABC-DEF0 /mnt/disco1 ext4 defaults 0 2
```
•	UUID=1234-5678-9ABC-DEF0: Monta la partición con este UUID.
•	/mnt/disco1: La partición se montará en /mnt/disco1.
•	ext4: Usa el sistema de archivos ext4.
•	defaults: Usa las opciones por defecto de montaje.
•	0: No hacer dump.
•	2: Verificar esta partición después de la raíz al inicio.

## Comando `dd`
Este comando es util para hacer pruebas de discos:
`dd if=/dev/zero of=testing bs=1M count=5120`
- of= es el nombre del archivo.
- bs= tamaño del bloque (1M)
- count=el total que quiero escribir (5120 5GB)


## Liks:

[Datadog](https://www.datadoghq.com/)
[Newrelic](https://newrelic.com/)
[ELK](https://www.elastic.co/elastic-stack)