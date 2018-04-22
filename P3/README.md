# Práctica 3: Balanceo de carga

Durante esta práctica trabajaremos lo que supone el uso de un balanceador de carga para un grupo de servidores, haciendo un benchmark al propio balanceador que hemos configurado con varios servidores web y un balanceador de carga como tal.

## Instalando y configurando los distintos balanceadores

Utilizaremos en esta práctica el servidor web Nginx configurado como balanceador de carga y el balanceador HaProxy.

Lo primero de todo es instalar cada uno. Empecemos por Nginx.

### Nginx

Al utilizar máquinas con Ubuntu Server incluido, para instalar directamente Nginx en nuestra máquina ejecutamos el siguiente comando en nuestra máquina:

`sudo apt-get install nginx`

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/instalacion%20nginx.png)

Con esto solo tenemos instalado Nginx, todavía no lo tenemos activado. O debería de ser así, ya que en mi caso Nginx se ha activado solo, sin hacer ningun tipo de llamada a `systemctl`.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/activaci%C3%B3n%20nginx.png)

En el caso de que no sea así, no os preocupéis, solo tenéis que ejecutar `systemctl start nginx`.

Con todo esto ya realizado, nos queda la configuración para que Nginx no sea un servidor web, sino un balanceador de carga.

Para ello, modificaremos (o crearemos, si el fichero no existe) el fichero `/etc/nginx/conf.d/default.conf`, eliminando todo lo que contenga y escribiendo lo que aparece más abajo.

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/configuraci%C3%B3n%20nginx.png)

Hay que tener en cuenta que las direcciones IP de nuestros servidores pueden diferenciarse de los vuestros. Por tanto, tomadlo como referencia y no copieis a lo loco.

A partir de aquí podemos realizar varios tipos de algoritmos para balancear la propia carga. Si uno quiere experimentar, aquí hay un enlace para ver las [distintas opciones de configuración de nginx](http://nginx.org/en/docs/http/ngx_http_upstream_module.html).

Para comprobar si nuestro balanceador funciona o no, lo que debemos de hacer es comprobar desde otra máquina (no nos valen los propios servidores) si el balanceador nos redirecciona a alguno de los servidores o no.

En mi caso, yo he utilizado una máquina con Windows 10 instalado, así que lo único que he tenido que hacer es poner la dirección IP de mi balanceador en el navegador web instalado por defecto.

TODO: Poner imagen de como se carga el archivo html de uno de los servidores

Para llegar a diferenciar que servidor es al que estamos conectados, es recomendable cambiar los `index.html` que tenemos por defecto y que cada uno muestre por pantalla una cosa diferente.

TODO: Poner como algoritmo un round-robin en el balanceador (en los dos casos)

### HaProxy

Después de haber hecho todo esto con Nginx, ahora le toca el turno a HaProxy. Para instalarlo, de nuevo es muy sencillo:

`sudo apt-get install haproxy`

TODO: Insertar imagen de instalación de HaProxy

Ahora que ya tenemos instalado HaProxy, tendremos que activarlo primero. Pero claro, seguramente Nginx estará ocupando el puerto 80 y, por tanto, no podamos activar el servicio. Hay una solución, y no, no es desinstalar Nginx.

Lo que debemos de hacer primero es parar el servicio de Nginx ejecutando `systemctl stop nginx`. Teniendo ya esto hecho, ejecutamos `systemctl start haproxy`. Ahora ya podremos trabajar con HaProxy sin problemas.

TODO: Insertar la imagen con el intento de iniciar haproxy, después parar nginx e intentar de nuevo iniciar haproxy

Con todo esto hecho ahora toca configurar HaProxy. 