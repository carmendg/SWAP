# Práctica 5 SWAP
> Realizada por Ángel García Illescas y Carmen Domínguez González

### Información previa a la realización de los ejercicios:

* Se deben instalar dos máquinas virtuales con el sistema operativo Ubuntu Server y además deben contener instalado "LAMP" .
  * En nuestro caso se llaman "SWAP-Ubuntu-Server" y "SWAP-Ubuntu-Server-2"
  * Cuándo hablemos de *máquina 1* estaremos refiriéndonos a la máquina "SWAP-Ubuntu-Server"
  * Cuándo hablemos de *máquina 2* estaremos refiriéndonos a la máquina "SWAP-Ubuntu-Server-2"
  * En ambos casos el usuario se llama igual, es decir, el usuario es "carmen".
  * La IP *192.168.56.103* coresponde con "SWAP-Ubuntu-Server"
  * La IP *192.168.56.104* corresponde con "SWAP-Ubuntu-Server-2"

![Las IPs de las dos máquinas servidoras](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura0.png "Las IPs de las dos máquinas servidoras")

### Ejercicios:

**Ejercicio 1:** *Creamos una base de datos y la rellenamos con algunos datos.*

Nota: La base de datos la vamos a crear en la *máquina 1*, por tanto todos los siguientes comandos se ejecutarán desde ahí.

Lo primero que debemos hacer para realizar el ejercicio es acceder a MySQL. Para ello ejecutamos `mysql -u root -p`. Si al instalarla, le pusimos contraseña se la debemos poner introducir a continuación y si no le pusimos ninguna simplemente le damos al enter.

![Iniciar sesión en MySQL](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura1.png "Iniciar sesión en MySQL")

Nota: No debemos olvidar nunca que mientras estemos conectados a mysql para ejecutar cualquier comando debemos escribir al final ';'.

Una vez iniciada la sesión realizaremos los siguientes pasos:

* Creamos la base de datos mediante el comando `create database contactos`.
* Nos conectamos a esa base de datos mediante el comando `use contactos`.
* Mostramos las tablas que tenemos creadas mediante `show tables`. Si no tenemos ninguna nos devolverá un mensaje, en caso contrario nos mostrará las tablas que hayamos creado.
* Creamos una tabla que se llame datos y que tenga como columnas:
	* *nombre* de tipo varchar(100)
	* *tlf* de tipo int.
	* Para ello ejecutamos `create table datos(nombre varchar(100),tlf int)`.
* Comprobamos que se ha creado correctamente mostrandola (`show tables`). Y además podemos ver las características de la misma mediante `describe datos`.
* Insertamos algunos datos. En nuestro caso, hemos insertado tres datos:
	* `insert into datos(nombre,tlf) values ("pepe",958349877)`
	* `insert into datos(nombre,tlf) values ("maria",958256541)`
	* `insert into datos(nombre,tlf) values ("paco",958641258)`
* Comprobamos que se han introducido correctamente haciendo una consulta. Por ejemplo, mostrando todas las filas que haya en la tabla (`select * from datos`).
* Para salir de mysql bastará con ejecutar `quit`. Éste comando irá sin ';'.

![Comandos creación base de datos.](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura2.png "Comandos creación base de datos")

**Ejercicio 2:** *Crear una copia de seguridad con `mysqldump` en la máquina 1 y copiarla en la máquina 2.*

***Copia de seguridad máquina 1***

El objetivo es crear una copia de seguridad de la base de datos en la *máquina 1* pero si mientras lo hacemos ésta se está actualizando no lo estaremos haciendo correctamente. Por ello lo primero que debemos hacer es conectarnos a MySQL (`mysql -u root -p`) y ejecutar `flush tables with read lock`. Con este comando evitamos las actualizaciones.

![Desactivar la posibilidad de actualizar la base de datos](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura3.png "Desactivar la posibilidad de actualizar la base de datos")

Salimos de MySQL (`quit`) y realizamos la copia mediante `mysqldump contactos -u root -p > /tmp/contactos.sql`. Si queremos ver que se ha realizado correctamente podemos ver si aparece el fichero en el lugar dónde lo hemos creado a través de `ls -l /tmp/contactos.sql`.

![Realización de la copia de seguridad](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura4.png "Realización de la copia de seguridad")

Por último debemos volver a activar la posibilidad de actualizar la base de datos. Para ello nos volvemos a conectar a MySQL y ejecutamos `unlock tables` y salimos.

![Activar la posibilidad de actualizar la base de datos](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura5.png "Activar la posibilidad de actualizar la base de datos")

***Copia de seguridad en la máquina 2***

Primero obtenemos el fichero que contiene la copia de la base de datos que hemos creado anteriormente. Para ello desde la *máquina 2* ejecutamos el comando`scp 192.168.56.103:/tmp/contactos.sql /tmp/`. Para comprobar que se ha realizado correctamente podemos ver si aparece el fichero en el lugar dónde lo hemos creado a través de `ls -l /tmp/contactos.sql`.

![Copia de seguridad para la máquina 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura6.png "Copia de seguridad para la máquina 2")

**Ejercicio 3:** *Restauración de la base de datos en la máquina 2*

Aunque tenemos el fichero donde está la copia de la base de datos, en él no se especifica sobre que base de datos se está haciendo por tanto debemos crearla de nuevo y luego importar los datos contenidos en la copia.

***Creación de la base de datos***

Seguiremos algunos pasos de los que realizamos en el ejercicio 1, en concreto haremos lo siguiente:

* Conectarnos a MySQL: `mysql -u root -p`.
* Creamos la base de datos y le ponemos el mismo nombre que tiene el fichero: `create database contactos`.
* Salimos de MySQL: `quit`.

![Creamos la base de datos contactos en la máquina 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura7.png "Creamos la base de datos contactos en la máquina 2")

***Importamos el contenido del fichero a la base de datos***

Para importarlo simplemente ejecutamos `mysql -u root -p contactos < /tmp/contactos.sql`.

![Importamos el contenido del fichero a la base de datos](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura8.png "Importamos el contenido del fichero a la base de datos")

***Comprobamos que la importación se ha hecho de forma correcta***

Para comprobarlo realizamos los siguientes pasos:

* Conectarnos a MySQL: `mysql -u root p`.
* Conectarnos a la base de datos: `use contactos`.
* Mostramos las tablas que contiene: `show tables`.
* Mostramos el contenido de la tabla: `select * from datos`.

![Comprobación de la importación](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura9.png "Comprobación de la importación")

**Ejercicio 4:** *Configuración maestro-esclavo de forma automática*

En nuestro caso el maestro es la *máquina 1* y el esclavo es la *máquina 2*.

***Edición del archivo de configuración de mysql del maestro***

Editamos el archivo de configuración `/etc/mysql/mysql.conf.d/mysqld.cnf` realizando los siguientes cambios:

* Comentamos el parámetro *bind-address*: `#bind-address 127.0.0.1`.
* Añadimos dónde debe almacenar el log de errores: `log_error = /var/log/mysql/error.log`.
* Establecemos el identificador del servidor: `server-id = 1`.
* Establecemos el registro binario: `log_bin = /var/log/mysql/bin.log`.

En nuestro caso sólo hemos tenido que comentar la linea de *bind-address* y cambiar el identificador del servidor ya que el resto venía ya configurado.

Guardamos los cambios realizados y reiniciamos el demonio de mysql para que adopte la nueva configuración. Ésto lo hacemos mediante: `service mysql restart`. Además si queremos ver que no se han producido errores podemos ver el estado del demonio (`service mysql status`) y si está running no ha habido ningún fallo en la configuración.

***Edición del archivo de configuración de mysql del esclavo***

Editamos el archivo de configuración `/etc/mysql/mysql.conf.d/mysqld.cnf` realizando los siguientes cambios:

* Comentamos el parámetro *bind-address*: `#bind-address 127.0.0.1`.
* Añadimos dónde debe almacenar el log de errores: `log_error = /var/log/mysql/error.log`.
* Establecemos el identificador del servidor: `server-id = 2`.
* Establecemos el registro binario: `log_bin = /var/log/mysql/bin.log`.

En nuestro caso sólo hemos tenido que comentar la linea de *bind-address* y cambiar el identificador del servidor ya que el resto venía ya configurado.

Guardamos los cambios realizados y reiniciamos el demonio de mysql para que adopte la nueva configuración. Ésto lo hacemos mediante: `service mysql restart`. Además si queremos ver que no se han producido errores podemos ver el estado del demonio (`service mysql status`) y si está running no ha habido ningún fallo en la configuración.

![Edición de los archivos en ambas máquinas](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura10.png "Edición de los archivos en ambas máquinas")

![Reiniciando el demonio en ambas máquinas](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura11.png "Reiniciando el demonio em ambas máquinas")

***Creamos un usuario en la máquina 1 con accesos a la replicación***

Para ello seguimos los siguientes pasos:

* Nos conectamos a mysql: `mysql -u root -p`.
* Creamos el usuario: `create user esclavo identified by 'esclavo'`.
* Le asignamos permisos: `grant replication slave on *.* to 'esclavo'@'%' identified by 'esclavo'`
* Ejecutamos las tres siguientes ordenes para establecer los privilegios sobre la tabla.
	* `FLUSH PRIVILEGES`
	* `FLUSH TABLES`
	* `FLUSH TABLES WITH READ LOCK`

Tras esto, obtenemos los datos de la BD que vamos a replicar para posteriormente usarlos en la configuración del esclavo (*máquina 2*). Para ello ejecutamos la orden: `SHOW MASTER STATUS`.

![Usuario de la máquina 1](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura12.png "Usuario de la máquina 1")

***Configuración de mysql del esclavo***

Para ello simplemente ejecutamos realizamos los tres siguientes pasos:

* Nos conectamos a mysql: `mysql -u root -p`.
* Ejecutamos la siguiente orden para establecer cúal es el maestro: `change master to master_host='192.168.56.103', master_user='esclavo', master_password='esclavo', master_log_file='mysql-bin.000001', master_log_pos=1139, master_port=3306`.
* Iniciamos el esclavo: `start slave`.

![Configuración del esclavo](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura13.png "Configuración del esclavo")

***Comprobación de que la configuración es correcta***

Primero debemos ver que el esclavo está funcionando correctamente para ello ejecutamos el comando `show slave status\G`. Si en la variable *Seconds_Behind_Master* es distinta de *null* la configuración estará bien hecha. En caso contrario tendremos algún error.

![Comprobación del estado del esclavo 1](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura14.png "Comprobación del estado del esclavo 1")

Como vemos en la imagen a nosotros nos ha dado un fallo a la hora de realizarlo debido a los puertos.
Lo hemos solucionado abriendo ese puerto en la *máquina 1* y este es el resultado de volver a ejecutar `show slave status\G`.

![Comprobación del estado del esclavo 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura15.png "Comprobación del estado del esclavo 2")

Volvemos a tener un fallo, esta vez es porque ambas máquinas tienen el mismo UUID. Para solucionar esto debemos borrar en la *máquina 2* el archivo  `/var/lib/mysql/auto.cnf`. Y volvemos a ejecutar el comando de antes obteniendo este resultado.

![Comprobación del estado del esclavo 3](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura16.png "Comprobación del estado del esclavo 3")

Como vemos, no se ha producido ningún fallo por tanto lo que debemos hacer a continuación es volver a activar la posibilidad de modificar los datos de la base de datos del maestro. Por tanto ejecutamos `unlock tables`.

Por último introducimos nuevos datos en la base de datos del maestro y si hacemos un `select * from datos` en el esclavo (*máquina 2*) veremos que se han actualizado y por tanto podemos concluir que la configuración es correcta y que funciona como se espera.

![Comprobación del estado del esclavo 4](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%205/Imagenes/captura17.png "Comprobación del estado del esclavo 4")