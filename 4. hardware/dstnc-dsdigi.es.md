---
title: dsTNC y dsDIGI
description: Módem KISS TNC y digipeater APRS de hardware basado en un procesador DSP dsPIC, con un demodulador AFSK 1200 muy eficaz.
template: doc
tableOfContents: true
---

dsTNC es un **módem AFSK 1200 y controlador KISS TNC de hardware** diseñado por Tomek SP9UOB.

El mismo hardware también puede ejecutar el firmware **dsDIGI**, convirtiéndose en un digipeater APRS autónomo.

Página del proyecto dsTNC:

https://sp9uob.verox.pl/dstnc.html

Descripción de dsDIGI:

https://sp9uob.verox.pl/dsdigi.html

El proyecto es un ejemplo interesante del uso de procesamiento digital de señal en un módem APRS de hardware.

En lugar de utilizar un circuito de módem analógico clásico, la decodificación AFSK se realiza mediante un procesador **dsPIC30F4013**.

## Dos usos para el mismo hardware

El hardware dsTNC puede trabajar en dos funciones principales.

### KISS TNC

```text
Radio
  |
audio + PTT
  |
dsTNC
  |
KISS
  |
ordenador / aplicación APRS
```

En este modo el dispositivo gestiona el módem AFSK y la capa AX.25, mientras que el software externo se ocupa del procesamiento posterior de los paquetes.

### Digipeater autónomo

Con el firmware dsDIGI, el equipo puede trabajar sin ordenador:

```text
Radio
  |
audio + PTT
  |
dsDIGI
  |
digipeater APRS
```

La misma placa puede funcionar tanto como módem de hardware como nodo APRS autónomo.

## DSP en lugar de un módem analógico clásico

Una de las características más interesantes de dsTNC es su método de demodulación.

El diseño utiliza **procesamiento digital de señal, DSP**.

El audio del receptor pasa por un filtro pasa-bajos y llega al ADC del procesador.

El procesamiento posterior se realiza por software.

Ruta de recepción simplificada:

```text
Radio
  |
audio
  |
filtro
  |
ADC
  |
DSP
  |
AX.25
```

Este enfoque permite utilizar algoritmos de demodulación más avanzados sin necesidad de un circuito de módem tradicional.

## Demodulador AFSK 1200 muy eficaz

Este es uno de los puntos fuertes del diseño.

El autor utilizó un algoritmo inspirado en una solución descrita por Thomas Sailer HB9JNX.

Durante pruebas con el conocido **WA8LMF APRS Test CD**, dsTNC decodificó correctamente en la segunda pista:

```text
900 tramas
```

En el mismo test, un módem clásico basado en **TCM3105** decodificó:

```text
747 tramas
```

El resultado demuestra la eficacia del demodulador DSP, especialmente con señales ruidosas y distorsionadas.

## AFSK 1200

El modo principal de trabajo de dsTNC es el APRS clásico:

```text
AFSK 1200 Bd
1200 Hz / 2200 Hz
Bell 202
```

Es la modulación estándar utilizada por el APRS clásico en la banda de 2 m.

El audio recibido es muestreado por el ADC del procesador, mientras que la señal transmitida se genera digitalmente mediante PWM y se filtra antes de llegar al transmisor.

## KISS TNC

El firmware dsTNC implementa **KISS TNC**.

El puerto serie puede trabajar a:

```text
57600 bit/s
```

Esto permite usar el dispositivo con:

- aplicaciones APRS,
- Xastir,
- UI-View,
- la pila AX.25 de Linux,
- otro software compatible con KISS.

KISS proporciona una interfaz sencilla y ampliamente utilizada entre un módem/TNC y una aplicación.

Gracias a ello dsTNC no depende de un único programa.

## dsDIGI

El firmware alternativo para el mismo hardware es **dsDIGI**.

Con él, el dispositivo puede trabajar como digipeater APRS autónomo.

La configuración básica incluye:

- indicativo,
- alias del digipeater,
- posición,
- altitud,
- símbolo APRS,
- beacon,
- intervalo del beacon,
- ruta del beacon,
- TXDELAY,
- TXTAIL,
- velocidad del puerto serie.

La configuración se realiza mediante un terminal de texto sencillo.

## WIDE1-1

dsDIGI fue diseñado principalmente para trabajar como digipeater local usando:

```text
WIDE1-1
```

El alias puede modificarse y la implementación admite un alias del tipo `WIDEn-N`.

Esto proporciona una configuración sencilla y clara para un digipeater local.

## Beacon de la estación

dsDIGI puede generar automáticamente su propio beacon de posición.

Se pueden configurar:

- indicativo,
- latitud,
- longitud,
- altitud,
- símbolo,
- comentario,
- ruta,
- intervalo.

La posición se añade automáticamente al contenido del beacon.

Esto permite al digipeater identificarse correctamente en la red APRS.

## Telemetría

Una función interesante de dsDIGI es la generación de telemetría APRS.

El dispositivo puede informar de:

- tensión de alimentación,
- temperatura al conectar un sensor **DS18B20**,
- número de paquetes recibidos,
- número de paquetes transmitidos.

Esto permite observar remotamente el estado básico de la instalación.

En un digipeater instalado en un lugar de difícil acceso puede ser muy útil.

## Información de diagnóstico

dsDIGI no se limita a retransmitir paquetes.

El terminal puede mostrar información sobre las tramas recibidas, como:

- origen,
- destino,
- ruta,
- contenido,
- posición decodificada,
- distancia al digipeater,
- rumbo hacia la estación recibida.

Por ejemplo:

```text
Dist = 56 km
bearing = 78 degrees
```

Esto permite observar cómodamente el tráfico local sin software APRS adicional.

## Estado del digipeater

dsDIGI también genera información de estado propia.

Puede incluir:

- tiempo de funcionamiento,
- número de tramas recibidas,
- número de paquetes repetidos.

Así es posible comprobar fácilmente la actividad del dispositivo.

## Ajuste del trayecto de transmisión

El firmware también puede generar tonos de prueba:

```text
1200 Hz
2200 Hz
```

Pueden utilizarse para ajustar el nivel de audio y la desviación de transmisión.

Es una función muy práctica durante la puesta en marcha del módem.

## Construcción sencilla

Una de las ventajas de dsTNC es su número relativamente reducido de componentes.

La mayoría de las funciones del módem se implementan por software en el procesador dsPIC.

Según el autor, el circuito no requiere ajustes especiales.

Después de programar el procesador, la tarea principal de puesta en marcha es ajustar correctamente el nivel de audio.

## Diseño de hardware basado en DSP

dsTNC y dsDIGI son un ejemplo interesante de hardware APRS basado en DSP.

El proyecto combina:

- demodulación digital AFSK,
- KISS,
- digipeater autónomo,
- telemetría APRS,
- análisis de posiciones de estaciones recibidas,
- diagnóstico sencillo mediante terminal.

El firmware dsDIGI v1.25 disponible en la página del proyecto está fechado:

```text
24 de noviembre de 2013
```

El proyecto sigue siendo técnicamente interesante y muestra un uso práctico del dsPIC como módem y controlador APRS.

## Funciones de dsTNC y dsDIGI

La diferencia se recuerda fácilmente así.

### dsTNC

```text
radio
  |
módem AFSK
  |
KISS
  |
aplicación externa
```

Principalmente un módem de hardware y TNC.

### dsDIGI

```text
radio
  |
módem AFSK
  |
lógica de digipeater
```

Un digipeater autónomo que utiliza el mismo hardware.

El firmware instalado determina la función del dispositivo.

## ¿Para quién es este proyecto?

dsTNC y dsDIGI pueden ser interesantes para quienes:

- quieren utilizar un KISS TNC de hardware,
- necesitan un digipeater autónomo sencillo,
- experimentan con AX.25 y AFSK 1200,
- están interesados en demodulación DSP,
- quieren estudiar una implementación de hardware de un módem APRS,
- ya poseen un dispositivo dsTNC o dsDIGI.

Una parte especialmente interesante del proyecto es el **eficaz demodulador DSP utilizado en dsTNC**.

## Documentación

Fuentes principales:

- dsTNC: https://sp9uob.verox.pl/dstnc.html
- dsDIGI: https://sp9uob.verox.pl/dsdigi.html

En la página del proyecto también están disponibles:

- esquema,
- lista de componentes,
- dibujo de montaje,
- diseño PCB,
- firmware,
- historial de cambios.
