# Práctica 3 SWAP
> Realizada por Ángel García Illesca y Carmen Domínguez González

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
  * El usuario es "carmen"
  * Su IP se corresponde con *192.168.56.105*

![Las IPs de las dos máquinas servidoras](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura0.png "Las IPs de las dos máquinas servidoras")

![Las IPs del balanceador y la que lanza peticiones](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura1.png "Las IPs de las dos máquinas (balanceador, peticiones)")

### Ejercicios:

**Ejercicio 1:** *Configurar nginx como balanceador de carga en la máquina 3*

##### Instalación *nginx*

Los pasos que vamos a seguir para hacerlo son:

* Actualizamos nuestro repositorio mediante la ejecución de: `sudo apt-get update` y la ejecución de `sudo apt-get dist-upgrade`
* Eliminamos las dependencias que no sean necesarias mediante la ejecución de: `sudo apt-get autoremove`
* A continuación instalamos *nginx* mediante: `sudo apt-get install nginx`
* Y por último inicializamos el servicio con: `sudo systemctl start nginx`

![Instalación de nginx parte1](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura2.png "Instalación de nginx parte1")

![Instalación de nginx parte2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura3.png "Instalación de nginx parte2")

![Instalación de nginx parte3](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura4.png "Instalación de nginx parte3")

Nota: Para comprobar que se ha inicializado correctamente el servicio ejecutamos `sudo systemctl status nginx`

##### Configuración *nginx* como balanceador de carga

Para conseguirlo debemos modificar el fichero `/etc/nginx/conf.d/default.conf`
* Incluimos una directiva para definir el grupo de servidores finales (*máquina 1* y *máquina 2*). Ésto lo haremos de la siguiente manera:
```
upstream apaches {
	server 192.168.56.103;
	server 192.168.56.104;
}
```
Tal y como lo hemos implementado, estaremos usando un algoritmo de balanceo mediante "*round-robin*".
* Justo debajo debemos definirle que esas son las máquinas a las que debe repartir el tráfico que le llega. Para ello incluiremos justo debajo el siguiente trozo de código:
```
server {
 	listen 80;
 	server_name balanceador;
    
	access_log /var/log/nginx/balanceador.access.log;
	error_log /var/log/nginx/balanceador.error.log;
	root /var/www/;
    
	location /
	{
		proxy_pass http://apaches;
		proxy_set_header Host $host;
		proxy_set_header X-Real-IP $remote_addr;
		proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_http_version 1.1;
		proxy_set_header Connection "";
	}
}
```

![Configuración de nginx Round-Robin](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura5.png "Configuración de nginx Round-Robin")

Para que se hagan efectivas las modificaciones hechas en el fichero de configuración volvemos a iniciar el servicio `sudo systemctl restart nginx`

A continuación lo que hacemos es probar que el balanceo de carga se hace correctamente para ello usaremos el comando `curl http://192.168.56.102/hola.html`.

Cómo tenemos dos máquinas servidoras bastará con ejecutarlo dos veces.

Nota: Ésta ejecución se hace sobre la máquina lanzadora.

![Prueba de funcionamiento Round-Robin](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura6.png "Prueba de funcionamiento Round-Robin")

Por último vamos a comprobar que funciona tambíen usando el algoritmo de ponderación. Para ello volveremos a modificar el fichero `/etc/nginx/conf.d/default.conf` sólamente la parte de la directiva.

```
upstream apaches {
	server 192.168.56.103 weight=2;
	server 192.168.56.104 weight=1;
}
```

Nosotros hemos decidido darle el peso de 1 a la *máquina 2* y el peso 2 a la *máquina 1* por lo que la *máquina 2* tendrá una carga menor (hemos supuesto que es menos potente) y la *máquina 1* aceptará más carga.

![Configuración de nginx Ponderación](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura7.png "Configuración de nginx Ponderación")

Nota: Debemos volver a reiniciar el servicio para que acepte la nueva configuración.

Y por último mostramos el resultado `curl` cuando estamos usando un algoritmo de ponderación.

En este caso tendremos que ejecutarlo tres o cuatro veces.

![Prueba de funcionamiento Ponderación](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura8.png "Prueba de funcionamiento Ponderación")


**Ejercicio 2:** *Configurar haproxy como balanceador de carga en la máquina 3*

##### Instalación *haproxy*

Para instalarlo simplemente debemos ejecutar `sudo apt-get install haproxy`

![Instalación de haproxy](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura9.png "Instalación de haproxy")

Nota: Para comprobar que se ha inicializado correctamente el servicio ejecutamos `sudo systemctl status haproxy`

##### Configuración *haproxy* como balanceador de carga

Para conseguirlo debemos modificar el fichero `/etc/haproxy/haproxy.cfg`
```
global
	daemon
	maxconn 256
defaults
	mode http
	contimeout 4000
	clitimeout 42000
	srvtimeout 43000
frontend http-in
	bind *:80
	default_backend servers
backend servers
	server 		m1 192.168.56.103:80 maxconn 32
	server 		m2 192.168.56.104:80 maxconn 32
```

![Configuración de haproxy Round-Robin](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura10.png "Configuración de haproxy Round-Robin")

Para que se hagan efectivas las modificaciones hechas en el fichero de configuración volvemos a iniciar el servicio `sudo systemctl restart haproxy` y para que funcione correctamente debemos parar el servicio nginx `sudo systemctl stop nginx`.

A continuación lo que hacemos es probar que el balanceo de carga se hace correctamente para ello usaremos el comando `curl http://192.168.56.102/hola.html`.

Cómo tenemos dos máquinas servidoras bastará con ejecutarlo dos veces.

Nota: Ésta ejecución se hace sobre la máquina lanzadora.

![Prueba de funcionamiento Round-Robin](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura11.png "Prueba de funcionamiento Round-Robin")

Por último vamos a comprobar que funciona tambíen usando el algoritmo de ponderación. Para ello volveremos a modificar el fichero `/etc/haproxy/haproxy.cfg` sólamente la parte del backend.

```
global
	daemon
	maxconn 256
defaults
	mode http
	contimeout 4000
	clitimeout 42000
	srvtimeout 43000
frontend http-in
	bind *:80
	default_backend servers
backend servers
	server 		m1 192.168.56.103:80 maxconn 32 weight 2
	server 		m2 192.168.56.104:80 maxconn 32 weight 1
```

Nosotros hemos decidido darle el peso de 1 a la *máquina 2* y el peso 2 a la *máquina 1* por lo que la *máquina 2* tendrá una carga menor (hemos supuesto que es menos potente) y la *máquina 1* aceptará más carga.

![Configuración de haproxy Ponderación](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura12.png "Configuración de haproxy Ponderación")

Nota: Debemos volver a reiniciar el servicio para que acepte la nueva configuración.

Y por último mostramos el resultado `curl` cuando estamos usando un algoritmo de ponderación.

En este caso tendremos que ejecutarlo tres o cuatro veces.

![Prueba de funcionamiento Ponderación](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura13.png "Prueba de funcionamiento Ponderación")


**Ejercicio 3:** *Someter a la granja web a una alta carga, generada con la herramienta Apache Benchmark, teniendo nginx y teniendo haproxy*

Para realizar este ejercicio hemos decidido utilizar el Apache Benchmark con la siguiente especificicación:
* el número de peticiones (n) será 10000
* el nivel de concurrencia (c) será 400

Por tanto nuestro comando a ejecutar será:
`ab -n 10000 -c 400 http://192.168.56.102/hola.html`

##### *nginx* sometido a una alta carga

Paramos el servicio de *haproxy* e inicializamos el servicio de *nginx*, lanzamos el benchmark, y vemos los resultados:

![Alta carga nginx parte 1](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura14.png "Alta carga nginx parte 1")

![Alta carga nginx parte 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura15.png "Alta carga parte 2")

Como podemos ver en la foto, ha tardado 10.533 segundos en resolver todas las peticiones(10000) y que no ha fallado ninguna petición.

##### *haproxy* sometido a una alta carga

Paramos el servicio de *nginx* e inicializamos el servicio de *haproxy*, lanzamos el benchmark, y vemos los resultados:

![Alta carga haproxy parte 1](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura16.png "Alta carga haproxy parte 1")

![Alta carga haproxy parte 2](https://github.com/carmendg/SWAP/blob/master/Practicas/Practica%203/Imagenes/captura17.png "Alta carga haproxy parte 2")

Como podemos ver en la foto, ha tardado 6.689 segundos en resolver todas las peticiones(10000) y que no ha fallado ninguna petición.

##### Comparación de ambos y conclusiones

|  | *nginx* | *haproxy* |
| ---------- | ---------- | ---------- |
| Tiempo ejecución total (s) | 10.533 | 6.689 |
| Número de peticiones realizadas | 10000   | 10000   |
| Número de peticiones falladas | 0   | 0   |
| Tiempo por petición (ms) | 421.308   | 267.568   |

Cómo podemos ver en la tabla, ambos balanceadores han sido capaces de distribuir correctamente la carga a la que los hemos sometido, ya que no se han producido ningún fallo y se han atendido a todas las peticiones.

La diferencia está en que el balanceador *haproxy* ha tardado menos tiempo en atenderlas y por tanto menos tiempo en completar toda la carga. Por este motivo diríamos que para esta carga el que mejor funciona es *haproxy*.

