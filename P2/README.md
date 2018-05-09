# Práctica 2: Clonar la información de un sitio web

Teniendo ya la [anterior práctica](https://github.com/Jerobastian/SWAP_Practicas/tree/master/P1) terminada, pasamos a una de las cosas más triviales pero muy importantes para servidores web: la clonación de información.

Abordaremos como hacer copias de manera segura con `SSH` y `Rsync`, además de programar tareas que realizamos recurrentemente con el demon `cron`.

Se da por hecho de que ya se ha realizado toda la configuración necesaria que hemos visto en la anterior práctica (el enlace se encuentra en el primer párrafo).

## Primer punto: Copiando archivos de una máquina a otra

La copia de archivos entre servidores es una de las tareas más comunes en el trabajo de servidores web. Ahora bien, no podemos realizar estas copias de cualquier manera, debemos de asegurarnos que durante la "transmisión" no se modifique ni que nadie nos de el cambiazo. Por eso, vamos a utilizar `SSH` para este caso.

Primero, deberemos de **crear un archivo comprimido con los elementos que queremos enviar**. Para ello, utilizaremos `tar`.

Para hacerlo todo en una sola linea, utilizaremos un **pipe**(`|`) y **conectaremos** mediante `SSH` **con la otra máquina** y enviaremos el archivo.

`tar czf - <directorio con los archivos que queremos enviar> | ssh <usuario>@<ip del destino> 'cat > ~/tar.tgz'`

Con esta orden lo que hemos hecho ha sido crear el archivo y mandarlo mediante ssh a la máquina destino. En la siguiente imagen se ve como ha resultado exitoso.

--------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P2/Imagenes/SSH%20transfer.png)

--------

El ejemplo más común es copiar la carpeta `/var/www` ya que esta contiene **toda la información de la estructura de nuestra página web.**

Aunque la transferencia se pueda hacer, **no es recomendable** hacerlo con `SSH`. Más adelante veremos la herramienta que utilizaremos.

## Segundo punto: Accediendo con SSH a otra máquina sin contraseña

`SSH` sigue siendo **una de las mejores herramientas** para trabajar en otras máquinas de manera remota, pero **es una molestia el tener que introducir la contraseña** del usuario de la otra máquina. **Aunque esto no tiene por qué ser la norma**.

Crearemos una clave pública y privada para nuestro equipo. Estas claves nos permitiran identificarnos en las otras máquinas de manera automática y dejarnos paso.

Primero creamos nuestra clave pública y privada. Para ellos, ejecutamos la siguiente orden:

`ssh-keygen -b 4096 -t rsa`

**ATENCIÓN**: Los campos que apareceran hay que dejarlos en blanco y presionar `enter`.

--------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P2/Imagenes/Generating%20key.png)

--------

Con esto hemos creado nuestras dos claves para poder conectarnos automáticamente a otras máquinas sin tener que introducir contraseña en el directorio `/home/<nuestro usuario>/.ssh`.

Ahora vamos a autenticarnos en la otra máquina para que nos recuerde para siempre. Para ello debemos de ejecutar la siguiente orden:

`ssh-copy-id <usuario>@<ip de la máquina destino>`

Deberemos de introducir la contraseña esta vez, pero a partir de aquí no nos la pedirá más en los siguientes accesos.

--------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P2/Imagenes/Adding%20key.png)

--------

Como se puede observar en la imagen anterior, ya no nos pide contraseña.

## Tercer punto: Rsync

Rsync es una herramienta para clonado de archivos, sobre todo dirigido al sector de los servidores web.

Primero, debemos buscar si tenemos instalado o no Rsync en nuestras máquinas. Para ello, vamos a intentar ver el manual del programa.

`man rsync`

En mi caso, con máquinas **Ubuntu 16**, `rsync` se encuentra instalado. Si este no es vuestro caso, [mirad en este enlace](http://rsync.samba.org/download.html).

Con `rsync` instalado, vamos a hacer un par de configuraciones para allanarnos el camino.

Primero, vamos a configurar los permisos de `/var/www` para poder tener todos en nuestro usuario habitual. Para ello, ejecutamos lo siguiente:

`sudo chown <nombre del grupo al que pertenece tu usuario>:<tu nombre de usuario> -R /var/www`

Como podemos observar en la siguiente imagen, el usuario propietario de la carpeta ha cambiado totalmente.

--------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P2/Imagenes/Changing%20owner.png)

--------

Con esto realizado, ya nos podemos embarcar a usar `rsync`. Para ello ejecutamos la siguiente orden en la máquina que quiere clonar los archivos de la master:

`rsync -avz -e ssh <ip de la máquina master>:<directorio que queremos clonar> <directorio donde almacenaremos lo clonado>`

Veamos un ejemplo:

--------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P2/Imagenes/Rsync.png)

--------

El ejemplo de la imagen crea un nuevo directorio `www` dentro del directorio `/var/www`. Si lo que queremos es que sustituya la carpeta con el mismo nombre, tendremos que cambiar el último apartado de la linea por solo `/var`.

Si tenemos usuarios diferentes en las dos máquinas, podemos comprobar que la carpeta anterior a realizar `rsync` tenía como usuario propietario el de la máquina en la que estamos trabajando ha cambiado al del usuario de la máquina master.

## Cuarto punto: Programar tareas con crontab

`cron` es un administrador de procesos que ejecuta de manera secundaria tareas indicadas en periodos de tiempos indicados en el fichero `crontab`.

Para saber como funciona la sintaxis del fichero, es recomendable leer el [guión de la práctica](https://prado.ugr.es/moodle/pluginfile.php/1008922/mod_resource/content/7/practica_2_guion_rsync.pdf).

Sabiendo como funciona la sintaxis, vamos a añadir una tarea que lo que realice es un rsync cada hora. Para ello debemos de indicar el minuto de la hora en el que queremos que se ejecute. Nosotros vamos a ponerlo para que sea a en punto, así que añadimos la siguiente linea en el fichero de la máquina donde queremos tener copiados los ficheros:

`0 * * * * <nombre de nuestro usuario> rsync -avz -e ssh <ip de la máquina master>:<directorio que queremos clonar> <directorio donde almacenaremos lo clonado>`

En mi caso utilizaré:

`0 * * * * jerobastian rsync -avz -e ssh 192.168.1.115:/var/www /var`

Esto hara que a cada hora en punto se realice `rsync`. Para comprobar que se ha hecho, añadiremos un fichero en el directorio en la máquina master y comprobaremos antes y después del `rsync` si está o no.

--------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P2/Imagenes/Cron.png)

--------

### Recomendaciones

Es recomendable reiniciar la máquina donde tenemos el fichero `/etc/crontab` para que se apliquen los cambios.