# Ejecicios Tema 3 SWAP
> Realizados por Carmen Domínguez González

#####Ejercicio 1: *Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el enrutamiento del tráfico de un servidor para pasar el tráfico desde una subred a otra.*

**Linux**
Para esta distribución tenemos el comando `route` con el que podemos añadir una ruta (add) o borrarla (del). Cuando lo ejecutamos realmente estamos modificando la configuración de las redes y si queremos que las modificaciones que hemos hecho se mantengan debemos hacer que `route` se ejecute en el archivo `/etc/network/interfaces`.
Por ejemplo para añadir una ruta ejecutaríamos: `up route add -net ...`.

*Referencia:* https://www.hscripts.com/es/tutoriales/linux-commands/route.html

**Windows**

En esta distribución tenemos la herramienta `Enrutamiento y servicio de acceso remoto` que proporciona el propio sistema operativo con la que podemos realizar las tareas del enrutamiento.

*Referencia:* https://msdn.microsoft.com/es-es/library/dn614140(v=ws.11).aspx

#####Ejercicio 2: *Buscar con qué órdenes de terminal o herramientas gráficas podemos configurar bajo Windows y bajo Linux el filtrado y bloqueo de paquetes.*

**Windows**

* *Firewall Windows Defender*: https://support.microsoft.com/es-es/help/4028544/windows-10-turn-windows-defender-firewall-on-or-off
* *Enrutamiento y servicio de acceso remoto*: https://technet.microsoft.com/es-es/library/cc513968(v=ws.10).aspx
* *IPSec*: https://help.comodo.com/topic-436-1-901-11544-.html

**Linux**

* *iptables*: http://www.ite.educacion.es/formacion/materiales/85/cd/linux/m6/cortafuegos_iptables.html
* *ufw*: https://help.ubuntu.com/community/UFW

* *shorewall*: http://shorewall.org/


