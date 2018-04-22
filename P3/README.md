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

![img](https://raw.githubusercontent.com/Jerobastian/SWAP_Practicas/master/P3/configuracion%20nginx.png)

Hay que tener en cuenta que las direcciones IP de nuestros servidores pueden diferenciarse de los vuestros. Por tanto, tomadlo como referencia y no copieis a lo loco.

A partir de aquí podemos realizar varios tipos de algoritmos para balancear la propia carga. Si uno quiere experimentar, aquí hay un enlace para ver las [distintas opciones de configuración de nginx](http://nginx.org/en/docs/http/ngx_http_upstream_module.html).

### HaProxy

