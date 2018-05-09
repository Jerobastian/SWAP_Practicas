# Práctica 3: Balanceo de carga

Durante esta práctica trabajaremos lo que supone el uso de un balanceador de carga para un grupo de servidores, haciendo un benchmark al propio balanceador que hemos configurado con varios servidores web y un balanceador de carga como tal.

## Instalando y configurando los distintos balanceadores

Utilizaremos en esta práctica el servidor web Nginx configurado como balanceador de carga y el balanceador HaProxy.

Lo primero de todo es instalar cada uno. Empecemos por Nginx.

### Nginx

Al utilizar máquinas con Ubuntu Server incluido, para instalar directamente Nginx en nuestra máquina ejecutamos el siguiente comando en nuestra máquina:

`sudo apt-get install nginx`

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/instalacion%20nginx.png)

Con esto solo tenemos instalado Nginx, todavía no lo tenemos activado. O debería de ser así, ya que en mi caso Nginx se ha activado solo, sin hacer ningun tipo de llamada a `systemctl`.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/activaci%C3%B3n%20nginx.png)

En el caso de que no sea así, no os preocupéis, solo tenéis que ejecutar `systemctl start nginx`.

Con todo esto ya realizado, nos queda la configuración para que Nginx no sea un servidor web, sino un balanceador de carga.

Para ello, modificaremos (o crearemos, si el fichero no existe) el fichero `/etc/nginx/conf.d/default.conf`, eliminando todo lo que contenga y escribiendo lo que aparece más abajo.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/configuraci%C3%B3n%20nginx.png)

Hay que tener en cuenta que las direcciones IP de nuestros servidores pueden diferenciarse de los vuestros. Por tanto, tomadlo como referencia y no copieis a lo loco.

A partir de aquí podemos realizar varios tipos de algoritmos para balancear la propia carga. Si uno quiere experimentar, aquí hay un enlace para ver las [distintas opciones de configuración de nginx](http://nginx.org/en/docs/http/ngx_http_upstream_module.html).

Para comprobar si nuestro balanceador funciona o no, lo que debemos de hacer es comprobar desde otra máquina (no nos valen los propios servidores) si el balanceador nos redirecciona a alguno de los servidores o no.

En mi caso, yo he utilizado una máquina con Ubuntu Server instalado, así que lo único que he tenido que hacer es ejecutar `curl http://<ip del balanceador>` para obtener el html de la máquina.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/prueba%20nginx.png)

Para llegar a diferenciar que servidor es al que estamos conectados, es recomendable cambiar los `index.html` que tenemos por defecto y que cada uno muestre por pantalla una cosa diferente.

Si hemos llegado a tener problemas, debemos de comentar la línea del fichero `/etc/nginx/nginx.conf` donde pone `include /etc/nginx/sites-enabled/`

### HaProxy

Después de haber hecho todo esto con Nginx, ahora le toca el turno a HaProxy. Para instalarlo, de nuevo es muy sencillo:

`sudo apt-get install haproxy`

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/instalacion%20haproxy.png)

Ahora que ya tenemos instalado HaProxy, tendremos que activarlo primero. Pero claro, seguramente Nginx estará ocupando el puerto 80 y, por tanto, no podamos activar el servicio. Hay una solución, y no, no es desinstalar Nginx.

Lo que debemos de hacer primero es parar el servicio de Nginx ejecutando `systemctl stop nginx`. Teniendo ya esto hecho, ejecutamos `systemctl start haproxy`. Ahora ya podremos trabajar con HaProxy sin problemas.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/activacion%20haproxy.png)

Con todo esto hecho ahora toca configurar HaProxy. Para ello, editaremos el archivo de configuración de HaProxy (`/etc/haproxy/haproxy.conf`), borrando todo lo que hay e introduciendo lo que se puede ver en la imagen de abajo.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/configuracion%20haproxy.png)

Con la configuración terminada, vamos a iniciar de nuevo el servicio. Para ello, pararemos HaProxy con `systemctl stop haproxy`, pero no lo iniciaremos con la opción `start` de `systemctl` directamente, sino que antes tendremos que ejecutar la siguiente linea de comando:

`sudo /usr/sbin/haproxy -f /etc/haproxy/haproxy.conf`

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/configuracion%202%20haproxy.png)

En este caso nos saltarán algunos warning, pero no es motivo de preocuparse por ahora, ya que son cosas funcionales que se utilizaran poco en versiones posteriores del software.

Todavía no hemos terminado, ya que ahora toca comprobar que nuestro balanceador Haproxy funciona perfectamente:

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/prueba%20haproxy.png)

## Sobrecargando al balanceador

Teniendo ya configurado el balanceador con distintas opciones software, vamos a sobrecargarlo para ver como responde. Para ello, ejecutaremos un benchmark desde nuestra máquina cliente: Apache Benchmark.

Para ello, debemos de instalar el benchmark en nuestra máquina cliente:

`sudo apt-get install apache2-utils`

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/instalaci%C3%B3n%20ab.png)

Con Apache Benchmark ya instalado, vamos a lanzarlo directamente a nuestro balanceador. Debemos de tener en cuenta que en los dos casos cuenta con un algoritmo round-robin.

Para lanzarlo desde nuestra máquina cliente al balanceador, ejecutamos la siguiente línea de comando:

`ab -n 10000 -c 20 <ip del balanceador>/index.html` para realizar el benchmark a Nginx.

`ab -n 10000 -c 20 <ip del balanceador>:85/index.html` para realizar el benchmark a HaProxy.

Vamos a ver nuestros resultados:

Nginx

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/prueba%20ab%20nginx.png)

HaProxy

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/Imagenes/prueba%20ab%20haproxy.png)

En los dos casos no hemos tenido ningún tipo de pérdida de paquetes. Ahora bien, HaProxy ha tardado menos en completar el benchmark y, por tanto, puede proporcionar más peticiones por segundo que si el balanceador software fuera Nginx. Así que, si tenemos que decidir el instalar un balanceador software u otro según el benchmark, nos decantaríamos por HaProxy.