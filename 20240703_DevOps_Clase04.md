### Arranque, servicios HTML5: Fundamentos Web y apagad

#### Mecanismos de arranque

Los 2 mecanismos principales de arranque en Linux son `SysVinit` y `systemD`, describiremos a estos dos con detalle a continuación.

#### Pasos típicos de arranque:
1. Encendido: El usuario presiona el botón de encendido.
2. POST: El BIOS/UEFI realiza la verificación inicial del hardware.
3. Dispositivo de Arranque: Se determina el dispositivo de arranque (uso de MBR o UEFI).
4. Bootloader: Se ejecuta el bootloader que carga el kernel.
5. Kernel: El kernel se inicia y carga initramfs.
6. Sistema de Archivos: Initramfs monta el sistema de archivos raíz.
7. Init: El proceso init (o systemd en muchos sistemas modernos) se inicia, creando los procesos del usuario y iniciando los servicios del sistema.


### SysVinit

Muchos sistemas operativos UNIX usan este mecanismo, que fue por primera vez implementado en UNIX System III y posteriormente en UNIX System V.
SysVinit es el sistema de inicio tradicional para muchas distribuciones de Linux. Se basa en un modelo de scripts que se encuentran en el directorio `/etc/rc.d/`, que se ejecutan en secuencia según el nivel de ejecución (runlevel) del sistema.
Cada nivel de ejecución tiene un conjunto de scripts que se ejecutan al entrar o salir de dicho nivel.
SysVinit no soporta la inicialización paralela de servicios de manera nativa; los procesos se inician de forma secuencial, lo que puede alargar el tiempo de arranque.

#### Proceso de Arranque en SysVinit
1. El kernel se carga y pasa el control al proceso `init`.
2. `init` se inicia desde `/sbin/init` y determina el nivel de ejecución actual.
3. Se ejecutan los scripts en el directorio correspondiente (`/etc/init.d/`) en el orden definido para ese nivel de ejecución.

#### Proceso init
El primer proceso se llama `init`, que arranca luego de la carga del kernel. Al ser el primer proceso en ejecutarse toma el PID 1. El init es un demonio (es decir, corre en segundo plano y sin una terminal que lo controle), el cual es el padre de todos los procesos iniciados durante el uso del sistema.
Al iniciar el sistema tendrá un nivel de ejecución predeterminado (runlevel), en el que se definirán los distintos procesos a iniciar. Entre los niveles de ejecución se encuentran:
● La configuración principal del `init` es el archivo `/etc/inittab` en el que se define cuál es el nivel de ejecución predeterminado.
● Luego de leer la configuración, el init cargará los scripts que se encuentran dentro de `/etc/rc2.d` (en caso de que el runlevel fuera el 2) o `/etc/rc3.d` (en caso de que el runlevel fuera el 3)

#### Niveles de Ejecución (Runlevel)

Los niveles de ejecución en SystemV describen ciertos estados del equipo, que se caracterizan por ejecutar ciertos procesos. En general, existen 8 niveles de ejecución, que van del 0 al 6 y S o s, siendo estos últimos alias del mismo nivel de ejecución. De estos ocho niveles, tres son considerados reservados, y son los siguientes:

##### **Niveles Reservados**
|Nivel|Descripción|
|---- |----  |
|Nivel 0      |     Apagado del sistema. Inicia el proceso normal de apagado. |
|Nivel 1,s,S  |  Modo usuario único, se utiliza para la reparación del sistema, ya que iniciará solo los procesos necesarios para el arranque del sistema. Las tres opciones 1, s y S significan lo mismo. |
|Nivel 6      |    Reinicio del sistema. Inicia la secuencia de reinicio |

##### **Dependiendo de la distribución:**
|Nivel|Descripción|
|-----|-----------|
|Nivel 2   |     Nivel predeterminado, multiusuario con o sin entorno gráfico  (Debian, Ubuntu).|
|Nivel 3   |     Nivel predeterminado, multiusuario sin entorno gráfico (Red Hat, Slackware, CentOS).|
|Nivel 4   |     Nivel multiusuario o reservado.|
|Nivel 5   |     Nivel multiusuario con entorno gráfico (Red Hat).|


##### Nivel Reservado: Single User Mode (Usuario único)

Este modo se utiliza para el mantenimiento del sistema. En este modo las conexiones remotas, red y la mayor parte de los servicios se encuentran desactivados. Generalmente se 
utiliza para corregir problemas del sistema de archivos, y que el sistema no puede resolver automáticamente.
Podemos acceder a este modo porque el sistema así lo pide o especificando por la línea de comandos del GRUB.

* Línea de ejemplo:
    ```
    kernel /vmlinuz-2.6.27.21-170.2.56.fc10.i686 
    ro root=/dev/hda1 rhgb quiet
    ```
    - `kernel /vmlinuz-2.6.27.21-170.2.56.fc10.i686`: Especifica la ubicación del kernel que se va a cargar. El archivo `vmlinuz` es la imagen del kernel comprimida. Esta línea indica que se cargará la versión `2.6.27.21` del kernel, que es específica de la distribución _Fedora Core 10 (fc10)_ para arquitecturas de 32 bits (i686).
    - `ro`: Indica que el sistema de archivos raíz (root filesystem) se montará como **solo lectura** durante el arranque. Esto es común para permitir que el sistema realice comprobaciones antes de montarlo como lectura/escritura.
    - `root=/dev/hda1`: Especifica la partición que contiene el sistema de archivos raíz. En este caso, `/dev/hda1` es la primera partición en el primer disco duro (usando la nomenclatura tradicional de Linux).
    - `rhgb`: Este es un parámetro específico de Red Hat y sus derivados, que habilita el gráfico de arranque (Red Hat Graphic Boot).
    - `quiet`: Este parámetro suprime la mayoría de los mensajes de arranque, proporcionando una experiencia de arranque más limpia y menos verbosa.

* Ejemplo con single:
    ```
    linux /boot/vmlinuz-3.13.0-29-generic 
    root=/dev/sda1t single
    ```
    - `linux /boot/vmlinuz-3.13.0-29-generic`: Aquí, se carga un kernel diferente, en este caso, la versión `3.13.0-29` para distribuciones de Ubuntu o sus derivados. La ruta especifica que está en el directorio `/boot`.
    - `root=/dev/sda1`: Similar al caso anterior, especifica la partición que contiene el sistema de archivos raíz, en este caso, la primera partición en el primer disco duro (sda).
    - `single`: Se refiere al modo de un solo usuario, lo que significa que el sistema se iniciará en un modo de rescate que proporciona acceso como root sin autenticar la contraseña (no se inician servicios de red ni de usuario).

Ambos ejemplos son configuraciones de cómo el bootloader inicia diferentes versiones del kernel en sistemas Linux. Son lineas de un bootloader (como GRUB) que se utilizan para cargar el kernel. 
Los archivos de configuración de GRUB, se encuentran típicamente en `/boot/grub/grub.cfg` o `/etc/grub.d/`. Este archivo se genera en parte a través de un comando como `update-grub` (o `grub-mkconfig`). 
Algunos detalles relevantes sobre este archivo son:

**Formato:** En el caso de GRUB Legacy, las líneas pueden estar en el archivo `menu.lst`, mientras que en GRUB 2 se usa un formato más dinámico y se suelen generar varias entradas automáticamente.
**Entradas:** Cada entrada en el archivo de configuración define un kernel específico junto con sus parámetros de arranque.
**Módulos y Temas:** GRUB también permite la carga de distintos módulos y el uso de temas, lo que permite personalizar la apariencia del menú de arranque.
**Personalización:** Puedes añadir entradas manualmente si deseas modificar o agregar un kernel adicional, así como sus parámetros de configuración. Para ello, es importante tener cuidado al editar, ya que errores pueden provocar que el sistema no arranque correctamente.

En estos dos ejemplos de arriba, se observa que para modo mantenimiento se agrega al final de la línea un número 1 o la palabra single como parámetros.

Para cambiar a modo single desde otro nivel se  utiliza telinit 1 o init 1. Esta opción no es la más recomendada, porque no genera ningún tipo de aviso a los usuarios que se encuentran conectados. Más adelante se detalla cómo hacerlo.
En el modo mantenimiento no vamos a tener servicios de red ni uso de la mayoría de los servicios.

#### Estructura de los runlevels

Los directorios que veremos a continuación, por si solos, no cumplen función; es por eso que cumplen con una lógica que fue definida desde que se creó `Unix System V`, donde se especificó cómo tenía que estar situados cada demonio de arranque. Cuando un sistema Linux arranca, éste inicia una serie de scripts que se encuentran en `/etc`. Luego llama al nivel que corresponda para inicializar o detener los demonios requeridos.
La técnica de inicialización difiere entre algunas distribuciones de Linux, pero todas ellas tienen que cumplir con la Linux Standard Base para poder tener un orden genérico.
Ejemplo:

```shell
$ ls -la /etc/rc*
lrwxrwxrwx 1 root root 7 Apr 29 16:38 /etc/rc -> rc.d/rc
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc0.d -> rc.d/rc0.d
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc1.d -> rc.d/rc1.d
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc2.d -> rc.d/rc2.d
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc3.d -> rc.d/rc3.d
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc4.d -> rc.d/rc4.d
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc5.d -> rc.d/rc5.d
lrwxrwxrwx 1 root root 10 Apr 29 18:33 /etc/rc6.d -> rc.d/rc6.d
lrwxrwxrwx 1 root root 13 Apr 29 16:38 /etc/rc.local -> rc.d/rc.local
lrwxrwxrwx 1 root root 15 Apr 29 16:38 /etc/rc.sysinit -> rc.d/rc.sysinit
/etc/rc.d:
total 136
drwxr-xr-x 10 root root 4096 Apr 29 16:38 .
drwxr-xr-x 133 root root 12288 Aug 29 21:47 ..
drwxr-xr-x 2 root root 4096 Aug 28 20:18 init.d
-rwxr-xr-x 1 root root 2255 Jul 4 2009 rc
drwxr-xr-x 2 root root 4096 Aug 28 08:53 rc0.d
drwxr-xr-x 2 root root 4096 Aug 28 08:53 rc1.d
drwxr-xr-x 2 root root 4096 Aug 28 20:18 rc2.d
drwxr-xr-x 2 root root 4096 Aug 28 20:18 rc3.d
drwxr-xr-x 2 root root 4096 Aug 28 08:53 rc4.d
drwxr-xr-x 2 root root 4096 Aug 28 20:18 rc5.d
drwxr-xr-x 2 root root 4096 Aug 28 08:53 rc6.d
-rwxr-xr-x 1 root root 220 Jul 4 2009 rc.local
-rwxr-xr-x 1 root root 27239 Jul 4 2009 rc.sysinit
```

#### /etc/init.d

En este directorio se encuentran todos los scripts encargados de levantar cada uno de los servicios del sistema.
Algunos de los servicios que podemos encontrar en el `init.d` son los referentes a:

    - httpd       Servidor Web Apache
    - smb         Servidor Samba
    - postfix     Servidor de correo
    - dhcpd       Servidor DHCP
    - named       Servidor DNS
    - mysqld      Manejador de Base de Datos M

Los nombres pueden cambiar de acuerdo a la 
distribución utilizada:

```shell
root@foo:/home/ubuntu# ls -l /etc/rc.d/init.d/
-rwxr-xr-x 1 root root 2974 jun 23 10:18 dhcpd
-rwxr-xr-x 1 root root 3099 feb 25 2008 httpd
-rwxr-xr-x 1 root root 4239 mar 3 2008 mysqld
-rwxr-xr-- 1 root root 6154 ago 6 05:05 named
-rwxr-xr-x 1 root root 1745 sep 18 10:26 smb
-rwxr-xr-x 1 root root 4112 mar 29 2008 postfix
(...salida cortada...)
```

#### /etc/rc.sysinit ó /etc/init.d/rcS

En sistemas basados en Red Hat, se hace uso del script `rc.sysinit` para la inicialización.
La secuencia de comandos `rcS` de Debian hace el mismo trabajo mediante la ejecución de varios pequeños scripts colocados en dos directorios diferentes.
En cada caso, el script se pone en marcha por `init` en el arranque. Este se ocupa de algunas tareas esenciales para preparar el sistema para su uso, tales como montar sistemas de ficheros.
Algunas de las tareas son:
● Configuración de reloj del sistema.
● Configuración de los parámetros del Kernel.
● Levantamiento de dispositivos RAID y LVM.
● Activación y actualización de cuotas en disco.
● Activación de la partición SWAP

#### /etc/rc.local

Este archivo es un script que se llama después de todos los demás scripts de `init` (después de que todos los demonios del sistema han iniciado).
Contiene las personalizaciones locales que afectan el inicio del sistema y proporciona una alternativa para modificar los scripts de inicio de otros. Muchos administradores prefieren evitar cambiar `rc.sysinit`, porque esos cambios se perderán durante un sistema de actualización.
El contenido de `rc.local` no se pierde en una actualización.

Ejemplo:
```shell
root@foo:/home/ubuntu# cat /etc/rc.local
#!/bin/sh
#
# This script will be executed *after* all the other init scripts.
# You can put your own initialization stuff in here if you don't
# want to do the full Sys V style init stuff.
touch /var/lock/subsys/local
```

#### /etc/rc

Este archivo es un script que se usa para cambiar entre los niveles de ejecución. No se usa en Debian.

```shell
root@foo:/home/ubuntu# ls -la /etc/rc
lrwxrwxrwx 1 root root 7 Apr 29 16:38 /etc/rc → rc.d/rc
```

#### El manejo de los servicio y el directorio `/etc/init.d`

La tarea de iniciar y detener servicios del sistema (también conocidos como demonios, cuya intención es ser ejecutados en segundo plano, como por ejemplo, un servidor web) está a cargo de archivos y enlaces simbólicos en `/etc/init.d` y por una serie de directorios de nivel de ejecución específicos llamados desde `/etc/rc0.d` a `/etc/rc6.d`.

En el directorio tendremos una serie de scripts que nos proveerán de diversas funcionalidades para cada servicio.

Son scripts que llevan argumentos que dan un comportamiento a éstos, ya sean los más básicos, como detener o iniciar un servicio.

Al ejecutar el httpd sin parámetros:
```shell
root@foo:/home/ubuntu# ./httpd
Usage: httpd 
{start|stop|restart|condrestart|reload|status|fulls
tatus|graceful|help|configtest}
```
La salida nos indica que podemos ejecutar esos parámetros.

Detener el servicio de httpd:

```shell
root@foo:/home/ubuntu# ./httpd stop
Stopping httpd: [ OK ]
```
Si agregamos un nuevo servicio por medio de un paquete rpm o dpkg, éste creará los scripts correspondientes sin tener que hacer nada. En otros casos tendríamos que agregarlos en el archivo `rc.local` o crear nuestros propios scripts de control.
Es importante tener en cuenta que estos archivos son simplemente shell scripts que contienen los parámetros usados normalmente para administrar los demonios. No todos los demonios de Linux reconocen los argumentos detener, iniciar, etc por la línea de comandos. Pero los scripts en `/etc/init.d` están estandarizados para que podamos ejecutarlos con los parámetros usados normalmente.

#### Los directorios `/etc/rc0.d` a `/etc/rc6.d`
Los scripts de inicio en `/etc/init.d` no son ejecutados directamente por el proceso init. En su lugar, cada uno de los directorios `/etc/rc0.d` a `/etc/rc6.d` contienen enlaces simbólicos que apuntan a los scripts del directorio `/etc/init.d`. Se usan enlaces simbólicos en lugar de archivos regulares para evitar trabajo innecesario en caso de querer cambiar un script.
Cuando el proceso de inicio entra en ejecución en un nivel N, se examinan todos los enlaces en el directorio `rcN.d` asociados. A estos enlaces se les dan nombres especiales en las formas de `KNNname` y `SNNname`. Vamos a explicarlo:


#### Prefijos K y S
Estas letras corresponden a matar (Kill) e iniciar (Start) respectivamente.
**Cada nivel de ejecución define un estado en el que determinados servicios se están ejecutando y en otros no lo están**.
El prefijo S se utiliza para marcar los archivos de todos los servicios que se van a ejecutar en determinado runlevel.
El prefijo K se utiliza para todos los demás servicios que no deben estar en ejecución en determinado runlevel.


#### Secuencia NN
Número de orden. Esta parte del nombre del enlace es un entero de dos dígitos (con un cero a la izquierda, si es necesario). En él, se especifica el orden relativo de los servicios a iniciar o detener. El número más bajo representa la primera secuencia de comandos ejecutados por init, y el mayor número es el último. No hay reglas estandarizadas para la elección de estos números, pero es importante que cuando añadamos un nuevo servicio, nos aseguremos que se inicie después o antes de los servicios que este necesite o dependa.
Si dos servicios poseen el mismo número, posiblemente se ejecuten por orden alfabético.


#### name (Nombre)
El nombre que se usa para el servicio tiene relación con lo que hace. Si el servicio se llama networks, éste tratará sobre algo de redes, si el servicio se llama sshd, tiene relación con el tipo de conexión segura, secure shell.
Cuando init tiene que iniciar un runlevel, usará el directorio correspondiente a su nivel (por ejemplo, `/etc/rc2.d`) y empezará a ejecutar los servicios que estén detallados.

El orden establecido de ejecución estará representado por el número que está a continuación de las letras K o S.
● La K es indicio de que ese script se llamará solamente para apagar algo.
● La S es indicio de que será utilizado para iniciarlo (Ej.: S10network luego S11auditd, S12restorecond, S12syslog).

#### Configurar el nivel de ejecución predeterminado
Para seleccionar el nivel predeterminado de arranque, hay que editar el archivo `/etc/inittab`. 
Ejemplo de contenido. Definir el runlevel 5 como predeterminado.

```shell
id:5:initdefault:
```

#### Determinar el runlevel del sistema
Para verificar en qué runlevel nos encontramos, se utiliza el comando runlevel, el cual mostrará el runlevel anterior y el actual.
Si no hubo un nivel anterior, mostrará una N:

```shell
root@foo:/home/ubuntu# runlevel
N 2
```

#### Cambiar el runlevel del sistema
Para cambiar de un runlevel al otro se utiliza el comando init o telinit (es un link simbólico).

#### Para reiniciar sin aviso
```shell
root@foo:/home/ubuntu# init 6
INIT: Switching to runlevel: 6
INIT: sending processes the TERM signal
Stopping Postfix Mail Transport Agent: postfix
(...salida cortada...)
```

#### Para apagar sin aviso
```shell
root@foo:/home/ubuntu# init 0
INIT: Switching to runlevel: 0
Stopping Postfix Mail Transport Agent: postfix
(...salida cortada...)
```

#### Comando chkconfig

Este comando es útil para activar o desactivar los servicios que se aplican durante el arranque del equipo, así como también conocer el estado de los servicios que se están ejecutando.
Este comando se usa en distribuciones de estilo Red Hat.
Para conocer el estado de los procesos activos, podemos ejecutar el siguiente comando:

    # chkconfig –list

Para conocer el estado de httpd en cada runlevel:

    # chkconfig –-list httpd
    httpd 0:desactivado 1:desactivado 
    2:desactivado 3:desactivado 4:desactivado 
    5:desactivado 6:desactivado

Para deshabilitar el servicio apache2:

    # chkconfig httpd off

Para habilitar el servicio apache2:

    # chkconfig httpd on

Para activar el httpd en los niveles 3 y 5:

    # chkconfig –-level 35 httpd on

Para desactivar el httpd en los niveles 3 y 5:

    # chkconfig –-level 35 httpd off

Para desactivar httpd a mano en los niveles 3 y 5:

    # rm /etc/rc[35].d/S11httpd

Para activar httpd a mano en los niveles 3 y 5:

    # ln -s /etc/init.d/httpd /etc/rc3.d/S11httpd
    # ln -s /etc/init.d/httpd /etc/rc5.d/S11httpd

#### Comando `update-rc.d`

Este comando pertenece a las distribuciones basadas en Debian y es análogo a chkconfig.
Para desactivar el servicio apache2:

    # update-rc.d -f apache2 remove

Para activar el servicio apache2 en los niveles predeterminados:

    # update-rc.d apache2 defaults

#### Levantando, deteniendo y reiniciando servicios
Para iniciar, detener, o reiniciar un servicio se puede poner la ruta completa del ejecutable en `init.d` seguido del parámetro.

    # /etc/init.d/nombreDelServicio 
    {start|stop|status|restart|reload}

Supongamos que tenemos ya instalado y configurado un servidor web Apache y lo único que falta es iniciar el servicio. Para ello, solo bastará teclear lo siguiente:

    # /etc/init.d/httpd start

Para detener este servicio, solo debemos cambiar la palabra start por stop:

    # /etc/init.d/httpd stop

Para reiniciar el servicio:

    # /etc/init.d/httpd restart

Otra manera es mediante el comando service:

    # service httpd {start|stop|status|restart|reload}

Para iniciar el servicio httpd:

    # service httpd start

### Upstart

Es un gestor de servicios alternativo a SysVinit. Se basa en la detección de eventos.
Los servicios se llaman `jobs` y en lugar de usar scripts de shell, poseen archivos de configuración con la extensión `.conf` dentro del directorio `/etc/init.`.
Este mecanismo fue creado por Ubuntu, pero el proyecto está actualmente descontinuado.

### systemd
Es un sistema de inicialización más moderno que busca superar las limitaciones de SysVinit. Introduce un modelo basado en unidades (units), que pueden ser servicios, montajes de sistemas de archivos, dispositivos, etc.
Inicia los servicios de manera paralela, lo que mejora considerablemente el tiempo de arranque del sistema.
Systemd permite manejar servicios y timers, junto con otras características avanzadas como sockets y targets (grupos de unidades).
Diseñado para ser un reemplazo de SysVinit, y muchas distribuciones modernas de Linux (como Fedora, Ubuntu, Debian) han adoptado systemd como su sistema de inicialización por defecto.

#### Unidades (Units)
Los servicios se definen en archivos `.service` y pueden incluir dependencias, lo que permite a `systemd` saber qué necesita iniciar primero y qué puede esperar.

#### Proceso de Arranque
1. Tras el inicio del kernel, el proceso `init` se destaca nuevamente, pero en este caso el ejecutable es `/bin/systemd`.
2. `systemd` determina el estado del sistema a través de sus archivos de configuración y comienza a iniciar sus unidades.
3. Se gestionan de manera paralela, lo que reduce significativamente el tiempo necesario para que el sistema esté completamente operativo.

### Uso del comando wall

El comando wall se puede utilizar para enviar un mensaje a todas las terminales antes de cambiar de nivel de ejecución, apagar o reiniciar el sistema. 

    # wall “Pasaremos a modo monousuario para realizar 
    mantenimiento del sistema. Por favor, desconectarse.” 

Es posible restringir el mensaje a un grupo determinado del sistema. En este ejemplo, solamente los usuarios del grupo desarrolladores verán el mensaje:

    # wall -g desarrolladores “Pasaremos a modo monousuario para realizar mantenimiento del sistema. Por favor, desconectarse.”

### Uso del comando shutdown

Con el comando shutdown, podremos planear nuestra tarea de apagado o reinicio de sistema.

Sintaxis

    # shutdown [opciones] tiempo [mensaje de alerta]

Opciones:

    -f  Inicio rápido: omite el chequeo del disco al reiniciar.
    -h  Apaga el equipo después de detener todo.
    -k  Mensaje de alerta sin realizar el evento de apagado o de reiniciado.
    -r  Reinicia después de detener todo.
    -F  Fuerza al chequeo de discos después del reinicio.
    -c  Cancelar shutdown

Reiniciar el equipo en 60 segundos y enviar aviso.

    # shutdown -r 60 “salir del sistema”

Reiniciar el equipo ya mismo (ojo, usarlo si no hay
nadie conectado):

    # shutdown -r now

### El botón de Power

El hardware actual en su gran mayoría soporta el apagado de un equipo mediante el botón de Power. Esto se puede lograr gracias al paquete acpid que proporciona el servicio del mismo nombre

**Directorios `/sys`, `/proc` y  `/dev` **

### Directorio `/dev`

#### Dispositivos de caracteres
Estos dispositivos procesan la información carácter por carácter.

    - /dev/tty        Terminales.
    - /dev/console    Terminal.
    - /dev/input      Dispositivos de entrada (mouse).
    - /dev/raw        Permite acceso directo a dispositivos de IO.
    - /dev/pts        Pseudoterminal.
    - /dev/lp         Impresora

#### Dispositivos de bloques
Estos dispositivos procesan la información usando bloques y buffers.

    - /dev/ramN           Disco en RAM (N es el número de disco).
    - /dev/hd[a-z]        Disco IDE.
    - /dev/sd[a-z]        Disco SATA/SCSI/USB.
    - /dev/mapper/        Mapea dispositivos en uno virtual (LVM y otras  tecnologías).

### Directorio `/proc`

El kernel de Linux tiene dos funciones principales:
● Controlar el acceso a los dispositivos físicos del ordenador.
● Establecer cuándo y cómo los procesos interactúan con estos dispositivos. 
El directorio / proc / contiene una jerarquía de archivos especiales que representan el estado actual del kernel – permitiendo a las aplicaciones y usuarios mirar detenidamente en la vista del kernel del sistema.

Dentro del directorio `proc`, se puede encontrar una gran cantidad de información que detalla el hardware del sistema y cualquier proceso en ejecución. Además, algunos de los archivos dentro del árbol de directorios `proc` pueden ser manipulados por los usuarios y las aplicaciones comunican los cambios de configuración al kernel

El núcleo de Linux agrupa la información relacionada a la asignación de recursos en el directorio /proc.
Los archivos relevantes de esta carpeta son los siguientes:
```shell
- /proc/dma           Archivo que contiene el histórico DMA del equipo.
- /proc/interrupts    Archivo que contiene el histórico IRQ del equipo.
- /proc/ioports       Archivo que contiene el histórico Entrada/Salida del equipo.
- /proc/bus/pci       La asignación de recursos puede ser consultada con comandos como lspci o dmesg.
```

Nota: En distribuciones que ya no usan más el directorio /proc para estos dispositivos se puede instalar el paquete lssci y usar la herramienta del mismo nombre.


#### Dispositivos SCSI

    root@foo:/home/ubuntu# cat /proc/scsi/scsi
    Attached devices:
    Host: scsi0 Channel: 00 Id: 00 Lun: 00
    Vendor: ATA Model: HITACHI HTS72503 Rev: PC3Z
    Type: Direct-Access ANSI SCSI revision: 05
    Host: scsi1 Channel: 00 Id: 00 Lun: 00
    Vendor: Optiarc Model: DVD RW AD-7930H Rev: 1.D0
    Type: CD-ROM ANSI SCSI revision: 05
    Host: scsi7 Channel: 00 Id: 00 Lun: 00
    Vendor: WD Model: My Passport 070A Rev: 1032
    Type: Direct-Access ANSI SCSI revision: 02
    Host: scsi7 Channel: 00 Id: 00 Lun: 01
    Vendor: WD Model: Virtual CD 070A Rev: 1032
    Type: CD-ROM ANSI SCSI revision: 04
    Host: scsi7 Channel: 00 Id: 00 Lun: 02
    Vendor: WD Model: SES Device Rev: 1032
    Type: Enclosure ANSI SCSI revision: 04

#### Dispositivo USB

    root@foo:/home/ubuntu# cat /proc/scsi/usb-storage/7 
    Host scsi7: usb-storage
    Vendor: Western Digital
    Product: My Passport 070A
    Serial Number: 575844304342394830303435
    Protocol: Transparent SCSI
    Transport: Bulk
    Quirks: SANE_SENSE

#### Un poco de todo:

    root@foo:/home/ubuntu# cat /proc/scsi/sg/device_strs 
    ATA HITACHI HTS72503 PC3Z
    Optiarc DVD RW AD-7930H 1.D0
    WD My Passport 070A 1032
    WD Virtual CD 070A 1032
    WD SES Device 1032

#### Procesadores

    root@foo:/home/ubuntu# cat /proc/cpuinfo
    processor : 0
    vendor_id : GenuineIntel
    cpu family : 6
    model : 37
    model name : Intel(R) Core(TM) i5 CPU M 520 @ 2.40GHz
    stepping : 2
    cpu MHz : 1199.000
    cache size : 3072 KB
    physical id : 0
    siblings : 4
    core id : 0
    cpu cores : 2
    apicid : 0
    initial apicid : 0
    fpu : yes
    fpu_exception: yes
    cpuid level : 11
    wp : yes
    flags : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush dts acpi 
    mmx fxsr sse sse2 ss ht tm pbe syscall nx rdtscp lm constant_tsc arch_perfmon pebs bts rep_good nopl 
    xtopology nonstop_tsc aperfmperf pni pclmulqdq dtes64 monitor ds_cpl vmx smx est tm2 ssse3 cx16 xtpr pdcm 
    sse4_1 sse4_2 popcnt aes lahf_lm ida arat dts tpr_shadow vnmi flexpriority ept vpid
    bogomips : 4788.11
    clflush size : 64
    cache_alignment : 64
    address sizes: 36 bits physical, 48 bits virtual
    power management:

#### Interrupciones

    root@foo:/home/ubuntu# cat /proc/interrupts 
    CPU0 CPU1 CPU2 CPU3 
    0: 636 72 10 3 IO-APIC-edge timer
    1: 382 290 60 51 IO-APIC-edge i8042
    8: 17 42 32 21 IO-APIC-edge rtc0
    9: 2850519 1206078 6738 6524 IO-APIC-fasteoi acpi
    12: 287326 300885 286864 252200 IO-APIC-edge i8042
    16: 0 0 0 0 IO-APIC-fasteoi mmc0
    17: 11 9 7 7 IO-APIC-fasteoi 
    19: 443317 384748 4966 2223 IO-APIC-fasteoi ehci_hcd:usb2, ips
    23: 573117 8424 448034 229068 IO-APIC-fasteoi ehci_hcd:usb1
    40: 1947952 355339 2232 2222 PCI-MSI-edge ahci
    41: 5960060 222011 5813 4436 PCI-MSI-edge 
    42: 214062 433404 305030 271928 PCI-MSI-edge em1
    43: 6925 11652 8915 8042 PCI-MSI-edge snd_hda_intel
    44: 1053 40 1 0 PCI-MSI-edge firewire_ohci
    NMI: 14734 102240 102151 102148 Non-maskable interrupts
    LOC: 205959095 204173038 208079406 205355370 Local timer interrupts
    SPU: 0 0 0 0 Spurious interrupts
    PMI: 14734 102240 102151 102148 Performance monitoring interrupts
    IWI: 0 0 0 0 IRQ work interrupts
    RES: 6553839 6862024 6213795 6119409 Rescheduling interrupts
    CAL: 79345 96195 107918 94748 Function call interrupts
    TLB: 865267 886619 951455 897022 TLB shootdowns
    TRM: 0 0 0 0 Thermal event interrupts
    THR: 0 0 0 0 Threshold APIC interrupts
    MCE: 0 0 0 0 Machine check exceptions
    MCP: 601 599 599 599 Machine check polls
    ERR: 0
    MIS: 0

#### Dma

    root@foo:/home/ubuntu# cat /proc/dma 
    4: cascade

#### Memoria

    root@foo:/home/ubuntu# cat /proc/meminfo 
    MemTotal: 7971292 kB
    MemFree: 199292 kB
    Buffers: 204932 kB
    Cached: 2354312 kB
    SwapCached: 2004 kB
    Active: 3530724 kB
    Inactive: 1858672 kB
    Active(anon): 2336544 kB
    Inactive(anon): 780000 kB
    Active(file): 1194180 kB
    Inactive(file): 1078672 kB
    Unevictable: 20 kB
    Mlocked: 20 kB
    SwapTotal: 4095996 kB
    SwapFree: 4057368 kB
    Dirty: 704 kB
    Writeback: 0 kB
    AnonPages: 2828328 kB
    Mapped: 2108892 kB
    Shmem: 286392 kB
    Slab: 462600 kB
    SReclaimable: 398168 kB
    SUnreclaim: 64432 kB
    KernelStack: 6240 kB
    PageTables: 61664 kB
    NFS_Unstable: 0 kB
    Bounce: 0 kB
    WritebackTmp: 0 kB
    CommitLimit: 8081640 kB
    Committed_AS: 7055916 kB
    VmallocTotal: 34359738367 kB
    VmallocUsed: 589656 kB
    VmallocChunk: 34359067120 kB
    HardwareCorrupted: 0 kB
    AnonHugePages: 886784 kB
    HugePages_Total: 0
    HugePages_Free: 0
    HugePages_Rsvd: 0
    HugePages_Surp: 0
    Hugepagesize: 2048 kB
    DirectMap4k: 12288 kB
    DirectMap2M: 8237056 kB

#### Estadísticas de disco

    root@foo:/home/ubuntu# cat /proc/diskstats
    1 0 ram0 0 0 0 0 0 0 0 0 0 0 0
    1 1 ram1 0 0 0 0 0 0 0 0 0 0 0
    1 2 ram2 0 0 0 0 0 0 0 0 0 0 0
    1 3 ram3 0 0 0 0 0 0 0 0 0 0 0
    8 0 sda 726060 44424 22017281 6018144 937356 113957 34200861 35812071 0 4276213 41832765
    8 1 sda1 722 1167 10734 2024 11 9 52 122 0 2025 2145
    8 2 sda2 725165 43246 22005075 6012069 889836 113948 34200809 34177257 0 2834939 40190903
    11 0 sr0 65 0 500 1926 0 0 0 0 0 1925 1925
    253 0 dm-0 769318 0 22003547 8706032 1051845 0 34200809 158419832 0 4396589 167187026
    8 16 sdb 623 455 17109 9156 44 0 8204 7109 0 7858 16265
    8 17 sdb1 87 91 1424 1169 0 0 0 0 0 1122 1169
    8 18 sdb2 85 91 1408 985 0 0 0 0 0 952 985
    11 1 sr1 28 0 224 175 0 0 0 0 0 175 175
    (...salida cortada...)

#### IOPorts

    root@foo:/home/ubuntu# cat /proc/ioports 
    0000-0cf7 : PCI Bus 0000:00
    0000-001f : dma1
    0020-0021 : pic1
    0040-0043 : timer0
    0050-0053 : timer1
    0060-0060 : keyboard
    0062-0062 : EC data
    0064-0064 : keyboard
    0066-0066 : EC cmd
    0070-0071 : rtc0
    0080-008f : dma page reg
    00a0-00a1 : pic2
    00c0-00df : dma2
    00f0-00ff : fpu
    03c0-03df : vga+
    0800-080f : pnp 00:03
    0cf8-0cff : PCI conf1
    0d00-ffff : PCI Bus 0000:00
    1000-107f : pnp 00:03
    1000-1003 : ACPI PM1a_EVT_BLK
    1004-1005 : ACPI PM1a_CNT_BLK
    1008-100b : ACPI PM_TMR
    1010-1015 : ACPI CPU throttle
    1020-102f : ACPI GPE0_BLK
    1030-1033 : iTCO_wdt
    1050-1050 : ACPI PM2_CNT_BLK
    1060-107f : iTCO_wdt
    1180-11ff : pnp 00:03
    15e0-15ef : pnp 00:03
    1600-1641 : pnp 00:03
    (...salida cortada…)


#### Sistemas de Archivos

    root@foo:/home/ubuntu# cat /proc/filesystems 
    nodev sysfs
    nodev rootfs
    nodev bdev
    nodev proc
    nodev cgroup
    nodev cpuset
    nodev tmpfs
    nodev devtmpfs
    nodev binfmt_misc
    nodev debugfs
    nodev securityfs
    nodev sockfs
    nodev usbfs
    nodev pipefs
    nodev anon_inodefs
    nodev devpts
    ext3
    ext4
    nodev ramfs
    nodev hugetlbfs
    iso9660
    nodev autofs
    nodev pstore
    nodev mqueue
    nodev fuse
    fuseblk
    nodev fusectl
    xfs


#### Procesos

    root@foo:/home/ubuntu# ls -d /proc/[0-9]*
    /proc/1 /proc/13481 /proc/17301 /proc/20891 /proc/23308 /proc/26 /proc/27417 /proc/27541 
    /proc/28 /proc/403 /proc/68
    /proc/1034 /proc/13484 /proc/1731 /proc/21 /proc/23310 /proc/26791 /proc/27421 /proc/27559 
    /proc/28011 /proc/405 /proc/69
    /proc/1041 /proc/13495 /proc/17315 /proc/21128 /proc/23450 /proc/268 /proc/27422 /proc/27562 
    /proc/2805 /proc/406 /proc/6947
    /proc/1049 /proc/13516 /proc/17320 /proc/2138 /proc/23475 /proc/27 /proc/27424 /proc/27563 
    /proc/28179 /proc/4356 /proc/7
    /proc/1050 /proc/13533 /proc/1737 /proc/21717 /proc/2353 /proc/27087 /proc/27425 /proc/27564 
    /proc/28199 /proc/4357 /proc/70
    (...salida cortada…)


### Directorio `/sys`

A partir de la versión 2.6 del kernel Linux comenzó a utilizarse un nuevo sistema de archivos llamado sysfs. Este sistema de archivos es virtual y se monta sobre el directorio `/sys`.
Es un directorio con información específica de hardware. El servicio haldaemon extrae datos de `/sys` para ser utilizado por aplicaciones de escritorio.

```
Tener en cuenta que el servicio haldaemon ha sido 
reemplazado por udisks y upower.
```

#### Servicio udev

El servicio udev sirve para mejorar el uso del directorio `/dev` (que contiene los archivos que son nodos de dispositivos en general) y para manejar el comportamiento del sistema frente a eventos relacionados con dispositivos de bloque, red, usb, etc. También se basa en la información que extrae del directorio `/sys` para trabajar. El comportamiento mencionado por parte de udev se basa en archivos de reglas que están en el directorio `/usr/lib/udev/rules.d`, como podemos ver en los siguientes slides.

    root@foo:/home/ubuntu# ls /usr/lib/udev/rules.d/ 
    39-usbmuxd.rules 60-serial.rules 77-mm-dell-port-types.rules 
    80-libinput-device-groups.rules
    40-usb-media-players.rules 64-btrfs-dm.rules 77-mm-ericsson-mbm.rules 
    80-mm-candidate.rules
    40-usb_modeswitch.rules 64-btrfs.rules 77-mm-fibocom-port-types.rules 
    80-net-setup-link.rules
    50-firmware.rules 64-xorg-xkb.rules 77-mm-haier-port-types.rules 
    80-udisks2.rules
    50-udev-default.rules 65-libwacom.rules 77-mm-huawei-net-port-types.rules 
    84-nm-drivers.rules
    55-dm.rules 66-azure-ephemeral.rules 77-mm-longcheer-port-types.rules 
    85-hdparm.rules
    56-lvm.rules 69-cd-sensors.rules 77-mm-mtk-port-types.rules 
    85-hwclock.rules
    60-block.rules 69-libmtp.rules 77-mm-nokia-port-types.rules 
    85-nm-unmanaged.rules
    60-bridge-network-interface.rules 69-lvm-metad.rules 77-mm-pcmcia-device-blacklist.rules 
    85-regulatory.rules
    60-cdrom_id.rules 69-wacom.rules 77-mm-qdl-device-blacklist.rules 
    89-alsa-ucm.rules
    60-crda.rules 70-joystick.rules 77-mm-sierra.rules 
    90-alsa-restore.rules
    60-drm.rules 70-mouse.rules 77-mm-simtech-port-types.rules 
    90-console-setup.rules 
    60-evdev.rules 70-power-switch.rules 77-mm-telit-port-types.rules 
    90-libinput-model-quirks.rules
    60-input-id.rules 70-touchpad.rules 77-mm-ublox-port-types.rules 
    90-nm-thunderbolt.rules
    60-libgphoto2-6.rules 70-u2f.rules 77-mm-usb-device-blacklist.rules 
    90-pulseaudio.rules
    60-libsane.rules 70-uaccess.rules 77-mm-usb-serial-adapters-greylist.rules 
    95-cd-devices.rules
    60-persistent-alsa.rules 71-seat.rules 77-mm-x22x-port-types.rules 
    95-dm-notify.rules
    60-persistent-input.rules 73-seat-late.rules 77-mm-zte-port-types.rules 
    95-upower-csr.rules
    60-persistent-storage-dm.rules 73-special-net-names.rules 78-sound-card.rules 
    95-upower-hid.rules
    60-persistent-storage.rules 73-usb-net-by-mac.rules 80-debian-compat.rules 
    95-upower-wup.rules
    60-persistent-storage-tape.rules 75-net-description.rules 80-drivers.rules 
    97-hid2hci.rules
    60-persistent-v4l.rules 75-probe_mtd.rules 80-ifupdown.rules 
    99-systemd.rules
    60-sensor.rules 77-mm-cinterion-port-types.rules 80-iio-sensor-proxy.rules


Estos archivos no hay que tocarlos sino los que están en el directorio `/etc/udev/rules.d`:
En el caso que se necesite modificar alguna regla:
Lo correcto es copiar el archivo a este último directorio y hacer los cambios pertinentes.

```shell
root@foo:/home/ubuntu# ls /etc/udev/rules.d/
60-cdrom_id.rules 60-vboxadd.rules
```

#### Monitoreo de los eventos de udev

Escucha los eventos de udev del kernel y los que se envían mediante una regla de udev, mostrando la ruta del dispositivo respecto al directorio sys. Se puede usar para analizar el tiempo que le toma cada evento.
La siguiente es una muestra recortada al insertar un CDROM:

```shell
root@foo:/home/ubuntu# udevadm monitor
monitor will print the received events for:
UDEV - the event which udev sends out after rule processing
KERNEL - the kernel uevent
KERNEL[17145.085584] change /devices/pci0000:00/0000:00:01.1/ata2/host1/target1:0:0/1:0:0:0/block/sr0 (block)
UDEV [17145.191044] change /devices/pci0000:00/0000:00:01.1/ata2/host1/target1:0:0/1:0:0:0/block/sr0 (block)

```

#### Simular el agregado de un dispositivo

Con el siguiente comando se puede simular el agregado de un CDROM (la salida está abreviada):

```shell
root@foo:/home/ubuntu# udevadm test /sys/block/sr0
DEVPATH=/devices/pci0000:00/0000:00:01.1/ata2/host1/target1:0:0/1:0:0:0/block/sr0
DEVNAME=/dev/sr0
DEVTYPE=disk
MAJOR=11
MINOR=0
ACTION=add
SUBSYSTEM=block
ID_CDROM=1
SYSTEMD_MOUNT_DEVICE_BOUND=1
ID_CDROM_DVD=1
ID_CDROM_MRW=1
ID_CDROM_MRW_W=1
DEVLINKS=/dev/dvd /dev/cdrom /dev/disk/by-path/pci-0000:00:01.1-ata-2 
/dev/disk/by-id/ata-VBOX_CD-ROM_VB2-01700376
```

#### Directorios Sysfs

Sysfs es un sistema de archivos virtual basado inicialmente en `ramfs` y que está implementado en el kernel de `Linux 2.6`. El núcleo exporta hacia el espacio de usuario información sobre los dispositivos y controladores. Esta información se organiza dentro de `/sys`, agrupada en directorios de forma jerárquica.

|Directorio | Descripción |
|--|---|
|/sys/block | Un directorio para cada dispositivo de tipo bloque.|
|/sys/bus | Contiene un directorio por cada tipo de bus físico del sistema.|
|/sys/class | Directorios de los dispositivos organizados en clases por el kernel. Una clase de dispositivo describe un tipo de dispositivo funcional.|
|/sys/devices | Muestra los dispositivos físicos que han sido encontrados por los diferentes tipos de bus registrados en el kernel.|
|/sys/firmware | Contiene interfaces para manipular objetos y atributos específicos del firmware.|
|/sys/modules | Un directorio por cada módulo cargado por el kernel.|
|/sys/fs | Un directorio por cada sistema de archivos.|
|/sys/power | Contiene información del sistema de alimentación eléctrica|

#### D-bus

D-bus es un sistema de comunicación entre aplicaciones mediante mensajes. Para ello, usa un demonio ejecutable que funciona como bus del sistema al que se conectan las aplicaciones.
Dispone de dos funcionalidades:
● Comunicación entre las aplicaciones de una misma sesión de usuario.
● Comunicación entre el sistema operativo y la sesión de escritorio.
El servicio `udev` entonces, se encarga de detectar y hacer determinadas acciones con dispositivos, los cuales se ven reflejados en `/sys`. 
El servicio D-bus al tener capacidad para enviar mensajes entre aplicaciones puede, entre otras cosas, realizar una acción determinada en un entorno de escritorio, relacionada con un dispositivo detectado.


### Anexos Hardware: 

### Dispositivos hotplug y coldplug

Hotplug (conexión en caliente) es la capacidad que tienen algunos dispositivos para conectarse y desconectarse del equipo sin necesidad de reiniciar el sistema. Estos dispositivos pueden configurarse de forma automática y funcionar correctamente.Entre los tipos de conexión que permiten hotplug se encuentran:
● USB.
● Firewire.
● SATA.
● SAS.

### HAL
HAL es una capa de abstracción del hardware (como ya se indicó, este software fue quitado recientemente y reemplazado por otros), es decir, una capa situada entre el software y el hardware. Las aplicaciones no acceden directamente al hardware sino que lo hacen a través de HAL. Éste aglutina toda la información de los dispositivos que proviene de diferentes fuentes y proporciona un acceso homogéneo a las aplicaciones. 
Cuando un dispositivo es añadido, se genera una señal a través del bus del sistema de mensajes con todos los detalles del dispositivo añadido.
En la actualidad, HAL ha sido reemplazada por udev.

### Arquitectura del Sistema
Debemos empezar a mencionar distintos componentes dentro de un equipo, como periféricos de entrada y salida, chipsets integrados a nuestro equipo y todo el hardware que involucra una PC.
Para ver esta información, existe un programa llamado BIOS (Basic Input Output System) que nos mostrará un listado y configuración referente a cada componente de nuestro equipo, con una serie de parámetros específicos para cada uno. Toda esta información puede ser manipulada allí con diferentes tipos de opciones. 

Una de las opciones que más nos va importar es la referida a los `IRQ`, `I/O`, `fecha`, `puertos paralelos` y `serie`, entre otros. En `LPIC-1` se evaluará tener una idea de cómo funciona el BIOS, ya que las configuraciones varían según los fabricantes; por eso necesitamos saber cómo arrancar el equipo desde un CD, red, diOtro punto importante es saber las diferencias 
entre IDE y SCSI, tener una noción de qué son 
estas tecnologías, tanto para un disco rígido 
como para un CD/DVD.
Para ir cerrando un poco toda esta idea, veremos 
cómo nuestro sistema operativo Linux interactúa 
con nuestro hardware desde un nivel lógico
observando determinados archivos.
sco interno, puerto serial o un USB y realizar distintas configuraciones de los recursos mediante la BIOS.

Otro punto importante es saber las diferencias entre IDE y SCSI, tener una noción de qué son estas tecnologías, tanto para un disco rígido como para un CD/DVD.
Para ir cerrando un poco toda esta idea, veremos cómo nuestro sistema operativo Linux interactúa con nuestro hardware desde un nivel lógico observando determinados archivos.

### Asignación de recursos

Para permitir que los periféricos y dispositivos del equipo se comuniquen directamente con los recursos del sistema, en particular con el CPU (Central Processing Unit), el sistema asigna recursos tales como líneas y canales de comunicación para cada dispositivo.
Muy particularmente, estos recursos son conocidos como solicitudes de interrupción (IRQ), direcciones de entrada/salida y accesos directos a memoria (DMA). 

### Solicitudes de interrupción (IRQ)
Antes de explicar qué es una solicitud de interrupción, debemos conocer cuál es la función de un procesador. El procesador es el encargado de procesar y administrar los datos y peticiones que le llegan. Sin embargo, un solo procesador no es capaz de procesar simultáneamente varias peticiones, por lo que solo atiende de a una a las peticiones que a él llegan, es aquí donde las solicitudes de interrupción empiezan a jugar un papel importante.
Cuando un periférico desea acceder a un recurso, envía un pedido de interrupción al procesador para llamar su atención. Los periféricos cuentan con un número de interrupción que se denomina IRQ (Peticiones de Interrupción). Es como si cada periférico tirara de un “hilo” que está atado a una campana para indicarle al equipo que desea que le preste atención. Este “hilo” es, de hecho, una línea física que conecta cada ranura de expansión así como cada interfaz entrada/salida a la motherboard.

Ejemplo: supongamos que un programa 1 está siendo atendido por el procesador, pero súbitamente una solicitud de interrupción llega al procesador. Es entonces cuando el programa 1 llega a ser suspendido momentáneamente por un programa 2. Este programa 2 es ahora atendido por el procesador y hasta que termine, el programa 1 interrumpido, puede continuar ejecutándose. 

### Direcciones de entrada/salida
Las direcciones de entrada/salida representan direcciones específicas en la memoria del sistema. Dichas direcciones son asignadas por el CPU a cada uno de los dispositivos del hardware del sistema para que puedan escribir y leer datos sobre la misma.

### Accesos Directos a Memoria (DMA)
El Acceso Directo a Memoria (DMA) permite a cierto tipo de componentes del equipo acceder a la memoria del sistema para leer o escribir, independientemente del procesador.
Muchos sistemas hardware utilizan DMA, incluyendo controladores de unidades de disco, tarjetas gráficas y tarjetas de sonido. DMA es una característica esencial en todos los equipos modernos, ya que permite a dispositivos de diferentes velocidades comunicarse sin someter al procesador a una carga masiva de interrupciones. 

Una transferencia DMA consiste, de manera principal, en copiar un bloque de memoria de un dispositivo a otro. En lugar de que el procesador inicie la transferencia, la transferencia se lleva a cabo por el controlador DMA. 
Un ejemplo típico es mover un bloque de memoria desde una memoria externa a una interna más rápida. Tal operación no ocupa el procesador y como resultado puede ser planificado para efectuar otras tareas. 

Las transferencias DMA son esenciales para aumentar el rendimiento de aplicaciones que requieran muchos recursos.
Regularmente, un equipo consta de 8 canales DMA. Los primeros cuatro canales DMA poseen un ancho de banda de 8 bits, mientras que los DMA 4 a 7 poseen un ancho de banda de 16 bits. 

```
Cómo suelen asignarse los canales DMA:
● DMA0: libre.
● DMA1: (tarjeta de sonido)/libre.
● DMA2: controlador de disquete.
● DMA3: puerto paralelo (puerto de la 
impresora).
● DMA4: controlador de acceso directo 
a memoria (conectado a DMA0).
● DMA1: (tarjeta de sonido)/libre.
● DMA6: (SCSI)/libre.
● DMA7: disponible.
```
### Recursos

*En libros*
● “LPI Linux Certification in a Nutshell”, Third Edition, June 2010, Adam Haeder, Stephen Addison Schneiter, Bruno Gomes Pessanha & James Stanger.
● “LPIC-1: Linux Professional Institute Certification Study Guide: (Exams 101 and 102)”, 2nd Edition, February 2009, Roderick W. Smith.

_En Internet_
● [Linux para todos](http://www.linuxparatodos.net//web/comunidad/base-de-conocimiento/-/wiki/Base%20de%20Conocimiento/Certificaci%C3%B3n+LPI+101#section-Certificaci_C3_B3n+LPI+101-ManteniendoLaIntegridadDeLosSistemasDeFicheros).
● [Directorios Sys Proc](http://softgpl.info/?q=101-1-configuracion-del-hardware-pag-1).


### Módulos del Kernel

Los kernel modernos están modularizados; estos módulos están compilados en el kernel y solo son cargados cuando se los necesita. 
Estos módulos están separados del kernel y son cargados o borrados por el superusuario, dependiendo de la situación que esté expuesta. Sin embargo, hay ciertos parámetros en el archivo del gestor de arranque y de la línea comandos del kernel que afectan a este, dado que estos no controlan módulos del kernel.
Una vez conectado físicamente el dispositivo, el kernel intentará cargar el módulo (comúnmente lo conocemos como `driver`) necesario para que el dispositivo pueda funcionar.
Para comprobar que se ha instalado correctamente o para instalar el módulo si disponemos de un driver precompilado, podemos utilizar las siguientes herramientas de gestión de módulos:

#### Comando lsmod
**lsmod**
Muestra los módulos que se han cargado en memoria durante el proceso de arranque.

#### Comando modprobe
**modprobe [módulo]** 
Carga el módulo definido. Si depende de otros módulos los cargará primero.

#### Comando rmmod
**rmmod [módulo]**
 Descarga el módulo de la memoria.

#### Comando modinfo
**modinfo [módulo]**
Proporciona información del módulo

#### Comando Blacklist
El comando `blacklist` sirve para evitar que se carguen ciertos módulos.

#### Comando `depmod -a`
El archivo `modules.dep` es el árbol de módulos que arma el sistema cuando arranca. Para regenerarlo, hay que tipear `depmod -a`.

#### Directorios `/lib/modules/(versión_del_kernel)` `/etc/modules.conf` y `/etc/modules`
Los módulos compilados se encuentran en `/lib/modules/(versión_del_kernel)`. Para configurar sus parámetros, utilizaremos el archivo `/etc/modules.conf` o `/etc/modules`.

Para que un módulo se cargue siempre en memoria, puede ser necesario añadir un archivo al directorio `/etc/modprobe.d` durante el proceso de arranque. Dependiendo la distribución, podemos tener algo así:

    # ls -l /etc/modprobe.d/
    total 32
    -rw-r--r-- 1 root root 884 ago 22 12:18 blacklist.conf
    -rw-r--r-- 1 root root 140 nov 16 08:14 broadcom-wl-blacklist.conf
    -rw-r--r--. 1 root root 382 jul 21 16:24 dist-alsa.conf
    -rw-r--r--. 1 root root 5309 jul 21 16:24 dist.conf
    -rw-r--r--. 1 root root 473 jul 21 16:24 dist-oss.conf
    -rw-r--r--. 1 root root 30 feb 8 2011 openfwwf.conf
    -rw-r--r--. 1 root root 64 abr 20 2011 wireless.conf
        
Contenido del directorio `/lib/modules`:

    # ls -l
    total 12
    drwxr-xr-x. 2 root root 4096 oct 30 23:01 2.6.40.4-5.fc15.x86_64
    drwxr-xr-x 2 root root 4096 nov 18 05:05 2.6.40.6-0.fc15.x86_64
    drwxr-xr-x 7 root root 4096 nov 23 03:36 2.6.41.1-1.fc15.x86_64

Según el kernel que tengamos, tendremos los diferentes módulos. 
Contenido del directorio de la versión del kernel:

    # ls
    build   modules.alias       modules.builtin.bin      modules.devname        modules.isapnpmap   modules.order 
    modules.symbols         updates
    extra   modules.alias.bin   modules.ccwmap          modules.drm             modules.modesetting  modules.pcimap 
    modules.symbols.bin     vdso
    kernel  modules.block       modules.dep             modules.ieee1394map     modules.networking  modules.seriomap 
    modules.usbmap
    misc    modules.builtin     modules.dep.bin         modules.inputmap        modules.ofmap       modules.softdep     source


#### Comando modinfo

El comando modinfo muestra información acerca de los módulos.
El valor **`alias`** nos permitirá **llamar a un driver del sistema** de otra forma más significativa. El comando `modinfo` también muestra los parámetros que soporta un módulo.

Ejemplo:
Mostrar información del módulo **i915**:

    # modinfo i915
    filename: /lib/modules/2.6.41.1-1.fc15.x86_64/kernel/drivers/gpu/drm/i915/i915.ko
    license: GPL and additional rights
    description: Intel Graphics
    author: Tungsten Graphics, Inc.
    license: GPL and additional rights
    srcversion: 61F1EF8287C2CB6B830FE31
    alias: pci:v00008086d0000015Asv*sd*bc03sc*i*
    alias: pci:v00008086d00000106sv*sd*bc03sc*i*
    depends: drm,drm_kms_helper,i2c-core,video,i2c-algo-bit
    vermagic: 2.6.41.1-1.fc15.x86_64 SMP mod_unload 
    parm: modeset:Use kernel modesetting [KMS] (0=DRM_I915_KMS from .config, 1=on, -1=force vga console 
    preference [default]) (int)
    parm: fbpercrtc:int
    parm: panel_ignore_lid:Override lid status (0=autodetect [default], 1=lid open, -1=lid closed) (int)
    parm: powersave:Enable powersavings, fbc, downclocking, etc. (default: true) (int)
    parm: semaphores:Use semaphores for inter-ring sync (default: false) (int)
    parm: i915_enable_rc6:Enable power-saving render C-state 6 (default: true) (int)
    parm: i915_enable_fbc:Enable frame buffer compression for power savings (default: -1 (use per-chip 
    default)) (int)
    parm: lvds_downclock:Use panel (LVDS/eDP) downclocking for power savings (default: false) (int)
    parm: lvds_use_ssc:Use Spread Spectrum Clock with panels [LVDS/eDP] (default: true) (int)
    parm: vbt_sdvo_panel_type:Override selection of SDVO panel mode in the VBT (default: auto) (int)
    parm: reset:Attempt GPU resets (default: true) (bool)
    parm: enable_hangcheck:Periodically check GPU activity for detecting hangs. WARNING: Disabling this 
    can cause system wide hangs. (default: true) (bool)
    (...salida cortada...)


#### Comando lsmod

El comando `lsmod` muestra información referida a los módulos cargados (en `/proc/modules`):
En la salida se ve el **nombre del módulo**, **su peso** y **de quién depende**.

    # lsmod
    Module          Size        Used by
    nls_utf8        1389        1 
    xfs             695061      0 
    usb_storage     45982       0 
    uas             7775        0 
    r8712u          146231      0 
    snd_hda_codec_hdmi 23548    1 
    snd_hda_codec_conexant 55283 1 
    snd_hda_intel   24072       4 
    snd_hda_codec   85181       3 snd_hda_codec_hdmi,snd_hda_codec_conexant,snd_hda_intel
    snd_hwdep       6264        1 snd_hda_codec
    snd_seq         52186       0 
    uvcvideo        56989       0 
    snd_seq_device  5941        1 snd_seq 
    i2c_i801        9237        0 
    snd_pcm         78498       3 snd_hda_codec_hdmi,snd_hda_intel,snd_hda_codec
    e1000e          174892      0 
    rfkill          16336       4 bluetooth,thinkpad_acpi
    i2c_algo_bit    4958        1 i915
    i2c_core        25728       6 i2c_i801,videodev,i915,drm_kms_helper,drm,i2c_algo_bit
    video           12388       1 i915
    (...salida cortada…)


#### Insmod & rmmod

El comando **`insmod` permite cargar módulos, con la diferencia de que no resuelve dependencias**, y de que *hay que especificar el path* hasta el módulo.

##### Cargar módulo:

    # insmod /lib/modules/2.6.41.1-1.fc15.x86_64/kernel/fs/fat/vfat.ko
    insmod: error inserting 'vfat.ko': -1 Unknown symbol in module

##### Muestra las dependencias:

    # modprobe --show-depends vfat**

##### Cargar el módulo necesario y cargar nuevamente el que falló:

    # insmod /lib/modules/2.6.41.1-1.fc15.x86_64/kernel/fs/fat/fat.ko 
    # insmod /lib/modules/2.6.41.1-1.fc15.x86_64/kernel/fs/fat/vfat.ko 

##### Remover un módulo:

    # rmmod fat
    ERROR: Module fat is in use by vfat


##### Sacar módulo dependiente:

    # rmmod vfat

##### Listar los módulos que contengan "fat":

    # lsmod | grep fat
    fat 44588 0 

##### Sacar el módulo que antes falló:

    # rmmod fat
    # lsmod |grep fat

#### Comando Modprobe y archivos `/etc/modprobe.conf`, `/etc/modprobe.d/modprobe.conf` o `/etc/modprobe.d/aliases`

El comando **modprobe permite cargar un módulo con el nombre**, *sin necesidad de especificar el path completo, ni los módulos dependientes*, dado que utiliza el contenido del `modules.dep` para resolver las dependencias.

    # modprobe --show-depends fat
    insmod /lib/modules/2.6.18-194.el5/kernel/fs/fat/fat.ko
    # modprobe --show-depends nfs
    insmod /lib/modules/2.6.18-194.el5/kernel/net/sunrpc/sunrpc.ko
    insmod /lib/modules/2.6.18-194.el5/kernel/fs/nfs_common/nfs_acl.ko
    insmod /lib/modules/2.6.18-194.el5/kernel/fs/fscache/fscache.ko
    insmod /lib/modules/2.6.18-194.el5/kernel/fs/lockd/lockd.ko
    insmod /lib/modules/2.6.18-194.el5/kernel/fs/nfs/nfs.ko

##### Muestra ubicación del módulo:

    # modprobe -l nfs
    /lib/modules/2.6.18-194.el5/kernel/fs/nfs/nfs.ko

Similar a rmmod:

    # modprobe -rav nfs
    rmmod /lib/modules/2.6.18-194.el5/kernel/fs/nfs/nfs.ko
    rmmod /lib/modules/2.6.18-194.el5/kernel/fs/lockd/lockd.ko
    rmmod /lib/modules/2.6.18-194.el5/kernel/fs/fscache/fscache.ko
    rmmod /lib/modules/2.6.18-194.el5/kernel/fs/nfs_common/nfs_acl.ko
    remove { /bin/umount /var/lib/nfs/rpc_pipefs > /dev/null 2>&1 || :; } ; /sbin/modprobe -r --ignore-remove sunrpc
    rmmod /lib/modules/2.6.18-194.el5/kernel/net/sunrpc/sunrpc.ko

Para enviarle parámetros a un módulo del kernel, tienen que ser insertados en un archivo de configuración específico `/etc/modprobe.conf`, `/etc/modprobe.d/modprobe.conf` o `/etc/modprobe.d/aliases`.
Ahora encontraremos los distintos parámetros que puede necesitar un módulo (dirección I/O, interrupción, canal DMA, etc.). Si quisiéramos cambiar algo, porque es requerido por el hardware, tendríamos que especificarlo ahí.

### Parámetros por línea de comando

#### Comando `modprobe` y otras formas de pasar Opciones al Kernel
Existen tres maneras de pasar opciones al Kernel y controlar su comportamiento:
1. Al construir y compilar el núcleo.
2. Cuando se inicia el Kernel, a través de los archivos de configuración, o desde la línea de comandos de LILO o GRUB.
3. Durante la ejecución, modificando los archivos de los directorios /proc y /sys.


##### Comando `modprobe` para pasar parámetros desde la línea de comandos a los módulos que no están integrados en el núcleo
También se pueden pasar parámetros desde la línea de comandos a los módulos que no están integrados en el núcleo. La sintaxis es la siguiente:

    #modprobe módulo parámetro

##### Comando `modprobe` para Cargar modulo usbcore con parámetro autosuspend habilitado:

    #modprobe usbcore autosuspend=1

Si el módulo está integrado, se pasa al kernel la opción:

    modulo.parámetro
    usbcore.autosuspend=1

#### Cambiar los parámetros de un módulo en tiempo de ejecución mediante el directorio `/sys`:

    # echo -n ${value} > /sys/module/${modulename}/parameters/${parm}.
    # echo -n 1 > /sys/module/usbcore/parameters/autosuspend

### Obtener información del hardware del equipo

#### Firmware
En muchas arquitecturas de computadoras la herramienta de detección y configuración de hardware es el propio firmware que viene embebido en la placa madre. Tanto con BIOS como con hardware es posible habilitar y deshabilitar periféricos y unidades de almacenamiento.

#### Comando `update-pciids`
Con el comando `update-pciids` se descarga un listado actualizado con los ID de los distintos dispositivos. 

#### Comando lspci
El comando lspci nos imprime información detallada sobre los buses y dispositivos PCI conectados al equipo. Así mismo, nos brinda información detallada sobre los IRQ y direcciones de Entrada/Salida asociados a los dispositivos PCI encontrados. Para ello, solo se tiene que agregar el parámetro -v al comando. 
Veamos un ejemplo a continuación:

    # lspci
    00:00.0 Host bridge: Intel Corporation Core Processor DRAM Controller (rev 02)
    00:02.0 VGA compatible controller: Intel Corporation Core Processor Integrated Graphics Controller (rev 02)
    00:16.0 Communication controller: Intel Corporation 5 Series/3400 Series Chipset HECI Controller (rev 06)
    00:16.3 Serial controller: Intel Corporation 5 Series/3400 Series Chipset KT Controller (rev 06)
    00:19.0 Ethernet controller: Intel Corporation 82577LM Gigabit Network Connection (rev 06)
    00:1a.0 USB Controller: Intel Corporation 5 Series/3400 Series Chipset USB2 Enhanced Host Controller (rev 06)
    00:1b.0 Audio device: Intel Corporation 5 Series/3400 Series Chipset High Definition Audio (rev 06)
    00:1c.0 PCI bridge: Intel Corporation 5 Series/3400 Series Chipset PCI Express Root Port 1 (rev 06)
    00:1d.0 USB Controller: Intel Corporation 5 Series/3400 Series Chipset USB2 Enhanced Host Controller (rev 06)
    00:1e.0 PCI bridge: Intel Corporation 82801 Mobile PCI Bridge (rev a6)
    00:1f.0 ISA bridge: Intel Corporation Mobile 5 Series Chipset LPC Interface Controller (rev 06)
    00:1f.2 SATA controller: Intel Corporation 5 Series/3400 Series Chipset 6 port SATA AHCI Controller (rev 06)
    00:1f.3 SMBus: Intel Corporation 5 Series/3400 Series Chipset SMBus Controller (rev 06)
    00:1f.6 Signal processing controller: Intel Corporation 5 Series/3400 Series Chipset Thermal Subsystem (rev 06)
    0d:00.0 SD Host controller: Ricoh Co Ltd MMC/SD Host Controller (rev 01)
    0d:00.1 System peripheral: Ricoh Co Ltd Memory Stick Host Controller (rev 01)
    ff:00.0 Host bridge: Intel Corporation Core Processor QuickPath Architecture Generic Non-core Registers (rev 02)
    (...salida cortada...)

En forma de árbol: 

    # lspci -t
    -+-[0000:ff]-+-00.0
     |           +-00.1
     |           +-02.0
     |           +-02.1
     |           +-02.2
     |           \-02.3
     \-[0000:00]-+-00.0
                 +-02.0
                 +-16.0
                 +-16.3
                 +-19.0
                 +-1a.0
                 +-1b.0
                 +-1c.0-[02]--
                 +-1c.3-[05-0c]--
                 +-1c.4-[0d]--+-00.0
                 |            +-00.1
                 |            \-00.3
                 +-1d.0
                 +-1e.0-[0e]--
                 +-1f.0
                 +-1f.2
                 +-1f.3
                 \-1f.6

Con la opción -tv ,obtenemos más información:

    # lspci -tv
    -+-[0000:ff]-+-00.0 Intel Corporation Core Processor QuickPath Architecture Generic Non-core Registers
     |           +-00.1 Intel Corporation Core Processor QuickPath Architecture System Address Decoder
     |           +-02.0 Intel Corporation Core Processor QPI Link 0
     |           +-02.1 Intel Corporation Core Processor QPI Physical 0
     |           +-02.2 Intel Corporation Core Processor Reserved
     |           \-02.3 Intel Corporation Core Processor Reserved
     \-[0000:00]-+-00.0 Intel Corporation Core Processor DRAM Controller
                 +-02.0 Intel Corporation Core Processor Integrated Graphics Controller
                 +-16.0 Intel Corporation 5 Series/3400 Series Chipset HECI Controller
                 +-16.3 Intel Corporation 5 Series/3400 Series Chipset KT Controller
                 +-19.0 Intel Corporation 82577LM Gigabit Network Connection
                 +-1a.0 Intel Corporation 5 Series/3400 Series Chipset USB2 Enhanced Host Controller
                 +-1b.0 Intel Corporation 5 Series/3400 Series Chipset High Definition Audio
                 +-1c.0-[02]--
                 +-1c.3-[05-0c]--
                 +-1c.4-[0d]--+-00.0 Ricoh Co Ltd MMC/SD Host Controller
                 |            +-00.1 Ricoh Co Ltd Memory Stick Host Controller
                 |            \-00.3 Ricoh Co Ltd FireWire Host Controller
                 +-1d.0 Intel Corporation 5 Series/3400 Series Chipset USB2 Enhanced Host Controller
                 +-1e.0-[0e]--
                 +-1f.0 Intel Corporation Mobile 5 Series Chipset LPC Interface Controller
                 +-1f.2 Intel Corporation 5 Series/3400 Series Chipset 6 port SATA AHCI Controller
                 +-1f.3 Intel Corporation 5 Series/3400 Series Chipset SMBus Controller
                 \-1f.6 Intel Corporation 5 Series/3400 Series Chipset Thermal Subsystem

#### Comando lshw

Es una potente herramienta de texto para extraer información sobre el hardware presente.

|Opciones | Descripción |
|-|-|
|-c clase | Muestra una clase determinada (memory, network, disk, cpu).
|-html | Exporta en formato html.
|-short | Modo resumido

##### lshw -short

    # lshw -short
    H/W  path       Device      Class       Description
    ========================================================
                                system      2522AR3 ()
    /0                          bus         2522AR3
    /0/0                        memory      128KiB BIOS
    /0/6                        processor   Intel(R) Core(TM) i5 CPU M 520 @ 2.40GHz
    /0/6/a                      memory      32KiB L1 cache
    /0/6/b                      memory      256KiB L2 cache
    /0/6/c                      memory      3MiB L3 cache
    /0/2a                       memory      8GiB System Memory
    /0/2a/0                     memory      4GiB SODIMM DDR3 Synchronous 1334 MHz (0,7 ns)
    /0/2a/1                     memory      4GiB SODIMM DDR3 Synchronous 1334 MHz (0,7 ns)
    (...salida cortada…)

##### lshw -c disk

    #lshw -c disk
        *-cdrom 
            description: DVD-RAM writer
            product: CDDVDW SH-224DB
            vendor: TSSTcorp
            physical id: 0
            bus info: scsi@0:0.0.0
            logical name: /dev/cdrom4
            logical name: /dev/cdrw4
            logical name: /dev/dvd4
            logical name: /dev/dvdrw4
            logical name: /dev/sr0
            version: SB01
            capabilities: removable audio cd-r cd-rw dvd dvd-r dvd-ram
            configuration: ansiversion=5 status=nodisc
        *-disk:0
            description: ATA Disk
            product: WDC WD10EZEX-21M
            vendor: Western Digital
            physical id: 1
            bus info: scsi@1:0.0.0
            logical name: /dev/sda
            version: 01.0
            serial: WCC3F2754535
            size: 931GiB (1TB)
            capabilities: partitioned partitioned:dos
            configuration: ansiversion=5 sectorsize=4096 signature=3afa0487
    (...salida cortada...)


#### Comando dmidecode

Otro comando interesante que nos devuelve toda la información del hardware es dmidecode:

|Opciones | Descripción |
|-|-|
|-t tipo | Tipo de dispositivo (0: BIOS, 4: Procesador, 5: Banco Memoria, 6: Memorias)

    # dmidecode -t 5
    # dmidecode 2.11
    SMBIOS 2.6 present.

    Handle 0x0007, DMI type 5, 20 bytes
    Memory Controller Information
        Error Detecting Method: None
        Error Correcting Capabilities:
            None
        Supported Interleave: One-way Interleave
        Current Interleave: One-way Interleave
        Maximum Memory Module Size: 4096 MB
        Maximum Total Memory Size: 8192 MB
        Supported Speeds:
            Other
        Supported Memory Types:
            DIMM
            SDRAM
        Memory Module Voltage: 2.9 V
        Associated Memory Slots: 2
            0x0008
            0x0009
        Enabled Error Correcting Capabilities:
            Unknown

### Dispositivos USB

Los dispositivos USB (Universal Serial Bus), permiten conectar diversos tipos de periféricos(teclados, discos rígidos, pen drives, cámaras, impresoras, etc).
Cuando se configura el núcleo, se ve una sección llamada USB support que contiene opciones USB. Los dispositivos USB son agregados al equipo en forma de árbol, identificados de forma unívoca por el sistema.

```
Existen 4 tipos de controladores USB:
● OHCI.
● UHCI.
● EHCI.
● XHCI.
```

El soporte para dispositivos USB está disponible desde la versión 2.2.7 del kernel de Linux, y, con ello, la liberación de 3 módulos o controladores para la misma.

|Controlador | Módulo kernel |
|-|-|
OHCI (Compaq) | usb-ohci.o
UHCI (Intel) | usb-uhci.o
EHCI (USB 2.0) | ehci-hdc.o

● Las controladoras OHCI y UHCI son USB 1.1, siendo capaces de transmitir como máximo 12Mbps.
● EHCI es USB 2.0 y puede transmitir, teóricamente, 480Mbps. Un dispositivo Usb 2.0 enchufado en uno Usb 1.1 únicamente será capaz de transmitir a un máximo de 12Mbps. 
● Los dispositivos XHCI pueden alcanzar un ancho de banda desde los 5 Gbps.

Existe una cantidad enorme de dispositivos USB:

| Dispositivos de Interfaz Humana (HID) | Dispositivos de Comunicación | Dispositivo de almacenamiento masivo | Audio |  IrDA | Impresoras |
|-|-|-|-|-|-|
|● Dispositivos de entrada (mouse, teclado, etc). | ● Modems. | ● Wifi. | ● Dispositivos de disco. | ● Lectores de tarjetas. | ● Placas de sonido. | ● Dispositivos infrarrojos. | ● Impresoras. | ● Cables Usb a puerto paralelo. |

Ejemplo: 
#### Comando `lsusb`
Listar dispositivos USB:

    # lsusb
    Bus 001 Device 010: ID 046d:c52e Logitech, Inc. 
    Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 002 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
    Bus 001 Device 002: ID 8087:0020 Intel Corp. Integrated Rate Matching Hub
    Bus 002 Device 002: ID 8087:0020 Intel Corp. Integrated Rate Matching Hub
    Bus 001 Device 004: ID 17ef:480f Lenovo Integrated Webcam [R5U877]
    Bus 002 Device 009: ID 1058:070a Western Digital Technologies, Inc. My Passport Essential SE

Listar en forma de árbol:

    # lsusb -t
    /: Bus 02.Port 1: Dev 1, Class=root_hub, Driver=ehci_hcd/3p, 480M
        |__ Port 1: Dev 2, If 0, Class=hub, Driver=hub/8p, 480M
            |__ Port 1: Dev 9, If 0, Class=stor., Driver=usb-storage, 480M
    /: Bus 01.Port 1: Dev 1, Class=root_hub, Driver=ehci_hcd/3p, 480M
        |__ Port 1: Dev 2, If 0, Class=hub, Driver=hub/6p, 480M
            |__ Port 2: Dev 10, If 0, Class=HID, Driver=usbhid, 12M
            |__ Port 2: Dev 10, If 1, Class=HID, Driver=usbhid, 12M
            |__ Port 6: Dev 4, If 0, Class='bInterfaceClass 0x0e not yet handled', Driver=uvcvideo, 480M
            |__ Port 6: Dev 4, If 1, Class='bInterfaceClass 0x0e not yet handled', Driver=uvcvideo, 480M

### Mensajes de Logs

#### Definición y uso de Logs

De acuerdo al artículo [Log File de Wikipedia](https://en.wikipedia.org/wiki/Log_file), un archivo de log registra los eventos que ocurren en un sistema operativo o en software que se ejecuta en él.
Tener un sistema que nos provea de información acerca de lo que está pasando en nuestro equipo es de fundamental importancia, dado que podemos entender cómo está funcionando nuestro equipo desde diferentes puntos de vista. 
Si las aplicaciones o funciones críticas informan cada evento en el equipo; a partir de esto, podemos generar estadísticas para solucionar problemas repetitivos o detectar fallas que nos ayudarán a solucionar diferentes problemas o criterios.
Estos mensajes que generan las aplicaciones o un servicio de log del sistema se pueden encontrar en el directorio `/var/log`.

```
Por ejemplo,
Muchos mensajes son reportados en el archivo `/var/log/syslog` o en el `/var/log/messages`.
Por otro lado, si un servicio genera muchos mensajes, probablemente estos serán escritos en un archivo separado como lo hace el servidor Web Apache o un servicio de correo.
```
#### El directorio `/var/log`
El directorio `/var/log` contiene los logs principales:

    drwxr-x--- 2 root adm 4096 Sep 30 18:02 /var/log/apache2
    drwxr-xr-x 2 root root 4096 Dec 4 07:43 /var/log/apt
    -rw-r--r-- 1 root root 0 May 27 2014 /var/log/aptitude
    -rw-r--r-- 1 root root 1097 May 26 2014 /var/log/aptitude.1.gz
    -rw-r--r-- 1 root root 813 Jun 5 2013 /var/log/aptitude.2.gz
    -rw-r--r-- 1 root root 492 Apr 19 2013 /var/log/aptitude.3.gz
    -rw-r----- 1 root adm 84378 Dec 9 21:53 /var/log/auth.log
    -rw-r----- 1 root adm 144929 Dec 7 08:01 /var/log/auth.log.1
    -rw-r----- 1 root adm 9712 Nov 30 08:06 /var/log/auth.log.2.gz
    -rw-r----- 1 root adm 7331 Nov 23 07:56 /var/log/auth.log.3.gz
    -rw-r----- 1 root adm 7015 Nov 16 08:08 /var/log/auth.log.4.gz
    -rw-r----- 1 root adm 31 Apr 18 2013 /var/log/boot
    drwxr-xr-x 2 root root 4096 Dec 9 08:06 /var/log/cups
    -rw-r----- 1 root adm 9503 Dec 9 21:46 /var/log/daemon.log
    (...salida cortada...)


#### El protocolo `SysLog`
Estos mensajes y notificaciones se realizan mediante un protocolo llamado `syslog`. En 2001 se publicó la primera RFC al respecto. Uno de sus principios fundamentales es la simplicidad de configuración tanto para la transmisión como para la recepción de mensajes.
Luego se hicieron mejoras al protocolo como la de separar el contenido del mensaje de su transporte y el soporte de cifrado. 

#### Sysklogd (Syslog Daemon) y el archivo de configuración `/etc/syslog.conf`

Este servicio actualmente casi no se usa. Lo podemos encontrar en versiones anteriores a Red Hat 6, CentOS 6. En el caso de Debian, se encontrará en versiones anteriores a Lenny. 
Cuando instalamos el paquete correspondiente, contendrá dos utilitarios (`syslogd` y `klogd`) que proveerán soporte para el registro de eventos del sistema. Syslogd y klogd corren como demonios (procesos en segundo plano) y envían los mensajes del sistema a diferentes lugares (logs de distintos servicios, correo, seguridad, errores, autentificación, etc.).
El demonio syslogd (Syslog Daemon) se lanza automáticamente al arrancar un sistema Unix, siendo el encargado de guardar informes sobre el funcionamiento de la máquina. Recibe mensajes de las diferentes partes del sistema (núcleo, programas…) y los envía y/o almacena en diferentes localizaciones, tanto locales como remotas, siguiendo un criterio definido en el fichero de configuración `/etc/syslog.conf`, donde especificamos las reglas a seguir para gestionar el almacenamiento de mensajes del sistema.

#### Syslog-ng y Rsyslog

##### Syslog-ng
El servicio syslog-ng fue creado en el año 1998. 
Algunas funcionalidades que tiene son:
● Trabaja con cualquier clase de datos que no está organizado de una manera predeterminada.
● Recibe y envía mensajes formateados en el lenguaje JSON.
● Clasifica y organiza los mensajes con analizadores de sintaxis incorporados.

##### Rsyslog
Este proyecto empezó en el 2004 cuando su autor principal Rainer Gerhards decidió escribir un potente sistema de registro de eventos para que pueda competir con syslog-ng. Éste fue usado por Fedora a partir del 2007, por SUSE a partir del 2009 y Debian a partir de su versión 5, entre otras distribuciones que decidieron usarlo.

### Fuentes y más recursos
● [RFC 3164 - The BSD Syslog Protocol](https://tools.ietf.org/html/rfc3164)
● [RFC 5424 - The Syslog Protocol](https://tools.ietf.org/html/rfc5424#page-4)
● [RFC 5426 - Transmission of Syslog Messages over UDP](https://tools.ietf.org/html/rfc5426)

### Herramientas de Logs

#### Commando Logger
Sintáxis:
```
# logger [opciones] [ubicacion archivo] [Mensaje]

```
Con este comando podremos hacer llamadas a syslog(3) para que el sistema pueda escribir un log donde se le indique.

    Opciones:
    -p   Prioridad (puede utilizarse servicio.prioridad).
    -t   Marca, agrega un texto para identificar el mensaje.

Ejemplo:
    # logger -p mail.info "Mensaje de prueba"
    # tail /var/log/mail.log
    Dec 9 22:43:35 debian evillarreal: Mensaje de prueba

    # logger -t "marca de prueba" -p mail.info "Mensaje de prueba"
    # tail /var/log/mail.log
    Dec 9 22:46:47 buegsevi marca de prueba: Mensaje de prueba

#### Commando `systemd-cat`
Este comando tiene algunas similitudes con `logger`, pero es específico de `systemd`. 
Con este comando podemos volcar la salida de cualquier comando a un log:

    systemd-cat -t kernel uname -a
    root@debian10-eit:~# journalctl -t kernel
    -- Logs begin at Tue 2020-12-29 14:30:33 UTC, end at Tue 2020-12-29 22:24:40 UTC. --
    Dec 29 22:24:40 debian10-eit kernel[652]: Linux debian10-eit 5.9.15-200.fc33.x86_64 #1
    SMP Wed Dec 16 19:14:35 UTC 2020 x86_64 GNU/Linux

#### Commando `lastlog`
Con `lastlog` y `last` estos comandos podremos obtener información acerca de los intentos fallidos de logueo de los usuarios del sistema y de las veces que se pudieron conectar.

`lastlog` muestra la última vez que se logueó cada usuario.

Sintaxis:

    lastlog [opciones] [user]

    Opciones
    -u              Define el usuario a consultar.
    -t días         Muestra solo los registros que no sean más antiguos que la cantidad de días definida.
    -b días         Muestra solo los registros que sean más antiguos que la cantidad de días definida.

Ejemplo:

```shell
root@foo:/home/ubuntu# lastlog -u root
Username Port From Latest
root tty2 Fri Sep 19 15:20:39 -0300 2014
```

#### Commando `last`
El comando last muestra las últimas veces que un usuario ingresó en el sistema pero también las veces que se apagó o reinició el equipo.
Indica también la versión de kernel con la que se inició, lo cual es muy importante.
Sintaxis:

    last [opciones] [user]

    Opciones
    -f archivo Lee otro archivo en lugar de usar /var/log/wtmp.
    -numero Cantidad de líneas a mostrar.
    -F Más datos acerca de login y logout.
    -w Más datos acerca del usuario y dominios

Ejemplo:
```shell
root@foo:/home/ubuntu# last
user1 pts/12 10.1.5.23 Tue Dec 9 22:51 still logged in
user1 pts/11 alm-s01-v01 Tue Dec 9 22:50 - 23:00 (00:09)
user1 pts/6 10.1.5.23 Tue Dec 9 21:52 still logged in
user1 pts/6 10.1.5.23 Mon Dec 8 13:03 - 07:54 (18:51)
user1 pts/6 10.1.5.23 Sat Dec 6 09:49 - 09:11 (23:22)
(...salida cortada...)
```
#### Comando `Faillog` para ver logins fallidos en ubuntu (comprobar esto)
Muestra los logueos fallidos de los usuarios.

Sintaxis: 
    faillog [opciones] [user]

    -a      Muestra todos los eventos.
    -l      Bloquea por un tiempo determinado el login luego de fallar.
    -u      Muestra información del usuario definido.

Ejemplo
```shell
[root@CentOS-5 init.d]# faillog -u root
Usuario Fallos Máximo Último
root 0 0 12/31/69 21:00:00 -0300
```

#### Comando `faillock` para ver logins fallidos en CentOs

Si bien `rsyslog` registra los logins fallidos en un archivo existen algunas herramientas que permiten visualizarlos.
En CentOS por ejemplo contamos con el comando faillock:

```shell
root@foo# faillock
educacionit:
When Type Source Valid
2020-08-10 14:01:50 RHOST 10.0.3.1 V
2020-08-10 14:01:54 RHOST 10.0.3.1 V
root:
When Type Source Valid
```
Para eso es necesario usar una configuración en el sistema de autenticación, a continuación mostramos de
las modificaciones que habría que realizar y el archivo involucrado:

    #%PAM-1.0
    # This file is auto-generated.
    # User changes will be destroyed the next time authselect is run.
    auth required pam_env.so
    auth required pam_faillock.so preauth silent deny=4 unlock_time=1200
    auth sufficient pam_unix.so try_first_pass nullok
    auth required pam_faillock.so authfail deny=4 unlock_time=1200
    auth required pam_deny.so
    account required pam_unix.so
    account required pam_faillock.so
    password requisite pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
    password sufficient pam_unix.so try_first_pass use_authtok nullok sha512 shadow
    password required pam_deny.so
    session optional pam_keyinit.so revoke
    session required pam_limits.so
    session optional pam_systemd.so
    session [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
    session required pam_unix.so

Si queremos que quede de manera permanente hacemos lo siguiente:

    # authconfig --enablefaillock --update

En el caso de Debian, existe una herramienta similar llamada `pam_tally2`:

    # pam_tally2 --user sergio
    Login Failures Latest failure From
    sergio 6 08/04/20 03:37:38 10.0.3.1


En este caso también habría que modificar ciertos archivos en `/etc/pam.d`, por ejemplo, de este modo:

    Archivo /etc/pam.d/common-auth
    auth required pam_tally2.so
    auth [success=1 default=ignore] pam_unix.so
    nullok_secure
    auth requisite pam_deny.so
    auth required pam_permit.so
    auth optional pam_cap.so

#### Aplicación Snoopy

Esta herramienta sirve para registrar todos los comandos ejecutados en los logs. Lo hace mediante la carga previa anterior a la ejecución de cualquier programa de la librería libsnoopy.so.
Se puede instalar de este modo:

    # curl -O
    https://github.com/a2o/snoopy/raw/install/install/install-snoopy.sh
    # chmod u=rwx,ox=rx install-snoopy.sh
    # ./install-snoopy.sh stable

Una vez reiniciado el sistema, podremos ver con journalctl -f:

    may 10 15:58:40 debian.educacionit.local snoopy[1160]: [uid:0 sid:1144 tty:/dev/pts/0 cwd:/root
    filename:/bin/ls]: ls
    may 10 15:58:43 debian.educacionit.local snoopy[1161]: [uid:0 sid:1144 tty:/dev/pts/0 cwd:/root
    filename:/bin/journalctl]: journalctl -f
    may 10 15:58:49 debian.educacionit.local snoopy[1162]: [uid:0 sid:1144 tty:/dev/pts/0 cwd:/root
    filename:/usr/bin/clear]: clear
    may 10 15:59:21 debian.educacionit.local snoopy[1190]: [uid:0 sid:1144 tty:/dev/pts/0 cwd:/root
    filename:/usr/bin/vi]: vi .bashrc
    may 10 15:59:27 debian.educacionit.local snoopy[1191]: [uid:0 sid:1144 tty:/dev/pts/0 cwd:/root
    filename:/bin/journalctl]:

En lugar de habilitarlo para todo el sistema, se puede activar para una sesión de shell determinada:

    # chmod 755 /lib/libsnoopy.so.0.0.0 ; LD_PRELOAD = /lib/libsnoopy.so.0.0.0 bash

Para deshabilitarlo, sencillamente salimos de bash:

    # bash

Si en cambio la habilitamos globalmente y queremos revertirlo:

    # snoopy-disable
    SNOOPY: Removing from /etc/ld.so.preload: /lib/libsnoopy.so
    SNOOPY: Disabled.
    SNOOPY: Hint: Your system needs to be restarted to finish snoopy cleanup.

Luego, solamente resta reiniciar.

En Debian se puede instalar desde los repositorios, luego se puede habilitar/deshabilitar con el comando `dpkg-reconfigure`.

### Rotación de Logs, archivos `/etc/logrotate.conf`, `/etc/logrotate.d/` y `/etc/crond.daily/logrotate`

Erik Troan y Preston Brown son los autores de Logrotate, una utilidad para administrar las políticas de los logs de tu equipo.
Logrotate es un estándar en sistemas RedHat y Debian. Con esta herramienta, podremos especificar todo tipo de parámetros a la hora de administrar nuestros logs.
Un archivo de configuración de Logrotate, consiste en una serie de especificaciones para los grupos de archivos de log que vamos a administrar.  Las opciones especificadas fuera de cada contexto de un log concreto, (errors, rotate, weekly…) se aplican a todos ellos, pero pueden ser reemplazadas con una especificación concreta para un log en particular.
En nuestro sistema, la utilización de logs es algo imprescindible y es por eso que éstos crecen constantemente y hay que tener alguna utilidad para especificar el comportamiento.
El directorio de configuración global se encuentra en `/etc/logrotate.conf`; sino, también, tenemos otro directorio en `/etc/logrotate.d/`, donde podremos poner individualmente cada configuración.
Para que cada una de las configuraciones tenga efecto, se programa una entrada en el crontab del sistema, para que corran cada determinado tiempo (`/etc/crond.daily/logrotate`).

    Opciones:
    -d      Se utiliza para debug no hace nada, simula la rotación.
    -f      Fuerza la rotación
    -v      Nos da más información.

Ejemplo
```shell
root@foo:/home/ubuntu# logrotate -d /etc/logrotate.conf
reading config file /etc/logrotate.conf
including /etc/logrotate.d
reading config file apache2
reading config file apt
reading config file aptitude
reading config file cups
reading config file dpkg
reading config file mysql-server
reading config file pm-utils
reading config file rsyslog
reading config file samba
reading config file vsftpd 
Handling 29 logs
rotating pattern: /var/log/apache2/*.log weekly (52 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/apache2/access.log
 log does not need rotating
considering log /var/log/apache2/error.log
 log does not need rotating
not running prerotate script, since no logs will be rotated
not running postrotate script, since no logs were rotated
rotating pattern: /var/log/apt/term.log monthly (12 rotations)
empty log files are not rotated, old logs are removed
considering log /var/log/apt/term.log
 log does not need rotating
```

El directorio `/etc/logrotate.d` es un lugar estándar para los archivos de configuración de Logrotate.
Todos los paquetes software conscientes de logrotate (la gran mayoría) se integran con este sistema de administración de logs en la parte de su proceso de instalación, lo que simplifica ampliamente la administración.

Ejemplo
```shell
root@foo:/home/ubuntu# ls /etc/logrotate.d
apache2         cups        lighttpd        ppp
apt             dirmngr     monit           vsftpd
aptitude        dpkg        mysql-server    rsyslog
```

Configuración de Ejemplo
```shell
root@foo:/home/ubuntu# cat /etc/logrotate.d/vsftpd
/var/log/vsftpd.log
{
    create 640 root adm
    missingok
    notifempty
    rotate 4
    weekly
}
```

    Opciones
    missingok               No se producirá ningún error si el archivo de log no existe.
    notifempty              No rotar el log si éste está vacío.
    sharedscripts           Los scripts de postrotate solo se ejecutarán una vez que los logs viejos sean comprimidos.
    delaycompress           Sirve por si algún programa está escribiendo y necesita al archivo, éste no se comprime.
    postrotate/endscript    Lo que esté dentro de estas directivas, se ejecutará luego de la rotación de archivos.
    compress                Comprime los archivos rotados.
    daily                   Rotar diariamente.
    weekly                  Rotar semanalmente.
    monthly                 Rotar mensualmente.
    yearly                  Rotar anualmente.

Lo que no esté definido se tomará del archivo de configuración global `/etc/logrotate.conf`.







