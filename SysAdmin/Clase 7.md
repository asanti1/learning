**Proceso**

  
Un **proceso** es una instancia de un programa en ejecución. Representa la ejecución dinámica de un conjunto de instrucciones por parte del sistema operativo, incluyendo todos los recursos necesarios para su funcionamiento, como memoria, registros de CPU y archivos abiertos. Cada proceso tiene un contexto, lo que significa que el sistema operativo gestiona información como el estado actual de la ejecución, las variables y el entorno. Además, los procesos pueden estar en diferentes estados, como ejecutándose, esperando o finalizando, dependiendo de si están utilizando la CPU o esperando algún evento externo.

En esencia, un proceso es más que el simple código del programa; es una entidad completa que el sistema operativo gestiona para ejecutar y coordinar las tareas del software de manera eficiente y segura.

Los sistemas operativos administran los procesos de manera que puedan ejecutarse de manera eficiente y segura. Esto implica la asignación de recursos, la planificación de la ejecución de procesos en la CPU, la gestión de la comunicación entre procesos y la protección de la memoria para evitar que un proceso dañe el espacio de memoria de otro proceso.

Los procesos pueden ser de varios tipos, incluyendo procesos de usuario (iniciados por usuarios) y procesos del sistema (iniciados por el sistema operativo). Además, los procesos pueden estar en diferentes estados, como activo, en espera, suspendido o terminado, dependiendo de su actividad y estado actual de ejecución. 


Los procesos, al igual que los archivos, tienen un propietario (usuario) y pertenecen a un grupo. Esto es crucial porque determina los privilegios que el proceso puede tener sobre la estructura de archivos y directorios, de acuerdo con el sistema de permisos de Unix. El propietario y el grupo de un proceso influyen en las operaciones que puede realizar, como leer, escribir o ejecutar archivos y acceder a ciertos recursos del sistema.  

**Listar procesos**

El comando '**ps**' es una herramienta para mostrar procesos en ejecución en el sistema.

Sin ningún parámetro muestra información de la shell en uso (que es un proceso) y los procesos hijos.

$ **ps**

```
    **PID TTY          TIME CMD**

    848 pts/0    00:00:00 bash

    853 pts/0    00:00:00 ps
```

Todo proceso tiene un identificador o '**pid**' (process id), la terminal al a que esta asociado o '**tty/pts**', dueño o 'user', estado de ejecución o 'stat', etc.

$ **ps aux | head -2**
```
**USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND**

**root**           1  0.0  0.3 167608 12060 **?**        **Ss**   09:51   0:01 **/sbin/init**

**www-data**     643  0.0  0.1 1212492 6876 **?**        **Sl**   09:51   0:00 **/usr/sbin/apache2**

**educaci+**     848  0.0  0.1  10500  4892 **pts/0**    **Ss**   10:18   0:00 **-bash**
```
  
**Arbol de procesos**
  
El comando '**pstree**' permite mostrar el árbol de procesos.

**Mostrar procesos hijos.**

$ **ps aux | grep apache2**
```
root         **540**  ... ?        Ss   09:51   0:00 /usr/sbin/apache2 -k start

www-data     643  ... ?        Sl   09:51   0:00 /usr/sbin/apache2 -k start

www-data     644  ... ?        Sl   09:51   0:00 /usr/sbin/apache2 -k start

  
```

**Mostrar árbol del procesos '540'**

$ **pstree 540**

```
apache2───2*[apache2───26*[{apache2}]]
```
  

**Mostrar árbol del procesos '540' y los pids**

$ **pstree -p 540**

```
apache2(**540**)─┬─apache2(**643**)─┬─{apache2}(**648**)

             │              ├─{apache2}(**649**)

             │              └─{apache2}(**689**)

             └─apache2(**644**)─┬─{apache2}(**647**)

                            ├─{apache2}(**697**)

                            └─{apache2}(**698**)
  
```

**Mostrar arbol del procesos '540', los pids y los padres**

$ **pstree -ps 540**
```
**systemd(1)**───apache2(540)─┬─apache2(643)─┬─{apache2}(648)

                          │              ├─{apache2}(649)

                          └─apache2(644)─┬─{apache2}(647)

                                         ├─{apache2}(654)

                                         ├─{apache2}(697)

                                         └─{apache2}(698)

  
```

**Mostrar el proceso 1 y la primer generacion de procesos hijos**

$ **pstree** 
```
systemd─┬─agetty

        ├─apache2───2*[apache2───26*[{apache2}]]

        ├─cron

        ├─dbus-daemon

        ├─mariadbd───7*[{mariadbd}]

        ├─sshd───sshd───sshd───bash───pstree

        ├─systemd───(sd-pam)

        ├─systemd-journal

        ├─systemd-logind

        ├─systemd-timesyn───{systemd-timesyn}

        └─systemd-udevd

  
```

**Laboratorio: Arbol del procesos**

Obtener el '**pid**' de la shell en uso y mostrar el árbol de procesos de esta.

$ **ps**

```
    **PID** TTY          TIME CMD

    **848** pts/0    00:00:00 bash

    955 pts/0    00:00:00 ps

```  

$ **pstree -ps 848**

```
systemd(1)───sshd(504)───sshd(833)───sshd(847)───bash(848)───pstree(958)
```

**Espacio de memoria**

El **espacio de memoria** se refiere a la cantidad de memoria asignada por el sistema operativo a un proceso para que pueda almacenar y gestionar sus datos mientras se ejecuta. Incluye áreas como el código del programa, las variables (tanto globales como locales), el **heap** (donde se asigna memoria dinámica), y la pila de ejecución **(stack)**, que se usa para el control de funciones y almacenamiento temporal de datos. Este espacio garantiza que cada proceso tenga su propio entorno aislado para funcionar correctamente sin interferir con otros procesos en el sistema.

**Procesos padres y procesos hijos**

Un **proceso padre** es aquel que crea o inicia otro proceso, llamado **proceso hijo**. Esta relación se establece en el momento en que un proceso utiliza una llamada al sistema, como fork() en Unix/Linux, para crear un nuevo proceso. El proceso hijo hereda ciertos recursos y atributos del proceso padre, como variables de entorno y archivos abiertos, pero se ejecuta de forma independiente.

En la jerarquía de procesos, esta relación siempre existe en sistemas operativos que soportan multitarea, lo que significa que todo proceso, excepto el primero creado por el sistema (como init o systemd en Linux), es un hijo de algún otro proceso. Así, los procesos se organizan en una estructura de árbol, donde siempre hay un vínculo claro entre procesos padres e hijos.

Los procesos hijos pueden ser adoptados por `systemd` si el proceso padre se cierra, pero esto depende del tipo de proceso; en algunos casos, los hijos también se cerrarán junto con el padre.

**Procesos y variables**  

Cuando un proceso almacena variables en su espacio de memoria, estas no son accesibles por otros procesos, son variables locales. Sin embargo, si un proceso permite la exportación de sus variables, es decir, que los procesos hijos hereden las variables del proceso padre, estas se conocen como variables de entorno. El entorno de un proceso incluye las variables que ha generado y exportado, así como el árbol de procesos hijos que heredan estas variables.  Las variables de entorno de usuario contienen información específica para un usuario en particular. Estas variables residen en el espacio de memoria asignado a la shell con la cual el usuario interactúa con el sistema. 

Por lo tanto, cuando un usuario inicia una sesión de shell, las variables de entorno se cargan en la memoria de esa sesión, permitiendo que los comandos y procesos ejecutados dentro de esa shell accedan a ellas.  

**Variables locales**
Se crear y se le dan valor en el espacio de memoria de un proceso,

**shell '848'**

$ **var="hola mundo!"**

$ **echo $var**

```
hola mundo!
```

**shell '915'**

$ **echo $var**

```
_(vacio)_
```

**Variables de entorno**

**Variables de entorno del sistema**

Se crean al inicio del sistema y son heredadas por todos los proceos del sistema, estas variable suelen tener informacion como el nombre de host ($HOSTNAME) o regionalizacion ($LANG). Como se crean al inicio del sistema normalmetne cuando estas son modificadas se debe reiniciar el sistema porque los proceos que ya la hayan importado no relfejan los cambios de manera dinamica.

La variable entonces se replica en multiples espacios de memoria, si se modifica en uno no se modifica en otro.
  
**shell '848'**

$ **echo $HOSTNAME**

```
sysadmin
```

Alterar el valor de la variable,
  
$ **HOSTNAME='otro-valor'**

$ **echo $HOSTNAME**

**_otro-valor_**

**shell '915'**

$ **echo $HOSTNAME**

**_sysadmin_**

**Variables de entorno de usuario**

A nivel técnico son iguales que las variables de entorno de sistema, la diferencia que punto de creacion es la shell que se carga para un usuario y sus variables están relacionadas con el usuario.

$ **echo $USER**

```
educacionit
```

$ **echo $HOME**

```
/home/educacionit
```

$ **echo $PATH**

```
/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games
```


**Laboratorio: Vocado de variables**

  
En la shell con pid '**848**' existen las variables '**var**' y '**HOSTNAME**' con valores modificados.


$ **echo $var**

``hola mundo!``


$ **echo $HOSTNAME**

```
otro-valor
```

Crear un script que imprima variables de entorno de usuario, de sistema y locales.
  

**Fichero 'variables.sh'**

```
#!/bin/bash

echo ""

echo Variables de entorno del sistema

echo ""

echo HOSTNAME $HOSTNAME

echo ""

echo Variables de entorno de usuario

echo ""

echo USER $USER

echo ""

echo HOME $HOME

echo ""

echo Variables locales

echo ""

echo var $var

echo ""

read -p "presione enter para continuar..."

  
```

  

**Ejecutar script y mantenerlo en ejeucion**

Al ejecutar el script se dispara otro proceso bash que se encarga de procesar las instrucciones del archivo 'variables.sh'.

$ **bash variables.sh**

```
Variables de entorno del sistema

HOSTNAME **sysadmin**

Variables de entorno de usuario

USER **educacionit**

HOME **/home/educacionit**

Variables locales

var

presione cualqueir tecla para continuar...
  
```
  
**Árbol de proceso**

El script fue ejecutado en la shell '848', en otra shell imprimimos el árbol de proceso de ese pid.

$ **pstree -p 848**

bash(848)───bash(**1015**)

El pid '**1015**' corresponde al interprete '**hijo**' del interprete donde se ejecuto el script.

**Variables exportadas**

Una **variable exportada** en el contexto de sistemas operativos como Unix/Linux es una variable de entorno que se hace disponible para los procesos hijos del shell. Esto significa que al usar el comando `export` en Bash, como en `export VAR="valor"`, la variable `VAR` no solo estará disponible en el shell actual, sino que también se heredará por cualquier programa o script ejecutado desde ese shell. Exportar una variable es útil cuando se necesita compartir configuraciones o datos entre procesos.

**Crear y exportar una variable**

**En la shell '848'**


$ **export variable_exportada="hello world!"**

$ **echo $var**

```
hola mundo!
```

$ **echo $variable_exportada**

```
hello world!
```

$ **echo $HOSTNAME** 

```
otro-valor
```

  

**Modificar el valor de una varialbe de entorno (shell 848)**

$ **USER=jorge**

**Modificar script. Fichero 'variables.sh'**
```
#!/bin/bash

echo ""

echo Variables de entorno del sistema:

echo ""

echo HOSTNAME $HOSTNAME

echo ""

echo Variables de entorno de usuario:

echo ""

echo USER $USER

echo ""

echo HOME $HOME

echo ""

echo Variables locales:

echo ""

echo var $var

echo Variables exportadas:

echo ""

echo variable_exportada $variable_exportada

echo ""

read -p "presione cualqueir tecla para continuar..."
```
  

**Ejecutar en la shell '848'**

$ **bash variables.sh** 

```
  

Variables de entorno del sistema:

  

HOSTNAME sysadmin

  

Variables de entorno de usuario:

  

USER jorge

  

HOME /home/educacionit

  

Variables locales:

  

var

  

Variables exportadas:

  

variable_exportada hello world!

  

presione cualqueir tecla para continuar...

  
```

**Resumen**

Una variable de entorno es una variable exportada, se crea un un proceso padre y son heredadas por los procesos hijos.

**Código del script**

```
#!/bin/bash

echo ""

echo Variables de entorno del sistema:

echo ""

echo HOSTNAME $HOSTNAME

echo ""

echo Variables de entorno de usuario:

echo ""

echo USER $USER

echo ""

echo HOME $HOME

echo ""

echo Variables locales:

echo ""

echo var $var

echo Variables exportadas:

echo ""

echo variable_exportada $variable_exportada

echo ""

read -p "presione cualqueir tecla para continuar..."
```
  

---

**Crear usuario**

El grupo '**1000**' ya existe.

  

\# **adduser _usuario --gid 1000_**

**Cambiar contraseña**

\# **passwd educacionit**