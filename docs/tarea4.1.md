# Servidor DNS

## Que es un servidor DNS

    Un servidor DNS es un servidor que se encarga de traducir las peticiones en texto plano a una dirección ip.

## Instalación de servidor DNS 

Bind es el estándar para servidores DNS. Es una herramienta de software libre que se puede usar en la mayoria de plataformas Unix y Linux conocido como named (name daemon).
Bind9 es la versión recomendada y es la que usaremos. Lo instalaremos de la siguiente manera:

``sudo apt-get install bind9 bind9utils bind9-doc``

## Configuración del servidor

Puesto que solo usaremos ``IPV4``, lo configuraremos en el directorio ``/etc/default``; y para indicarle qeu sólo use IPv4, modificamos la línea ``OPTIONS = "-u bind -4"``.

El archivo de configuración principal ``named.conf`` de Bind está en el directorio: ``/etc/bind``

Veremos esto:

![]()

Este archivo sirve para agrupar los archivos de configuración que usaremos. 

### Configuración *named.conf.options*

Es una buena práctica realizar copias de seguridad a los archivos de configuración cuando van a ser modificados.

``sudo cp /etc/bind/named.conf.options /etc/bind/named.conf.options.backup``

Ahora editaremos el archivo ``named.conf.options`` e incluiremos lo siguiente:

- Por motivos de seguiridad añadiremos una lista de acceso para que sólo puedan hacer consultas recursivas aquellos hosts que permitamos.
    En nuestro caso, los confiables son los de la red 192.168.X.0/24 donde la 'X' dependerá de la red de casa. De esta forma tendremos que añadir algo así.

![]()

Esta inicialmente configurado para ser un servidor DNS caché. Y se guardarán las zonas en ``/var/cache/bind``

- Solo se permiten consultas recursivas a los hosts que hemos decidido en la lista de acceso.
- No permitir transferencias de zona a nadie.
- Configurar el servidor para que escuche las consultas DNS en el puerto 53. Se debe colocar la IP de la indterfaz de Debian.
- Permitir las consultas recursivas, ya que en el primer punto ya le hemos dichos que sólo puedan hacerlas los hosts de la ACL.
- Debemos comentar la línea en la que pone ``listen-on-v6 {any};`` puesto que no vamos a responder consultar IPv6.

Podemos comprobar si la configuracion es correcta con el comando ``sudo named-checkconf``.

Reiniciamos y comprobamos el servidor.

![]()

### Configuración *named.conf.local*

En este archvivo configuraremos aspectos relativos a nuestras zonas. Vamos a declarar la zona "deaw.es". Por ahora indicaremos que el servidor DNS es maestro para esta zona.

![]()

### Creación del archivo de zona



