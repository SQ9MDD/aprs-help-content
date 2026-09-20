---
title: SSID en AX.25 y APRS
description: Explicación técnica del campo SSID de AX.25, las convenciones de APRS y las diferencias entre el direccionamiento por RF y APRS-IS.
template: doc
tableOfContents: true
---

SSID, **Secondary Station Identifier**, forma parte de una dirección AX.25. APRS utiliza este campo de acuerdo con su función en el protocolo y, además, aplica convenciones que indican el uso típico de una estación.

Es necesario distinguir entre:

- SSID como campo de dirección AX.25,
- recomendaciones de SSID utilizadas en APRS,
- usos históricos del SSID,
- identificadores que existen únicamente en APRS-IS o en otros sistemas.

## SSID en AX.25

Una dirección AX.25 está formada por seis octetos para el indicativo y un séptimo octeto que contiene, entre otros campos, el SSID de cuatro bits.

Para una dirección de origen o destino:

```text
bit:   7 6 5 4 3 2 1 0
       C R R S S S S E
```

Para una dirección de digipeater:

```text
bit:   7 6 5 4 3 2 1 0
       H R R S S S S E
```

Significado:

- `SSSS` - SSID,
- `R` - bits reservados,
- `C` - Command/Response,
- `H` - Has Been Repeated,
- `E` - Extension Bit.

El SSID ocupa cuatro bits, por lo que su rango de valores es `0..15`.

Esta limitación se aplica a cualquier dirección AX.25 real, independientemente de que aparezca como dirección de origen, destino o digipeater.

## SSID en APRS

APRS no modifica el formato del SSID de AX.25. Sin embargo, para las estaciones de origen se adoptaron convenciones que permiten sugerir la función prevista de una estación.

Son **recomendaciones**, no forman parte de la sintaxis de APRS ni constituyen una regla de validación.

Recomendaciones clásicas de APRS:

| SSID | Uso típico |
|---|---|
| `-0` | estación principal del operador, normalmente fija y con capacidad de mensajería |
| `-1` | estación adicional genérica |
| `-2` | estación adicional genérica |
| `-3` | estación adicional genérica |
| `-4` | estación adicional genérica |
| `-5` | otras redes o sistemas |
| `-6` | actividad especial, satélites, otras bandas, etc. |
| `-7` | equipo portátil o estación transportable por una persona |
| `-8` | barco, autocaravana o segundo móvil principal |
| `-9` | estación móvil principal, normalmente con capacidad de comunicación |
| `-10` | Internet, iGate, EchoLink, Winlink y usos similares |
| `-11` | globo, aeronave, nave espacial |
| `-12` | APRStt, DTMF, RFID, dispositivos, trackers unidireccionales |
| `-13` | estación meteorológica |
| `-14` | vehículo de conductor profesional o uso similar |
| `-15` | estación adicional genérica |

A nivel de AX.25, `CALL-9` es simplemente una estación con SSID `9`. Su interpretación como estación móvil principal procede de la convención de APRS.

El software no debería utilizar esta tabla para rechazar paquetes ni para imponer un tipo de estación.

## `-9` y `-12`

En las recomendaciones clásicas de APRS, `-9` está destinado principalmente a la estación móvil principal del operador, normalmente con capacidad de comunicación por APRS o voz.

`-12` es apropiado, entre otros usos, para trackers unidireccionales.

Por tanto, un dispositivo instalado en un vehículo no tiene que utilizar automáticamente `-9`. Si únicamente transmite posición y no dispone de comunicación de retorno, `-12` puede describir mejor su función.

## Uso histórico del SSID para seleccionar el símbolo

En las primeras versiones de APRS, el SSID también se utilizaba para seleccionar el símbolo de los trackers que transmitían datos NMEA-0183 sin procesar.

Este mecanismo fue sustituido posteriormente por otros métodos de codificación del símbolo, entre ellos el esquema `GPSxyz` basado en la dirección de destino y los formatos de posición APRS que incluyen directamente el símbolo.

Por tanto, un source SSID moderno no debe utilizarse para determinar el símbolo de la estación.

## SSID de origen, destino y digipeater

La trama:

```text
SQ9MDD-9>APRS-2,WIDE1-1:
```

contiene tres valores SSID independientes:

```text
SQ9MDD-9     source SSID = 9
APRS-2       destination SSID = 2
WIDE1-1      digipeater SSID = 1
```

Todos se codifican de la misma forma a nivel de AX.25, pero su significado en APRS depende de dónde aparezcan.

Las recomendaciones como `-7` handheld, `-9` mobile o `-13` weather se aplican principalmente al **source SSID**. Los SSID de destino y los SSID presentes en la ruta pueden ser utilizados por otros mecanismos de APRS y no deben interpretarse mediante la tabla de funciones de estación.

## APRS-IS y las limitaciones de AX.25

APRS-IS utiliza una representación textual de los paquetes y permite un sufijo tras el guion formado por uno o dos caracteres alfanuméricos, sujeto al resto de restricciones de formato de APRS-IS.

Por ejemplo, es posible el identificador:

```text
SQ9MDD-D
```

Este identificador puede ser utilizado por una estación existente en APRS-IS, por ejemplo una pasarela o un sistema que integre APRS con otra red.

Sin embargo, no puede codificarse como una dirección de origen AX.25 nativa en RF, porque el campo SSID de AX.25 solo contiene un valor de cuatro bits `0..15`.

Por ello, una pasarela APRS-IS -> RF no puede asumir que todos los identificadores válidos de APRS-IS puedan copiarse directamente a un campo de dirección AX.25.

## Third-party traffic

APRS define un mecanismo de **third-party traffic** en el que la cabecera y los datos originales pueden transportarse dentro del campo de información de otra trama AX.25 válida.

Esto permite conservar información sobre una estación procedente de otra red, pero no amplía el espacio de direcciones de AX.25. Un identificador como `SQ9MDD-D` puede aparecer dentro de datos third-party, pero no se convierte por ello en una dirección de origen AX.25 nativa.

## Identificadores fuera del rango `0..15`

En la documentación de APRS aparecen ejemplos históricos de sufijos como:

```text
-63
-tt
-ID
-A ... -Z
```

Se utilizaron para objetos o estaciones generados por sistemas de Internet y otras redes. No son valores adicionales del SSID de cuatro bits de AX.25.

Para una dirección RF nativa, el rango sigue siendo `0..15`.

## Objetos APRS

El nombre de un objeto APRS se transporta en el campo de información, no en el campo de dirección AX.25, y puede contener hasta nueve caracteres.

Por tanto, nombres como:

```text
TEST-63
TEST-ID
WX-A
```

pueden contener terminaciones que parecen SSID, pero esas terminaciones forman parte del nombre del objeto y no están sujetas a la limitación de cuatro bits del SSID de AX.25.

## Consecuencias para pasarelas y software

Una implementación APRS debe distinguir entre la validación de una dirección AX.25 nativa y la validación de un identificador utilizado en APRS-IS.

Para una dirección transmitida directamente por RF, el SSID debe estar dentro del rango `0..15`.

El software no debe asumir automáticamente que cualquier identificador presente en APRS-IS puede:

- convertirse en una dirección AX.25,
- retransmitirse por RF,
- utilizarse como source address,
- enviarse mediante KISS a un TNC como dirección nativa de una trama.

Esto es especialmente importante para pasarelas APRS-IS -> RF, integraciones DMR/APRS y D-STAR/APRS, hotspots, sistemas de telemetría y aplicaciones que generan estaciones APRS exclusivamente en Internet.

## Interpretación del SSID en el software

El SSID puede utilizarse como información auxiliar sobre la función prevista de una estación, pero no debe sustituir los datos contenidos en el paquete.

Si `CALL-9` transmite un símbolo APRS concreto, el software debe utilizar el símbolo del paquete. Si `CALL-13` no transmite datos meteorológicos, el SSID por sí solo no convierte el paquete en un paquete meteorológico.

Las convenciones de SSID sirven principalmente para organizar las estaciones y facilitar su identificación por parte de los operadores.

## Resumen

Reglas principales:

```text
AX.25 SSID
    4 bits
    rango 0..15

APRS source SSID
    utiliza el SSID de AX.25
    puede seguir además las recomendaciones 0..15

APRS-IS
    puede contener identificadores sin
    representación AX.25 directa

objeto APRS
    el nombre se encuentra en el information field
    un sufijo tras un guion no tiene por qué ser un SSID

third-party traffic
    puede transportar una representación textual
    de una estación externa a RF, pero no amplía AX.25
```

Al interpretar un valor escrito después de un guion, primero hay que determinar si forma parte de una dirección AX.25 real, de un identificador APRS-IS o de un nombre transportado en el campo de información.

## Fuentes

- [AX.25 Link Access Protocol for Amateur Packet Radio, Version 2.2](https://www.ax25.net/AX25.2.2-Jul%2098-2.pdf)
- [APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [Bob Bruninga WB4APR, APRS SSID Recommendations](https://github.com/SQ9MDD/APRSBox/blob/main/APRS-SPEC/SSIDs.txt)
- [APRS-IS - Connecting to APRS-IS](https://www.aprs-is.net/connecting.aspx)
- [APRS-IS - Server Design](https://www.aprs-is.net/ServerDesign.aspx)
