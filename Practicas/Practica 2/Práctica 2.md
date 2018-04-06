# Práctica 2 SWAP
> Realizada por Ángel García Illescas y Carmen Domínguez González

### Información previa a la realización de los ejercicios:

* Se deben instalar dos máquinas virtuales con el sistema operativo Ubuntu Server y además deben contener instalado "LAMP" .
  * En nuestro caso se llaman "SWAP-Ubuntu-Server" y "SWAP-Ubuntu-Server-2"
  * Cuándo hablemos de *máquina 1* estaremos refiriéndonos a la máquina "SWAP-Ubuntu-Server"
  * Cuándo hablemos de *máquina 2* estaremos refiriéndonos a la máquina "SWAP-Ubuntu-Server-2"
* En ambos casos el usuario se llama igual, es decir, el usuario es "carmen".
* La IP *192.168.56.102* coresponde con "SWAP-Ubuntu-Server"
* La IP *192.168.56.103* corresponde con "SWAP-Ubuntu-Server-2"

![Las IPs de las dos máquinas](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura0.png "Las IPs de las dos máquinas")

### Ejercicios:

**Ejercicio 1:** *Probar el funcionamiento de la copia de archivos por `ssh`*

Vamos a crear el directorio *practica2-1* en la *máquina 1* y también tres ficheros dentro (*fichero1*, *fichero2*, *fichero3*).

Nuestro destino será la *máquina 2* por tanto en nuestro caso deberemos ejecutar el comando:
`tar czf - practica2-1 | ssh crmen@192.168.56.103 'cat > ~/tar.tgz'`

![Resultado de copia de archivos](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura1.png "Resultado de copia de archivos")

**Ejercicio 2:** *Clonado de una carpeta entre las dos máquinas*

Primero instalamos la herramienta *rsync* o comprobamos que está instalada. Ésto lo conseguimos mediante el comando: `sudo apt-get install rsync`

![Instalación del paquete para la máquina 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura2.png "Instalación del paquete para la máquina 2")

A continuación le damos permisos al usuario con el que vamos a realizar la clonación. En nuestro caso el usuario es *carmen*. Para ello haremos uso del comando:
`sudo chown carmen:carmen -R /var/www/html`

![Asignación de permisos al usuario](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura3.png "Asignación de permisos al usuario")

Una vez hecho esto, vamos a comprobar que funciona el clonado de carpetas de una máquina a otra. Se realizará sobre la carpeta */var/www/* de la *máquina 1*. Por tanto desde la *máquina 2* ejecutaremos: `rsync -avc -e ssh carmen@192.168.56.102:/var/www/html /var/www/`

![Resultado de la clonación](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura4.png "Resultado de la clonación")

Nota: El clonado lo estamos haciendo sobre carpeta */var/www/* que ya existe y por tanto estamos sobreescribiendo el contenido. Para demostrar que se realiza bien vamos a crear otro fichero (*hola.html*) dentro de la carpeta situada en la *máquina 1*

**Ejercicio 3:** *Configuración de `ssh` para acceder sin que solicite contraseña*

Primero obtenemos la clave pública-privada del "cliente" que será nuestra *máquina 2*. Para ello simplemente utilizaremos el comando `ssh-keygen -b 4096 -t rsa`

Como nuestra intención es poder conectarnos sin contraseña, cuando nos pida *passphrase* lo dejaremos en blanco.

Nota: El directorio donde se guardará la clave será en `/home/carmen/.ssh/id_rsa`

![Generación de la clave pública](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura5.png "Generación de la clave pública")

Lo siguiente que hacemos es copiarlo en la máquina server (*máquina 1*) desde la que queremos acceder mediante `ssh-copy-id 192.168.56.102`

![Copia de clave en la otra máquina](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura6.png "Copia de la clave en la otra máquina")

Por último comprobamos que podemos conectarnos mediante `ssh` desde la máquina cliente (*máquina 2*) a la máquina server (*máquina 1*) sin que nos pida la contraseña.

![Conexión desde ssh](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura7.png "Conexión de ssh")


**Ejercicio 4:** *Establecer una tarea en cron que se ejecute cada hora para mantener actualizado el contenido del directorio `/var/www` entre las dos máquinas*

Usaremos `nano` para modificar el archivo `/etc/crontrab` en el que incluiremos una línea para que se actualice el contenido de `/var/www` cada hora. Ésto lo podemos conseguir a partir del comando utilizado en el *ejercicio 2*.

Ponemos * /1 * en la segunda columna para que se haga cada hora y el resto como * ya que no nos importa en que minuto sea ni que día.

En conclusión la línea que pondremos será:
`* * * * *  carmen rsync -avc -e ssh carmen@192.168.56.102:/var/www/html /var/www/`

![Modificación de crontab](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%202/Imagenes/captura8.png "Modificacion de crontab")

Nota: Nosotros lo ejecutamos en la *máquina 2* para que se haga la copia de la *máquina 1* que es la que hace la función de servidor.


