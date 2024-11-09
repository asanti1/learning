
**Fecha y hora del sistema**

Consultar estado del servicio de fecha y hora.

$ **timedatectl** 
```
               Local time: Mon 2024-10-28 10:02:39 -03
           Universal time: Mon 2024-10-28 13:02:39 UTC
                 RTC time: Mon 2024-10-28 13:02:39
                Time zone: America/Argentina/Buenos_Aires (-03, -0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```
Local Time Se refiere a la hora actual en la zona horaria configurada para el  
sistema. Es la hora que usualmente ves en tu reloj y que se ajusta según tu ubicación geográfica específica.

Time zone Indica la zona horaria configurada para el sistema. Se utiliza para  
ajustar la hora local a partir de la hora universal (UTC) según la ubicación geográfica y las reglas de horario de verano correspondientes.

Universal Time (UTC) Es la hora en el meridiano de Greenwich, sin ajustes por  
horario de verano u otras variaciones locales. La UTC es la base del tiempo en todo  
el mundo, desde la cual se calculan todas las otras zonas horarias.


RTC Time RTC significa "Real Time Clock" (Reloj de Tiempo Real), y se refiere a la  
hora mantenida por un reloj de hardware en la placa madre de tu computadora, incluso  
cuando está apagada. Este reloj suele alimentarse con una pequeña batería y normalmente se actualiza en funcion de la hora del sistema.  

NTP service Servicio de provision de hora de internet, este entrega la hora (UTC) al  
sistema operativo utilizando diferentes servidores de hora.

**Zonas horarias**

La zona horaria está representada por archivos que definen las características de la hora en dicha zona.

El sistema lee el fichero '/etc/localtime' que es un enlace simbolico al fichero que  
representa la zona horaria:  

$ **file /etc/localtime**
```
/etc/localtime: symbolic link to /usr/share/zoneinfo/America/Argentina/Buenos_Aires
```


**Laboratorio: Cambiar zona horaria**

Consultar la hora con el comando 'date' y 'timedatectl'.

Eliminar el fichero **/etc/localtime** y recrearlo utilizando un archivo de zona horaria a eleccion.

Consultar la hora con el comando **date** y **timedatectl** y comparar las salidas con el anterior.


**Hora Buenos Aires**

$ **date**
```
Mon Oct 28 10:25:44 AM -03 2024
```
$ **timedatectl** 
```
               Local time: Mon 2024-10-28 10:26:04 -03
           Universal time: Mon 2024-10-28 13:26:04 UTC
                 RTC time: Mon 2024-10-28 13:26:04
                Time zone: America/Argentina/Buenos_Aires (-03, -0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```
**Eliminar /etc/localtime**

\# **rm /etc/localtime**

Establecer **Saigon** como zona horaria

\# **ln -s /usr/share/zoneinfo/Asia/Saigon /etc/localtime**

Hora Saigon

$ **date**
```
Mon Oct 28 08:29:52 PM +07 2024
```
$ **timedatectl** 
```
               Local time: Mon 2024-10-28 20:30:11 +07
           Universal time: Mon 2024-10-28 13:30:11 UTC
                 RTC time: Mon 2024-10-28 13:30:11
                Time zone: Asia/Saigon (+07, +0700)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no

```

**Herramienta timedatectl**

Listar comandos de 'timedatectl'

$ **timedatectl**
```
list-timezones  revert  set-ntp   set-timezone     show-timesync    timesync-status  
ntp-servers      set-local-rtc    set-time         show             status 
```

**Listar zonas horarias para 'Argentina'**

$ **timedatectl list-timezones | grep Arg**
```
America/Argentina/Buenos_Aires
America/Argentina/Catamarca
America/Argentina/ComodRivadavia
America/Argentina/Cordoba
America/Argentina/Jujuy
America/Argentina/La_Rioja
America/Argentina/Mendoza
America/Argentina/Rio_Gallegos
America/Argentina/Salta
America/Argentina/San_Juan
America/Argentina/San_Luis
America/Argentina/Tucuman
America/Argentina/Ushuaia
```

**Cambiar zona horaria**

\# **timedatectl set-timezone America/Argentina/Buenos_Aires**

$ **file /etc/localtime** 
**/etc/localtime: symbolic link to ../usr/share/zoneinfo/America/Argentina/Buenos_Aires**

**Hora de internet (ntp)**

El protocolo NTP (Network Time Protocol) es un protocolo de red utilizado para sincronizar los relojes de los sistemas informáticos con una referencia de tiempo precisa, como un servidor de tiempo. NTP garantiza que todos los dispositivos en una red mantengan la misma hora, lo cual es crucial para la coordinación de tareas, registros de eventos, y otras operaciones dependientes del tiempo. NTP ajusta los relojes de los sistemas de manera gradual para evitar grandes saltos de tiempo, manteniendo la precisión y estabilidad en la sincronización.

**Servicio de ajuste de hora**

El servicio 'systemd-timesyncd' es el encargado de actualizar la hora a paritr de un servidor de hora que puede estar en el propio host o en internet o en la red local, no es el unico servicio para establcer la hora pero es el que mas se utiliza en sistemas operativos gestionados mediante systemd, otra alternativa es por ejemplo 'open-ntpd'. 

Si el servicio de hora no esta en ejeucucion o sencillamente el host no esta en red el sistema operativo mantendra la hora a partir del relog RTC y la calculara dependiendo si el RTC esta configurado como UTC o TZ.


**Consultar el estado del servicio y utilizacion**

\# **systemctl status systemd-timesyncd.service**
```
● systemd-timesyncd.service - Network Time Synchronization
     Loaded: loaded (/lib/systemd/system/systemd-timesyncd.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-10-28 09:48:43 -03; 1h 7min ago
       Docs: man:systemd-timesyncd.service(8)
...
Oct 28 09:48:45 sysadmin systemd-timesyncd[286]: Contacted time server 162.159.200.1:123 (0.debian.pool.ntp.org).

```
$ **timedatectl** 
```               Local time: Mon 2024-10-28 11:05:46 -03
           Universal time: Mon 2024-10-28 14:05:46 UTC
                 RTC time: Mon 2024-10-28 14:05:46
                Time zone: America/Argentina/Buenos_Aires (-03, -0300)
System clock synchronized: yes
              NTP service: active
          RTC in local TZ: no
```

**Mostrar servidores de hora en uso**

$ **timedatectl show-timesync**
```
FallbackNTPServers=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3.debian.pool.ntp.org
ServerName=0.debian.pool.ntp.org
ServerAddress=162.159.200.1
```

**Selección de servidores** 

Los servidores actuales son los predefinidos para la distribución pero se pueden establecer otros, por ejemplo bajo el criterio de cercanía.

Lista de servidores publicos para Argentina  

https://www.ntppool.org/es/zone/ar


**Fichero /etc/systemd/timesyncd.conf**
```
[Time]
\#NTP=
\#FallbackNTP=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3.debian.pool.ntp.org
\#RootDistanceMaxSec=5
\#PollIntervalMinSec=32
\#PollIntervalMaxSec=2048
\#ConnectionRetrySec=30
\#SaveIntervalSec=60
```

**Laboratorio: Setear servidores NTP**

Configurar **timesyncd** para utilizar como servidor de Argentina como primario y los de Debian como 'Fallback'.


Fichero **/etc/systemd/timesyncd.conf**

```
[Time]
NTP=2.ar.pool.ntp.org
FallbackNTP=0.debian.pool.ntp.org 1.debian.pool.ntp.org 2.debian.pool.ntp.org 3.debian.pool.ntp.org
```

Aplicar cambios

\# **systemctl restart systemd-timesyncd.service**

Verificar

\# **systemctl status systemd-timesyncd.service**
```
● systemd-timesyncd.service - Network Time Synchronization
     Loaded: loaded (/lib/systemd/system/systemd-timesyncd.service; enabled; preset: enabled)
     Active: active (running) since Mon 2024-10-28 11:17:56 -03; 35s ago
       Docs: man:systemd-timesyncd.service(8)
   Main PID: 1156 (systemd-timesyn)
     Status: "Contacted time server 162.159.200.123:123 (2.ar.pool.ntp.org)."
Oct 28 11:17:57 sysadmin systemd-timesyncd[1156]: Contacted time server 162.159.200.123:123 (2.ar.pool.ntp.org).

```
**Servidor NTP  **

En entornos donde el acceso a Internet es limitado o no se permite por razones de seguridad, un servidor NTP interno (como otros) asegura que todos los dispositivos mantengan una sincronización de tiempo adecuada sin necesidad de acceso externo.

**Instalar servidor NTP  **

El servidor ntp sera una nueva VM dentro de la red donde se encuentran los sistemas clientes.

Si el sistema operativo tiene algún servicio de hora en funcionamiento que pueda entrar en conflicto debe ser desactivado, **systemd-timesyncd** es un cliente que se ejecuta como servicio.

 
Instalar paquete  

\# **apt update && apt install ntpsec**


**Configuracion relay**

Este servidor obtiene la hora de otros servidores de internet usando la directiva 'pool'.

Fichero de configuración **/etc/ntpsec/ntp.conf**

```
# Servidores de hora de referencia  
pool 2.ar.pool.ntp.org iburst  
pool 3.south-america.pool.ntp.org iburst  
pool 0.south-america.pool.ntp.org iburst  
pool 3.debian.pool.ntp.org iburst  
restrict default kod nomodify nopeer noquery limited  

# Este paremetro establece desde donde se permiten  
# las solicitudes  
restrict 127.0.0.1  
# Se añade la red local  
restrict 192.168.0.0 mask 255.255.255.0  
restrict ::1 
```

Aplicar cambios  

Una vez editado el fichero reniciamos el servicio.  

\# **systemctl restart ntpsec** 

Implementacion

Sponiendo que el servidor 'ntp' de la red local tiene la direccion '192.168.0.96' podemos usar desde un cliente el comando 'ntpdate'.

\# **apt install ntpdate**

$ **ntpdate 192.168.0.96**
```
2024-10-28 11:49:50.55952 (-0300) +0.023342 +/- 0.000201 192.168.0.96 s2 no-leap
```

Si queremos implementarlo hay que pasar la **ip** al servicio de sincronizacion de hora del sistema operativo cliente.


**Configuracion RTC para ntpsec**
```
# ntpsec rtc

# /etc/ntpsec/ntp.conf

driftfile /var/lib/ntpsec/ntp.drift

leapfile /usr/share/zoneinfo/leap-seconds.list

tos maxclock 11

tos minclock 4 minsane 3

server 127.127.1.0 # Usa el reloj local (RTC) como fuente de tiempo

fudge 127.127.1.0 stratum 10 # Establece el stratum en un valor alto para indicar que es una fuente interna
```

**Gestion de hora**


Establecer hora de manera manual 

Siempre que el servicio de hora esté disponible, el sistema tomará la hora de un servidor de red. Si el servicio no está disponible, el sistema utilizará la hora del RTC (Real-Time Clock). El RTC se mantiene sincronizado con el servicio de hora del sistema y funciona gracias a una batería o fuente de energía que permite mantener el reloj en funcionamiento incluso cuando el sistema está apagado. Sin embargo, si el RTC no tiene batería y el sistema se apaga, al encenderse nuevamente, el RTC podría reiniciarse a las 00:00 del 1 de enero de 1970. En ese caso, la hora se actualizará a la correcta cuando el servicio de hora de red esté disponible.  

**Establecer hora del sistema**  

\# **timedatectl set-time "2023-11-17 14:30:00"**


Sincronizar rtc respecto del sistma 

Del sistema al relog de hardware.  

\# **hwclock --systohc**

Del reloj de hardware al sistema

\# **hwclock --hctosys**

Establecer hora con el comando 'date '

\# **date MMDDhhmm[[CC]YY][.ss]**

MM representa el mes.  
DD representa el día.  
hh representa la hora en formato de 24 horas.  
mm representa los minutos.  
CC representa el primer dígito del año (opcional).  
YY representa los últimos dos dígitos del año.  
.ss representa los segundos (opcional).  

Para establecer la fecha y la hora a "17 de noviembre de 2023, 14:30:00"  

       MMDDhhmmCCYY.ss
\# **date 111714302023.00**

Comando date y formato de hora

$ **date**
```
Mon Oct 28 12:16:00 PM -03 2024
```

El comando date permite especificar el formato de salida con la opción +, seguida del formato deseado. Algunos de los especificadores de formato más comunes incluyen:

%Y - Año (por ejemplo, 2023)  
%m - Mes (01-12)  
%d - Día del mes (01-31)  
%H - Hora (00-23)  
%M - Minuto (00-59)  
%S - Segundo (00-59)  
%A - Nombre completo del día de la semana (por ejemplo, Sunday) %a - Nombre abreviado del día de la semana (por ejemplo, Sun) %B - Nombre completo del mes (por ejemplo, September)  
%b - Nombre abreviado del mes (por ejemplo, Sep)

**Fomrato de hora ISO 8601**

Para registrar eventos y luego cargar los datos en una base de datos, el formato de timestamp más utilizado es el formato ISO 8601, que tiene la ventaja de ser estandarizado y ampliamente soportado por muchas bases de datos y lenguajes de programación:
    
**YYYY-MM-DDTHH:MM:SSZ**
        
YYYY es el año. 
MM es el mes (01-12). 
DD es el día del mes (01-31). 
T es el separador entre la fecha y la hora. 
HH es la hora en formato de 24 horas (00-23). 
MM son los minutos (00-59). 
SS son los segundos (00-59). 
Z indica el uso de la zona horaria UTC. Si quieres incluir tu zona horaria local, en lugar de Z se podría usar +/-HH:MM para indicar el desfase horario.

**Mostrar hora 'ISO 8601' en TZ**

$ **date +"%Y-%m-%dT%H:%M:%S%Z"**
2024-10-28T12:18:53-03

$ **date +"%Y-%m-%dT%H:%M:%S"**
2024-10-28T12:19:09

En UTC 'ISO 8601'

$ **date -u +"%Y-%m-%dT%H:%M:%S%Z"**
2024-10-28T15:21:16UTC

$ **date -u +"%Y-%m-%dT%H:%M:%S"** 
2024-10-28T15:22:01

**Laboratorio: Script de gestion de paquetes**

Crear un script que al ejecutarlo realice ciertas tareas de mantimiento de paquetes y actualizcion completa del sistema. 


**Uso de apt**

'**update**' mantiene la base de datos de software disponible en repositorios actualizada.
'**install**' instala las dependencias de paquetes faltantes.
'**upgrade**' actualiza todo el software del sistema gestionado por apt.
'**autoremove**' elimina paquetes huerfanos, dependencias no utilizadas.

**Fichero 'apt-maintainer.sh'**

```
#!/bin/bash

log=/var/log/apt_maintainter.log

echo "$(date +"%Y-%m-%dT%H:%M:%S%Z") - Inicia script de mantenimiento de paquetes..." >> $log

apt update

echo "$(date +"%Y-%m-%dT%H:%M:%S%Z") - Base de datos de paquetes actualizada." >> $log

apt install -f -y

echo "$(date +"%Y-%m-%dT%H:%M:%S%Z") - Instaladas dependencias falatantes." >> $log

apt upgrade -y

echo "$(date +"%Y-%m-%dT%H:%M:%S%Z") - Actualizado el sistema." >> $log

apt autoremove -y

echo "$(date +"%Y-%m-%dT%H:%M:%S%Z") - Eliminadas dependendencias huerfanas." >> $log

echo "$(date +"%Y-%m-%dT%H:%M:%S%Z") - Terminado." >> $log
```


\# **bash apt-maintainer.sh**
```
2024-10-28T12:36:37-03 - Inicia script de mantenimiento de paquetes...
2024-10-28T12:36:39-03 - Base de datos de paquetes actualizada.
2024-10-28T12:36:40-03 - Actualizado el sistema.
2024-10-28T12:36:43-03 - Eliminadas dependencias huérfanas.
2024-10-28T12:36:43-03 - Terminado.
```
Ver logs

\# **cat /var/log/apt_maintainter.log**
```
2024-10-28T12:36:37-03 - Inicia script de mantenimiento de paquetes...
2024-10-28T12:36:39-03 - Base de datos de paquetes actualizada.
2024-10-28T12:36:39-03 - Instaladas dependencias faltantes.
2024-10-28T12:36:40-03 - Actualizado el sistema.
2024-10-28T12:36:43-03 - Eliminadas dependencias huérfanas.
2024-10-28T12:36:43-03 - Terminado.
```

**Formato 'epoch'**

El formato epoch (también conocido como Unix timestamp) es una forma de representar puntos en el tiempo como la cantidad de segundos que han transcurrido desde una fecha de referencia fija: el 1 de enero de 1970 a las 00:00:00 UTC. Esta fecha se llama "Epoch" o "Unix epoch". 


$ **date +%s**
```
1730129969
```

Convertir a formato hora  UTC

$ **date -u -d @1730129969**
```
Mon Oct 28 03:39:29 PM UTC 2024
```

Convertir a formato hora TZ local

$ **date -d @1730129969**
```
Mon Oct 28 12:39:29 PM -03 2024
```

