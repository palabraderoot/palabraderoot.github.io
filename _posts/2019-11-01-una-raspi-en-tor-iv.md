---
layout: post
title: Aventuras de una Raspberry Pi en Tor (IV)
subtitle: Creando el servicio oculto en Tor
tags: [diy,raspberry,open source]
---

Ya tenemos listo nuestro servidor web, ahora vamos a hacer que sea accesible desde la red Tor con su propia dirección .onion

![](https://i.imgur.com/47F4FVW.png){: .center-block :}

#### Instalando Tor

Es un proceso muy simple, tampoco hay que explicar demasiado.

```sh
sudo apt update 
sudo apt install tor
```

#### Configurando el HiddenService

Como dijimos en el [primer post](https://palabraderoot.github.io/2019-10-04-una-raspi-en-tor-i/) de la serie,

> *...*
> *Reciben el nombre de **hidden services** o servicios ocultos. Un dominio .onion es la dirección de un servicio  que **solo es accesible a través de la red Tor**. No tiene por qué ser una página web, puede ser casi cualquier otro tipo de sistema (irc, ssh, etc).*
>
> *Hay que destacar que al ser un servicio que no sale de la red Tor toda la comunicación es anónima.*
>
> *...*

Vamos al lío, editamos el archivo `/etc/tor/torrc` y ya sea al final del archivo o por la zona que indica la configuración (busca esta línea en el fichero)

```sh
############### This section is just for location-hidden services ###
```

Añadimos lo siguiente

```bash
HiddenServiceDir /var/lib/tor/web/
HiddenServicePort 80 127.0.0.1:80
```

Guardamos el fichero de configuración y reiniciamos Tor

```sh
sudo service tor restart
```

Si todo ha salido bien ahora tenemos nuestro servidor web en Tor con su propia dirección .onion

Vale, ¿*Dónde leches veo la dirección para pasársela a todos mis amigos y un par de conocidos*?

Cada servicio que añadimos en el fichero `/etc/tor/torrc` genera una carpeta con el nombre del servicio que hayamos elegido en `/var/lib/tor/nombre_del_servicio`. Dentro encontraremos dos archivos, `hostname` y `private_key`. Conviene guardar a buen recaudo ambos ficheros para futuras migraciones si no queremos perder la dirección asignada.

En nuestro caso

```sh
hackencio@pizerow:~ $ sudo cat /var/lib/tor/web/hostname
3g2upl4pq6kufc4m.onion
```

Para probar que todo ha ido bien, podemos abrir Tor Browser y visitar la dirección.

![](https://i.imgur.com/QnJNdjp.png){: .center-block :}

Una de las ventajas añadidas de usar Tor es que si estamos detrás de un router NAT no hay necesidad de abrir puertos. Una vez configurado y activado el **servicio oculto** funcionará sin ninguna otra configuración de puertos extra.

***P.S.*** Es probable que la dirección generada por Tor sea del tipo `4acth47i6kxnvkewtm6q7ib2s3ufpo5sqbsnzjpbi7utijcltosqemad.onion`. 

*Don't Panic!*, no es más que la nueva versión (*"v3"*) de los servicios ocultos Tor. Pasa de las direcciones de 16 caracteres a 56.

Trae entre otras cosas una mejora en criptografía, remplazando el algoritmo usado anteriormente *SHA1/DH/RSA1024*, por *SHA3/ed25519/curve25519*.

Para una información más detallada:

[https://trac.torproject.org/projects/tor/wiki/doc/NextGenOnions](https://trac.torproject.org/projects/tor/wiki/doc/NextGenOnions)

Y si aun así, quieres una dirección de las antiguas, siempre puedes añadir HiddenServiceVersion 2 a las dos líneas anteriores en `torrc`.

------

Si te ha gustado el artículo, puedes [invitarme a un café](https://www.paypal.me/TheRealomiK/1.2) vía Paypal [![PayPal Logo](https://i.imgur.com/Tpa3ejG.png)](https://www.paypal.me/TheRealomiK/1.2)
