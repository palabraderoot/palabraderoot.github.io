---
layout: post
title: Aventuras de una Raspberry Pi en Tor (III)
subtitle: Preparando el servidor web con NGINX
tags: [diy,raspberry,open source]
---

NGINX es un servidor web ligero que vamos a utilizar para, no creo que se sorprenda nadie, servir páginas web en la red Tor desde la Raspberry PI. La instalación y configuración inicial es bastante sencilla, vamos a ello.

![](https://i.imgur.com/wqJVXPG.png)

### Instalación y configuración inicial

```bash
sudo apt update
sudo apt install nginx
```

Sencillo, ¿No?. Ya tenemos instalado un pequeño servidor web, si apuntamos hacia la ip de la raspberry con un navegador podremos ver si todo ha salido bien.

![](https://i.imgur.com/KsCF5NU.png)

### Afinando la configuración teniendo en mente Tor

#### Enredando con los headers

En la configuración predeterminada de NGINX el banner del servidor **expone el número de versión** de NGINX que estamos usando. Esto se podría considerar **[una vulnerabilidad](https://www.netsparker.com/blog/web-security/information-disclosure-issues-attacks/)**, una filtración de identidad que puede hacer mucho más sencillo identificar o incluso explotar algún fallo conocido.

Voy a llamar a *Hackencio™* para que nos haga una demostración.

```bash
hackencio@pizerow:~ $ curl -I 192.168.2.78
HTTP/1.1 200 OK
Server: nginx/1.14.2
Date: Sat, 05 Oct 2019 18:24:45 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Sat, 05 Oct 2019 18:12:27 GMT
Connection: keep-alive
ETag: "5d98dd0b-264"
Accept-Ranges: bytes
```

Como podemos ver, aparece la versión del servidor web, `Server: nginx/1.14.2` 

Para enmascarar la versión y que no aparezca en los headers hay que editar el archivo **nginx.conf** para buscar y descomentar la línea **server_tokens off**

```bash
sudo nano /etc/nginx/nginx.conf

...
http {

        ##
        # Basic Settings
        ##

        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;
        server_tokens off;
...
```

Solo nos resta salvar la configuración y reiniciar el servidor NGINX

```bash
sudo service nginx restart
```

Si ahora nuestro buen amigo repitiese la prueba anterior...

```bash
hackencio@pizerow:~ $ curl -I 192.168.2.78
HTTP/1.1 200 OK
Server: nginx
Date: Sat, 05 Oct 2019 18:39:17 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Sat, 05 Oct 2019 18:12:27 GMT
Connection: keep-alive
ETag: "5d98dd0b-264"
Accept-Ranges: bytes
```

Ya no aparece la versión, y eso es bueno.

#### Trabajando en las "orejas" de NGINX

Por defecto NGINX va a tener las orejas bien abiertas, va a responder en el puerto 80 a cualquiera y lo que es más importante, por cualquier sitio. Vamos a encargarnos de hacer un poco más restrictivo nuestro servidor.

En Tor, cuando alguien hace una petición web a nuestra dirección .onion, tras todos los saltos entre nodos, sus enrutamientos y cifrados cebolleros varios, a nuestro servidor va a aparecerle la petición como una conexión desde 127.0.0.1, y ahí es donde vamos a escuchar. * 

Para hacer cambios es recomendable primero hacer una copia de seguridad del fichero de configuración **default**.

```bash
sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.bkup
```

Y ahora manos a la obra, por defecto NGINX se pone a la escucha en cualquier dispositivo en el puerto 80, tanto por ipv4 como ipv6.

```bash
sudo nano /etc/nginx/sites-available/default
```

Buscamos estas líneas:

```bash
server {
        listen 80 default_server;
        listen [::]:80 default_server;
```

Las cambiamos por:

```bash
server {
        listen 127.0.0.1:80;
```

#### Evitando leaks de directorio

Cuando se puede ver públicamente la estructura de directorio y archivos de un servidor estamos revelando demasiada información interna de cómo funciona nuestro servicio. Lo que nos puede llevar a otra vulnerabilidad de filtrado de información ([Information Disclosure Issues](https://www.netsparker.com/blog/web-security/information-disclosure-issues-attacks/)).

Vamos a arreglarlo también. Volvemos a editar el archivo **default**

```bash
sudo nano /etc/nginx/sites-available/default
```

Buscamos las líneas:

```bash
     # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _;
```

Y les añadimos a continuación:

```bash
		autoindex off;		
```

Ya es el momento de volver a reiniciar NGINX para aplicar los últimos cambios.

```bash
sudo service nginx restart
```

#### Generando nuestra portada

¿Qué sería de nuestro servidor si no le ponemos una portada personalizada? 

![](https://i.imgur.com/qD3reB0.png)

Primero nos deshacemos de la página de bienvenida de NGINX. Podemos borrarla o renombrarla.

```bash 
sudo mv /var/www/html/index.nginx-debian.html /var/www/html/index.nginx-debian.bkup
```

Luego crearemos nuestro archivo index.html

```bash
sudo nano /var/www/html/index.html
```

Con algo similar a:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Adorad al hipnosapo!</title>
    <style>
      #content {
        display: table;
        margin: 0 auto;
        text-align: center;
      }

      #container {
        width:100%
      }
    
    </style>
  </head>
  <body>
    <div id="container">
      <div id="content">
          <img src="https://i.imgur.com/sc1GYoJ.gif">
          <h1>Adoradme!</h1>
      </div>
    </div>
  </body>
</html>
```

En el próximo artículo crearemos nuestro **hidden service** en la red Tor.

------

Si te ha gustado el artículo, puedes [invitarme a un café](https://www.paypal.me/TheRealomiK/1.2) vía Paypal [![PayPal Logo](https://i.imgur.com/Tpa3ejG.png)](https://www.paypal.me/TheRealomiK/1.2)
