---
layout: post
title: Aventuras de una Raspberry Pi en TOR (I)
subtitle: Mirando bajo la superficie de la Red
tags: [diy,raspberry,open source]
---

Quizá tengas por ahí una vieja raspberry en un cajón esperando a que decidas darle algún uso. O puede que hayas montado algún servicio ya en ella. Que quieras acceder desde fuera de tu red local a esos datos de los sensores de humedad y temperatura que has colocado en tus ficus.

Quizá como a mí **solo te mueva la curiosidad**, el *enredar* con gadgets y tecnología.

![img](https://i.imgur.com/OBuQaJq.png)

Sea cual sea tu motivación, ¡vivan los **Proyectos de Fin de Semana**!

En mi caso voy a utilizar una entrada en años **Raspberry Pi Model B Rev 2**. Modelo de 2012 ya descatalogado desde hace un tiempo.

> También del año 2012, es una variante del Modelo A, trajo consigo diversas mejoras, la inclusión del doble de memoria RAM, pasando de 256MB a 512MB. Trajo consigo un puerto USB más y, por fin, un conector Ethernet (RJ-45) Se mantuvo tanto su tamaño como su coste. No hubo variaciones ni en el procesador ni en la parte gráfica. Tiempo después se lanzó el Modelo B+, que incluyó 4 puertos USB y pasó de usar una SD a una MicroSD.
>
> fuente: [**wikipedia**](https://es.wikipedia.org/wiki/Raspberry_Pi#Raspberry_Pi_1_modelo_B_(descontinuada)_y_B+)

El objetivo es que al final de la serie de artículos hayamos podido configurar y asegurar (*con un éxito aceptable, todo es susceptible de mejora*) un **servidor web** como **servicio oculto** dentro de la red **TOR**.

![img](https://i.imgur.com/G32OSvt.jpg)

> Creemos que todos deberían poder explorar Internet con privacidad. Somos el Tor Project, una ONG 501(c)3 US. Promovemos los derechos humanos y defendemos tu privacidad en linea a través del software libre y las redes abiertas.
>
> fuente: [**torproject**](https://www.torproject.org/es/about/history/)

## ¿Qué es la red TOR?

La red TOR es una red de nodos distribuidos alrededor del mundo, mantenida por voluntarios, por la que una y otra vez nuestro tráfico es enrutado y cifrado. Intentando aislar y homogeneizar a todos los usuarios de la red para resistir la identificación de nuestra huella digital.

Para una información más detallada sobre TOR:

[https://torproject.org](https://torproject.org)

[https://tor.derechosdigitales.org/torificate/](https://tor.derechosdigitales.org/torificate/)

Además la red TOR permite acceder a una parte de la **dark web**, dando acceso a los dominios .onion

## ¿Qué son los dominios .onion?

Reciben el nombre de **hidden services** o servicios ocultos. Un dominio .onion es la dirección de un servicio que **solo es accesible a través de la red TOR**, ya sea una web (*usando TOR Browser*), un servidor IRC (con torsocks, torify...), o cualquier otro servicio.
