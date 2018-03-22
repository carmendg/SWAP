# Práctica 1 SWAP
> Realizada por Ángel García Illescas y Carmen Domínguez González

### Información previa a la realización de los ejercicios:

- Se deben instalar dos máquinas virtuales con el sistema operativo Ubuntu Server y además deben contener instalado "LAMP" .
  - En nuestro caso se llaman "SWAP-Ubuntu-Server" y "SWAP-Ubuntu-Server-2"
 -- Cuándo hablemos de *máquina 1* estaremos refiriendonos a la máquina "SWAP-Ubuntu-Server"
 -- Cuándo hablemos de *máquina 2* estaremos refiriendonos a la máquina "SWAP-Ubuntu-Server-2"
- En ambos casos el usuario se llama igual, es decir, el usuario es "carmen".
- La IP *192.168.56.102* coresponde con "SWAP-Ubuntu-Server"
- La IP *192.168.56.103* corresponde con "SWAP-Ubuntu-Server-2"

### Ejercicios:

**Ejercicio 1:** *Comprobar que se puede establecer conexión entre las dos máquinas que se han instalado mediante `ssh`*

Para realizar este ejercicio debemos hacer uso del comando `ssh usuario@ip`
En nuestro caso *usuario* contendrá el valor de carmen y la IP variará dependiendo de en qué máquina lo estemos ejecutando.

En la siguiente imagen veremos cuáles son las IP's de las dos máquinas (en color rojo la *máquina 1* y en color azul la *máquina 2*) y cómo ejecutamos el comando descrito anteriormente.

![Ejecución del comando ssh](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%201/Imagenes/captura1.png "Ejecución del comando ssh")

A la hora de ejecutarlo nos pide la contraseña del usuario al que estamos accediendo y para demostrar que realmente hemos podido acceder ejecutamos `ifconfig` y veremos que la IP coincide con la IP que hemos puesto al ejecutar el ssh.

![Comprobación de que se ha conectado correctamente ](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%201/Imagenes/captura2.png "Comprobación de que se ha conectado correctamente")

Por tanto queda demostrado que se puede establecer conexión entre ambas máquinas.

**Ejercicio 2:** *Comprobar que se puede establecer conexión entre las dos máquinas que se han instalado mediante `curl`*

Primero debemos crearnos en `/var/www/html` un fichero llamado *hola.html* que contendrá lo siguiente:
```
<HTML>
	<BODY>
		Esto funciona :)
	</BODY>
</HTML>
```

A continuación ejecutaremos el comando `curl http://ip/hola.html` donde *ip* contendrá el valor de la IP de la máquina que va a hacer la función de "servidor", es decir, en la que hemos creado el fichero *hola.html*

**Nota:** El comando se ejecutará desde la máquina que hace de "Cliente".

Primero lo hemos hecho tomando como "Servidor" a la *máquina 1* (En rojo) y "Cliente" a la *máquina 2* (En azul) y en la siguiente imagen veremos el resultado de ejecutar dicho comando.

![Ejecución del comando curl en máquina 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%201/Imagenes/captura4.png "Ejecución del comando curl en máquina 2")

Como se observa en la *máquina 2* se obtiene el contenido del "hola.html".

Por último lo hacemos tomando como "Servidor" a la *máquina 2* (En rojo) y "Cliente" a la *máquina 1* (En azul) y en la siguiente imagen veremos el resultado de ejecutar dicho comando.

![Ejecución del comando curl en máquina 1](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%201/Imagenes/captura3.png "Ejecución del comando curl en máquina 1")

Como se observa en la *máquina 1* se obtiene el contenido del "hola.html".

Por tanto queda demostrado que se puede establece conexión entre ambas máquinas.
