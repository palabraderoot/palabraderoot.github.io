---
layout: post
title: Aventuras de una Raspberry Pi en Tor (I)
subtitle: Mirando bajo la superficie de la Red
tags: [diy,raspberry,open source]
---

Quizá tengas por ahí una vieja raspberry en un cajón esperando a que decidas darle algún uso. O puede que hayas montado algún servicio ya en ella. Que quieras acceder desde fuera de tu red local a esos datos de los sensores de humedad y temperatura que has colocado en tus ficus.

Quizá como a mí **solo te mueva la curiosidad**, el *enredar* con gadgets y tecnología.

![img](https://i.imgur.com/OBuQaJq.png){: .center-block :}

Sea cual sea tu motivación, ¡vivan los **Proyectos de Fin de Semana**!

En mi caso voy a utilizar una entrada en años **Raspberry Pi Model B Rev 2**. Modelo de 2012 ya descatalogado desde hace un tiempo.

> También del año 2012, es una variante del Modelo A, trajo consigo diversas mejoras, la inclusión del doble de memoria RAM, pasando de 256MB a 512MB. Trajo consigo un puerto USB más y, por fin, un conector Ethernet (RJ-45) Se mantuvo tanto su tamaño como su coste. No hubo variaciones ni en el procesador ni en la parte gráfica. Tiempo después se lanzó el Modelo B+, que incluyó 4 puertos USB y pasó de usar una SD a una MicroSD.
>
> fuente: [**wikipedia**](https://es.wikipedia.org/wiki/Raspberry_Pi#Raspberry_Pi_1_modelo_B_(descontinuada)_y_B+)

El objetivo es que al final de la serie de artículos hayamos podido configurar y asegurar (*con un éxito aceptable, todo es susceptible de mejora*) un **servidor web** como **servicio oculto** dentro de la red **TOR**.

![img](https://i.imgur.com/G32OSvt.jpg){: .center-block :}

> Creemos que todos deberían poder explorar Internet con privacidad. Somos el Tor Project, una ONG 501(c)3 US. Promovemos los derechos humanos y defendemos tu privacidad en linea a través del software libre y las redes abiertas.
>
> fuente: [**torproject**](https://www.torproject.org/es/about/history/)

## ¿Qué es la red Tor?

La red TOR es una red de nodos distribuidos alrededor del mundo, mantenida por voluntarios, por la que una y otra vez nuestro tráfico es enrutado y cifrado. Intentando aislar y homogeneizar a todos los usuarios de la red para resistir la identificación de nuestra huella digital.

Para una información más detallada sobre Tor:

[https://torproject.org](https://torproject.org)

[https://tor.derechosdigitales.org/torificate/](https://tor.derechosdigitales.org/torificate/)

Además la red TOR permite acceder a una parte de la **dark web**, dando acceso a los dominios .onion

> 1. **Technology is neither good nor bad; nor is it neutral.**
> 2. Invention is the mother of necessity
> 3. Technology comes in packages, big and small.
> 4. Although technology might be a prime element in many public issues, nontechnical factors take precedence in technology-policy decisions.
> 5. All history is relevant, but the history of technology is the most relevant.
> 6. Technology is a very human activity – and so is the history of technology.
>
> *Melvin Kranzberg's [[six laws of technology]](https://en.wikipedia.org/wiki/Melvin_Kranzberg)*

Muchas veces, por no decir siempre, al oír términos como *dark web*, *Tor*, etc, damos por sentado que todo el contenido allí alojado va a ser malo por definición.

¿La dark web es utilizada para contenido de dudosa legalidad o directamente ilegal? **Rotundamente sí**, pero también es utilizada para ello la red convencional ([*ejemplo*](https://pousta.com/dealers-modernizados-vendedores-drogas-instagram-tinder/)).  Como bien dice la primera ley de Melvin Krazenberg, *"la tecnología no es buena ni es mala. Pero tampoco neutral"*. 

Todo va a depender de cómo nos acerquemos a la tecnología y con qué fin. Sirva como ejemplo en positivo el de activistas o periodistas que consiguen saltarse la férrea censura de ciertos países y conseguir así una voz gracias a la **dark web**.

## ¿Qué son los dominios .onion?

Reciben el nombre de **hidden services** o servicios ocultos. Un dominio .onion es la dirección de un servicio que **solo es accesible a través de la red TOR**, ya sea una web (*usando TOR Browser*), un servidor IRC (con torsocks, torify...), o cualquier otro servicio.

> Las direcciones con el pseudo-dominio .onion son opacas, no mnemotécnicas y fruto de una combinación de 16 caracteres alfanuméricos generados manualmente, ya que así está basándose en una clave pública cuando Tor es configurado. Esa combinación de 16 caracteres puede ser creada con cualquier letra del alfabeto y con dígitos decimales que empiecen por 2 y acaben en 7 representando así un número de 80-bit en base32
>
> fuente: **[wikipedia](https://es.wikipedia.org/wiki/.onion#targetText=.onion%20es%20un%20pseudodominio%20de,medio%20de%20la%20red%20Tor.)**

