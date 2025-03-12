# BALANCEO DE CARGA CON PROXY INVERSO EN NGINX

## 1. INTRODUCCION

Para entender esta práctica primero debemos saber que es un proxy inverso y un balanceador de carga:

### 1.1 Proxy inverso
 Un proxy inverso es un tipo de servidor que se encuentra entre el cliente y servidor y se encarga de recibir las peticiones y redireccionarlas al servidor web, para despues responder dicha solicitud.

### 1.2 Balanceador de carga
 Un balanceador de carga, es un tipo de proxy inverso que además se encarga de distribuir las peticiones entre servidores para que así un solo servidor no se tenga que encargar de todas las solicitudees. Esto es necesario cuando un sitio necesita de varios servidores por la cantidad de peticiones que reciba o por la disposición geográfica del mismo; esta implementación aporta un nivel mayor de seguridad ya que al haber dos servidores dando las mismas respuestas, si uno cae ya sea por un ataque o por cualquier otro problema tendremos el otro resolviendo las peticiones mientras este esté caido. Y por último esto también mejora la experiencia del usuario ya que reduce la cantidad de errores percibidas por el usuario, detectando un servidor defectuoso y redireccionando las peticones a otro, y por otra parte reduciendo la velocidad de respuesta. 


## 2. TAREA

Para esta práctica vamos a necesitar el proxy inverso de la práctica anteior, y replicar el servidor web que teniamos.

- Cada servidor web tendrá un sitio web específico para esta practica.
    - El webserver2 tendrá un IP asignada de forma fija mediante la configuración DHCP.
- Cambiaremos la configuración del proxy inverso para que también haga el balanceo de carga.
- Haremos las peticiones HTTP desde el navegador WEB de nuestra máquina anfitriona, accediendo a `http://balanceo`.
 

## 3. CONFIGURACIONES

Primero necesitamos desenlazar la carpeta con la web que teniamos previamente `/etc/nginx/sites-enabled` y ejecutar `unlink nombre-archivo`.

### Nginx Servidor Web 1

El primer servidor web será el servidor principal que hemos estado usando hasta ahora, donde tenemos instalado ya el servicio web.

Debemos configurar este servidor web para que sirva el siguiente ``index.html`` que debéis crear dentro de la carpeta ``/var/www/webserver1/html``:

```html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Web Server 1</title>
</head>
<body>
    <header>Esto es el webserver 1</header>
    <p>Prueba de balanceo de carga recargando esta página</p>
</body>
</html>
```

El nombre del sitio web que en los archivos correspondientes en sites-availables es webserver1. En donde escribiremos la siguiente configuración.

```bash
server {
    listen 8080;

    server_name webserver1 www.webserver1;

    root /var/www/webserver1/html;
    index index.html index.htm;

    location / {            
            add_header Host servidor_web1_a23lupual;
            try_files $uri $uri/ =404;
    }
}
```

Como veis el servidor escuchara el puerto 8080 y le añadiremos la cabecera Host: servidor_web1_a23lupual.

### Nginx Servidor 2

En este servidor web debemos realizar una configuración idéntica al servidor web 1, pero cambiando webserver1 por webserver2 (también en el index.html), 
así como el nombre de la cabecera añadida, que será Serv_Web2_vuestronombre

````html
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Web Server 2</title>
</head>
<body>
    <header>Esto es el webserver 2</header>
    <p>Prueba de balanceo de carga recargando esta página</p>
</body>
</html>
````

##### Config Nginx

```bash
server {
        listen 8080;

        server_name webserver2 www.webserver2;

        root /var/www/webserver2/html;
        index index.html index.htm;

        location / {
                add_header Host servidor_web2_a23lupual;
                try_files $uri $uri/ =404;
        }
}
```

Como podéis observar la configuración es casí idéntica, cambiando simplemente el nombre y la ruta.

### Configuración del Proxy Inverso

Tras haber realizado las configuraciones anteriores, ya tendremos los 2 servidores web listos, entre los cuales vamos a distribuir las peticiones.

Por tanto, deberemos configurar el servidor proxy para que haga uso de los servidores web y distribuya estas peticiones.

 - Crearemos en sites-available el archivo de configuración ``balanceo``

El cual tendrá el siguiente formato:

```bash
upstream backend_hosts {
            random;
            server ________:____;
            server ________:____;
}
        server {
            listen 80;
            server_name ________;      
            location / {
                proxy_pass http://backend_hosts;
            }
        }
```

Donde: 

- upstream backend_hosts: son los servidores en los que se reparte la carga.
- Colocaremos las ip de cada servidor, como su puerto.
- A este grupo le pondremos un nombre que es backend_hosts.
- El parametro random, es para que el balanceo sea aleatorio.

Una vez configurado se vería así:

```bash
upstream backend_hosts {
            random;
            server 192.168.116.117:8080;
            server 192.168.116.142:8080;
}
        server {
            listen 80;
            server_name practica2_alberto;
            location / {
                add_header Host servidor_web2_a23lupual;
                proxy_pass http://backend_hosts;
            }
        }
```

## Comprobaciones

### Comprobación del balanceo de carga

Si accedeis a vuestro sitio web, debéis poder accediendo sin problemas:

- Recargar la página varias veces y veréis como se va alternando entre los dos servidores web.

Como podemos ver el servidor proxy hace el valanceo y nos muestra las páginas:

!['página server 1'](../assets/imagenes/fotos2.4/foto_server2.png)

!['página server 2'](../assets/imagenes/fotos2.4/foto_server.png)

### Comprobación cuando cae un servidor

Nuestro balanceador de carga está constantemente monitorizando “la salud” de los servidores web. De esta forma, si uno deja de funcionar por cualquier 
razón, siempre enviará las solicitudes a los que queden “vivos”. Vamos a comprobarlo:

Para esto apagaremos el primer servidor con el comando ``sudo poweroff``

!['apagando servidor 1'](../assets/imagenes/fotos2.4/poweroff.png)

Y como podemos ver el servidor 2 sigue funcionando sin problemas.

!['servidor 2'](../assets/imagenes/fotos2.4/server1_caido.png)

## Cuestiones

### 1. Least connections (least_conn)

Este método distribuye las peticiones a los servidores con menos carga, para así mejorar la eficiencia del balanceo de carga.

### 2. IP Hash (ip_hash)

Este método distribuye las peticiones a los servidores basándose en la IP del cliente, de esta forma si un cliente accede a un servidor, siempre se le redirigirá a ese servidor.

### 3. Least Time (Menor tiempo)

Este método distribuye las peticiones a los servidores con menos tiempo de respuesta, de esta forma se mejora la eficiencia del balanceo de carga.

- Si quiero añadir 2 servidores web más al balanceo de carga, describe detalladamente qué configuración habría que añadir y dónde.

Para esto debemos ir al bloque de configuración upstream backend_hosts y al igual que hicimos anteriormente añadiremos dos líneas más de configuración:

```bash
upstream backend_hosts {
            random;
            server 192.168.116.117:8080;
            server 192.168.116.142:8080;

            server nuevo_server1;
            server nuevo_server2;
}
```
