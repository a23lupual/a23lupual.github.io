# Práctica 6.1 - Dockerización del despliegue de una aplicación Node.js

## Introducción

En esta practica vamos a dockerizar una aplicación que hace peticiónes a una API para gestionar direcciones que guardará los datos en una BBDD PostgreSQL.

## Ventajas de Dockerizar

1. Cofiguración rápida del entrono local para el desarrollo.

2. Evitar una inconsistencia en la configuración de entronos.

3. Despliegues rápidos.

4. Mejor control de versiones.

5. Al tener control de versiones se puede hacer rollback al código.

6. Como se suele hacer, se establece un entrono local, de integración, de puesta en escena y de producción.

7. Hay un gran apoyo de la comunidad implementando con grandes imagenes que se pueden reutilizar.

## Despliegue con Docker

Clonar el repositorio que usaremos:

````
$ git clone https://github.com/raul-profesor/DAW_practica_6.1_2024.git
````

Este es el contenido del archivo ``Dockerfile``, para poder construir la imagen y correr el contenedor.

````
_____ node:18.16.0-alpine3.17 

_____  mkdir -p /opt/app

_____ /opt/app

_____ src/package.json src/package-lock.json .

_____ npm install

_____ src/ .

_____ 3000

_____ ["npm", "run", "start:dev"]

````

Cada linea es un comando que docker va a ejecutar para la construcción del contenedor.

De esta manera para tener nuestra aplicación corriendo simplemente serán un par de comandos.

Hacemos un build de la imagen de Docker. Le indicamos que ésta se llama ``librodirecciones`` y que haga build con el contexto del directorio de trabajo, así como del Dockerfile que hay en el:

``$ docker build -t librodirecciones .``

Y solo nos quedaría iniciar el contenerdor con la aplicación. Necesatiaremos especificar las opciones ``-p``, mediante le diremos que escuche peticiones de cualquier máquina desde el peusto 3000 (``-p 3000:3000``), y también la opción ``-d`` que lo haremos correr en background. Así quedaría el comando:

```$ docker run -p 3000:3000 -d librodirecciones```

Solamente nos quedaría comproba el contendedor con ``http://IP_Maq_Virtual:3000``.

 