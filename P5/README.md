# Práctica 5: Replicación de bases de datos MySQL
Una de las funciones más importantes para páginas web de comercio electrónico es mantener una base de datos actualizada en referencia a la lista de usuarios y pedidos que se registran y se realizan respectivamente al día. Para realizar esta tarea, vamos a trabajar con MySQL y crear una base de datos que, además, la configuraremos como esclavo-maestro para que, cuando se modifique la del maestro, se realicen los mismos cambios en el esclavo.

## Creando la base de datos
Si hemos instalado un LAMP en nuestro servidor, tendremos MySQL disponible en nuestra máquina. Si no es así, deberemos de instalar `mysql` con `sudo apt-get install`.

Para empezar a usar MySQL, lo que tendremos que hacer es ejecutar la siguiente línea:

`mysql -u root -p`

donde nos pedirá la contraseña del usuario root del propio programa (que habremos configurado en la instalación del servicio).

Para crear una base de datos, ejecutaremos `CREATE DATABASE <nombre_de_la_base_de_datos>;`. Pero esto no significa que utilicemos directamente la base de datos creada, debemos de indicarlo con `USE <nombre_de_la_base_de_datos>;`.

TODO: Insertar la imagen "Usando MySQL".

Con la base de datos creada, empezaremos a usarla creando una tabla. Ahí almacenaremos los distintos elementos que tengan el mismo tipo de atributos que hemos especificado en ella.

En mi caso, he decidido crear una tabla donde guardaré varios juegos de GameBoy. Para ello voy a crear una tabla llamada `gameboy` con la instrucción `CREATE TABLE`.

Con la tabla creada, podremos empezar a insertar datos. Para ello, ejecutaremos la instrucción `INSERT INTO <nombre_de_la_tabla>(tipos_de_datos) VALUES (datos_a_insertar);`.

TODO: Insertar la imagen "Insertando datos en una tabla".

Si en algún momento queremos visualizar la tabla, lo podemos hacer ejecutando la orden `SELECT * FROM <nombre_de_la_tabla>`, aunque si queremos especificar un campo o varios de la tabla, podemos indicarlos cambiando el asterisco por el nombre de estos.

TODO: Insertar la imagen "Mostrando datos de una tabla".

## Exportando e importando una base de datos
Algo que es muy importante es tener salvaguardada nuestra base de datos. Para ello podemos recurrir a exportar las órdenes necesarias para crear las tablas e insertar los datos respectivos en estas. Para ello, utilzaremos el programa `mysqldump`.

Para exportar nuestro fichero SQL, debemos de ejecutar lo siguiente:

`mysqldymp <nombre_de_la_base_de_datos> -u root -p > fichero.sql`

Con esto ya tendremos nuestra base de datos "exportada".

TODO: Insertar la imagen "Creando el fichero SQL".

Para copiar de una máquina a otra, podemos hacerlo mediante scp, donde en la [práctica anterior](https://github.com/Jerobastian/SWAP_Practicas/tree/master/P4) vimos como se utilizaba.

Con la copia ya hecha, vamos a ejecutar la importación con el propio `mysql`. Para ello ejecutaremos `mysql -u root -p <nombre_de_la_base_de_datos> < fichero.sql`.

TODO: Insertar la imagen "Importación realizada con éxito".

## Creando un maestro-esclavo
Aunque el método visto en el apartado anterior nos puede ayudar a mantener una base de datos común en dos máquinas diferentes, se puede ver que no es una opción muy eficiente. Por eso, vamos a ver como realizar una configuración maestro-esclavo entre dos servidores.

Una configuración maestro-esclavo hace que los cambios que se realicen en la base de datos del maestro se realicen también en el esclavo. Sí, aún así no es la mejor configuración (para eso está la configuración maestro-maestro), pero sabiendo este tipo de configuración, pasar al siguiente nivel es mucho más sencillo.

Para ello deberemos de configurar primero el servicio MySQL para la tarea. Editaremos el fichero `/etc/mysql/mysql.conf.d/mysqld.cnf` comentando y descomentando las líneas que se ven en las imágenes de abajo para la máquina que va a ser la maestra.

TODO: Insertar la imagen "Editando el fichero de configuración de MySQL".

TODO: Insertar la imagen "Descomentando líneas".

También debemos de realizar lo mismo en la esclava, pero con un pequeño cambio: en `server-id` debemos de poner en vez de 1, 2.

Con esto hecho, debemos de reiniciar el servicio para que se cargue la configuración realizada.

TODO: Insertar la imagen "Reiniciando el servicio".

Con todo esto ya hecho, vamos a indicar a las máquinas quien es el maestro y quien es el esclavo. Para ello, primero vamos a prohibir realizar modificaciones en la base de datos del maestro antes de finalmente activarlo como tal.

TODO: Insertar la imagen "Configuración en el maestro".

Ahora, veremos los datos necesarios para conectar desde el esclavo al maestro. Con esos datos, deberemos de ejecutar lo que se ve en la máquina de la parte derecha de la imagen de más abajo para conseguir la configuración.

TODO: Insertar la imagen "Creando el esclavo".

Teniendo todo ya ejecutado, vamos a comprobar que todo está funcionando. Deberemos de ejecutar desde MySQL la instrucción `SHOW SLAVE STATUS\G;` donde nos mostrará el estado del esclavo. Debemos de fijarnos en el campo `Seconds_Behind_Master` que deberá de tener un valor diferente a `NULL`. Si no es así, deberemos de revisar la configuración por si hemos cometido un error.

TODO: Insertar la imagen "Esclavo ya activo".