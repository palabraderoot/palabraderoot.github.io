---
layout: post
title: De sistemas de mensajería, robots y servidores
subtitle: Obtener el notificaciones de un servidor vía Telegram
tags: [linux, bash]
---
No creo que haga falta remarcar la importancia que tienen las aplicaciones de mensajería en nuestro día a día ni lo popular que se está volviendo Telegram.

¿Qué tal si enredamos un poco con una de las funciones mas chulas que nos ofrece? **Los Bots**.

![]( https://i.imgur.com/glrj53R.png ){: .center-block :}

Los bots son aplicaciones de terceros que se ejecutan dentro de Telegram, los usuarios pueden interactuar por comandos o peticiones y se controlan a través de su [API](https://core.telegram.org/bots/api) usando HTTPS.

Las posibilidades son infinitas, y eso es un poco impreciso, así que precisemos. Como muestra vamos a crear un bot y un par de scripts para que nos avise:

- Si se reinicia el servidor.
- Cuando se realice una actualización desatendida de paquetes.

Pero empecemos por el principio,

### Creando el Bot

Tan simple como hablar a [BotFather](https://telegram.me/botfather) (*el bot creador de bots, botception?*) enviando el comando `/newbot`.  Esto iniciará una cadena de preguntas por parte BotFather para configurarlo.

Nos preguntará el nombre del bot (por ejemplo, *Palabra de Root*), su nombre de usuario (ej, "PalaBraDeRootBot")  y a continuación nos dará un token de acceso al API, **guarda ese dato a buen recaudo** porque lo vamos a necesitar ya mismo.{: .center-block :}

![]( https://i.imgur.com/dma4h7w.png )

Otro dato que necesitamos es el **ID de nuestra cuenta en Telegram**, lo puedes conseguir enviando `/start` a otro bot, [Userinfobot](https://telegram.me/userinfobot).

### Creando los scripts

![]( https://i.imgur.com/GC6Fk6E.png ){: .center-block :}

#### Aviso cuando se reinicie el servidor

Que se vaya la luz (y vuelva!), picos de tensión, fallo en un servicio, sobrecalentamiento... Hay muchas razones por las que un servidor puede sufrir un reinicio no programado. Hagamos que nuestro bot nos avise cada vez que haya un reinicio.

Primero el script, muy simple:

```bash
nano ~/mis_scripts/reboot2bot.sh
```

```bash
#!/bin/sh
TOKEN="TU-TOKEN-AQUI"
ID="TU-ID-AQUI"
URL="https://api.telegram.org/bot$TOKEN/sendMessage"
HOST_NAME="$(uname -n)"

MSG="El server [$HOST_NAME] acaba de reiniciarse. Espero que haya sido a propósito!"

curl -s -X POST $URL -d chat_id=$ID -d text="$MSG"
```

```bash
chmod +x ~/mis_scripts/reboot2bot.sh
```

Ya podemos probarlo, si lo lanzamos `~/mis_scripts/reboot2bot.sh`, deberíamos recibir la notificación en nuestra cuenta de Telegram.

#### Aviso de actualizaciones automáticas

Lo he visto demasiadas veces. 

 *Hackencio™* tiene un nuevo proyecto ultra-super-chachi para su raspberry →  *Hackencio* configura todo lo configurable, codifica todo lo codificable → *Hackencio* disfruta, maravillado de su creación →  *Hackencio* deja pasar los meses y piensa ¿Si esto funciona, *pa qué* tocarlo?

Repetid conmigo, **siempre hay que mantener al día nuestro software**, como mínimo se deberían realizar todas las actualizaciones de seguridad.

Lo ideal es hacer este proceso manual y periódico, hay varios problemas bastante serios si seguimos el camino de las actualizaciones automáticas pero, puestos a elegir entre automatizar y no actualizar en absoluto, está claro lo que es mejor. ¿No? Pues vamos al lío.

```bash
nano ~/mis_scripts/update2bot.sh
```

```bash
#!/bin/sh

TOKEN="TU-TOKEN-AQUI"
ID="TU-ID-AQUI"
URL="https://api.telegram.org/bot$TOKEN/sendMessage"

HOST_NAME="$(uname -n)"
UPDATE="$(apt-get update)"
UPGRADE="$(apt-get upgrade -y)"

MSG="[$HOST_NAME] Daily Update Notification %0A$UPGRADE"

curl -s -X POST $URL -d chat_id=$ID -d text="$MSG"
```

También podemos probar este script, solo hay que tener en cuenta que para ejecutar las actualizaciones de sistema hacen falta privilegios así que hará falta usar sudo `sudo ~/mis_scripts/reboot2bot.sh`.

### Programando los scripts

#### Crontab

En dos líneas Crontab es un fichero de texto donde se indican scripts y cuando deben ser ejecutados. Cada usuario tiene su propio fichero para rellenar.

![]( https://i.imgur.com/nzHttN7.png ){: .center-block :}

##### Para el script de aviso de reinicio

Editaremos el archivo crontab del usuario con el siguiente comando

```bash
crontab -e
```

Y añadiremos al archivo

```bash
@reboot sleep 30 && /home/usaurio/mis_scripts/reboot2bot.sh
```

En esa línea le estamos indicando que tras el reinicio y pasados 30 segundos ejecute el script `reboot2bot.sh`

##### Para el script de las actualizaciones automáticas

Modificaremos el archivo crontab del usuario root 

```bash
sudo crontab -e
```

Y añadiremos al archivo

```bash
0 2 * * *  root  /home/usaurio/mis_scripts/update2bot.sh
```

Listo, ahora todos los días a las 2 de la madrugada, se lanzará el script de actualización automática.



Como dije las funciones de las que podemos dotar a nuestro Bot son incontables, si os apetece ver en qué está enredando la gente, hay unos cuantos (*mas de veintisiete mil*) repositorios en github que ojear.

 https://github.com/search?q=telegram+bot 



------

Si te ha gustado el artículo, puedes [invitarme a un café](https://www.paypal.me/TheRealomiK/1.2) vía Paypal [![PayPal Logo](https://i.imgur.com/Tpa3ejG.png)](https://www.paypal.me/TheRealomiK/1.2)
