---
title: Dirección de origen, indicativo y SSID
description: Cómo APRS identifica el origen de un paquete mediante el campo Source Address de AX.25 y qué función cumplen el indicativo y el SSID.
template: doc
tableOfContents: true
---

En la representación textual de una trama APRS, el primer elemento es la dirección de origen:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

En este ejemplo:

```text
SQ9MDD-7
```

es la **Source Address**, es decir, la dirección de origen de la trama AX.25. Identifica la estación que originó el paquete.

No tiene por qué ser la estación desde la que el paquete se recibió directamente por radio. Si un digipeater retransmite la trama, la Source Address sigue identificando su origen original.

Este artículo se ocupa únicamente de esta parte del direccionamiento. Los elementos situados después del carácter `>` se describen por separado.

## ¿De qué se compone la Source Address?

Una dirección de origen AX.25 está formada por un indicativo y un SSID:

```text
CALLSIGN-SSID
```

Para:

```text
SQ9MDD-7
```

los elementos son:

```text
callsign: SQ9MDD
SSID:     7
```

**SSID** significa **Secondary Station Identifier**.

El indicativo y el SSID son elementos separados de una dirección AX.25. El guion solo forma parte de su representación textual.

## Indicativo

Una dirección AX.25 clásica dispone de un máximo de seis posiciones para la parte correspondiente al indicativo.

Ejemplos de valores válidos:

```text
SQ9MDD
SP5ABC
N0CALL
AB1CDE
```

La cadena:

```text
SQ5AUTO
```

contiene siete caracteres, por lo que no cabe en el campo de indicativo de una dirección AX.25 clásica.

El SSID no cuenta para este límite porque se codifica por separado. Por tanto:

```text
SQ9MDD-15
```

sigue conteniendo el indicativo de seis caracteres `SQ9MDD`.

Las direcciones AX.25 utilizan letras mayúsculas y cifras. El software puede aceptar una entrada en minúsculas y normalizarla a mayúsculas.

## SSID

El SSID permite crear varias direcciones diferentes que utilizan el mismo indicativo base:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

Son cuatro direcciones AX.25 diferentes. Pueden representar distintos dispositivos o distintas funciones que trabajan con el mismo indicativo base.

El rango del SSID es:

```text
0-15
```

Esto se debe al formato AX.25, que reserva cuatro bits para este valor.

Por tanto:

```text
SQ9MDD-15
```

es una dirección válida, mientras que:

```text
SQ9MDD-16
```

queda fuera del rango de SSID definido por AX.25.

### SSID 0

El valor `0` normalmente se omite en la representación textual.

Por ello:

```text
SQ9MDD
```

y:

```text
SQ9MDD-0
```

representan la misma dirección AX.25.

En el uso habitual de APRS se encuentra casi siempre la primera forma.

## ¿Por qué es importante el SSID?

La Source Address completa identifica una estación lógica concreta.

Si varios dispositivos que utilizan el mismo indicativo base deben funcionar simultáneamente en APRS, los distintos SSID permiten diferenciarlos. Cada dispositivo puede entonces transmitir de forma independiente su propia posición, estado, mensajes u otros datos.

Si dos dispositivos utilizan la misma Source Address, a nivel de direccionamiento APRS aparecen como el mismo origen de paquetes. Esto puede provocar, por ejemplo, que se mezclen posiciones, estados o el historial de la estación.

## ¿El número SSID define el tipo de estación?

No de forma absoluta.

En la comunidad APRS existen convenciones populares para algunos números SSID, por ejemplo `-7` para equipos portátiles o de mano y `-9` para estaciones móviles.

Son convenciones de operación, no la función fundamental del campo SSID.

La función principal del SSID es distinguir direcciones de estaciones lógicas. Por tanto, no debe deducirse el tipo de dispositivo únicamente a partir del número SSID.

## La Source Address identifica el origen del paquete

La Source Address identifica la estación que originó el paquete, pero no tiene por qué ser el nombre del elemento descrito por los datos APRS.

Los objetos APRS son un buen ejemplo. Una estación:

```text
SQ9MDD-4
```

puede transmitir un paquete que describe un objeto llamado:

```text
REPEATER
```

La Source Address sigue siendo:

```text
SQ9MDD-4
```

mientras que `REPEATER` es el nombre del objeto contenido en los datos APRS.

Por tanto, la Source Address responde a la pregunta:

**«¿De dónde procede este paquete?»**

No siempre responde a:

**«¿Qué describen los datos contenidos en este paquete?»**

## Ejemplos de direcciones de origen

| Dirección | Válida | Observaciones |
|---|---|---|
| `SQ9MDD` | sí | indicativo `SQ9MDD`, SSID 0 |
| `SQ9MDD-0` | sí | la misma dirección con el SSID 0 escrito explícitamente |
| `SQ9MDD-7` | sí | SSID 7 |
| `SQ9MDD-15` | sí | valor SSID máximo |
| `SQ9MDD-16` | no | SSID fuera del rango 0-15 |
| `SQ5AUTO` | no | el indicativo contiene siete caracteres |

## ¿Qué sigue?

En la trama de ejemplo:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

ya conocemos el significado del primer elemento:

```text
SQ9MDD-7
```

El siguiente artículo describe la **Destination Address**, el campo situado inmediatamente después del carácter `>`, y la forma en que APRS lo utiliza, entre otros usos, como TOCALL.
