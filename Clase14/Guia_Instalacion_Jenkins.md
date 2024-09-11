
# Guía de Instalación de Jenkins en Ubuntu

Esta guía te ayudará a instalar Jenkins en un sistema operativo Ubuntu.

## Prerrequisitos

1. Un servidor Ubuntu actualizado.
2. Acceso a una cuenta con privilegios de `sudo`.
3. Java instalado en tu sistema.

## Pasos de Instalación

### 1. Actualizar el sistema

Antes de instalar Jenkins, asegúrate de que tu sistema esté actualizado:

```bash
sudo apt update
sudo apt upgrade -y
```

### 2. Instalar Java

Jenkins requiere Java para ejecutarse. Instala OpenJDK:

```bash
sudo apt-get install openjdk-17-jre -y
```

Verifica la instalación de Java:

```bash
java -version
```

### 3. Agregar el repositorio de Jenkins

Importa la clave GPG y añade el repositorio de Jenkins:

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### 4. Instalar Jenkins

Después de agregar el repositorio, actualiza la lista de paquetes e instala Jenkins:

```bash
sudo apt update
sudo apt install jenkins -y
```

### 5. Iniciar Jenkins

Inicia el servicio de Jenkins:

```bash
sudo systemctl start jenkins
```

Habilita Jenkins para que se inicie automáticamente al arrancar el sistema:

```bash
sudo systemctl enable jenkins
```

### 6. Configurar el Firewall

Si tienes un firewall activado, permite el puerto 8080, que es el puerto por defecto de Jenkins:

```bash
sudo ufw allow 8080
sudo ufw enable
sudo ufw status
```

### 7. Acceder a Jenkins

Jenkins se ejecuta en el puerto 8080. Puedes acceder a Jenkins visitando:

```
http://your_server_ip:8080
```

### 8. Desbloquear Jenkins

Durante la primera instalación, Jenkins te pedirá que ingreses una contraseña de administrador. Obtén esta contraseña ejecutando:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 9. Completar la configuración

Después de ingresar la contraseña, sigue las instrucciones en pantalla para completar la instalación de Jenkins. Puedes instalar los plugins recomendados o seleccionar los que necesites.

¡Jenkins está ahora listo para ser utilizado!

## Recursos adicionales

- [Documentación oficial de Jenkins](https://www.jenkins.io/doc/)
