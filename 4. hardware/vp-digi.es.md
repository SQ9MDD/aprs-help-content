---
title: VP-Digi
description: Controlador moderno y de bajo consumo para digipeater APRS y módem KISS con AX.25, FX.25 y varios modos de módem.
template: doc
tableOfContents: true
---

VP-Digi es un controlador autónomo de digipeater AX.25 y un módem TNC KISS destinado, entre otros usos, a APRS.

El proyecto fue diseñado como un dispositivo pequeño, económico y de bajo consumo basado en STM32. Combina las funciones de un TNC de hardware, un módem Packet Radio y un digipeater APRS avanzado.

Repositorio oficial:

https://github.com/sq8vps/vp-digi

Documentación:

https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md

VP-Digi es un proyecto open source y continúa en desarrollo. Las nuevas versiones incorporan correcciones, mejoras de los módems, desarrollo de FX.25 y mejoras en AX.25 y KISS.

## ¿Para qué sirve VP-Digi?

VP-Digi es una alternativa interesante a los TNC clásicos y a los ordenadores más grandes utilizados en estaciones APRS.

Una instalación típica puede verse así:

```text
Radio
  |
audio + PTT
  |
VP-Digi
  |
KISS / USB / UART
  |
APRSBox / ordenador / otra aplicación
```

También puede funcionar de forma totalmente autónoma como digipeater:

```text
Radio
  |
audio + PTT
  |
VP-Digi
```

En este caso no necesita Raspberry Pi, PC ni sistema operativo.

## Arquitectura moderna

VP-Digi está basado en un microcontrolador **STM32F103**.

Esta arquitectura permite realizar digitalmente:

- modulación y demodulación,
- detección de canal ocupado,
- codificación y decodificación AX.25,
- soporte FX.25,
- filtrado de duplicados,
- funciones de digipeater,
- KISS TNC,
- generación de beacons.

No es por tanto un simple tracker ni un generador de paquetes, sino un controlador completo de la capa de radio para Packet Radio y APRS.

## Compatibilidad con AX.25 y APRS

Una de las características más importantes de VP-Digi es la correcta implementación de **AX.25**, protocolo base del Packet Radio clásico y APRS.

El dispositivo puede trabajar como:

- módem AX.25,
- TNC KISS,
- digipeater APRS,
- generador de beacons,
- módem Packet Radio,
- interfaz de radio para software externo.

Gracias a KISS puede colaborar con muchas aplicaciones sin depender de un protocolo propietario del fabricante.

Ejemplo:

```text
VP-Digi
   |
KISS
   |
APRSBox
```

o:

```text
VP-Digi
   |
KISS
   |
software Packet Radio
```

## Módems soportados

VP-Digi soporta varios tipos de módem.

### Bell 202 a 1200 Bd

El módem APRS clásico para VHF:

```text
AFSK 1200 Bd
1200 / 2200 Hz
```

Es el modo básico utilizado por el APRS clásico en 2 m.

### Bell 103 a 300 Bd

Modo destinado principalmente a HF:

```text
AFSK 300 Bd
1600 / 1800 Hz
```

### G3RUH a 9600 Bd

VP-Digi también soporta:

```text
GFSK 9600 Bd
G3RUH
```

Este modo requiere una ruta de audio adecuada y normalmente acceso a flat audio.

No todas las radios permiten 9600 Bd a través de la entrada de micrófono y salida de altavoz convencionales.

### V.23 a 1200 Bd

También está disponible:

```text
AFSK 1200 Bd
1300 / 2100 Hz
```

compatible con V.23.

## FX.25

VP-Digi también soporta **FX.25**.

FX.25 amplía AX.25 con corrección de errores FEC basada en Reed-Solomon. Puede aumentar la probabilidad de recepción correcta en condiciones de radio difíciles manteniendo compatibilidad con AX.25 clásico.

VP-Digi puede:

- recibir AX.25,
- recibir FX.25,
- transmitir AX.25,
- transmitir FX.25.

FX.25 puede configurarse de forma independiente para recepción y transmisión.

## Detección de canal ocupado

VP-Digi no basa el DCD únicamente en datos decodificados correctamente.

La ocupación del canal se detecta mediante la presencia de una señal modulada válida.

Esto permite detectar antes que el canal está ocupado y ayuda a reducir colisiones.

En la práctica, la radio conectada debería trabajar normalmente con el squelch abierto.

## TNC KISS

Una de las funciones principales de VP-Digi es el modo **KISS**.

En esta configuración VP-Digi se ocupa de la capa de radio:

```text
AX.25
modulación
demodulación
PTT
DCD
```

mientras una aplicación externa gestiona la lógica de nivel superior.

Puede ser, por ejemplo:

- APRSBox,
- un cliente Packet Radio,
- software Winlink,
- software propio,
- herramientas de diagnóstico.

Por eso VP-Digi no está limitado a la función de digipeater.

## USB y dos puertos UART

El dispositivo ofrece:

- USB,
- UART1,
- UART2.

Cada puerto puede trabajar de forma independiente como:

- KISS,
- monitor de tramas,
- terminal de configuración.

Esto permite construir instalaciones flexibles, por ejemplo utilizando un puerto para KISS y otro para monitorización y diagnóstico.

## Digipeater APRS

VP-Digi incorpora un mecanismo de digipeater avanzado.

Puede configurar, entre otras cosas:

- 4 alias New-N como `WIDEn-N`,
- 4 alias simples,
- número máximo de saltos,
- modo trace,
- modo direct-only,
- viscous delay,
- filtrado de paquetes,
- buffer de duplicados.

Esto ofrece mucho más control que un repetidor de paquetes simple.

## Viscous delay

VP-Digi soporta **viscous delay**.

En lugar de repetir inmediatamente un paquete, el digipeater puede esperar brevemente y comprobar si otra estación ya lo ha repetido.

En ese caso puede omitir su propia retransmisión.

Esto ayuda a reducir:

- duplicados innecesarios,
- ocupación del canal,
- retransmisiones.

Es especialmente útil en zonas con varios digipeaters con cobertura solapada.

## Direct-only

Para alias individuales también puede activarse el modo **direct-only**.

En este modo el digipeater solo repite paquetes recibidos directamente de la estación de origen.

Esto limita la repetición innecesaria de paquetes que ya han pasado por otros digipeaters.

## Filtrado de estaciones

VP-Digi puede filtrar paquetes por indicativo.

Hay dos modos:

- blacklist,
- whitelist.

Esto permite bloquear determinadas estaciones o aceptar únicamente indicativos seleccionados.

## Protección contra duplicados

El digipeater incluye un buffer para detectar paquetes repetidos.

Evita retransmitir varias veces la misma trama recibida por diferentes rutas.

El tiempo del buffer es configurable.

## Beacons

VP-Digi admite hasta **8 beacons independientes**.

Para cada uno puede configurarse:

- contenido,
- intervalo,
- retraso,
- ruta APRS,
- activación o desactivación.

Ejemplo:

```text
!5002.63N/02157.91E#VP-Digi
```

## Monitor de tramas

VP-Digi también puede funcionar como monitor de tráfico AX.25.

Los paquetes recibidos pueden verse por un puerto serie, lo que resulta útil para:

- puesta en marcha de la estación,
- comprobación de configuración,
- diagnóstico del audio,
- análisis de tráfico Packet Radio y APRS.

## Bajo consumo de energía

Una de las grandes ventajas de VP-Digi es su consumo muy reducido.

Según la documentación del proyecto, el dispositivo consume aproximadamente:

```text
40-50 mA
```

Es especialmente útil en estaciones:

- alimentadas por batería,
- alimentadas por energía solar,
- instaladas donde no hay alimentación permanente,
- usadas como digipeaters autónomos,
- funcionando las 24 horas.

En estas instalaciones, normalmente la radio consume mucha más energía que el propio controlador.

## Proyecto abierto y activo

VP-Digi es un proyecto open source.

El repositorio ofrece:

- código fuente,
- esquema,
- documentación,
- firmware preparado,
- historial de cambios.

El proyecto continúa desarrollándose y las nuevas versiones incorporan correcciones y nuevas funciones.

Para un dispositivo de infraestructura destinado a trabajar durante mucho tiempo, esto es una ventaja importante.

## ¿Para quién es VP-Digi?

VP-Digi encaja bien cuando se necesita:

- un digipeater APRS autónomo,
- un nodo 24/7 de bajo consumo,
- un módem KISS de hardware,
- un módem Packet Radio,
- una interfaz de radio para APRSBox u otro software,
- una plataforma para experimentar con AX.25 y FX.25,
- una estación pequeña de campo o solar.

## VP-Digi frente a un módem por software

VP-Digi y soluciones como Dire Wolf representan dos enfoques diferentes.

### VP-Digi

```text
microcontrolador
bajo consumo
sin sistema operativo
digipeater autónomo
KISS
```

### Módem por software

```text
ordenador
tarjeta de sonido
mayor potencia de cálculo
más flexibilidad DSP
integración más sencilla con servicios de red
```

En una instalación autónoma VP-Digi puede ser más sencillo y eficiente.

En una estación más compleja puede trabajar como módem KISS para software externo.

## Resumen

VP-Digi es un buen ejemplo de hardware APRS moderno.

Combina:

- compatibilidad AX.25,
- soporte FX.25,
- varios tipos de módem,
- digipeater avanzado,
- KISS,
- USB y UART,
- consumo muy bajo,
- firmware open source,
- desarrollo activo.

Puede funcionar tanto como digipeater autónomo como módem dentro de un sistema APRS mayor.

Resulta especialmente interesante donde importan **la compatibilidad con el protocolo, la fiabilidad, el bajo consumo y la posibilidad de trabajar sin un ordenador completo**.

## Documentación

Fuentes principales:

- repositorio del proyecto: https://github.com/sq8vps/vp-digi
- documentación en polaco: https://github.com/sq8vps/vp-digi/blob/main/doc/manual_pl.md
- descripción del proyecto SQ8L: https://sq8l.pzk.pl/index.php/vp-digi-tani-i-funkcjonalny-sterownik-digipeatera-aprs-wraz-z-modemem-kiss/
- changelog: https://github.com/sq8vps/vp-digi/blob/main/CHANGELOG.md
