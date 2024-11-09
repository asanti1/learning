**Menu GRUB**

Al arrancar el sistema, el gestor de arranque GRUB 2 muestra un menú cuyas opciones pueden variar según el sistema y la distribución que estemos utilizando. En cualquier caso, este menú permite elegir el sistema operativo con el que deseamos iniciar.
 
Si reiniciamos la máquina y accedemos al menú de GRUB > "Advanced options" (Opciones avanzadas), tendremos la posibilidad de iniciar el sistema utilizando versiones anteriores del kernel instaladas o acceder al modo de recuperación. Este modo es útil para realizar tareas de mantenimiento o solucionar problemas en el sistema cuando no se puede arrancar normalmente. 

**Opciones de GRUB** 

Sistemas operativos instalados: Si tienes varios sistemas operativos instalados (por ejemplo, Linux y Windows), GRUB te permite elegir cuál cargar.   

Opciones de arranque avanzadas: Puedes tener opciones adicionales, como modos de recuperación, diferentes versiones del kernel, o configuraciones especiales. 
 
Edición de comandos de arranque: GRUB también permite la edición temporal de los comandos de arranque antes de iniciar el sistema operativo.  


**Parametros del kernel**

Los parámetros del kernel son opciones que se le pasan al núcleo de Linux durante el proceso de arranque para modificar su comportamiento. Estos parámetros controlan diversas configuraciones del sistema, como la gestión de hardware, el comportamiento de los controladores y aspectos de seguridad. Se pueden utilizar para activar o desactivar características específicas, ajustar el rendimiento o resolver problemas de compatibilidad.


Los parámetros del kernel se especifican en la línea de comandos del gestor de arranque, como GRUB, y se aplican al inicio del sistema. Algunos ejemplos comunes son: 

**root**: Define la partición que se utilizará como sistema de archivos raíz (por ejemplo, root=/dev/sda1). 
**quiet**: Oculta mensajes detallados del kernel durante el arranque, mostrando solo información mínima. 
**nomodeset**: Desactiva el cambio de modo gráfico durante el arranque, útil para solucionar problemas con los controladores gráficos. 
**init**=/bin/bash: Cambia el programa de inicio predeterminado, útil para situaciones de recuperación. 
**acpi**=off: Desactiva la interfaz de configuración y energía avanzada (ACPI), a veces utilizada para resolver problemas de compatibilidad con hardware antiguo. 
**ro**: Carga el rootfs en modo 'solo lectura'. 

Estos parametros se definen en el fichero de configuracion de grub cuando se invoca el comando 'linux' el cual carga el kernel. El fichero fichero de onfiguracion ademas carga un kernel con opciones disitintas (si las hubiere) segun la entrada del menu.

\# **cat /boot/grub/grub.cfg | grep linux**

```
linux        /boot/vmlinuz-6.1.0-26-amd64 root=UUID=e8a4f356-6513-4c58-8a10-73474fdf7899 ro  quiet
linux        /boot/vmlinuz-6.1.0-26-amd64 root=UUID=e8a4f356-6513-4c58-8a10-73474fdf7899 ro  quiet
linux        /boot/vmlinuz-6.1.0-10-amd64 root=UUID=e8a4f356-6513-4c58-8a10-73474fdf7899 ro single 
```
**NOTA: Nunca se debe editar el fichero '/boot/grub/grub.cfg',**


**Modificar parametros del kernel de manera temporal**   

En el menu grub debemos posicionarnos sobre un item de la liste y presionar la letra 'e', de esta manera nos permite editar dicha entrada pero esta edicion no es permanente, solo afecta al siguiente 'booteo'. Una vez hechos los cambios se presioa F10 para arrancar.


**Laboratorio: Parámetros del kernel **

Iniciar el sistema operativo modificando los parámetros: 
- quiet 
- init (iniciar una shell bash) 
 
El sistema no se inicia como tal, solo se carga el kernel y se ejecuta el programa indicado, en este caso la shell bash y nada mas, si existen otros procesos estos son del kernel.

En este contexto podemos acceder al filesystem en modo de solo lectura aunque se podría cambiar.

En estado comandos como **poweroff** o **reboot** son imposibles ya que estos están relacionados con estados de **systemd**.

Eventualmente se puede enviar una señal de apagado al kernel de manera directa.

\# **echo o > /proc/sysrq-trigger**


**Ficheros de configuracion de grub** 

Lo ficheros de configuracion de grub que estan dentro de '/boot/' no se deben tocar bajo ningun aspecto.   

Para ello se utuliza el fichero '/etc/default/grub' y los scripts de complicacion '/etc/grub.d/' se encargan de generar la configuracion '/boot/grub/grub.cfg'.   


**Fichero grub.cfg**

El archivo grub.cfg es el archivo de configuración principal de GRUB 2, el gestor de arranque en la mayoría de los sistemas Linux. Este archivo contiene las instrucciones que definen el menú de arranque que se muestra cuando el sistema inicia. A través de este menú, el usuario puede seleccionar qué sistema operativo o kernel cargar, así como acceder a otras opciones avanzadas, como el modo de recuperación. Es importante destacar que este archivo no se debe editar manualmente en la mayoría de los casos, ya que se genera automáticamente mediante el comando update-grub basado en las configuraciones ubicadas en /etc/default/grub y los archivos en /etc/grub.d/. 


**Parametros del kernel permanentes** 

**Fichero /etc/default/grub **

Define opciones globales para GRUB 2, el gestor de arranque. Este archivo contiene parámetros que influyen en cómo se genera el archivo de configuración final grub.cfg cuando se ejecuta el comando update-grub , este comando procesa los scripts del directorio '/etc/grub.d'. 


Laboratorio: configuracion de grub

Modificar el archivo '/etc/default/grub' para que el kernel muestre mensajes al inicio y ademas el tiempo de espera del bootloader sea de 3 segundos.

**Fichero /etc/default/grub**
```

GRUB_DEFAULT=0
\#GRUB_TIMEOUT=5
GRUB_TIMEOUT=3
GRUB_DISTRIBUTOR=`lsb_release -i -s 2> /dev/null || echo Debian`
\#GRUB_CMDLINE_LINUX_DEFAULT="quiet"
GRUB_CMDLINE_LINUX_DEFAULT=""
GRUB_CMDLINE_LINUX=""
```


Generar **grub.cfg**

\# **update-grub2**

**SystemD **

Es un sistema de inicio y gestor de servicios para Linux. Su principal función es gestionar el arranque del sistema y la ejecución de los servicios. Además de controlar los servicios, systemd también gestiona la configuración del sistema, como la activación de unidades, la gestión de procesos, la conexión de dispositivos, el montaje de sistemas de archivos y la administración de sesiones de usuario. Es conocido por su capacidad para iniciar servicios en paralelo, mejorar el tiempo de arranque y proporcionar un control más detallado sobre los procesos del sistema.

Servicio **apache2**

Este es un servidor web y como todo servicio se gestiona con 'systemd'.

**Instalacion**

\# **apt update && apt install apache2**

Acceso

Cambiar ip por la ip de la vm.

http://192.168.0.11


**Gestion del servicio **

Un servicio como Apache2 es un programa que se ejecuta en segundo plano en el sistema, lo que significa que no interactúa directamente con el usuario una vez iniciado. Estos servicios, como Apache2, están diseñados para realizar tareas de manera continua o en respuesta a ciertos eventos, como servir páginas web en este caso.  

Dado que estos servicios no tienen interfaces directas con los usuarios, es necesario utilizar un sistema intermediario para su gestión. En la mayoría de los sistemas Linux modernos, systemd es el encargado de administrar estos servicios. Systemd permite iniciar, detener, reiniciar y supervisar servicios como Apache2 de manera centralizada, facilitando su control sin que el usuario tenga que interactuar directamente con el servicio en sí. 


**Consultar estado del servicio**


\# **systemctl status apache2.service**

```
● apache2.service - The Apache HTTP Server
# Informacion del servicio
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-10-21 11:41:47 -03; 4min 26s ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 2139 (apache2)
      Tasks: 55 (limit: 4644)
     Memory: 9.6M
        CPU: 49ms
# Arbol de procesos
     CGroup: /system.slice/apache2.service
             ├─2139 /usr/sbin/apache2 -k start
             ├─2141 /usr/sbin/apache2 -k start
             └─2142 /usr/sbin/apache2 -k start
# Ultimos registros (logs)
oct 21 11:41:47 sysadmin systemd[1]: Starting apache2.service - The Apache HTTP Server...
oct 21 11:41:47 sysadmin apachectl[2137]: AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 127.0.1.1. Se>
oct 21 11:41:47 sysadmin systemd[1]: Started apache2.service - The Apache HTTP Server.

```

**enabled/disabled** Indica si el servicio se inicia automaticamente con el sistema. **active/inactive** Indica si el servicio esta en ejecucion (running) o detenido (dead). 


**Detener servicio** 

\# **systemctl stop apache2.service** 

**Iniciar servicio** 

\# **systemctl start apache2.service** 

**Reiniciar servicio** 

\# **systemctl restart apache2.service** 


**Deshabilitar/habilitar el arranque del servicio al inicio del sistema**

\# **systemctl disable apache2.service** 

\# **systemctl enable apache2.service** 

**Recargar ficheros de configuracion (solo si esta disponible para el servicio) **

\# **systemctl reload apache2.service** 

**Consultar si el servicio esta en ejecucion** 

Este comando es muy util para scripting (ver ejemplo https://pastebin.com/aPCV1AsC).

\# **systemctl is-active apache2.service **
active/inactive

**Listar servicios** 

\# **systemctl list-units --type=service** 

```
# systemctl list-units --type=service
  UNIT            LOAD   ACTIVE SUB     DESCRIPTION    
  apache-htca     loaded active running Disk Cache C
  apache2.serv    loaded active running The Apache HTTP Server
  apparmor.ser    loaded active exited  Load AppArmor profiles
...
```


**Enlaces de archivos**

Un enlace simbolico (symlink o softlink) es un tipo de archivo que apunta a otro archivo, en otros sistemas com windows lo entendemos como 'acceso directo'.

El comando **ln**'permite la creacion de dos tipos de enlace:
- blandos (symlink)
- duros


**Creacion de enlace**

En nuestro directorio de usuario (standar) creamos un enlace llamado 'passwd' que apunta al fichero **/etc/passwd**

$ **ln -s /etc/passwd passwd**

Con el comando **ls**'podemos ver el tipo de archivo y hacia a donde apunta.

$ **ls -l passwd**
```
lrwxrwxrwx 1 educacionit educacionit 11 oct 21 12:29 passwd -> /etc/passwd
```
**l** indica que se trata de un enlace.
**passwd -> /etc/passwd** hacia a donde apunta.


También podemos hacer lo mismo con el comando 'file'.

$ **file passwd**
```
passwd: symbolic link to /etc/passwd
```
$ **file /etc/passwd**
```
/etc/passwd: ASCII text
```




--- 
**Desactivar ipv6** 

https://support.nordvpn.com/hc/en-us/articles/20164669224337-How-to-disable-IPv6-on-Linux 
Fichero **/etc/sysctl.conf**
```
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.default.disable_ipv6 = 1
net.ipv6.conf.lo.disable_ipv6 = 1
```

Aplicar cambios 

\# **sysctl -p**
