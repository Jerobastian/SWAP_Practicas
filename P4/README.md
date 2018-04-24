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

//TODO: Insertar imagen de la pantalla de configuración después de crear nuestro certificado SSL

Aunque hemos hecho todo esto, nos hace falta configurar un par de cosas para que cada vez que accedamos a nuestras páginas web, se pueda acceder a ellas mediante SSL. Para ello, añadiremos en el fichero `nano /etc/apache2/sites-available/default-ssl` las siguientes líneas después del apartado `SSLEngine on`:

`SSLCertificateFile /etc/apache2/ssl/apache.crt`
`SSLCertificateKeyFile /etc/apache2/ssl/apache.key`

Teniendo todo esto hecho, debemos de activar el sitio por defecto con `a2ensite default-ssl` y después reiniciar el servicio de Apache.

Para probar nuestro acceso por la "zona segura", deberemos de ejecutar `curl –k https://<ip de la máquina>/index.html`. Nos deberá de aparecer lo siguiente:

//TODO: Insertar imagen de la prueba de conexión mediante HTTPS

## Configurando el cortafuegos

//TODO: Hacer la sección de cortafuegos