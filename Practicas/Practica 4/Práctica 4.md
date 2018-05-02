# Práctica 4 SWAP
> Realizada por Ángel García Illescas y Carmen Domínguez González

### Información previa a la realización de los ejercicios:

* Se deben instalar dos máquinas virtuales con el sistema operativo Ubuntu Server y además deben contener instalado "LAMP" .
  * En nuestro caso se llaman "SWAP-Ubuntu-Server" y "SWAP-Ubuntu-Server-2"
  * Cuándo hablemos de *máquina 1* estaremos refiriéndonos a la máquina "SWAP-Ubuntu-Server"
  * Cuándo hablemos de *máquina 2* estaremos refiriéndonos a la máquina "SWAP-Ubuntu-Server-2"
  * En ambos casos el usuario se llama igual, es decir, el usuario es "carmen".
  * La IP *192.168.56.103* coresponde con "SWAP-Ubuntu-Server"
  * La IP *192.168.56.104* corresponde con "SWAP-Ubuntu-Server-2"


* Tendremos otra máquina instalada con el sistema operativo Ubuntu Server y además el servidor web "nginx"
  * En nuestro caso se llama "SWAP-Ubuntu-Balaceador"
  * Cuándo hablemos de *máquina 3* nos estaremos refiriendo a esta máquina.
  * El usuario es "carmen"
  * Su IP se corresponde con *192.168.56.102*


* Por último instalamos otra máquina con el sistema operativo Ubuntu Server con la herramienta "curl"
  * En nuestro caso se llama "SWAP-Ubuntu-Lanzadora"
  * Cuándo hablemos de *máquina 4* nos estaremos refiriendo a esta máquina.
  * El usuario es "carmen"
  * Su IP se corresponde con *192.168.56.105*

![Las IPs de las dos máquinas servidoras](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura0.png "Las IPs de las dos máquinas servidoras")

![Las IPs del balanceador y la que lanza peticiones](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura1.png "Las IPs de las dos máquinas (balanceador, peticiones)")

### Ejercicios:

**Ejercicio 1:** *Instalar un certificado SSL autofirmado para configurar el acceso por HTTPS*

##### Generamos e instalamos un certificado autofirmado en la *máquina 1*

Para poder hacerlo primero debemos activar el módulo SSL en la configuración de Apache, después generar los certificados y por último especificar la ruta de los certificados en la configuración.

**Activar módulo**
Para activar el módulo ejecutaremos `a2enmod ssl`. Y para que Apache tome la nueva configuración debemos reiniciar el servicio usando por ejemplo `service apache2 restart`.

![Activación del módulo ssl](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura2.png "Actiación del modulo ssl")

**Generar los certificados**
Primero creamos la carpeta dónde los vamos a guardar. Ésto lo conseguimos con `mkdir /etc/apache2/ssl`. A continuación generamos los certificados mediante la ejecución del siguiente comando:

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/apache2/ssl/apache.key -out /etc/apache2/ssl/apache.crt`

Al realizarlo, nos pedirá una serie de datos para configurar el dominio, en nuestro caso hemos puesto:
```
>> ES
>> Granada
>> Granada
>> swap
>> swap
>> swap
>> info@swap.es
```

![configuracion del dominio](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura3.png "configuracion del dominio")

**Especificamos la ruta de los certificados**
Editamos el fichero de configuración `/etc/apache2/sites-available/default-ssl.conf`. En él debemos incluir las siguientes dos líneas debajo de dónde pone SSLEngine on:

```
SSLCertificateFile /etc/apache2/ssl/apache.crt
SSLCertificateKeyFile /etc/apache2/ssl/apache.key
```

![Modificacion de default-ssl](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura4.png "modficacion de default-ssl")

Activamos el sitio *default-ssl* mediante `a2ensite default-ssl` y para que se haga efectivo reiniciamos el servicio `service apache2 reload`.

![Activación de default-ssl](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura5.png "activación de default-ssl")

Por último comprobamos que está bien configurado lanzando una petición por HTTPS desde la *máquina 4* mediante la herramienta curl:
`curl -k https://192.168.56.103/hola.html`

![Petición mediante https](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura6.png "petición mediante https")

Como vemos, se devuelve el fichero html, por tanto la petición se ha realizado correctamente mediante https.

##### Copiamos la configuración para las máquinas *máquina 2* y *máquina 3*

***Máquina 2***
Realizamos los mismos pasos que hicimos para la *máquina 1*.

Comprobación de que la configuración de la *máquina 2* está bien hecha lanzando una petición por HTTPS desde la *máquina 4* mediante la herramienta curl:
`curl -k https://192.168.56.104/hola.html`

![Petición mediante https m2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura7.png "petición mediante https m2")

***Máquina 3***
En este caso debemos configurar nginx para que acepte peticiones de este tipo y que realice correctamente el balanceo de carga. Para ello debemos copiar los ficheros `apache.key y apache.crt` y modificar el fichero de configuración `/etc/nginx/conf.d/default.conf`

* Copiamos la carpeta que contiene los ficheros *apache.key* y *apache.crt* mediante el comando `rsync -avz -e ssh carmen@192.168.56.103:/etc/apache2/ssl /etc/apache2/`.
![Copia de ficheros](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura8.png "copia de ficheros")
* Modificamos el fichero de configuración `/etc/nginx/conf.d/default.conf` añadiendo lo siguiente:
```
listen 443
ssl_certificate /etc/apache2/ssl/apache.key;
ssl_certificate_key /etc/apache2/ssl/apache.crt;
```
![Modificación configuración nginx](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura9.png "Modificación configuración nginx")
* Por último reiniciamos el servicio de *nginx* para que actualice la nueva información. `sudo systemctl restart nginx`

Comprobación de que la configuración de la *máquina 3* está bien hecha lanzando una petición por HTTPS desde la *máquina 4* mediante la herramienta curl:
`curl -k https://192.168.56.102/hola.html`

![Petición mediante https m3](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura10.png "petición mediante https m3")

**Ejercicio 2:** *Configuración del cortafuegos*

Nota: Nuesta configuración la vamos a hacer sobre la *máquina 1*.

Primero lo que hacemos es eliminar todas las reglas que pudiera tener el cortafuegos implementadas por defecto. Para ello ejecutaremos:
* `iptables -F`
* `iptables -X`
* `iptables -Z`
* `iptables -t nat -F`

Con el comando `iptables -L -n -v` vemos el estado del cortafuegos:

![Comprobación del estado después de eliminar las reglas](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura11.png "Comprobación del estado después de eliminar las reglas")

A continuación creamos una regla para denegar todo el tráfico que pueda entrar:
* `iptables -P INPUT DROP`
* `iptables -P OUTPUT DROP`
* `iptables -P FORWARD DROP`

Comprobamos el estado del cortafuegos después de denegar el tráfico:

![Comprobación del estado después de denegar tráfico](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura12.png "Comprobación del estado después de denegar tráfico")

Trás esto lo que hacemos es permitir cualquier acceso desde localhost. Esto lo conseguimos ejecutando los comandos siguientes:
* `iptables -A INPUT -i lo -j ACCEPT`
* `iptables -A OUTPUT -o lo -j ACCEPT`

Para ver que se ha hecho correctamente volvemos a ver el estado:

![Comprobación del estado después de permitir lo](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura13.png "Comprobación del estado después de permitir lo")

Por último lo que debemos hacer es permitir el acceso por SSH, HTTP y HTTPS.

***SSH***
* `iptables -A INPUT -p tcp --dport 22 -j ACCEPT`
* `iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT`

![Comprobación del estado después de permitir ssh](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura14.png "Comprobación del estado después de permitir ssh")

***HTTP***
* `iptables -A INPUT -p tcp --dport 80 -j ACCEPT`
* `iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT`

![Comprobación del estado después de permitir http](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura15.png "Comprobación del estado después de permitir http")

***HTTPS***
* `iptables -A INPUT -p tcp --dport 443 -j ACCEPT`
* `iptables -A OUTPUT -p tcp --sport 443 -j ACCEPT`

![Comprobación del estado después de permitir https](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura16.png "Comprobación del estado después de permitir https")

Cómo no es muy cómodo que cada vez que se inicie la máquina tener que configurar el cortafuegos, lo vamos a automatizar creando un script que se ejecute cada vez que se inicie la máquina.

En nuestro caso se el script se llamará `conf_cortafuegos.sh`. En él escribiremos lo siguiente:
```
#!/bin/bash

#Comandos para eliminar la configuración por defecto
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

#Comandos para denegar todo el tráfico que pueda entrar
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP

#Comandos para permitir el acceso desde localhost
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o  lo -j ACCEPT

#Comandos para permitir el acceso por ssh, http y https
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT

iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT

iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 443 -j ACCEPT
```

![script conf_cortafuegos.sh](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura17.png "script conf_cortafuegos.sh")

Nota: El script lo creamos en la carpeta `/etc/init.d/`.

Una vez creado, lo debemos poner en funcionamiento para que se ejecute cada vez que se incie la máquina. Para conseguirlo ejecutamos `update-rc.d conf_cortafuegos.sh defaults`.

Antes de ejecutarlo debemos asegurarnos de que el fichero que hemos creado tiene permisos de ejecución ( lo podemos mirar con el comando `ls -l /etc/init.d/conf_cortafuegos.sh`). Si no los tiene, se los debemos añadir por ejemplo mediante `chmod +x /etc/init.d/conf_cortafuegos`.

Además es posible que al ejecutarlo nos de un error diciendo: `insserv: warning: script 'conf_cortafuegos.sh' missing LSB tags and overrides`. Éste error lo solucionamos añdiendo al principio del script las siguientes líneas:

```
### BEGIN INIT INFO
# Provides:          conf_cortafuegos.sh
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start daemon at boot time
# Description:       Enable service provided by daemon.
### END INIT INFO
```
*Nota:* Ésta información la hemos obtenido de la siguiente página https://wiki.debian.org/LSBInitScripts.

Por tanto el script finalmente deberá contener:
```
#!/bin/bash
### BEGIN INIT INFO
# Provides:          conf_cortafuegos.sh
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Start daemon at boot time
# Description:       Enable service provided by daemon.
### END INIT INFO

#Comandos para eliminar la configuración por defecto
iptables -F
iptables -X
iptables -Z
iptables -t nat -F

#Comandos para denegar todo el tráfico que pueda entrar
iptables -P INPUT DROP
iptables -P OUTPUT DROP
iptables -P FORWARD DROP

#Comandos para permitir el acceso desde localhost
iptables -A INPUT -i lo -j ACCEPT
iptables -A OUTPUT -o  lo -j ACCEPT

#Comandos para permitir el acceso por ssh, http y https
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT

iptables -A INPUT -p tcp --dport 80 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 80 -j ACCEPT

iptables -A INPUT -p tcp --dport 443 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 443 -j ACCEPT
```
![script conf_cortafuegos.sh Modificado](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura18.png "script conf_cortafuegos.sh Modificado")

Si no nos da ningún fallo al ejecutarlo, nuestra terminal tendrá el siguiente aspecto.

![Resultado de asociar un demonio al script](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura19.png "Resultado de asociar un demonio al script")

Para comprobar que realmente funciona apagamos la máquina y la volvemos a iniciar y si el script es correcto, al ejecutar `iptables -L -n -v` debería salirnos la configuración que hemos dicho que se establezca en el script.

![Comprobación de funcionamiento del script](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%204/Imagenes/captura20.png "Comprobación de funcionamiento del script")


