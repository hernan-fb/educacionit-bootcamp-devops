#### Paso 1: Crear una instancia con Multipass
**multipass launch -n my-instance**
**multipass shell my-instance**

#### Paso 2: Crear un volumen adicional desde la instancia
**sudo dd if=/dev/zero of=/home/ubuntu/my-volume.img bs=1M count=1024
sudo losetup -fP /home/ubuntu/my-volume.img
sudo losetup -a**

#### Paso 3: Particionar y formatear el volumen
**sudo lsblk
sudo fdisk /dev/loop0**
- Cuando ingresemos el comando fdisk, vamos a entrar en la interfaz del programa. Dentro de fdisk, sigamos estos pasos:
    - n para nueva partición.
    - p para primaria.
    - Número de partición (1).
    - Acepta los valores predeterminados para el primer y último sector.
    - w para escribir los cambios.

**sudo mkfs.ext4 /dev/loop0p1**

#### Paso 4: Montar el volumen
**sudo mkdir /mnt/my-volume
sudo mount /dev/loop0p1 /mnt/my-volume
df -h | grep /mnt/my-volume**

#### Paso 5: Configuración de montaje automático
**sudo nano /etc/fstab**
Agregar esta linea al final del archivo: **/dev/loop0p1 /mnt/my-volume ext4 defaults 0 0**

#### Paso 6: Verificación final
**exit
multipass restart my-instance
multipass shell my-instance
df -h | grep /mnt/my-volume**