# Clase 7 - Comandos de Linux utiles
min 30 clase 7
1h finaliza actividad.
1h10 hace preguntas por cada ej. hasta 1h26

En la clase anterior vimos algunos comandos para administrar usuarios, grupos y permisos, antes de seguir sumando nuevos comandos hagamos una breve practica.

## Ejercicio 1

1. Crear un usuario llamado `user1` con contraseña `user1`.
   ```bash
    sudo useradd user1 -m -s /bin/bash -d /home/user1 -c "Usuario 1,Buenos Aires" -p $(echo "superPassword123456." | openssl passwd -1 -stdin)
    ```
2. Comprobar que el usuario `user1` fue creado.
   ```bash
    cat /etc/passwd | grep user1
    ```
3. Crear un grupo llamado `grupo1`.
   ```bash
    sudo groupadd grupo1
    ```
4. Agregar el usuario `user1` al grupo `grupo1`.
   ```bash
    sudo usermod -aG grupo1 user1
    ```
5. Comprobar que el usuario `user1` pertenece al grupo `grupo1`.
   ```bash
     id user1
    ```
6. Impersonar el usuario `user1`.
   ```bash
    sudo su - user1
    ```
7. Crear un archivo llamado `archivo1.txt` en la carpeta `/home/user1` con el contenido `Hola mundo`.
   ```bash
    echo "Hola mundo" > /home/user1/archivo1.txt
    ```
8. Revisar los permisos del archivo `archivo1.txt`.
   ```bash
    ls -l /home/user1/archivo1.txt
    ```

   hacer un exit.

## Ejercicio 2

Vamos a modificar informacion del usuario `user1`.

1. Cambiar los comentarios donde guardamos informacion adicional del usuario `Usuario 1` por `DevOps Educacion,devops@sitio.com`
    ```bash
     sudo usermod -c "DevOps Educacionit,Cordoba" user1
    ```
2. Instalar finger y ver informacion de una forma mas amigable.
   ```bash
    sudo apt install finger -y
   ```
3. Verificar que los cambios se realizaron correctamente.
    ```bash
    finger user1
    ```

## Ejercicio 3

Vamos a renombrar un usuario y sus archivos.

1. Renombra el `user1` a `devops`, asegurate de que `user1` fue creado en los ejercicios anteriore.
   ```bash
    sudo usermod -l devops user1
   ```
2. Cambiar el nombre de la carpeta `/home/user1` a `/home/devops`.
   ```bash
    sudo mv /home/user1 /home/devops
   ```
3. Verificar que los cambios se realizaron correctamente.
   ```bash
    cat /etc/passwd | grep devops
   ```
4. Actualiza la configuracion de directorio home del usuario.
   ```bash
    sudo usermod -d /home/devops devops
   ```
5. Asegurate que el usuario `devops` puede acceder a su carpeta.
   ```bash
    sudo su - devops
    exit
   ```
6. Crea un grupo llamado `devops` y agrega el usuario `devops` al grupo, como grupo principal, elimina los grupos `grupo1` y `user1`.
   ```bash
    sudo groupadd devops
    sudo usermod -g devops devops
    sudo delgroup grupo1
    sudo delgroup user1
   ```
6. Verifica que todo el contenido de /home/devops/ pertenezca al usuario `devops`, si no es propietario tanto el usuario como el grupo cambialo para que figure `devops`.
   ```bash
    ls -l /home/devops
    sudo chown -R devops:devops /home/devops
   ```
7. Verifica que el cambio se realizo correctamente.
   ```bash
    sudo ls -alh /home/devops
   ```
8. Crea un archivo en la carpeta `/home/devops` con el nombre `archivo2.txt` y el contenido `Hola mundo`.
   ```bash
    sudo su - devops
    echo "Hola mundo" > /home/devops/archivo2.txt
   ```
9. Elimina el archivo2.txt.
   ```bash
    rm /home/devops/archivo2.txt
    exit
   ```