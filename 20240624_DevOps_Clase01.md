Paula Romero: Asistente curso devops.
El bootcamp se aprueba cuando se hacen las actividades y con el 75% de asistencia.
 Por eso hacer asistencia con el nombre con el código.
 bootcamps@educacionit.com
 
 Profesor tutor es Franco.
 
 Juan Manuel Caimo profesor bootcamp devops.
 Le gusta jugar en la compu.
 Le entusiasman los desafíos que solucione con lo que aprendió. ser un granito de arena en este proceso.
 Su Experiencia profesional fue primero en el mac donalds, luego de soporte técnico (primero junior y despues para programadores), finalmente trabajó como un devops. Ahora cambió de trabajo.
 
 
 devops era una cultura que llevaba a las empresas a pensar el desarrollo de una manera. Infraestructura en código.
 
 Va a estar presentando y explicando el material de alumni.
 
 Además la parte de desafíos se ve con Franco, quien los calificará. Él va a estar para hacer soporte. 
 --------------------------------------------
 
 Vamos a aprender todo lo necesario para insertarme en el mundo laboral como devops Engineer.
 
 Lunes y Miércoles de 19 a 23h. No hay breaks.
 
 Los primeros 10 minutos son para las dudas.
 
 --------------MODULO 0 - Clase 01 --------------------------
 --------------Instalación e Introducción -------------------
 Franco:
 
 Entrega en fecha.
 Redactar documentación legible.
 Añadir material de soporte adicional.
 Cumplir con las consignas.
 El entregable debe ser funcional, funcionar sin errores y cumplir la consigna.
 Canal de comunicación via discord, donde responde las preguntas, más fiable que zoom que no tiene historial.
 
 
 El bootcamp está dividido en fases: Linux, cloud (aws 90% cuenta gratis, gsp de google, azure de microsoft hay que poner tarjeta en estos otros dos, usar free trial) y devops.
 
 AWS es la más usada y requerida en puestos laborales.
 Sabiendo AWS sabés las otras dos, cambian los nombres de los servicios.
 
 
 Devops es la última fase, donde se ven todas las herramientas. Se ve las tareas y el rol del devops. Tenemos Docker.
 
 Kubernetes. En una empresa su producto corre en docker, mientras ese producto escale y llegue a más usuarios vas a necesitar ponerlo en dos contenedores para que llegue a más usuarios, kubernetes está acá. Le decís quiero dos contenedores de docker y que tengan 4gb de ram y cierta cantidad de espacio, y kubernetes velará por mantener a la aplicación de esa manera.
 
 
 Ansible: Es como decía Nelson, al servidor le configurás ciertas cosas para que una app corra.
Manejar varios servidores puede llevar a errores y entonces Ansible permite que en lugar de tener 3 servidores que hay que configurar uno por uno, con Ansible configurás el estado que querés que tengan los servidores de linux, y el estado base va a ser ese.

Git es una herramienta que se utiliza para control de versiones. El respositorio de git 



Terraform: En lugar de tener los servidores creados a mano con terraform se hace con código. La infraestructura es lo que determina esta herramienta.

Jenkins habilita o permite lo que es el CI / CD del continuos Integration / continuos Deployment. 

----------------------------------------------4
----------------------------------------------

Linux

Gestor de paquetes (Package Manager)

sirver para instalar aplicaciones. Algunas solo están desarrolladas para ser instaladas por package manager.

Debian: apt-get
Ubuntu: apt-get
Fedora: yum o dnf

MacOs: HomeBrew (https://brew.sh/)

Windows: Chocolatey
			https://chocolatey.org/install
 
 
 Multipass es para emular un linux solo por linea de comandos. Sirve más que una máquina virtual. Wsl es un poco más pesado, el cual genera un sistema operativo de linux. Multipass genera una máquina virtual bastante liviana. Con Wsl podés acceder a los arhivos de windows. Con Multipass no se puede, está más contenido.
 
 crea una instancia
 multipass launch --name <nombre>
 
 listar nuestras instancias: multipass list
 
 usar la shell de una instancia: multipass shell <nombre>
 
 borrar una instancia multipass delete <nombre>
 
 Limpiar las instancias borradas: multipass purge.
 
 Con el list puedo saber las instancias que están corriendo, en ese caso puedo usar 
 
 multipass stop
 
 para estopearlas.
 
 ---------------------------------------
 
 Linux: diferencias entre snap y apt-get (y yum o dnf)
 
 
 www.linuxjourney.com
 
 pelado nerd trabaja como sre. Es de Mendoza.
 
 
 
 
 