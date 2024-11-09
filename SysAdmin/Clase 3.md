**La computadora**

Una computadora, en esencia, es un sistema compuesto por una unidad de almacenamiento que guarda los programas y los datos de manera permanente, una memoria en la que se cargan esos programas para que puedan ser ejecutados, y un procesador (CPU) que se encarga de interpretar y ejecutar las instrucciones de los programas. Los resultados o acciones del procesador se comunican a través de dispositivos de entrada y salida, permitiendo la interacción con el exterior. Este esquema básico es el mismo tanto en sistemas simples, como la placa lógica de un lavarropas, como en arquitecturas más complejas que utilizan BIOS o UEFI para gestionar el arranque y la inicialización del hardware antes de cargar el sistema operativo.

**Sistema operativo, núcleo o kernel y capa de hardware**

Un sistema operativo (SO) es un conjunto de software que administra los recursos de hardware de una computadora y proporciona servicios esenciales para los programas de aplicación. Actúa como una capa intermedia entre el hardware y el software de usuario, gestionando tareas como la ejecución de programas, el manejo de archivos, la gestión de la memoria y el control de dispositivos de entrada y salida. El sistema operativo también proporciona una interfaz de usuario, que puede ser gráfica (GUI) o de línea de comandos (CLI), para interactuar con el sistema. 

El kernel es el núcleo del sistema operativo. Es la parte central que se encarga de gestionar directamente los recursos del hardware, como la CPU, la memoria y los dispositivos de entrada/salida. El kernel opera en un nivel privilegiado del sistema y es responsable de tareas críticas como la administración de la memoria, la planificación de procesos, el manejo de interrupciones y la comunicación entre procesos. Existen diferentes tipos de kernels, como los monolíticos, los microkernels y los híbridos, cada uno con distintas arquitecturas y enfoques en la gestión de recursos. 

En el caso de GNU/Linux, el término se refiere a la combinación del kernel Linux con las herramientas y utilidades del proyecto GNU. Linux es el kernel del sistema, desarrollado originalmente por Linus Torvalds, y se encarga de interactuar directamente con el hardware. GNU es un conjunto de herramientas y utilidades desarrolladas por el proyecto GNU, que proporciona una colección de programas y bibliotecas esenciales para crear un sistema operativo completo.

Juntos, GNU y Linux forman lo que comúnmente se conoce como "distribuciones de Linux", que son sistemas operativos completos utilizados en una amplia variedad de dispositivos, desde servidores y super computadoras hasta teléfonos móviles y sistemas embebidos. Las distribuciones de Linux incluyen el kernel Linux, las herramientas GNU, un sistema de gestión de paquetes y, a menudo, una interfaz gráfica de usuario. 


**Servicios del sistema operativo** 

Los servicios del sistema operativo son programas o procesos que se ejecutan en segundo plano y proporcionan funcionalidades específicas para el sistema operativo y para otras aplicaciones.  

Estos servicios pueden incluir tareas como la gestión de archivos, la impresión, la seguridad, la conectividad de red, la administración de recursos y muchos otros aspectos del funcionamiento del sistema. 


Los servicios del sistema operativo pueden ejecutarse automáticamente al iniciar el sistema o pueden ser iniciados por otros programas cuando sea necesario. 

Muchos servicios del sistema son esenciales para poder 'usar' el sistema operativo, por ejemplo, si no estuviese en ejecucion el servicio 'ssh' no me podria conectar de manera remota, si no estuviese el servicio de autenticacion no podrian validarse los usuarios. 

**Systemd** 

es un sistema de inicialización y administración de servicios que reemplazó a los sistemas de inicialización anteriores como SysV init en muchas distribuciones modernas de Linux. Gestiona la secuencia de arranque del sistema y la administración de servicios. 


**Arranque del sistema** 

Al inciar el equipo seran los programas de la BIOS/UEFI los encargados de, primero reconocer el harware circundante y luego buscar medios 'booteables' (bios) o sistemas operativos (uefi). 


Cuando se utilizaba BIOS el programa detectaba algun medio marcado como 'booteable', esto es que en un medio de almacenamiento tenga un MBR escrito. Este es un conjunto de sectores con instrucciones de donde buscar un cargador de arranque, en linux este cargador esta en el directorio '/boot', mientras que en windows existe un partición oculta.

$ **ls /boot/ | grep 122**
```
efi/
grub/
config-5.15.0-122-generic
initrd.img-5.15.0-122-generic
System.map-5.15.0-122-generic
vmlinuz-5.15.0-122-generic
```

En el caso de la UEFI no se basa en la lectura de un MBR si no en la busqueda de un programa de arranque en cualquier disco (el primro que detecte), basicamente detecta sistemas operativos.  

$ **sudo ls /boot/efi/EFI**
```
BOOT  Microsoft  ubuntu 
```

**Bootloader **

El código de arranque inicia el bootloader o cargador de arranque, como GRUB en Linux,donde, a través de un menú, se puede elegir el sistema operativo que se desea iniciar.  

El sistema UEFI mantiene la compatibilidad con el arranque mediante MBR. En caso de no utilizar el modo de compatibilidad, intentará usar una partición GPT (Guid Partition Table) para cargar los archivos de inicio de los distintos sistemas operativos. Estos archivos tienen extensión .efi y .uefi y pueden arrancar directamente su propio bootloader o usar GRUB.  

**Directorio /boot**

$ **ls /boot/ -1**
```
config-6.1.0-10-amd64
config-6.1.0-11-amd64
config-6.1.0-26-amd64
grub
initrd.img-6.1.0-10-amd64
initrd.img-6.1.0-11-amd64
initrd.img-6.1.0-26-amd64
System.map-6.1.0-10-amd64
System.map-6.1.0-11-amd64
System.map-6.1.0-26-amd64
vmlinuz-6.1.0-10-amd64
vmlinuz-6.1.0-11-amd64
vmlinuz-6.1.0-26-amd64
```

**Disco de arranque initrd.img-...**

El disco de inicio initrd (Initial RAM Disk) o initramfs (Initial RAM File System) es una imagen de sistema de archivos temporales que se carga en la memoria RAM durante el proceso de arranque inicial de un sistema Linux. Este disco de inicio se utiliza para realizar tareas necesarias antes de que el sistema operativo principal se haya cargado completamente desde el disco duro. 

El initrd o initramfs es fundamental para realizar tareas como la detección y montaje de sistemas de archivos, la carga de módulos del kernel necesarios para el hardware específico del sistema, la configuración de la red, la inicialización de dispositivos, la activación de volúmenes lógicos (LVM) o sistemas de archivos cifrados, entre otras tareas. 

El disco de inicio initrd o initramfs sirve para proporcionar un entorno mínimo y funcional durante el proceso de arranque del sistema, permitiendo realizar las operaciones necesarias para configurar y preparar el sistema antes de que el sistema operativo principal se cargue por completo desde el disco duro.  


**Carga del kernel vmlinuz-...**

Una vez que el disco de arranque ha sido cargado en la memoria RAM y está listo para su uso, el bootloader del sistema operativo (como GRUB en la mayoría de los casos) carga el kernel del sistema operativo desde el disco duro hacia la memoria RAM.   

El fichero **vmlinuz** es una imagen del kernel  comprimida.  
Una vez que se localice y monte el sistema de archivos raíz, el initramfseducacionit
 cederá el control al gestor del sistema, en este caso 'Systemd'.  


$ **lsblk** 
```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda      8:0    0   20G  0 disk 
├─sda1   8:1    0   19G  0 part / \# sistema de archivos raiz montado por initrd
├─sda2   8:2    0    1K  0 part 
└─sda5   8:5    0  975M  0 part [SWAP]
```
**Proceso Init**

En este punto, se ejecutará el primer proceso llamado init o systemd, que se encargará de iniciar los diferentes servicios del sistema, realizar configuraciones, montar unidades y llevar a cabo otras tareas. También será responsable de apagar el sistema cuando sea necesario. 

La columna 'pid' del comando 'ps aux' muestra la id de proceso, la id 1 corresponde al primer programa ejecutado. En este caso el programa '/sbin/init' es el que ejecuta el servicio 'systemd' que es el encargado de terminar de cargar el sistema operativo. 
 
$ **ps aux | head -3**
```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND root           1  0.0  0.2 167512 12020 ?        Ss   16:00   0:00 /sbin/init root           2  0.0  0.0      0     0 ?        S    16:00   0:00 [kthreadd]
```



**Laboratorio: Identificacion de kernel**.

Utilizar el comando **uname -a** para identificar la version de kernal en uso.

$ **uname -a**

``Linux sysadmin 6.1.0-26-amd64 \#1 SMP PREEMPT_DYNAMIC Debian 6.1.112-1 (2024-09-30) x86_64 GNU/Linux
``
Listar y filtrar los archivos correspondientes al kernel en uso ubicados en el directorio '/boot'.

$ **ls -1 /boot/*-$(uname -r)**
```
/boot/config-6.1.0-26-amd64
/boot/initrd.img-6.1.0-26-amd64
/boot/System.map-6.1.0-26-amd64
/boot/vmlinuz-6.1.0-26-amd64
```
Extra, actualizar el sistema y repetir los pasos anteriores ( ¬¬ )

\# **apt update && apt upgrade**


**Modos de arranque del sistema**

**Seleccion de kernel a partir de 'grub menu'**

Luego de una actualización del sistema un nuevo kernel podría no tener compatibilidad con algún dispositivo o funcionar de manera estable. Desde el menu grub podemos bootear con versiones anteriores si estas se conservan. 

(re)Iniciar el sistema y seleccionar las opciones avanzadas de 'grub'.

Nos muestra una lista de versiones anteriores y los modos 'recovery'.

Iniciar con una version antigua, mostrar el kernel en uso con 'uname' e identificar los nucleos instaldos con 'apt'.

Version de kernel en uso

$ uname -a
Linux sysadmin 6.1.0-10-amd64 \#1 SMP PREEMPT_DYNAMIC Debian 6.1.38-1 (2023-07-14) x86_64 GNU/Linux

Versiones disponibles/instalads en el sistema

\# apt list --installed | grep linux-image

linux-image-6.1.0-10-amd64/now 6.1.38-1 amd64 [instalado, local]
linux-image-6.1.0-11-amd64/stable-security,now 6.1.38-4 amd64 [instalado, autodesinstalable]
linux-image-6.1.0-26-amd64/stable-security,now 6.1.112-1 amd64 [instalado, automático]
linux-image-amd64/stable-security,now 6.1.112-1 amd64 [instalado]


'linux-image-6.1.0-26-amd64' ultima version instalada, '6.1.0-10-amd64' version actualmente en uso temporalmente.


Iniciar en modo 'recovery mode'

Listar antes de reiniciar el arbol de procesos.

$ pstree
systemd─┬─cron
        ├─dbus-daemon
        ├─login───bash
        ├─mariadbd───7*[{mariadbd}]
        ├─sshd
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-timesyn───{systemd-timesyn}
        └─systemd-udevd

(re)Iniciar el sistema con la version de kernel a eleccion pero que sea 'recovery mode'. En el arranque se nos pedira introducir la contraseña de 'root' para iniciar en este modo, de lo contrario utilizar 'ctrl-d' para iniciar normalmente.

Una vez iniciado el sistema mostrar el arbol de procesos y compararlo con el aterior.

$ pstree
systemd─┬─systemd-journal
        ├─systemd-sulogin
        ├─systemd-timesyn
        └─systemd-udevd
        
        
El modo recovery inicia con los servicios minimos para poder hacer tareas de mantenimiento y/o repoaraciones, etc.

El conjunto de servicios que se cargan en este modo dependen de la distribucion.





---

Dist upgrade

Pasar de debian 11 a debian 12, no realizar este tipo de operaciones en sistemas altamente configurados.

Cambiar version de repositorios

\# sed -i 's/bullseye/bookworm/' /etc/apt/sources.list

Acutalizar la base de datos local

\# apt update

Upgrade de distribucion

\# apt upgrade --without-new-pkgs

Fichero '/etc/issue'
issue (Y/I/N/D/Z) por omision N

Reiniciar todos los servicios

Seleccionar 'si'.


Configuracion de grub-pc

Si aparece esta pantalla seleccionar con barra espaciadora:

[*] /dev/sda (21474 MB; VBOX_HARDDISK)
[ ] - /dev/sda1 (20449 MB; /)


Fanalmente...

\# apt full-upgrade


\# reboot


