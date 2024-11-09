
**Directorio '/root'**

Este directorio es el directorio de usuario (home) del usuario 'root'.


**Directorios '/sbin'**

Al igual que los directorios 'bin' estos contienen los ejecutables de diferentes programas, la diferencia es que solo el usuario 'root' tiene acceso a estos binarios.

Instalar 'iptables'

\# **apt install iptables**

Ejecutar iptables como usuario estándar

$ **iptables -L**
-bash: iptables: orden no encontrada

Ejecutar iptables como usuario 'root'

\# **iptables -L**
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
...

Imprimir la variable de entorno '$PATH' como 'root' y como usuario estandar

$ **echo $PATH**
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games

\# **echo $PATH**
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

Mostrar la ruta en la que se encuentra el binario 'iptables'

\# **which iptables**
/usr/sbin/iptables

Mostra permisos del binario 'iptables'

\# **ls -l $(which iptables)**
lrwxrwxrwx 1 root root 26 ene 16  2023 /usr/sbin/iptables -> /etc/alternatives/iptables

\# **ls -l /etc/alternatives/iptables**
lrwxrwxrwx 1 root root 22 oct 14 10:35 /etc/alternatives/iptables -> /usr/sbin/iptables-nft

\# **ls -l /usr/sbin/iptables-nft**
lrwxrwxrwx 1 root root 17 ene 16  2023 /usr/sbin/iptables-nft -> xtables-nft-multi

\# **ls -l /usr/sbin/xtables-nft-multi**
-rwxr-xr-x 1 root root 228512 ene 16  2023 /usr/sbin/xtables-nft-multi

Ejecutar el binario/enlace 'ipatbles' como usuario estandar

$ **/usr/sbin/iptables -L**
iptables v1.8.9 (nf_tables): Could not fetch rule set generation id: Permission denied (you must be root)


**Directorio '/var'**

El directorio /var en el sistema de archivos de Linux y otros sistemas Unix es donde se almacenan archivos variables, es decir, datos que cambian con el tiempo o durante la operación del sistema. Esto incluye archivos de registro (logs), bases de datos temporales, archivos de spool (como trabajos de impresión), archivos de caché, directorios de datos de servidores y otros datos dinámicos.

Algunos subdirectorios importantes dentro de /var son:
**/var/log:** Almacena archivos de registro del sistema, como logs de arranque o errores.
**/var/spool:** Contiene trabajos en cola para procesos como impresión, correo o tareas programadas.
 **/**var/tmp:** Directorio temporal similar a /tmp, pero los archivos pueden persistir por más tiempo.
 **/var/cache:** Almacena datos temporales de aplicaciones, como caché de paquetes.
 **/var/lib/mysql:** Directorio de datos del servidor 'mariadb'.


Listar procesos del sistema

$ **ps aux**
```
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.2  0.2 101940 11948 ?        Ss   10:05   0:00 /sbin/init
mysql        530  0.0  5.9 1275696 239476 ?      Ssl  10:05   0:00 /usr/sbin/mariadbd
educaci+     623  0.6  0.1   7968  4776 pts/0    Ss   10:09   0:00 -bash
```

**PID** ID de proceso.
**root** usuario administrador del sistema.
**mysql** usuario de servicios con privilegios limitados.
**educacionit** usuario estandar.

**Librerias del sistema**

Las librerías en los sistemas operativos son conjuntos de funciones o código predefinido que los programas usan para realizar tareas comunes, sin tener que escribir ese código desde cero.

**Configuracion de red**

En Linux, la configuración de red depende tanto de la distribución como del servicio de red instalado. En el caso de Debian sin entorno gráfico, el sistema suele usar el servicio "networking", donde las configuraciones de red se gestionan a través del archivo /etc/network/interfaces. Aquí se especifican los parámetros de las interfaces de red, como si deben obtener una dirección IP de manera dinámica o usar una dirección estática.

Cuando Debian se instala con un entorno de escritorio, es común que se utilice "NetworkManager", una herramienta más avanzada que facilita la administración de redes a través de una interfaz gráfica o comandos más intuitivos. Esta herramienta es particularmente útil para gestionar conexiones Wi-Fi, VPNs y conexiones Ethernet de manera dinámica.

Identificar iterfaces de red

$ **ip a**
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 ...
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    ...


2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    link/ether 08:00:27:b5:bd:51 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.57/24 brd 192.168.0.255 scope global dynamic enp0s3
    ...    
```
**lo** La interfaz de loopback es una interfaz de red virtual dentro del propio sistema operativo. Se utiliza para que el sistema se comunique consigo mismo.

La dirección de loopback más común es 127.0.0.1, y siempre apunta al propio equipo, permitiendo utilizar servicios de red localmente sin necesidad de una conexión externa.

**enp0s3** Interfaz de red fisica instalada en el sistema, la nomeclatura o nombre depende del tipo de interfaz, la tecnologia de socket, etc. Otros nombres comunes son 'eno1' o 'eno2' en el caso que hubiesen dos interfaces fisicas o puede ser 'eth0' donde el numero crece segun la cantidad de interfaces.


**Configuracion de la interfaz 'enp0s3'**

En el caso de Debian sin entorno gráfico, el sistema suele usar el servicio "networking.service", donde las configuraciones de red se gestionan a través del archivo /etc/network/interfaces.

$ **cat /etc/network/interfaces | grep -v "#"**

source /etc/network/interfaces.d/*

#\ **Configuracion de la interfaz lo**
auto lo
iface lo inet loopback

\# **Configuracion de la interfaz enp0s3**
allow-hotplug enp0s3  # se declara la interfaz y su comporamiento
iface enp0s3 inet dhcp # se define la configuracion de red de la intefaz

**Configuracion de red estatica**

Todo host para estar en una red necesita de 2 parametros obligatorios y luego otros opcionales:
```
    - Direccion de red, obligatorio.
    - Mascara de red, obligatorio.
    - Gateway, opcional.
    - DNS, opcional.
```

Ejemplo de configuracion estatica

Adaptar los parametros segun la red local.

iface enp0s3 inet static
address 192.168.0.11
netmask 255.255.255.0
gateway 192.168.0.1


Con el comando 'ip' se pueden obtener los parametros obtenidos por dhcp y aplicarlos de manera estatica.

Direccion de red y mascara

$ **ip a**
```
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    link/ether 08:00:27:b5:bd:51 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.57/24 brd 192.168.0.255 scope global dynamic enp0s3
    
```
Gateway

$ **ip route**
```
default via 192.168.0.1 dev enp0s3 
192.168.0.0/24 dev enp0s3 proto kernel scope link src 192.168.0.57 
```

Fichero '/etc/network/interfaces' completo

```
# This file describes the network interfaces available on your system

# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface

auto lo

iface lo inet loopback

# The primary network interface

allow-hotplug enp0s3

#iface enp0s3 inet dhcp

iface enp0s3 inet static

address 192.168.0.11

netmask 255.255.255.0

gateway 192.168.0.1
```

source /etc/network/interfaces.d/*

auto lo
iface lo inet loopback

allow-hotplug enp0s3
iface enp0s3 inet static
address 192.168.0.11
netmask 255.255.255.0
gateway 192.168.0.1


Aplicar cambios

Reiniciar el servicio de red, esto va a desconectar de red al sistema. La herramienta 'systemctl' me permite gestiar los servicios del sistema.

\# **systemctl restart networking.service**

Es posible que este comando no aplique los cambios, por otro lado el sistema puede tener varias interfaces de red, reiniciar el servicio implica interrumpir todas las conexiones establecidas en esas interfaces. La solucion es aplicar los cambios a una interfaz en concreto.

Detener interfaz

\# **ifdown enp0s3**

Iniciar interfaz

\# **ifup enp0s3**


**Hostname**

El hostname es el nombre asignado a un dispositivo dentro de una red para identificarlo de manera única. Funciona como un identificador que permite distinguir un dispositivo de otros en la misma red, facilitando la comunicación y administración. En sistemas operativos como Linux, el hostname se utiliza tanto para propósitos de red como para la personalización del sistema. 


El nombre de host se establece en el fichero '/etc/hostname'. 

\# **cat /etc/hostname**
Debian 

El nombre de host se puede cambiar editando el archivo o utilizando una herramienta de 
**systemd**.

**sysadmin** Nombre de host a establecer.

#\ **hostnamectl set-hostname sysadmin**

\# **cat /etc/hostname**
sysadmin

# reboot

No importa el Sistema Operativo siempre despues de un cambio de nombre de host hay que reiniciar el sistema por varias razones: 

 - Variables de entorno $HOSTNAME del sistema no se actualizan al mismo nombre.
 - Otros procesos pueden conservar el valor anterior de la variable $HOSTNAME
 - Otros sistemas operativos de la red todavía reconocen al host con el nombre anterior



Servidores DNS, fichero **/etc/resolv.conf**

El archivo /etc/resolv.conf en sistemas Linux contiene la configuración de los servidores DNS que se usarán para resolver nombres de dominio en direcciones IP. Dentro de este archivo se especifican los servidores DNS a los que el sistema enviará solicitudes para traducir los nombres de dominio, como www.ejemplo.com, en direcciones IP. Las líneas más comunes en este archivo son nameserver seguidas de la dirección IP de un servidor DNS. 

$ **cat /etc/resolv.conf**
```
domain home
search home
nameserver 200.115.192.29
nameserver 190.55.154.133
nameserver 181.47.254.164
```

Utilizar servidores de cloudflare y google..

Fichero '/etc/resolv.conf'

```
nameserver 1.1.1.1 # servidor primario
nameserver 8.8.8.8
```


Herramienta **dig**

Este comando permite realizar resoluciones **dns** y obtener estadísticas.

$ **dig educacionit.com**

```
ANSWER SECTION:
educacionit.com.        60        IN        A        54.207.106.237

Query time: 23 msec
SERVER: 1.1.1.1#53(1.1.1.1) (UDP)
WHEN: Mon Oct 14 12:20:08 -03 2024
MSG SIZE  rcvd: 60
```


Fichero **/etc/hosts**

El archivo /etc/hosts es utilizado para mapear nombres de dominio a direcciones IP de manera local, sin necesidad de consultar un servidor DNS externo. En este archivo, puedes definir manualmente qué nombres de dominio o hostnames corresponden a qué direcciones IP, lo que permite resolver esos nombres localmente en tu sistema.

$ **cat /etc/hosts**
```
127.0.0.1        localhost
127.0.1.1        debian
```

**debian** Durante la instalación se determino el nombre de host **debian** y se mapeo con la dirección '127.0.1.1' que también es una dirección de **localhost** (en realidad toda dirección dentro de la red 127.0.0.0/8).

Se ha cambiado el nombre de host pero esto no afecta al fichero '/etc/hosts', podemos mantener la congruencia adaptándolo a estos cambios.

$ **cat /etc/hosts**
```
127.0.0.1        localhost
#127.0.1.1        debian
127.0.1.1        sysadmin
```