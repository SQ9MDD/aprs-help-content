---
title: APRX
description: Daemon APRS ligero para sistemas POSIX que combina iGate, digipeater, múltiples interfaces de radio, telemetría y enrutamiento APRS-IS.
template: doc
tableOfContents: true
---

APRX es software especializado de infraestructura APRS, diseñado principalmente para funcionar como **iGate, digipeater o una combinación de ambos**.

Se ejecuta como daemon en sistemas POSIX, especialmente Linux, BSD y otros sistemas tipo Unix.

Repositorio oficial:

https://github.com/PhirePhly/aprx/

Página del proyecto:

https://thelifeofkenneth.com/aprx/

APRX está escrito en C y fue diseñado como software ligero para funcionamiento continuo de infraestructura.

No es un módem de audio. Utiliza un TNC externo, módem KISS, una interfaz AX.25 del sistema operativo u otra fuente compatible de tramas AX.25 ya decodificadas.

```text
Radio
  |
TNC / módem KISS
  |
APRX
  |
APRS-IS
```

## Autores e historia

La primera y segunda generación de APRX fueron creadas por **Matti Aarnio OH2MQK**.

```text
2007-2014
```

En los encabezados del código aparece descrito como:

```text
2nd generation APRS iGate and digi
```

Desde 2014 el mantenimiento y desarrollo posterior pasó a **Kenneth W. Finnegan W6KWF**.

Repositorio actual:

https://github.com/PhirePhly/aprx/

APRX es un proyecto maduro que sigue utilizándose en instalaciones reales.

## Filosofía de APRX

APRX fue diseñado con pocos requisitos y pocas dependencias.

Es adecuado para:

- ordenadores pequeños,
- routers,
- dispositivos embedded con Linux,
- Raspberry Pi,
- PCs antiguos,
- servidores 24/7.

No necesita interfaz gráfica.

```text
sistema
  |
daemon aprx
  |
/etc/aprx.conf
```

## APRX no es un módem

APRX no decodifica AFSK desde una tarjeta de sonido.

Necesita tramas AX.25 ya decodificadas, por ejemplo:

```text
Radio
  |
VP-Digi
  |
KISS
  |
APRX
```

o:

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
```

APRX se ocupa de routing, digipeating, iGate, APRS-IS, filtrado, beacons, telemetría y múltiples interfaces.

## RX iGate

```text
RF
 |
Radio
 |
TNC
 |
APRX
 |
APRS-IS
```

Ejemplo:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

## TX iGate

APRX también puede enviar tráfico seleccionado desde APRS-IS hacia RF.

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

Puede utilizar:

```text
relay-type third-party
```

junto con filtrado, rate limiting y viscous delay.

## Digipeater

APRX incluye un digipeater avanzado compatible con:

```text
WIDEn-N
```

y alias AX.25 normales.

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

## New-N

Ejemplos:

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

## Viscous digipeating

APRX puede esperar antes de repetir un paquete.

Si escucha la repetición de otra estación, puede cancelar la suya.

```text
paquete recibido
     |
     v
espera breve
     |
     +---- oído otra vez ---> DROP
     |
     +---- sin repetición --> TX
```

## Múltiples receptores

```text
Radio RX Norte ---> TNC ---\
                            \
Radio RX Sur -----> TNC ----> APRX ---> Radio TX
                            /
Radio RX Local ---> TNC ---/
```

También puede utilizar:

```text
igate-group
```

## Múltiples canales

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
otro RF     ---> canal local
```

## Interfaces KISS

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

## Modo monitor TNC2

APRX puede recibir:

```text
TNC2
```

en formato monitor textual.

## Linux AX.25

En Linux puede utilizar:

```text
ax25-device
```

## DPRS

```text
D-PRS -> APRS
```

## APRS-IS

Ejemplo de servidor:

```text
server rotate.aprs2.net
```

Ejemplo de filtro:

```text
filter "m/100"
```

## Filtrado

Se puede filtrar por:

- origen,
- destino,
- ruta,
- payload,
- área geográfica.

Ejemplo:

```text
filter -b/CALL
```

## Rate limiting

```text
ratelimit
srcratelimit
```

## Beacons

APRX puede generar posiciones, items, objetos, tramas raw y datos desde archivos.

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

## Telemetría

APRX puede recoger:

```text
RX
DROP
TX
```

y otros contadores.

## Monitor Erlang

Intervalos típicos:

```text
1 minuto
10 minutos
20 minutos
```

## Logging

```text
aprx-rf.log
aprx.log
erlang.log
```

## Configuración

Archivo principal:

```text
/etc/aprx.conf
```

Ejemplo:

```text
mycall SQ9ABC-1

<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>

<interface>
    serial-device /dev/ttyUSB0 9600 8n1 KISS
</interface>
```

Secciones habituales:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

## APRX con Dire Wolf

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
  |
APRS-IS
```

Dire Wolf gestiona AFSK y APRX la lógica de infraestructura.

## APRX con TNC de hardware

```text
Radio
  |
VP-Digi / TNC-X / otro TNC
  |
KISS
  |
APRX
```

## Requisitos bajos

APRX funciona bien en Raspberry Pi, pequeños x86, hardware antiguo, Linux embedded y máquinas virtuales.

## Madurez

APRX tiene una larga historia y está probado en muchas instalaciones.

La línea 2.9.1 sigue disponible en Debian.

Hoy debe considerarse principalmente **software maduro de infraestructura**.

## Qué no hace APRX

No es:

- módem de audio,
- cliente APRS gráfico,
- mapa APRS,
- programa de seguimiento,
- aplicación diaria de mensajería.

## ¿Para quién es?

Para:

- RX iGate,
- TX iGate,
- digipeater,
- digi + iGate,
- nodos con múltiples receptores,
- varias interfaces de radio,
- routing,
- filtrado,
- telemetría,
- operación 24/7.

## Licencia

```text
BSD 3-Clause
```

## Resumen

```text
múltiples receptores
      |
      v
    APRX
   /    \
 digi   iGate
   \    /
    RF / APRS-IS
```

APRX combina iGate, digipeating New-N y viscous, múltiples interfaces, KISS, Linux AX.25, filtrado, rate limiting, beacons, telemetría, monitorización de canal, DPRS y APRS-IS.

## Documentación

- repositorio: https://github.com/PhirePhly/aprx/
- página del proyecto: https://thelifeofkenneth.com/aprx/
- configuración de ejemplo: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- configuración compleja: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
