# Configuración de un servidor Nginx con Hosts Virtuales y directorios de usuario

En esta práctica vamos a vamos a crear varios hosts virtuales para alojar múltiple sitios web y que cada usuario de la máquina Debian maodifique su sitio a través de su carpeta personal, esto se consigue haciendo que cada host virtual apunte al directorio public_html.

## Instalación de Nginx

- Tener un entorno Debian.
- Instalar Nginx.
- Usar SSH para manipular el servidor.

## Creación de usuarios del sistema:

- Creación de usuarios.
    - Crea dos usuarios (usuario1 y usuario2).  

    ``sudo adduser usuario1`` ``sudo adduser usuario2``.

    - Asignación de contraseñas