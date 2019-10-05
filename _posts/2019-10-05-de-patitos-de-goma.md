---
layout: post
title: De patitos de goma
subtitle: Estúpidos y sensuales puertos USB...
tags: [diy,ethical hacking,open hardware,pentesting]
---

Hoy me apetece hablar sobre **Rubber Ducky**, un dispositivo muy majo para tener en nuestro *arsenal de [equipo rojo](https://www.viewnext.com/que-es-un-red-team/)*.

![patito de goma sobredimensionado](https://i.imgur.com/Cfak1lC.jpg){: .center-block :}

Bueno, en realidad no voy a hablar de la estupenda y bien terminada herramienta de pentesting que la empresa Hak5 creó, sino de lo que ocurrió después...

Voy a tratar de resumirlo:

*Hackencio™* descubre [Rubber Ducky de hak5](https://shop.hak5.org/products/usb-rubber-ducky-deluxe) → *Hackencio* flipa con la idea del dispositivo → *Hackencio* encuentra desorbitado el precio → *Hackencio* construye su propia versión y por supuesto, por muchísimo menos dinero.

### ¿Qué es Rubber Ducky?

El concepto es simple, un USB Rubber Ducky es una herramienta que emula un teclado (dispositivo HID) disfrazado de pendrive normal y corriente. Una vez pinchado en el dispositivo objetivo, ejecuta las instrucciones (*payload*) que hayamos preparado para la ocasión.

>  Nota para administradores y empresas, **la seguridad física también existe**.

![](https://i.imgur.com/oj80Row.jpg){: .center-block :}



Vale, *Hackencio* me ha convencido, quiero construir mi propio patito. ¿Qué necesito?

### Material necesario

Necesitaremos una placa **Digispark ATTiny85** que se puede encontrar en páginas como [aliexpress](https://es.aliexpress.com/wholesale?catId=0&initiative_id=SB_20191004102534&SearchText=attiny85&switch_new_app=y), [ebay](https://www.ebay.es/sch/i.html?_from=R40&_trksid=m570.l1313&_nkw=digispark+attiny85&_sacat=0), o en [Amazon (*enlace de afiliado*)](https://amzn.to/2AHdSIb). Su precio ronda el euro y medio por placa si la pides a china o los tres euros si compras en ebay / amazon. 

También el **IDE de Arduino**, desde el que programaremos nuestro pato.

Unos pocos datos sobre la placa de desarrollo Digispark ATTiny85

> Digispark es una placa de desarrollo de microcontroladores basada en Attiny85 (Amtel) similar a la línea Arduino, solo que más barata, más pequeña y un poco menos potente. 
>
>- Aquí están las especificaciones:
>- Soporte para Arduino IDE 1.0+ (OSX / Win / Linux)
>- Alimentación a través de USB o fuente externa: 5v o 7-35v (se recomienda 12v o menos, selección automática)
>- Regulador de 500ma 5V
>- USB incorporado
>- 6 pines de E / S (2 se usan para USB solo si su programa se comunica activamente a través de USB; de lo contrario, puede usar los 6 incluso si está programando a través de USB)
>- Memoria Flash de 8k (aproximadamente 6k después del gestor de arranque)
>- I2C y SPI (vis USI)
>- PWM en 3 pines (más posible con Software PWM)
>- ADC en 4 pines
>- LED de encendido y LED de prueba / estado

### Preparando el IDE

En primer lugar debemos ir a **archivo > preferencias** y buscar donde pone *Gestor de URLs adicionales de Tarjetas* para introducir la siguiente URL `http://digistump.com/package_digistump_index.json`

![](https://i.imgur.com/aggV2FU.png){: .center-block :}

Segundo toca dirigirse a **herramientas > placa > gestor de tarjetas** para instalar *digistump AVR Boards by Digistump*.

![](https://i.imgur.com/g3z1UkJ.png){: .center-block :}

Tercero y último, ya podremos seleccionar el tipo de placa y ponernos manos a la obra! **herramientas > placa > Digispark (Default - 16.5mhz)**

![](https://i.imgur.com/SG4bOJw.png){: .center-block :}

Es posible que tu sistema no reconozca de primeras la Digispark, no por falta de ganas, sino por falta de drivers. 

En windows necesitarás descargar lo siguiente: 

[https://github.com/digistump/DigistumpArduino/releases/download/1.6.7/Digistump.Drivers.zip](https://github.com/digistump/DigistumpArduino/releases/download/1.6.7/Digistump.Drivers.zip)

En linux, aquí tienes un tutorial paso a paso:

[https://digistump.com/wiki/digispark/tutorials/linuxtroubleshooting](https://digistump.com/wiki/digispark/tutorials/linuxtroubleshooting)

### Subiendo nuestro primer sketch

A ver, esto es arduino, si no subimos el viejo confiable sketch que hace parpadear el led de la placa para probar si funciona, esto *no es arduino ni es ná*.

```
// the setup routine runs once when you press reset:
void setup() {                
  // initialize the digital pin as an output.
  pinMode(0, OUTPUT); //LED on Model B
  pinMode(1, OUTPUT); //LED on Model A   
}

// the loop routine runs over and over again forever:
void loop() {
  digitalWrite(0, HIGH);   // turn the LED on (HIGH is the voltage level)
  digitalWrite(1, HIGH);
  delay(1000);               // wait for a second
  digitalWrite(0, LOW);    // turn the LED off by making the voltage LOW
  digitalWrite(1, LOW); 
  delay(1000);               // wait for a second
}
```

### Ahora, nuestro primer script

Vamos a hacer un pequeño sketch que en un sistema windows abra un navegador (iexplore), acceda a este blog y lo ponga a pantalla completa.

```
/*
 * Pequeño script para demostrar el uso de un Rubber Ducky en sistemas Windows
 * 
 * Enviamos la combinación de teclas Win + R, ejecutamos iexplore con una página 
 * web y finalmente mandamos F11 para poner el navegador a pantalla completa.
 * 
 */

#include "DigiKeyboard.h"

void setup() {
  // void
}

void loop() {
  DigiKeyboard.delay(2000);
  DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT);
  DigiKeyboard.delay(200);
  DigiKeyboard.println("iexplore palabraderoot.github.io");
  DigiKeyboard.delay(200);
  DigiKeyboard.sendKeyStroke(KEY_F11);
  for(;;){ /*empty*/ }
}
```

En este ejemplo hemos hecho algo bastante inocuo y que no pasa de ser una pequeña muestra de lo que se podría conseguir realmente con intención y *mala baba*. Solo hace falta darse una vuelta por [algunos repositorios](https://github.com/search?q=rubber+ducky+digispark+scripts) para comprobarlo.
