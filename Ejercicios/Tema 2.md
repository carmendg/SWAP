# Ejecicios Tema 2 SWAP
> Realizados por Carmen Domínguez González

#####Ejercicio 1: *Calcular la disponibilidad del sistema si tenemos dos réplicas de cada elemento (en total 3 elementos en cada subsistema)*

*Datos:*

Componente | Disponibilidad inicial | Disponibilidad con 2 elementos
-- | -- | --
Web | 85% | 97.75%
Applicación | 90% | 99%
Base de datos | 99.9% | 99.9999%
DNS | 98% | 99.96%
Firewall | 85% | 97.75%
Switch | 99% | 99.99%
Centro de datos | 99.99% | 99.99%
ISP | 95% | 99.75

La disponibilidad del sistema se calcula mediante la fórmula `As = Ac1 * Ac2 * ... * Acn` donde *Ac1 ... Acn* son las disponibilidades de los distintos subsistemas.

Cómo hemos añadido un nuevo elemento a cada substima, tenemos que calcular la nueva disponibilidad que nos ofrece. Para ello usamos la fórmula `As = Acn-1 + (( 1 - Acn-1) * Acn` donde *Acn-1* es la disponibilidad que tenemos ahora mismo (2 elementos) en ese subsistema y *Acn* es la disponibilidad que nos da ese subsistema (la inicial).

**Cálculo de la disponibilidad para los distintos subsistemas**

* *Web*: As = 97.75% + (100% - 97.75%) * 85% = 99.6625%
* *Applicación*: As = 99% + (100% - 99%) * 90% = 99.9%
* *Base de datos*: As = 99.9999% + (100% - 99.9999% ) * 99.9% = 99.999999%
* *DNS*: As = 99.96% + (100% - 99.96%) * 98% = 99.9992%
* *Firewall*: As = 97.75% + (100% - 97.75) * 85% = 99.6625%
* *Switch*: As = 99.99% + (100% - 99.99% ) * 99% = 99.9999%
* *Centro de datos*: As = 99.99%  + (100% - 99.99%) * 99.99% = 99.9999999%
* *ISP*: As = 99.75% + (100% - 99.75%) * 95% = 99.9875%

**Cálculo de la disponibilidad del sistema con dos réplicas en cada subsistema**

As = 99.6625% \*  99.9% \* 99.999999% \* 99.9992% \* 99.6625% \* 99.9999% \* 99.9999999% \* 99.9875% = 99.213515%

Por tanto la disponibilidad que tiene el sistema ahora mismo es de un ***99.213515%***.

#####Ejercicio 2: *Buscar frameworks y librerías para diferentes lenguajes que permitan hacer aplicaciones altamente disponibles con relativa facilidad.*

**Frameworks**

* *MOF(Microsoft Operations Framework)*: Proporciona diferentes directrices que facilitan la gestión de los productos y/o tecnologias de Microsof. Algunos ejemplos de estas directrices son: guias, herramientas de evaluación, plantillas, herramientas de soporte, etc.
	* Referencia: https://msdn.microsoft.com/es-es/library/aa560207(v=bts.10).aspx
* *GWT(Google Web Toolkit)*: Creado por Google y se caracteriza por simplificar el uso de AJAX. Su lenguaje es muy parecido a Java y además el mismo compilador lo transforma a html y JavaScript por tanto estamos fusionando dos lenguajes en uno sólo.
	* Referencia: https://es.wikipedia.org/wiki/Google_Web_Toolkit

**Librerías**

* *JQuery*: Es una librería de JavaScript que se caracteriza por incluir funciones que simplifican el trabajo de JavaScript y son más eficientes. Por ejemplo permiten modificar los HTML y controlar los eventos (pulsar un botón, enlaces, etc...), incluir animaciones, etc.
	* Referencia: https://jquery.com/
* *Math.js*: Es una librería de JavaScript y de Node.js. Su objetivo es simplificar los cálculos matemáticos y lo consigue proporcionando funciones y diferentes tipos de datos, consiguiendo de esta manera un código más sencillo, eficiente y simple para el programador.
	* Referencia: http://mathjs.org/

#####Ejercicio 3: *¿Cómo analizar el nivel de carga de cada uno de los subsistemas en el servidor? Buscar herramientas y aprender a usarlas.*

* *ApacheBench(ab)*: Es la herramienta que proporciona Apache HTTP para realizar pruebas de carga diferentes según los parámetros que se le pasen. Es de openSource.
	* Referencia: https://httpd.apache.org/docs/2.4/programs/ab.html
* *Glangia*: Es una herramienta de monitorización que normalmente se utiliza en clusters y Grids. Al igual que *ab* es de OpenSource.
	* Referencia: http://ganglia.sourceforge.net/
* *Nagios*: Esta herramienta se caracteriza por poder monitorizar la red, el servidor y las aplicaciones. Por tanto es una herramienta muy completa y al igual que las anteriores de OpenSource.
	* Referencia: https://www.nagios.org/
* *Munin*: Esta herramienta destaca porque permite monitorizar varios PC simultáneamente ya que se hace a través de la web, mostrando diferentes gráficas con los resultados obtenidos. También es OpenSource.
	* Referencia: http://munin-monitoring.org/

#####Ejercicios 4:

######A) Buscar ejemplos de balanceadores software y hardware (productos comerciales).

**Software**

* Nginx
* HAProxy
* Pen
* Pound
* Zen Load Balancer
* Amazon ELB

**Hardware**

* Cisco
* Citrix
* Barracuda
* Resonate

######B) Buscar productos comerciales para servidores de aplicaciones.

* WebLogic (Oracle)
* JBoss (Red Hat)
* WebSphere (IBM)
* Zope
* Base4 Server
* .NET (Microsoft)

######C) Buscar productos comerciales para servidores de almacenamiento.

* Amazon EC2
* Windows Azure
* Dell PowerEdge
* Lenovo ThinkServer
