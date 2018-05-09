# Práctica 4: Asegurar la granja web

Una de las cosas más importante en la configuración de una granja web es la seguridad de nuestros consumidores. Internet ahora mismo es una jungla y nosotros debemos de brindar una seguridad que nuestros clientes agradeceran.

Para ello, deberemos de instalar SSL y configurarlo, además de configurar nuestro cortafuegos.

## Instalando y configurando SSL

SSL (Secure Sockets Layer) es un protocolo que se basa en el protocolo TCP/IP, que nos proporciona un certificado que verifica que nuestro servicio es seguro para la transmisión de datos. Pero, ¿como obtenemos este certificado?

Lo primero de todo es tener Apache (`apache2`) y tendremos que activar el módulo SSL de Apache. Lo haremos ejecutando la siguiente linea de comando:

`sudo a2enmod ssl`

Después de haber activado el módulo, debemos de reiniciar Apache para poder tener acceso a él (`systemctl restart apache2`). Teniendo Apache ya reiniciado, crearemos un directorio llamado `ssl` dentro de la carpeta `/etc/apache2`y desde nuestro directorio principal ejecutamos la siguiente línea:

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt`

Esto nos creara mediante SSL un certificado de seguridad mediante la clave SSL que hemos originado al mismo tiempo.

Después de esto, tendremos que realizar unas configuraciones que se nos mostrarán en pantalla, tal y como aparece en la imagen de abajo.

//TODO: Insertar imagen "Creando nuestra clave"

Aunque hemos hecho todo esto, nos hace falta configurar un par de cosas para que cada vez que accedamos a nuestras páginas web, se pueda acceder a ellas mediante SSL. Para ello, añadiremos en el fichero `nano /etc/apache2/sites-available/default-ssl` las siguientes líneas después del apartado `SSLEngine on`:

`SSLCertificateFile /etc/apache2/ssl/apache.crt`
`SSLCertificateKeyFile /etc/apache2/ssl/apache.key`

Teniendo todo esto hecho, debemos de activar el sitio por defecto con `a2ensite default-ssl` y después reiniciar el servicio de Apache.

Para probar nuestro acceso por la "zona segura", deberemos de ejecutar `curl –k https://<ip de la máquina>/index.html`. Nos deberá de aparecer lo siguiente:

//TODO: Insertar imagen "Prueba con cURL"

Con esto ya hecho en una máquina, deberemos de hacer lo mismo con las demás. Para ello copiaremos de manera remota tanto el fichero `.crt` como el fichero `.key` creados. Lo haremos ejecutando la siguiente línea desde la máquina donde tenemos los certificados:

`scp /etc/apache2/ssl/apache.crt <usuario_maquina_destino>@<ip_de_la_maquina_destino>`
`scp /etc/apache2/ssl/apache.key <usuario_maquina_destino>@<ip_de_la_maquina_destino>`

//TODO: Insertar imagen "Añadiendo el certificado en el otro servidor"

Esto nos copiará los ficheros al directorio de trabajo del usuario especificado en el comando. Luego en las otras máquinas debemos de mover ese fichero al mismo directorio donde lo alojamos en la primera máquina.

//TODO: Insertar imagen "Añadiendo el otro certificado en el servidor 2"

Para el segundo servidor, hay que configurar lo mismo que hicimos en la primera máquina ya que este cuenta con Apache. En cambio, nuestro balanceador contiene Nginx, que necesita una configuración distinta para que se pueda acceder mediante HTTPS y HTTP.

//TODO: Insertar imagen "Añadidas las claves al balanceador"

Debemos de modificar el fichero de configuración de Nginx que tiene por defecto, que es `/etc/nginx/conf.d/default.conf`, modificando el fichero para que se nos quede tal que así:

//TODO: Insertar imagen "Configuración balanceador"

Con todo esto ya podemos balancear tanto con HTTP como con HTTPS. Ahora bien, ¿como bloqueamos el paso para que no se puedan conectar mediante otros protocolos?

## Configurando el cortafuegos

El cortafuegos o firewall es una herramienta muy útil para esto último que hemos mencionado. En este caso, utilizaremos el comando `iptables` para administrar los distintos protocolos por los que se puede acceder a nuestro servidor.

Nosotros hemos utilizado el siguiente script para Linux el cual solamente deja acceso a localhost y mediante los protocolos HTTP y HTTPS:

//TODO: Insertar imagen "Script iptables"

Con esto solo lo tendremos configurado así para la sesión activa. ¿Y si queremos que se ejecute este script cada vez que se arranque el sistema? Pues solamente tenemos que convertir el script en un demonio o `daemon`. Para hacerlo hay que seguir los pasos de la imagen de más abajo:

//TODO: Insertar imagen "Creando el script para que se inicie al arrancar"

Lo que hacemos es dar permisos de ejecución para luego llevarlo al directorio /etc/init.d que es donde se alojan los distintos scripts que se ejecutan justo al iniciar el sistema. Ahora bien tenemos que convertirlo en demonio con `update-rc.d` para que se pueda arrancar o parar sin problema como servicio.