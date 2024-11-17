# Practica 2.5 - Proxy inverso y balanceo de carga con SSL en NGINX

## Introducción

Teniendo en cuenta en el escenario en el que nos encontramos, ahora mismo los propios servidores web lo cual aumenta la carga que tienen que tienen que soportar, para esto vamos a trasladar el cifrado el servidor proxy, se puede pensar que la comunicación entre el proxy y el servidor web puede ser insegura, pero esta comunicación se lleva a cabo en una red privada, lo cual la protege de cualquier ataque.

## Certificados

HTTPS se basa en el uso de certificados digitales, que resumiendo al entrar en una web se nos entrega un certificado mostrando quien es la página. Para comprobar dichos certificados deberemos acceder a la Autoridad de Certificación (CA). Los navegadores tienen precargadas las Autoridades de certificación en las que confían, por lo tanto si una web usa una diferentes a estas nos aparecerá un error diciendo que esta página no es segura.

