Drive

https://drive.google.com/drive/folders/1SzPt9RN1IesWKL5w2Eeqqk3L9NLZz0s2?usp=sharing

Debian 12 Virtual Box VM

https://drive.google.com/file/d/1LsCgN3jkxNXuWMdf3c1OphzY8kMcmV2o/view?usp=drive_link

VirtualBox

https://www.virtualbox.org/wiki/Downloads



Importar VM

Abrir VirtualBox.

Menu Archivo > Importar servicio virtualizado > Ventana Servicio a importar: Seleccionar archivo '.ova' > Preferencia del servicio "Política de dirección MAC generar una nueva dirección MAC" > Terminar.

Configurar VM

Abrir la configuración de la VM importada.

Sección 'Sistema'

En esta sección se pueden modificar las características de memoria y CPU.

Sección 'Red'

Conectado a 'Adaptador puente', Nombre 'seleccionar interfaz conectada a la red'


Dirección '127.0.0.1'

Esto significa que el host no obtuvo dirección de red del DHCP local, esto puede ser porque:
    
    - No hay dhcp.
    - La computadora anfitrion esta en una vpn.
    - La red por cuestiones de seguridad no admite nuevos hosts, como en las redes empresariales.
    - El host anfitrion esta detras de un proxy.
    
La solución rápida es poner la VM detrás de la red NAT de VirtualBox.


Sección 'Red'
    
Conectado a 'NAT'. La dirección que toma en esta red es la 10.0.2.15/24 (10.0.2.0/24)

Mapeo de puertos RED NAT

Sección 'Red'

Abrir 'Avanzado' > Reenvió de puertos

Nombre: ssh
Protocolo: tcp
Ip anfitrion: 0.0.0.0
Puerto anfitrion: 2022
IP Invitado: 10.0.2.15
Puerto Invidado: 22

Credenciales

Usuario: educacionit
Contraseña: 1234

Usuario: root
Contraseña: 1234


Conexión 'ssh'

En windows abrir una terminal PowerShell o CMD.

 `ssh educacionit@direccionip_vm`

Por ejemplo:
`ssh educacionit@192.168.0.57`
...
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
...
`educacionit@192.168.0.57's password: 1234`
...
educacionit@debian:~$


Especificar puerto

`ssh -p 2022 educacionit@ipanfitrion`

alternativamente

 `ssh -p 2022 educacionit@127.0.0.1`



Actualización del SO

Loguearse como usuario 'root'

educacionit@debian:~$ su -
Contraseña: 1234

root@debian:~# 

Update de base de datos de paqeutes y upgrade de software

# apt update && apt upgrade
...
Se utilizarán 405 MB de espacio de disco adicional después de esta operación.
¿Desea continuar? [S/n] 

**Configuracion de grub-pc**

Si aparece esta pantalla seleccionar con barra espaciadora:

`[*] /dev/sda (21474 MB; VBOX_HARDDISK)`
`[ ] - /dev/sda1 (20449 MB; /)`


Instalar herramienta **'pstree'**

\# **apt install psmisc**

Volver a usuario estándar

\# **exit**
$

**Sistema Operativo**

Un sistema operativo es un conjunto de software que actúa como intermediario entre
el hardware de una computadora y las aplicaciones que utilizamos. Su función
principal es gestionar los recursos del sistema, como la memoria, el procesador, y
los dispositivos de entrada y salida, para que las aplicaciones puedan funcionar
correctamente. Además, el sistema operativo proporciona una serie de servicios y
herramientas que permiten a los usuarios interactuar con el sistema de manera
eficiente, como la gestión de archivos, la seguridad, y la administración de
procesos. En resumen, es el software fundamental que permite que una computadora
funcione y que podamos utilizarla para realizar diversas tareas.

El sistema operativo esta organizado en una estructura de directorios con funciones bien definidas, esta estructura para sistemas GNU/Linux sigue un estandar que se llama FHS (https://es.wikipedia.org/wiki/Filesystem_Hierarchy_Standard).


**Servicios del sistema**

Los servicios en un sistema operativo son programas que se ejecutan en segundo
plano para realizar tareas específicas de manera continua o según sea necesario.
Estos servicios proporcionan funciones esenciales para el funcionamiento del
sistema y el soporte de aplicaciones, como la gestión de red, la impresión, la
seguridad, la administración de procesos y la autenticacion.

**Listar árbol de procesos**

Los procesos son programas en ejecucion, los servicios son programas, el primer servicio a iniciarse es 'systemd', segun el arbol los procesos hijos directos de este son los servicios del sistema que systemd inicia.

$ pstree
systemd─┬─cron
        ├─dbus-daemon
        ├─dhclient
        ├─login
        ├─sshd
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-timesyn───{systemd-timesyn}
        └─systemd-udevd

'systemd' Gestiona el arranque y la carga de servicios del sistema, todos los proceos en ejecucion son hijos de manera directa o indirecta de este proceso.

'cron' Servicio de ejecucion de tareas programas.
'dhclient' Servicio de autoconfiguracion de red.
'login' Servicio de autenticacion de usuarios.
'sshd' Servicio de shell remota.
'systemd-journal' Registros (logs) del sistema.
'systemd-logind' Servicio de autenticacion de systemd.
'systemd-timesyn' Servicio de hora del sistema.
'systemd-udevd' Servicio de gestion de dispositivos.


**Directorio raiz '/'**

El directorio raíz es el nivel más alto en la estructura de directorios de un
sistema operativo. Es el punto de partida desde el cual se organizan todos los
archivos y directorios en el sistema.

En sistemas basados en Unix el directorio raiz es '/'.

$ ls -1 /
bin
boot
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var


**Directorio '/bin'**

Directorio de archivos binarios de usuario. Estos contienen progrmas en lenguaje
maquina (compilados) y ademas normalmente son ejecutables ya que en estos
directorios se encuentran los 'comandos'.

El programa 'ping' permite hacer pruebas de contectividad.

$ ping -c 1 8.8.8.8

Averguar donde se ecuentra el archiv ejecutable que se invoca cuando lanzamos el comadno 'ping'.

$ which ping
/usr/bin/ping

El interpte busca un archivo que se llame igual que el comadno en una serie de rutas predefinidas, si se encuentra el interprete ejecuta el binario (archivo ejecutable), todo lo que necesita es la ubicacion de este.

$ /usr/bin/ping -c 1 8.8.8.8

Las rutas las podemos observar imprimiendo la variable de entorno '$PATH'

$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games


Directorio '/boot/' 

En sistemas operativos basados en Linux, el directorio /boot contiene archivos necesarios para el arranque del sistema. Estos archivos incluyen el núcleo del sistema operativo (kernel), archivos de configuración del gestor de arranque (bootloader) y, en algunos casos, imágenes de inicio del sistema (initramfs).


$ ls -1 /boot/
grub/
config-6.1.0-26-amd64
initrd.img-6.1.0-26-amd64
System.map-6.1.0-26-amd64
vmlinuz-6.1.0-26-amd64


**Directorio '/etc'**

El directorio /etc es un directorio crucial en sistemas operativos basados en Unix y Linux. En el contexto del sistema de archivos de Unix/Linux, /etc se utiliza para almacenar archivos de configuración y datos específicos del sistema operativo y de servicios del sistema.  

Un programa de usuario es una aplicación que interactúa directamente con el usuario, como un navegador web o un editor de texto. Estos programas son iniciados por el usuario y pueden tener interfaces gráficas o de línea de comandos con las que el usuario interactúa, las configuraciones de estos programas de usaurios se guardan en el directorio del usuario. 

Por otro lado los servicios y otros elementos a nivel sistemea opertivo que no son de usuario se guardan en /etc, por ejemplo, los archivos dentro del directorio '/etc/pamd.d' contienen las cofniguracinoes de como funciona el servicio de autenticacion de usuarios del sistema operativo. 

$ **ls /etc/pam.d/ -1**
chfn 
chpasswd 
chsh 
common-account 
common-auth 
common-password 
common-session 
common-session-noninteractive 
... 

Si se instala el servicio de bases de datos 'msyql' las cofngiruaciones de este servidor estaran dentro del directorio 'etc'.

# apt install mariadb-server

Listar archivos de configuracion del servicio 'mariadb'.

# ls /etc/mysql/
conf.d  debian.cnf  debian-start  mariadb.cnf  mariadb.conf.d  my.cnf  my.cnf.fallback


---

Se ejecuta como usuario estandar.

$ comando

Se ejecuta con privilegios de 'root'.

# comando


Setear proxy

https://howtoforge.es/como-configurar-el-proxy-apt-en-ubuntu-20-04-lts/