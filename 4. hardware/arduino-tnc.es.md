---
title: Arduino TNC y Arduino TNC Plus
description: Módem KISS TNC abierto para APRS, desarrollado desde el proyecto original de KI4MCW, pasando por los experimentos de M1GEO, hasta Arduino TNC Plus de SQ9MDD y SQ5RWU.
template: doc
tableOfContents: true
---

Arduino TNC es un proyecto abierto de **KISS TNC para AFSK 1200** cuya historia incluye varias etapas de desarrollo y aportaciones de varios radioaficionados.

El proyecto comenzó con Robert Marshall **KI4MCW**, que creó un módem AFSK por software sin utilizar un circuito de módem dedicado.

El audio se muestreaba con el ADC de un microcontrolador AVR, mientras que la demodulación AFSK y la decodificación AX.25 se realizaban por software.

Proyecto original de KI4MCW:

https://sites.google.com/site/ki4mcw/Home/arduino-tnc

Experimentos y desarrollo por George Smart M1GEO:

https://www.george-smart.co.uk/arduino/arduino_tnc/

Versión de hardware polaca:

https://hamspirit.pl/SQ9MDD/?p=337

Arduino TNC Plus:

https://hamspirit.pl/SQ9MDD/?p=541

Código desarrollado por SQ9MDD y SQ5RWU:

https://github.com/SQ9MDD/arduino_tnc

## Idea del proyecto

La idea era sencilla: utilizar un microcontrolador AVR popular como módem AFSK sin un chip de módem especializado.

Esquema funcional:

```text
Radio
  |
audio + PTT
  |
Arduino
  |
AFSK / AX.25
  |
KISS
  |
ordenador / aplicación APRS
```

Arduino se ocupa de:

- muestreo de audio,
- demodulación AFSK,
- decodificación AX.25,
- generación de la señal de transmisión,
- control PTT,
- comunicación KISS.

La aplicación externa realiza las funciones de nivel superior de APRS o Packet Radio.

## Orígenes: KI4MCW

Robert Marshall KI4MCW comenzó a trabajar en Arduino TNC alrededor de 2010.

El proyecto era un módem APRS experimental por software pensado principalmente para plataformas Arduino basadas en AVR.

Uno de sus objetivos principales era realizar la mayor parte posible del trabajo directamente en el código del microcontrolador.

En lugar de utilizar un dispositivo como TCM3105 o MX614, el audio recibido se enviaba directamente al ADC del procesador.

```text
Radio
  |
audio
  |
ADC
  |
algoritmo de demodulación
  |
AX.25
```

Las versiones posteriores añadieron, entre otras funciones:

- recepción AFSK 1200,
- KISS,
- DCD,
- corrección automática del bias del ADC,
- transmisión de paquetes,
- soporte UART.

El código se optimizó intensamente para los recursos limitados de los AVR.

## Demodulación por software

El aspecto más interesante del proyecto original fue prescindir de un módem analógico dedicado.

Arduino muestreaba la señal de audio y detectaba los tonos:

```text
1200 Hz
2200 Hz
```

utilizados por el AFSK Bell 202 clásico de APRS.

Este enfoque permitía construir un módem completo con muy pocos componentes.

Al mismo tiempo exigía un código cuidadosamente optimizado porque el ATmega328P dispone de recursos de procesamiento limitados.

## Experimentos de M1GEO

George Smart M1GEO probó en detalle el proyecto KI4MCW y experimentó con su propio circuito de entrada.

Su trabajo incluyó:

- filtrado de entrada de audio,
- estabilización del bias del ADC,
- reducción de ruido procedente de USB,
- análisis de la frecuencia de muestreo,
- pruebas de distintas versiones del código KI4MCW.

M1GEO utilizó la versión 0.14 y obtuvo un buen funcionamiento como KISS TNC.

También experimentó con el envío de datos APRS por Bluetooth a un teléfono y diseñó su propia placa.

Esto demuestra una característica importante de Arduino TNC: el proyecto era suficientemente abierto y sencillo como para adaptarlo a distintos usos.

## Versión polaca de Arduino TNC

Sobre la base de los trabajos anteriores, SQ9MDD documentó una versión de hardware.

El objetivo era crear una interfaz TNC sencilla y económica que pudiera construirse con componentes fácilmente disponibles.

Configuración típica:

```text
Radio
  |
Arduino TNC
  |
USB / KISS
  |
Raspberry Pi / PC
  |
software APRS
```

La construcción se probó tanto con material de prueba WA8LMF como en el canal APRS real.

Funcionó bien como módem pequeño para Raspberry Pi, un iGate o una estación doméstica.

## Desarrollo del software por SQ5RWU

Łukasz **SQ5RWU** realizó una contribución importante a las versiones posteriores del código.

El historial de desarrollo incluye:

- comprobación CRC de las tramas recibidas,
- correcciones de estabilidad,
- mejoras en recepción,
- mejoras en transmisión,
- limpieza del código,
- correcciones necesarias para un funcionamiento estable.

La versión:

```text
0.15.3
```

contiene un conjunto importante de correcciones y mejoras de SQ5RWU.

Después se preparó:

```text
0.15.4
```

marcada como versión estable.

Al arrancar, el dispositivo muestra:

```text
Arduino TNC v.0.15.4
```

## KISS

Arduino TNC funciona como un **KISS TNC** clásico.

Configuración típica:

```text
Radio
  |
Arduino TNC
  |
KISS
  |
APRSBox / Xastir / Linux AX.25 / otro software
```

KISS es una interfaz estándar y sencilla entre un TNC y una aplicación.

Gracias a ello el dispositivo no depende de un programa concreto.

## AFSK 1200

El modo principal es:

```text
AFSK 1200 Bd
1200 / 2200 Hz
Bell 202
```

la modulación APRS clásica utilizada en VHF.

Arduino genera por software el audio de transmisión y controla el PTT.

## Arduino TNC Plus

La siguiente etapa del proyecto fue **Arduino TNC Plus**, o ATNC+.

Se desarrolló como ampliación del diseño anterior.

Se mantuvo la funcionalidad básica, pero se amplió la interfaz de radio.

El cambio más importante fue la incorporación de:

```text
TCM3105
```

utilizado como etapa de regeneración de señal según una solución de Adam SP5RZP.

Esquema funcional:

```text
Radio
  |
trayecto de audio
  |
TCM3105
  |
Arduino
  |
KISS
  |
ordenador
```

El objetivo era mejorar la señal entregada a la parte digital y aumentar la fiabilidad de decodificación en condiciones reales de radio.

## Cambios de hardware en ATNC+

Además del TCM3105, se introdujeron otras mejoras.

Entre ellas:

- condensadores de bloqueo de continua en las entradas de transformadores,
- trayecto de audio mejorado,
- posibilidad de controlar radios portátiles,
- jumper adicional de configuración,
- resistencia de 2,2 kΩ para determinados métodos de control PTT.

La placa ATNC+ es mayor que la versión original debido al circuito adicional del módem.

## Software estable para ATNC+

Las pruebas de Arduino TNC Plus mostraron también la necesidad de nuevas mejoras del software.

Una gran parte de este trabajo fue realizada por SQ5RWU.

El resultado fue una versión estable utilizada con ATNC+.

El código está disponible aquí:

https://github.com/SQ9MDD/arduino_tnc

Este repositorio debe considerarse una línea de desarrollo posterior y corregida, no la versión original de KI4MCW.

## Parámetros de comunicación

En la versión estable de Arduino TNC, el puerto serie trabaja a:

```text
19200 bit/s
```

para la interfaz KISS.

## Aislamiento de audio

El diseño utiliza transformadores de aislamiento de audio.

Su función es separar eléctricamente la radio de la parte digital.

Esto ayuda a reducir:

- bucles de masa,
- ruido del ordenador,
- problemas de nivel de audio,
- interferencias relacionadas con USB.

## Control PTT

Arduino controla el transmisor automáticamente.

El trayecto PTT utiliza aislamiento y un transistor.

```text
Arduino
  |
PTT
  |
aislamiento
  |
Radio
```

Esto permite trabajar de forma segura con distintos tipos de radios.

## Aplicaciones

Arduino TNC y ATNC+ pueden utilizarse como módem para:

- APRS iGate,
- estación APRS doméstica,
- Packet Radio,
- Raspberry Pi,
- ordenadores Linux,
- aplicaciones compatibles con KISS,
- proyectos experimentales propios.

Una instalación típica puede ser:

```text
Radio
  |
Arduino TNC Plus
  |
USB
  |
Raspberry Pi
  |
APRSBox
```

## Valor educativo

Uno de los mayores valores de Arduino TNC es que permite estudiar todo el recorrido del paquete.

El proyecto muestra en la práctica:

- cómo llega el audio al ADC,
- cómo se detectan los tonos AFSK,
- cómo se decodifica una trama AX.25,
- cómo funciona CRC,
- cómo funciona KISS,
- cómo se genera la señal TX,
- cómo se controla PTT.

Es un proyecto muy útil para personas interesadas tanto en APRS como en microcontroladores AVR.

## Historia del proyecto

El desarrollo puede resumirse así:

```text
KI4MCW
  |
Arduino TNC original por software
  |
M1GEO
  |
pruebas y desarrollo del trayecto de radio
  |
SQ9MDD
  |
versión de hardware polaca
  |
SQ5RWU
  |
correcciones y estabilización del código
  |
Arduino TNC Plus
```

No es, por tanto, una construcción de un solo autor, sino el resultado de experimentos y mejoras sucesivas realizadas por varios radioaficionados.

## ¿Para quién es este proyecto?

Arduino TNC puede ser interesante para quienes:

- quieren construir su propio KISS TNC,
- experimentan con Arduino y AVR,
- quieren conocer AFSK 1200 en la práctica,
- necesitan un módem sencillo para Raspberry Pi,
- están interesados en AX.25,
- quieren comprender cómo funciona un módem por software.

Arduino TNC Plus resulta además interesante para quienes quieren utilizar el trayecto de radio ampliado con regeneración de señal.

## Documentación

Fuentes principales:

- Arduino TNC original de KI4MCW: https://sites.google.com/site/ki4mcw/Home/arduino-tnc
- experimentos de M1GEO: https://www.george-smart.co.uk/arduino/arduino_tnc/
- Arduino TNC SQ9MDD: https://hamspirit.pl/SQ9MDD/?p=337
- Arduino TNC Plus: https://hamspirit.pl/SQ9MDD/?p=541
- código posterior de SQ9MDD/SQ5RWU: https://github.com/SQ9MDD/arduino_tnc
