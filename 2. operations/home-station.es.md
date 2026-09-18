---
title: Estación doméstica APRS
description: Cómo montar una estación APRS doméstica sencilla, desde la recepción de paquetes hasta un iGate o digipeater.
template: doc
tableOfContents: true
---

Una estación APRS doméstica es una de las formas más sencillas de empezar a utilizar APRS en la práctica.

Puede limitarse a recibir tráfico local, reenviar los paquetes recibidos a APRS-IS como iGate y, si la red local realmente lo necesita, también funcionar como digipeater.

## ¿Qué se necesita?

Una estación doméstica básica consta de cuatro elementos:

1. **Una radio para la banda de 2 m**
2. **Una antena**
3. **Un módem o TNC**
4. **Un ordenador con software APRS**

La frecuencia APRS depende de la región. En gran parte de Europa, incluida Polonia, APRS en 2 m utiliza **144.800 MHz FM**.

Una instalación típica puede tener este aspecto:

```text
Antena
  |
Radio 144.800 MHz
  |
TNC / módem
  |
Ordenador
  |
Internet
```

El ordenador no tiene que ser un PC convencional. Una Raspberry Pi, un pequeño ordenador x86 u otro sistema de bajo consumo capaz de funcionar continuamente suele ser suficiente.

## Radio

Para recibir APRS no es necesario disponer de una radio con APRS integrado.

Una radio FM normal capaz de trabajar en la frecuencia APRS local es suficiente.

Lo ideal es que la radio permita acceder a:

- audio del receptor,
- audio hacia el transmisor,
- control PTT.

Muchas radios ofrecen estas señales mediante un conector DATA, PACKET o de accesorios.

También es posible utilizar la salida de altavoz y la entrada de micrófono, aunque normalmente requiere más cuidado al ajustar los niveles de audio.

## Módem o TNC

APRS en 2 m utiliza habitualmente AFSK a 1200 baudios.

Para decodificar los paquetes se necesita un módem.

Puede ser un TNC de hardware tradicional, aunque hoy en día es muy habitual utilizar módems por software.

Una solución popular es **Dire Wolf**.

Dire Wolf utiliza la tarjeta de sonido del ordenador como módem AFSK y puede proporcionar una interfaz KISS TCP a otras aplicaciones APRS.

```text
Radio
  |
Tarjeta de sonido
  |
Dire Wolf
  |
KISS TCP
  |
Software APRS
```

## Antena

En una estación doméstica, la antena suele ser más importante que la potencia del transmisor.

Una antena sencilla para 2 m es suficiente para recibir tráfico local.

Si la estación va a funcionar como iGate o digipeater, conviene instalar la antena lo más alta posible y con buena visibilidad del entorno.

Sin embargo, el mayor alcance posible no siempre es una ventaja.

El objetivo de APRS es construir una red de radio útil, no recibir el mayor número posible de estaciones desde distancias muy grandes.

## Ordenador

El software APRS puede ejecutarse en:

- Raspberry Pi,
- un PC,
- un pequeño ordenador x86,
- un servidor doméstico,
- otro ordenador con Linux o Windows.

Para una estación que funciona continuamente, un pequeño ordenador con Linux suele ser una buena solución.

Por ejemplo:

```text
Raspberry Pi
   |
Dire Wolf
   |
KISS TCP
   |
APRSBox
```

## Primer paso: solo recepción

Lo mejor es empezar con una configuración solo de recepción.

Configura la radio en la frecuencia APRS utilizada en tu región.

En Polonia y gran parte de Europa:

```text
144.800 MHz
FM
sin CTCSS
sin DCS
```

Conecta el audio de la radio al ordenador e inicia el módem.

Cuando el nivel de audio esté correctamente ajustado, deberían empezar a aparecer paquetes APRS, por ejemplo:

```text
SQ9ABC-9>APRS,WIDE1-1,WIDE2-1:...
```

En esta fase todavía no es necesario transmitir nada.

Puedes observar tranquilamente la red y comprobar:

- qué estaciones se reciben,
- qué digipeaters funcionan cerca,
- cuánto tráfico existe en el canal,
- si el nivel de audio es correcto.

## Añadir un iGate

El siguiente paso puede ser poner en marcha un iGate.

Un iGate recibe paquetes por radio y los reenvía a APRS-IS.

```text
RF 144.800 MHz
      |
    Radio
      |
    Módem
      |
  APRSBox
      |
   APRS-IS
```

De este modo, las estaciones recibidas por tu instalación pueden aparecer también en la red APRS global.

La configuración suele requerir:

- tu indicativo,
- conexión con APRS-IS,
- un passcode APRS-IS válido,
- un puerto KISS o una conexión TNC configurada.

## ¿Y la transmisión?

No es necesario activar la transmisión inmediatamente.

Un buen orden es:

```text
Recepción
  |
Observación de la red local
  |
iGate RX
  |
Beacon propio
  |
iGate TX opcional
  |
Digipeater opcional
```

Esto permite conocer primero la red APRS local y decidir después qué funciones adicionales son realmente necesarias.

## Beacon propio

Si quieres que la estación sea visible en APRS, puede transmitir periódicamente su posición.

Una estación doméstica fija no necesita hacerlo con mucha frecuencia porque su posición no cambia.

El beacon puede incluir también información adicional, por ejemplo:

```text
PHG...
QRV 145.500 MHz
```

También puede contener información sobre un repetidor local, una página web u otro servicio útil.

## iGate hacia RF

Algunas estaciones también pueden reenviar determinados paquetes desde APRS-IS hacia la radio.

Normalmente se utiliza para mensajes dirigidos a estaciones locales.

Esta función requiere más cuidado.

APRS-IS no debe considerarse una fuente de tráfico que pueda retransmitirse por RF sin límites.

El canal APRS tiene una capacidad limitada, por eso el tráfico enviado desde Internet hacia RF debe filtrarse y limitarse a los paquetes realmente necesarios.

## Digipeater

Una estación doméstica también puede retransmitir paquetes de otras estaciones.

Sin embargo, esto no significa que cada iGate deba ser también un digipeater.

Antes de activarlo conviene comprobar:

- si ya existen digipeaters en la zona,
- qué cobertura ofrecen,
- si existen zonas reales sin cobertura,
- cuál es la ocupación del canal.

Añadir otro digipeater en una zona que ya tiene buena cobertura puede empeorar la red en lugar de mejorarla.

## Ejemplo de una estación completa

Una configuración posible es:

```text
Antena de 2 m
    |
Radio
    |
Interfaz de audio + PTT
    |
Dire Wolf
    |
KISS TCP
    |
APRSBox
    |
APRS-IS
```

APRSBox puede proporcionar entonces funciones como:

- recepción de paquetes,
- visualización del tráfico local,
- iGate,
- beacons propios,
- mensajería APRS,
- digipeater,
- estadísticas de la red.

No es la única configuración posible.

En lugar de Dire Wolf se puede utilizar un TNC de hardware, VP-Digi u otro dispositivo que ofrezca una interfaz KISS.

## ¿Por dónde empezar?

Una ruta sencilla es:

```text
1. Antena
2. Radio en la frecuencia APRS local
3. Dire Wolf
4. Recepción de paquetes
5. APRSBox u otro software APRS
6. iGate RX
```

Solo cuando todo funcione correctamente merece la pena activar la transmisión.

Este método permite conocer el tráfico APRS local antes de añadir más paquetes al canal.

Una estación APRS doméstica no tiene que convertirse en un nodo de red completo desde el primer día. Puede empezar como un simple receptor y desarrollarse gradualmente junto con la experiencia del operador y las necesidades de la red local.
