# Práctica 1: Presentación y preparación de las herramientas

Esta actividad es un calentamiento para iniciar las posteriores prácticas, suponiendo la tarea conectar dos servidores o máquinas mediante SSH y obtener su página principal mediante cURL.

Para poder realizar esto necesitamos que estas dos máquinas tengan tanto `LAMP` como `OpenSSH`, que se explica como instalarlo en el [guión proporcionado por el profesorado](https://prado.ugr.es/moodle/pluginfile.php/1008921/mod_resource/content/7/practica_1_guion_herramientas.pdf) .

A partir de aquí, abordaremos **como conectar las máquinas a una red interna** en **Ubuntu 16** para después realizar los dos puntos en concreto que se nos pide en la actvidad.

## Creando la red interna

Primero, debemos de añadir la tarjeta de red interna en cada máquina. Para ello, en la interfaz de Virtual Box, teniendo seleccionada la máquina, seleccionamos `Configuración`->`Red`->`Adaptador 2`.

A partir de aquí, realizamos los cambios que se indican en la imagen de más abajo.

-----------------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P1/InterNet.png)

-----------------

Teniendo la tarjeta de red interna instalada, toca ahora configurar la interfaz de la red.

Para ello, encendemos la máquina en cuestión, e introducimos lo siguiente:

`sudo vi /etc/network/interfaces`

Esto hace que abramos el archivo de interfaces de red con el editor de texto. Teniendo ya el fichero abierto, editamos como se muestra en las imágenes de más abajo. Si es tu primera vez con `vi`, te recomiendo que eches un vistazo a su manual (`man vi`).

-----------------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P1/before.png)

-----------------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P1/after.png)

-----------------

Terminado este paso, toca repetir todo para la otra máquina (*cambiando la dirección IP por otra*).

Teniendo todo esto hecho, hacemos `ping` desde una máquina a otra para comprobar si la red interna está totalmente operativa.

### Posibles errores

Recordemos que esto se ha realizado sobre **Ubuntu Server 16**, por tanto **la delimitación cambia por completo** con sus versiones anteriores. Adáptalo según la versión de tu sistema operativo.

Además, es probable que no tengamos **conexión a internet**. Si es así, debemos de comentar la línea de texto `gateway 192.168.1.1` con una almohadilla para dejarlo así:
`#gateway 192.168.1.1`

## Primer punto: Conexión mediante SSH

Teniendo todo lo anterior hecho, este punto es muy simple.

Lo único que tenemos que hacer es escribir: `ssh <nombre de usuario>@<ip de la otra máquina>`

Si hemos hecho todo bien, nos aparecerá lo siguiente:

-----------------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P1/SSH.png)

-----------------

## Segundo punto: Obtención de un fichero HTML mediante cURL

Primero y antetodo debemos de comprobar si **cURL ya está instalado en nuestra máquina**. En **Ubtunu 16** ya nos deja acceder a su manual sin aún haber usado la orden `apt-get`.

Si no lo tenemos ya instalado, lo hacemos como siempre se ha hecho desde la terminal:

```
sudo apt-get install curl
```

Teniendo esto ya hecho, vamos a utilizarlo para comprobar que tenemos conexión entre las dos máquinas mediante http con `curl http://<dirección del servidor>/<directorio, si hay>/<archivo HTML que queremos obtener>`.

-----------------

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P1/CURL.png)

-----------------

Se recomienda realizar un nuevo archivo `html` para que veamos los posibles errores que haya tenido cURL al intentar conseguir dicho archivo, pero con un encauce y la orden `tail` se puede comprobar sin problemas.
