**Directorios, units y targets**

Un target en systemd es un grupo de unidades que representan un estado o una meta particular del sistema. Se utilizan para organizar y gestionar el arranque del sistema, estableciendo en qué orden y cuáles servicios deben iniciarse. Los targets permiten definir diferentes estados del sistema, como "multi-user.target" (para un sistema multiusuario sin entorno gráfico) o "graphical.target" (que incluye la interfaz gráfica).

Entonces un target es un conjunto predefinido de servicios (o unidades). Estos targets no ejecutan directamente los servicios, pero sí coordinan qué servicios deben estar activos para cumplir una función o estado. Por ejemplo, "multi-user.target" agrupa todos los servicios necesarios para un entorno de usuario sin lanzar el servicio de interfaz gráfica.

Un target típico es el 'rescue mode' que no incluye todos los servicios habituales de un sistema operativo completo, sino los servicios mínimos que me permitirían realizar tareas de mantenimiento.


Un unit en systemd es el componente básico que systemd administra. Las "units" representan diferentes tipos de recursos o servicios que systemd puede controlar. Pueden ser servicios, sockets, dispositivos, montajes de sistemas de archivos, entre otros.

Cada unit tiene un archivo de configuración asociado que define cómo y cuándo debe iniciarse, detenerse o reiniciarse, así como sus dependencias y comportamiento. Estas units se clasifican según su tipo, por ejemplo:
   
- .service para servicios del sistema.
- .socket para sockets de red.
- .mount para puntos de montaje.
- .target para agrupar varias units, como mencionamos antes.

Un target representa  un conjunto de units que se cargan al inicio del sistema.

**Laboratorio: Units y targets**

Estado del servicio

Consultar si el servicio apache2 se inicia con el sistema.

\# **systemctl is-enabled apache2.service**
```
enabled
```

El servicio se inicia con el sistema.

**Desactivar el servicio**

\# **systemctl disable apache2.service**

```
Executing: /lib/systemd/systemd-sysv-install disable apache2
Removed "/etc/systemd/system/multi-user.target.wants/apache2.service".
```
El mensaje indica que el unit 'apache2.service' es removido del directorio 'multi-user.target.wants', este directorio representa el target 'multi-user' y systemd 'enciende' todos los servicios que tengan su fichero unit en ese directorio.

**Activar servicio al inicio**

\# **systemctl enable apache2.service**

```
Created symlink /etc/systemd/system/multi-user.target.wants/apache2.service → /lib/systemd/system/apache2.service.

'/etc/systemd/system/multi-user.target.wants/apache2.service' enlace simbolico.
'/lib/systemd/system/apache2.service' archivo original/destino.
```
Si systemd lee un directorio (target) y lanza los units que tenga dentro lo mejor es que se usen enlaces simbólicos que se pueden crear y eliminar sin afectar los originales.


Contenido del directorio **/etc/systemd/system/multi-user.target.wants/**

\# **ls -1 /etc/systemd/system/multi-user.target.wants/**
```
apache2.service
console-setup.service
cron.service
e2scrub_reap.service
mariadb.service
networking.service
remote-fs.target
ssh.service
```
Servicios en ejeuccion

```
systemd─┬─agetty
        ├─apache2───2*[apache2───26*[{apache2}]]
        ├─cron
        ├─dbus-daemon
        ├─mariadbd───7*[{mariadbd}]
        ├─sshd───sshd───sshd───bash───su───bash───pstree
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-timesyn───{systemd-timesyn}
        └─systemd-udevd
        
        
```
Seguimiento de enlaces

\# **ls -l /etc/systemd/system/multi-user.target.wants/**

```
total 0
lrwxrwxrwx 1  apache2.service -> /lib/systemd/system/apache2.service
lrwxrwxrwx 1  console-setup.service -> /lib/systemd/system/console-setup.service
lrwxrwxrwx 1  cron.service -> /lib/systemd/system/cron.service
lrwxrwxrwx 1  e2scrub_reap.service -> /lib/systemd/system/e2scrub_reap.service
lrwxrwxrwx 1  mariadb.service -> /lib/systemd/system/mariadb.service
lrwxrwxrwx 1  networking.service -> /lib/systemd/system/networking.service
lrwxrwxrwx 1  remote-fs.target -> /lib/systemd/system/remote-fs.target
lrwxrwxrwx 1  ssh.service -> /lib/systemd/system/ssh.service
```

**/lib/systemd/system/** Este directorio contiene todos los ficheros de units y targets que serán utilizados en el modo de arranque correspondiente mediante el uso de enlaces.


**Laboratorio: Desactivacion/activación manual**

\#  **systemctl is-active apache2**
```
active
```

\#  **systemctl is-enabled apache2**
```
enabled
```

**Eliminar enlace del target**

\# **rm /etc/systemd/system/multi-user.target.wants/apache2.service **

\# **reboot**

**Consultar estado del servicio**

\# **systemctl status apache2**
```
○ apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; disabled; preset: enabled)
     Active: inactive (dead)
       Docs: https://httpd.apache.org/docs/2.4/
```

**Recrear enlace**

\# **ln -s /lib/systemd/system/apache2.service /etc/systemd/system/multi-user.target.wants/apache2.service**

\# **reboot**

**Consultar estado del servicio**

\# **systemctl status apache2**
```
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; preset: enabled)
     Active: active (running) since Thu 2024-10-2
     
     
```
**Comando systemctl daemon-reload**

Normalmente siempre usaremos las herramientas de systemd, si el caso es que se editaron algunos ficheros de configuración que gestiona systemd, eliminado o recreado enlaces, etc. usamos luego de la modificación el comando 'daemon-reload'.

El comando **systemctl daemon-reload** le indica a systemd que recargue sus configuraciones. Esto es necesario cuando se realizan cambios en los archivos de configuración de las unidades (units) o se crean nuevas, ya que systemd carga estos archivos en memoria al arrancar el sistema.


**Archivos .service**

Estos archivos contienen los parámetros administrativos de un servicio, es lo que usa systemd y su herramienta systemctl para controlarlos:
    
**Fichero /lib/systemd/system/mi-servicio.service**
```
[Unit]
Description=Mi Servicio Ejemplo
\# Idica que el servicio se ejecuta luego de haberse iniciado
\# el target 'network.target'
After=network.target

[Service]
\# ruta al ejecutable del servicio
ExecStart=/usr/bin/mi-aplicacion
\# Politicas de reinicio
Restart=on-failure

[Install]
\# En que target se carga este servicio
WantedBy=multi-user.target

```
**Niveles de ejecución**

Los targets en systemd funcionan como una jerarquía o una cadena de dependencias. Es decir, un target puede incluir otros targets para alcanzar un estado más completo del sistema. Esto permite que los servicios se inicien en el orden correcto según las necesidades del sistema.

multi-user.target incluye network.target, lo que significa que, para que el sistema llegue al estado de "multi-usuario", primero debe asegurarse de que la red esté operativa. En este estado, el sistema está preparado para manejar varios usuarios, pero sin interfaz gráfica.

graphical.target incluye tanto multi-user.target como network.target. Esto significa que para alcanzar el entorno gráfico, el sistema necesita tener la red funcionando y todos los servicios necesarios para un entorno multiusuario. Luego, se carga la interfaz gráfica.


**Gestion de targets (run levels) **

Los sistemas operativos, como los que utilizan systemd, se organizan en estados que agrupan diferentes servicios. Cada estado define qué servicios se deben iniciar o detener, y en ese sentido funcionan de manera similar a los antiguos run leves de SysVinit. 

**Obtener target actual** 

Actualmente el sistema esta prerada para arrancar en un target que incluye el servicio grafico, solo que este servicio no esta instalado en el sistema, como el target es la uma de network y multi-user pero sin el grafico instalado nos queda como si fuese multi-user. En general los SO vienen seteados para funcionar en el target mas alto aunque este no contenga los servicios tipos correspondientes al target.

\# **systemctl get-default**
```
graphical.target
```

**Cambiar target a multi-user** 

El comando cambia el target predeterminado del sistema. 

\# **systemctl set-default multi-user.target**
```
Created symlink /etc/systemd/system/default.target → /lib/systemd/system/multi-user.target.
```
\# **reboot**


**Otros targets** 

https://www.tecmint.com/change-runlevels-targets-in-systemd/

**Rescue target** 

**rescue.target** en systemd es un modo de recuperación similar al modo monousuario. Inicia el sistema con los servicios mínimos necesarios para poder realizar tareas de mantenimiento o reparación, permitiendo el acceso solo a un usuario administrador, los servicios que se cargan en este target están determinados por quien desarrolla la distribución. 

**Poweroff/reboot target**

**poweroff.target** Ejecuta los scripts que detienen los servicios y apaga el sistema.

**reboot.target**  ejecuta los scripts que detienen los servicios y reinicia el sistema.


**Cambiar de target en 'caliente'**

Cambia a un target sin necesidad de reiniciar el sistema. Esta practica no es recomendable, salvo situaciones particulares.

\# **systemctl isolate rescue.target**


Como parámetros del kernel

Editamos los parámetros desde el emnuentry de grub2: 

    linux /boot/vmlinuz-... ro quiet systemd.unit=rescue.target
    
Iniciar con F10.

**Utilizando runlevels**

Si bien los runlevels en la mayoría de los sistemas no se utiliza mas systemd guarda retrocompatibilidad:

\# **systemctl isolate rescue.target**


\# **init 1**

Como parametro del kernel

```
linux /boot/vmlinuz-... ro quiet 1
```


**Reiniciar el sistema usando sysvinit**

En lugar de **systemctl isolate reboot.target.**

\# **init 6**
