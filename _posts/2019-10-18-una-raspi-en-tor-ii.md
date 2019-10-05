---
layout: post
title: Aventuras de una Raspberry Pi en Tor (II)
subtitle: Configurando y asegurando la raspberry
tags: [diy,raspberry,open source]
---

Hay ciertos pasos que siempre se deben realizar nada más arrancar por primera vez Raspbian. Una serie de buenas prácticas que nos permitirán asegurar nuestra raspberry. Esto es algo que habría que hacer siempre, pero teniendo en perspectiva nuestro pequeño proyecto (*un hidden service en la red Tor*) se convierte en algo realmente importante.

**Cambiar el password por defecto del usuario pi**

No hay negritas lo suficientemente marcadas como para hacer hincapié en esto. **Nunca hay que dejar la contraseña por defecto en un dispositivo**, y si se trata de uno que pensamos mantener conectado a Internet…

Me viene a la mente lo ocurrido con *Mirai* y los que vinieron detrás, tipos de **malware** que buscan dispositivos **IoT** para intentar conectar vía ssh o telnet **utilizando las contraseñas por defecto**.

*En resumen, Servicio conectado a la Red + Credenciales por defecto,* **mal rollo**.

**Espera, podemos mejorarlo, mandemos a tomar viento al usuario pi**

Aunque una vez cambiada la contraseña hemos dado un gran paso en la dirección adecuada, aún podemos ir más allá y deshacernos totalmente del usuario. Lo haremos en tres pasos.

Creamos un **nuevo usuario** y lo añadimos al **grupo sudo**:

```bash
sudo adduser nuevousuario
sudo adduser nuevousuario sudo
```

Accedemos con nuestro nuevo y flamante usuario para eliminar al usuario **pi** y sus pertenencias:

```
sudo deluser --remove-home pi
```

Con un solo comando hemos logrado dos cosas. Primero, comprobar que hemos sido añadidos al grupo sudo correctamente y mandar a paseo tanto al usuario pi como a su directorio home.

**Actualiza tu sistema operativo**

Otra buena práctica, que no por obvia, hay que dejar de remarcar. **Intenta tener tus sistemas siempre con los últimos parches disponibles**. Seamos francos, no nos va a proteger totalmente de nuevos bugs o problemas de seguridad, pero, sí de todo lo conocido hasta la fecha. No es mal negocio **¿No?**

```
sudo apt update
sudo apt upgrade
sudo apt dist-upgrade
```

**Un cortafuegos poco complicado**

Ahora toca el turno al cortafuegos, vamos a instalar una herramienta que simplifica la configuración de iptables, se trata de **Uncomplicated Firewall (ufw)**.

```
sudo apt install ufw
```

ufw funciona a base de comandos simples, por ejemplo si queremos abrir un puerto, digamos el 22 (*para seguir accediendo via ssh una vez activado el cortafuegos*) el comando sería `sudo ufw allow 22` , para bloquear de nuevo el puerto podemos escribir `sudo ufw deny 22` .

Es posible incluso acotar más incluyendo las fuentes desde las que admitiremos tráfico, algo como `sudo ufw allow 22 from 192.168.0.0/24`. Para ver las reglas que tenemos definidas podemos escribir `sudo ufw status`

Resumiendo:

```
sudo ufw allow 22
sudo ufw enable
sudo ufw status
```

*Venga, que ya casi estamos, un pequeño empujón extra y tendremos la configuración inicial a pedir de Milhouse*

**Ya puestos, ¿por qué no accedemos sin contraseñas?**

En vez de depender de una contraseña, podemos hacer que nuestro acceso y autentificación con el sistema sea **a través de pares de claves seguras**, ¿guay no?

Primero necesitaremos, obvio, un par de claves.

```
ls ~/.ssh
```

Si al listar el directorio oculto `.ssh` encontramos algo similar a `id_rsa id_rsa.pub` ya tenemos claves que podríamos usar, o también las podemos borrar y generar otras nuevas.

Vale, generemos unas claves, **atención** hay que generarlas **en el cliente** no en la raspberry.

```
ssh-keygen -b 4096 -t rsa
```

Una vez generadas solo nos queda pasar a la raspi la clave pública

```
ssh-copy-id -i <nombre-de-usuario>@<ip-de-la-raspi>
```

Si todo ha ido bien, nos pedirá la contraseña del usuario y una vez acabado el proceso, podremos acceder a nuestra rasperry sin necesidad de introducir la contraseña.

**Un par de retoques a nuestro servidor ssh y listo\***

Para terminar, por ahora, vamos a hacer un par, o tres configuraciones extra a nuestro sshd. Para ello, editemos (con nuestro editor favorito por supuesto) el fichero de configuración.

```
sudo nano /etc/ssh/sshd_config
```

Buscamos las siguientes líneas y las dejamos así.

```
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
```

A partir de ahora, o mejor dicho, a partir del siguiente reinicio ya sea completo `sudo reboot` o del servicio ssh `sudo service ssh restart` solo será posible el acceso usando pares de claves.

*No ha sido tan complicado ¿no?*

A partir de aquí se podría seguir configurando y afinando la seguridad mucho más. Configurar desde un detector de rootkits (*rkhunter*) a un analizador de logs (*logwatch*) que nos mande reportes a nuestro correo, o incluso a través de un bot de Telegram (*tengo que hablar del tema en un futuro*).

Creo que para nuestras pruebas y *cacharreos* hemos conseguido una configuración inicial bastante interesante. El siguiente paso será instalar los servicios que queramos colocar en Tor. En nuestro caso, un sencillo servidor web.

------

Si te ha gustado el artículo, puedes [invitarme a un café](https://www.paypal.me/TheRealomiK/1.2) vía Paypal [![PayPal Logo](https://i.imgur.com/Tpa3ejG.png)](https://www.paypal.me/TheRealomiK/1.2)
