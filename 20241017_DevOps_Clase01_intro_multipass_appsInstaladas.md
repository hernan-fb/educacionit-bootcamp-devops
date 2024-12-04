## Intro
- Paola: Hizo la KickOff de la bootcamp

- Paula Romero: Asistente, envía las carpetas drive de los desafíos. de 17h a 22h de lunes a viernes. Toma la asistencia.

- Ezequiel alias Edgar Gonzalez Rodriguez: Profesor / Facilitador. 41años.
                Tiene un hijo de 5 años. Le gusta aprender y resolver problemas con tecnología.
                Ing Sistemas se cambió a Licenciatura en Gestión de Tecnología. Trabaja en DevOps desde 2003, luego siguió con cloud y sistemas.
                La empresa creció y entonces aprendió bastante. Ahora cambió a otra empresa, multinacional. Sabe de infra. 

- Franco Congedo: Tutor, corrije entregas. Está en 3er año de ingeniería. Trabaja de DevOps por haber hecho la bootcamp. 
                    Le gustan los autos, está armando un Boogie. No nos asustemos por los contenidos ni por las entregas.

- Ezequiel Docente ? Me parece que se equivocó.

Es una formación intensiva. Requiere dedicación.

Plataforma Alumni, se cargan las clases de 24 a 48h después de la clase.

75% de asistencia para el certi de asistencia
y aprobar actividades en tiempo y forma para el certi de aprobación.


Desafíos DevOps: Aprender rápido

Martes y Jueves de 19h a 21h30.
Primeros 10min repaso y dudas.
Break entre 20h15 y 20h25.
Clases teóricas y prácticas (generalmente 1era y 2da parte)
Desafíos.
Este bootcamp tiene 3 módulos, cada módulo tiene sus propios desafíos.
- Entregar en fecha (suma más puntos).
- Es importante redactar bien la documentación.
- Cada entregable debe ser funcional.
- Comunicados con Discord.

Pide:
- Compromiso, 
- participar en clase, 
- hacer preguntas, abrir micrófono, 
- paciencia a quienes ya tienen experiencia.
- Feedback no verbal Check vs Cross vs Simbolito Tasa de Café (estoy ausente). Se puede usar en el break.
- Desafíos Cada dos semanas, con 1 semana de tiempo.

El primer módulo es de todos sistemas linux.
El segundo módulo ya tiene otros temas.

### contenido
- Primer Módulo: linux
- Segundo Módulo: Aws Academy, 
- Tercera Parte: Docker, Kubernetes
### Herramientas:
- linux 100%
- Docker
- Kubernetes
- Svn
- Aws Acádemy
- Ansible
- Jenkins
- Git
- Terraform

### Software
Linux: Usaremos Ubuntu como distribución de trabajo, aquí el gestor de paquete es apt.
Hay cambios entre redhat/centos unix vs ubuntu/debian unix, en gestor de paquetes.

MacOsx: HomeBrew, es lo mejor para instalar lo que se necesita por linea de comandos.

en Aws tiene su distribución de linux basada en centos, por lo que va a ser diferente ligeramente 

GitHub: Ponerle onda al repositorio.

Gestor de Paquetes: Usaremos Ubuntu y por tanto apt-get.
En Windows Gestor de Paquetes: Abrir una consola como administrador, y configurar execution policy:
```
Set-ExecutionPolicy AllSigned
```
y luego instalamos `chocolatey`
```
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePoingManager]::SecurityProtocol -bar 3072; iex((New-Object System.nEt.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

documentación:

https://chocolatey.org/install#individual
https://community.chocolatey.org/packages

- Docker virtualiza el kernel, Multipass crea una máquina virtual. En windows multipass necesita VirtualBox. Mac también tiene su virtualizador. Hiper-V es ligeramente mejor que virtualbox. Siempre va a ser necesario un virtualizador.

- GitHub, ver el readme, del repositorio con el mismo nombre que el usuario. reveer 2h01m.

- Tree, variantes:
    tree -h

#### Multipass

- Multipass es para emular un linux solo por linea de comandos. Sirve más que una máquina virtual. Wsl es un poco más pesado, el cual genera un sistema operativo de linux. Multipass genera una máquina virtual bastante liviana. Con Wsl podés acceder a los arhivos de windows. Con Multipass no se puede, está más contenido.

crea una instancia

```bash
 multipass launch --name <nombre>
```

 listar nuestras instancias: 
```bash
multipass list
```

 usar la shell de una instancia: 
 
```bash
multipass shell <nombre>
```

 borrar una instancia 
 
```bash
multipass delete <nombre>
```

 Limpiar las instancias borradas: 
```bash
 multipass purge.
```

 Con el list puedo saber las instancias que están corriendo, en ese caso puedo usar 
 
```bash
 multipass stop
``` 
 para estopearlas.

##### Comandos para ampliar recursos de multipass.

Es necesario detener la maquina y volver a iniciarla.

```bash
multipass stop devopsbootcamp
multipass set local.devopsbootcamp.cpus=4
multipass set local.devopsbootcamp.disk=60G
multipass set local.devopsbootcamp.memory=7G
multipass start devopsbootcamp
```

Links:

- [Ampliar recursos en multipass](https://multipass.run/docs/modify-an-instance)



### Algunas aplicaciones random recomendadas a lo largo de la cursada

#### Tailscale
Es gratuito con algún límite.
Tailscale es un servicio de red virtual privada (VPN) que utiliza tecnología de red eficiente basada en el protocolo WireGuard. Permite conectar dispositivos a través de Internet de manera segura y sencilla, creando una red privada que facilita la comunicación entre dispositivos sin necesidad de configurar complicadas reglas de firewall o NAT.

Algunas características clave de Tailscale incluyen:

1. **Fácil configuración**: Tailscale se puede configurar rápidamente en diversos sistemas operativos y dispositivos, incluyendo Windows, macOS, Linux, iOS y Android.
2. **Red P2P**: Establece conexiones directas entre dispositivos siempre que sea posible, lo que optimiza el rendimiento y reduce la latencia.
3. **Seguridad**: Utiliza cifrado de extremo a extremo para asegurar que los datos que se transfieren entre dispositivos estén protegidos.
4. **Acceso remoto**: Permite acceder a tus dispositivos de forma remota sin complicadas configuraciones.
5. **Administración de permisos**: Ofrece una interfaz para gestionar y controlar el acceso entre los dispositivos conectados.

Tailscale es especialmente útil para equipos que necesitan colaborar de manera segura y eficiente, así como para usuarios que desean acceder a dispositivos en diferentes redes de forma simple.