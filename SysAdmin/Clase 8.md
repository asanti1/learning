**Listar variables de entorno de usuario**

  

$ **env**

```
SHELL=/bin/bash

LANGUAGE=en_US:es

PWD=/home/educacionit

LOGNAME=educacionit

XDG_SESSION_TYPE=tty

MOTD_SHOWN=pam

HOME=/home/educacionit

LANG=en_US.UTF-8

SSH_CONNECTION=192.168.0.20 37698 192.168.0.11 22

XDG_SESSION_CLASS=user

TERM=xterm-256color

USER=educacionit

SHLVL=1

XDG_SESSION_ID=1

XDG_RUNTIME_DIR=/run/user/1000

SSH_CLIENT=192.168.0.20 37698 22

PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games

DBUS_SESSION_BUS_ADDRESS=unix:path=/run/user/1000/bus

SSH_TTY=/dev/pts/0

_=/usr/bin/env
```
 
**Variables de entorno y 'sudo'**
  
La herramienta sudo en sistemas operativos Unix y Linux permite a un usuario ejecutar comandos con privilegios de superusuario o de otro usuario autorizado, sin necesidad de cambiar de sesión. Su uso es esencial para tareas administrativas o de sistema que requieren permisos elevados, como la instalación de software o la modificación de configuraciones de red.

  
\# **apt update && apt install sudo**

'**sudo**' define en el archivo **/etc/sudoers** los privilegios que los usuarios pueden alcanzar, este archivo no se edita directamente sino a través de la herramienta **visudo**.

\# **visudo**


\# **User privilege specification**

```
root    ALL=(ALL:ALL) ALL

**educacionit     ALL=(ALL:ALL) ALL**

```


$ **sudo ls**

```
[sudo] contraseña para **educacionit**: **_<contraseña de usuario>_**

dir  passwd  variables.sh

```  

**Gestion de procesos** 

**Estados de un proceso** 

En Linux, los procesos pueden estar en diferentes estados durante su ciclo de vida. Estos estados ayudan al sistema operativo a gestionar los recursos de manera eficiente y a asegurar que todos los procesos reciban tiempo de CPU de manera equitativa. Aquí están los principales estados de un proceso en Linux:   

'**R**' Ejecutable (Running o Runnable): El proceso está siendo ejecutado o está en la cola listo para ejecutarse tan pronto como se libere la CPU.


'**S**' Interrumpible Sleep: El proceso está en estado de espera debido a la espera de un evento o recurso, como la entrada/salida de datos. Este estado permite que el proceso sea interrumpido y que el control vuelva al sistema operativo, lo que permite que otros procesos se ejecuten.

'**D**' Uninterruptible Sleep: Similar al estado anterior pero en este caso, el proceso no puede ser interrumpido hasta que la condición que lo detuvo se resuelva. Esto generalmente ocurre durante operaciones críticas del kernel, como la gestión directa de dispositivos de hardware.

'**T**' Detenido (Stopped): El proceso ha sido detenido, generalmente a través de una señal como SIGSTOP. Puede ser reiniciado mediante otra señal, como SIGCONT.

'**Z**' Zombi (Zombie): El proceso ha completado su ejecución, pero aún mantiene su entrada en la tabla de procesos para que el proceso padre pueda leer su estado de salida. Un proceso zombi espera que su proceso padre recupere su código de salida, lo cual es necesario para liberar completamente los recursos del sistema asignados al proceso. En este caso al no recuperar el codigo de salida el proceso queda 'colgado'.

Ademas de los codigos que represetnan el estado de un proceso, puedes encontrar algunos modificadores que pueden aparecer junto a los códigos de estado para dar más detalles:   

'**<**' (High-priority): El proceso tiene alta prioridad.   

'**N**' (Low-priority): El proceso tiene baja prioridad.   

'**L**' (has pages locked): El proceso tiene páginas bloqueadas en memoria (usualmente para operaciones de entrada/salida).   

La lista completa de modificadores y estados la podemos obtener del manual de 'ps'.

$ **man ps**

**Administración de procesos mediante señales** 

En Linux, las señales son mecanismos de comunicación que el sistema operativo/kernel usa para notificar a un proceso sobre un evento o solicitar que realice una acción específica. Las señales pueden ser enviadas por el sistema operativo, por otros procesos o incluso por el mismo proceso.  

La manera en que un proceso reacciona ante una señal depende de si el desarrollador ha programado una respuesta específica o acción para esa señal. Algunas señales son manejables por el proceso (es decir, se puede definir una acción específica), mientras que otras no pueden ser ignoradas o manejadas, como SIGKILL. En este caso, la señal no es recibida por el proceso; el kernel la procesa directamente, lo que resulta en la terminación inmediata del proceso, sin darle oportunidad de reaccionar. 

En resumen, una señal es recibida por un proceso que actúa en función de su programa para interpretar dicha señal, también hay señales que son recibidas por el kernel y actúan sobre un proceso y este no puede ignorarlas.

**Enviar señales a un proceso** 

El comando '**kill**' puede enviar señales a un proceso siempre que le proceso este bajo el mismo usuario que invoca 'kill', salvo 'root'. 

Sintaxis: 

  $ **kill -sig pid**

**Señales comunes** 

**SIGINT 2 (Signal Interrupt)** 

Se envía cuando se presiona Ctrl+C en la terminal o '**kill -2 pid**'. Interrumpe el proceso. 

En a una shell tenemos el proceso 'ping'. 

$ **ping google.com** 

```
PING google.com (216.58.202.110) 56(84) bytes of data. 

64 bytes from gru10s13-in-f14.1e100.net (216.58.202.110)... 
```
  
En otra shell avergiguamos el 'pid' y enviamos una señal de interrupcion. 

$ **ps aux | grep ping**
```
educaci+    **1426**  0.0  0.0  10068  1128 pts/1    S+   11:51   0:00 ping google.com
```
  

$ **kill -2 1426**
Luego en la shell donde se estaba ejecutando podemos ver la siguiente salida.

  

$ **ping google.com **

```
**--- google.com ping statistics ---**

49 packets transmitted, 49 received, 0% packet loss, time 48072ms

rtt min/avg/max/mdev = 7.925/14.944/21.991/3.475 ms

  
```

Básicamente el programa al recibir la señal '2' detiene su operación e imprime un informe y finalmente se cierra con un exit '0'.

**SIGKILL 9**  

Fuerza la terminación inmediata de un proceso, sin darle oportunidad de realizar tareas de limpieza. No puede ser ignorada. Limpia el proceso de memoria, es util cuando un proceso no responde (Z)

En a una shell tenemos el proceso '**ping**'.

$ **ping google.com** 
```
PING google.com (216.58.202.110) 56(84) bytes of data. 

64 bytes from gru10s13-in-f14.1e100.net (216.58.202.110)... 

```
  
En otra shell avergiguamos el 'pid' y enviamos una señal SIGKILL. 

$ **ps aux | grep  ping**

```
educaci+    **1430**  0.0  0.0  10068  1064 pts/1    S+   11:54   0:00 ping google.com
```
  

$**kill -9** **1430**


Luego en la shell donde se estaba ejecutando podemos ver la siguiente salida.


$ **ping google.com **

```
Killed
```

No hay informe, sencillamente el programa no termino ni siguió su flujo de trabajo, solo se barrio de la memoria. 

**No usar señal 9** 

El uso indiscriminado de la señal SIGKILL (señal 9) para terminar procesos puede tener varias consecuencias negativas, ya que es una señal que no permite al proceso realizar ninguna acción antes de ser finalizado. Algunas de las posibles consecuencias son: 

**Pérdida de datos**: Al usar SIGKILL, el proceso se detiene de inmediato sin posibilidad de cerrar archivos abiertos correctamente o completar operaciones en curso como transacciones SQL, lo que puede provocar corrupción o pérdida de datos.

**Inconsistencia en el sistema**: Procesos que mantienen estados en memoria o están en medio de transacciones podrían dejar el sistema en un estado inconsistente al ser terminados bruscamente, ya que no pueden liberar recursos o realizar tareas de limpieza, esto puede llevar a la existencia de procesos zombies.

**Recursos no liberados**: Dado que el proceso no puede liberar sus recursos (como archivos abiertos, memoria asignada, o sockets de red), esto puede causar fugas de recursos que afecten el rendimiento del sistema.

**Operacion I/O**: Las típicas operaciones de lectura y escritura en disco, si el proceso esta escribiendo y se interrumpe de manera abrupta no se termina la tarea de escritura, creando inasistencias en el filesytem del volumen.

**SIGHUP 1 (Signal Hang Up)** 

Se envía cuando se cierra la terminal que inició el proceso. Se utiliza a menudo para recargar la configuración de un proceso. Por ejemplo cuando se usa el reload sobre un servicio, este fue programado para releer archivos de configuracion cuando recibe esta señal.

**tty/pts perifericos de entrada y salida (e/s o i/o)**

Cuando un usuario inicia sesión o abre una terminal en un sistema, el proceso interactúa con un tty. Este dispositivo actúa como un intermediario que permite enviar y recibir caracteres desde y hacia un dispositivo de entrada/salida, como una terminal física o un emulador de terminal. Los tty originalmente se referían a las terminales físicas conectadas a las computadoras, pero hoy en día incluyen terminales virtuales o emuladores que usamos en entornos gráficos. 

  

$ **ps aux**
```
USER         PID %CPU %MEM    VSZ   RSS **TTY**      STAT START   TIME COMMAND

www-data    1504  0.0  0.1 1212492 6976 **?**        Sl   12:16   0:00 /usr/sbin/apache2

educaci+    1583  0.2  0.1  10500  4780 **pts/0**    Ss+  12:20   0:00 -bash

educaci+    1589  0.0  0.0  10068  1120 **pts/0**    S+   12:21   0:00 ping google.com

educaci+     869  0.0  0.1  12156  5924 **pts/1**    Ss   10:17   0:00 -bash

educaci+    1571  0.0  0.1  10508  4740 **tty1**     S+   12:18   0:00 -bash

```  

**Archivos tty/pts** 

Los archivos tty/pts en Linux representan dispositivos de terminal y se encuentran en el directorio /dev/. Estos archivos corresponden a interfaces de entrada/salida (E/S) que permiten la comunicación entre los procesos del sistema y los dispositivos de terminal.