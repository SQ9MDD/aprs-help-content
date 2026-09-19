---
title: Destination Address, TOCALL y ALTNET en APRS
description: Cómo APRS utiliza el campo Destination Address de AX.25, qué es un TOCALL, cómo identifica software y dispositivos y para qué sirve ALTNET.
template: doc
tableOfContents: true
---

En una trama APRS:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

el elemento:

```text
APBOX0
```

es la **Destination Address**, es decir, el campo de dirección de destino de la trama AX.25.

El nombre puede resultar engañoso. En AX.25 clásico, Destination Address realmente indica el destino de una trama, pero APRS también utiliza este campo para transportar información propia.

En los paquetes APRS modernos típicos suele contener un **TOCALL**, un identificador del programa, dispositivo o firmware que generó el paquete.

Destination Address y TOCALL no son conceptos equivalentes. Un TOCALL es solo una de las formas en que APRS utiliza el campo Destination Address.

## Destination Address en APRS

Destination Address utiliza el mismo formato de dirección AX.25 que Source Address. El campo puede contener hasta seis caracteres y un SSID.

Sin embargo, APRS lo utiliza de varias formas. Según el tipo de paquete puede contener:

- un TOCALL que identifica software o un dispositivo,
- una dirección APRS genérica,
- datos codificados por Mic-E,
- el nombre de una red ALTNET,
- información histórica asociada a mecanismos adicionales de APRS.

Por este motivo, la cadena situada después del carácter `>` no debe interpretarse automáticamente como el indicativo de un destinatario.

## TOCALL

El uso moderno más habitual de Destination Address es identificar el programa o dispositivo que generó un paquete.

Ejemplo:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

Aquí:

```text
SQ9MDD-4
```

es Source Address, mientras que:

```text
APBOX0
```

es Destination Address utilizada como TOCALL.

`APBOX0` es el identificador de APRSBox registrado en la base de datos actual de identificadores de dispositivos APRS.

Un TOCALL permite que el software APRS receptor reconozca qué aplicación, firmware o dispositivo generó un paquete. No identifica al destinatario del paquete.

## Registro actual de TOCALL

Las asignaciones actuales de identificadores APRS se mantienen en:

[github.com/aprsorg/aprs-deviceid](https://github.com/aprsorg/aprs-deviceid)

El archivo fuente principal es:

[tocalls.yaml](https://github.com/aprsorg/aprs-deviceid/blob/main/tocalls.yaml)

El repositorio contiene la base actual de TOCALL e identificadores Mic-E. El archivo YAML es la fuente a partir de la cual se generan también otras versiones legibles por máquinas.

El código fuente de la interfaz de búsqueda de identificadores se mantiene por separado en:

[github.com/aprsorg/aprs-deviceid-web](https://github.com/aprsorg/aprs-deviceid-web)

Al crear un nuevo programa o dispositivo APRS no se debe elegir simplemente un identificador `APxxxx` aleatorio y tratarlo como permanente. La asignación debe comprobarse y registrarse en la base actual.

## ¿Qué aspecto tienen los TOCALL?

Muchos identificadores modernos comienzan por:

```text
AP
```

seguidos de caracteres que identifican un proyecto, fabricante o familia de dispositivos.

Ejemplos de la base actual pueden tener formas como:

```text
APBOX0
APDW??
APDR??
```

No todas las entradas son valores exactos individuales. El registro también admite patrones con comodines, por lo que una sola entrada puede describir una familia de versiones o dispositivos.

Por ello, para reconocer TOCALL conviene utilizar la base actual en lugar de mantener una lista estática copiada manualmente.

## Destination Address genéricas

No toda Destination Address válida en APRS es el TOCALL de un programa concreto.

La especificación APRS también define valores genéricos utilizados para determinados tipos de tráfico, por ejemplo:

```text
APRS
BEACON
CQ
QST
TEST
WX
```

La especificación histórica define un grupo mayor de estas direcciones y permite que algunas contengan caracteres adicionales.

Forman parte del modo en que APRS utiliza Destination Address, pero no identifican una aplicación concreta del mismo modo que los TOCALL modernos.

## Destination Address no es el destinatario de un mensaje APRS

Esta diferencia es especialmente importante en los mensajes.

Ejemplo:

```text
SQ9MDD-7>APBOX0,WIDE1-1::SP9XYZ   :Test
```

contiene:

```text
Source Address:       SQ9MDD-7
Destination Address: APBOX0
```

mientras que el destinatario del mensaje APRS es:

```text
SP9XYZ
```

El destinatario se encuentra dentro del campo Information, es decir, en los datos APRS.

`APBOX0` sigue actuando como TOCALL e identifica el software que generó el paquete.

## Mic-E

Mic-E es una de las excepciones más importantes a la suposición simplificada:

```text
Destination Address = TOCALL
```

En los paquetes Mic-E, parte de la información se codifica directamente en Destination Address.

Los seis caracteres de este campo participan en la codificación de la posición y del estado Mic-E, por lo que una cadena que parece una dirección de destino poco habitual puede contener datos.

El reconocimiento de TOCALL debe tener en cuenta el tipo de paquete. No se puede comparar de forma incondicional cada Destination Address con `tocalls.yaml`.

## ALTNET

**ALTNET**, o Alternate Network, utiliza Destination Address como identificador de una red APRS separada o de un grupo de tráfico.

Por ejemplo, una red experimental podría utilizar:

```text
TEST
```

como Destination Address.

En ese caso `TEST` no es un TOCALL de software ni el indicativo de un destinatario. Identifica una red lógica.

ALTNET permite utilizar la misma infraestructura APRS separando al mismo tiempo determinado tráfico de la vista normal de la red. El software puede entonces mostrar o procesar únicamente los paquetes pertenecientes al ALTNET seleccionado.

ALTNET es otra razón por la que Destination Address no debe considerarse sinónimo de TOCALL.

## Uso histórico del SSID de destino para el encaminamiento

La especificación APRS original también definía el uso del **SSID de Destination Address** para seleccionar una ruta genérica de digipeaters.

Para los SSID de `-1` a `-7` se definieron históricamente las siguientes rutas:

| Destination SSID | Ruta histórica |
|---|---|
| `-0` | utilizar la ruta VIA normal |
| `-1` | `WIDE-1` |
| `-2` | `WIDE-2` |
| `-3` | `WIDE-3` |
| `-4` | `WIDE-4` |
| `-5` | `WIDE-5` |
| `-6` | `WIDE-6` |
| `-7` | `WIDE-7` |

Los valores de `-8` a `-15` estaban destinados a variantes direccionales, entre ellas norte, sur, este y oeste.

De ahí proceden las referencias según las cuales el SSID de Destination Address podía determinar el número deseado de retransmisiones.

Sin embargo, por ejemplo:

```text
APRS-2
```

no debe considerarse equivalente al actual:

```text
WIDE2-2
```

Son mecanismos diferentes.

La codificación de la ruta en el SSID de destino es anterior al mecanismo WIDEn-N y estaba pensada, entre otros casos, para trackers muy simples en los que un pequeño conjunto de interruptores podía seleccionar directamente el Destination SSID.

En documentación APRS más reciente este mecanismo está marcado como **obsolete**. Las implementaciones modernas deben transportar la ruta en el campo AX.25 Digipeater Addresses destinado a ese propósito.

## ¿Y los bits C?

El séptimo octeto de una dirección AX.25 contiene, además del SSID, bits asociados al mecanismo Command/Response de AX.25.

No forman parte del TOCALL ni determinan el significado APRS de Destination Address.

Las implementaciones APRS reales utilizan distintas combinaciones de estos bits. Por ello, el software APRS no debería intentar determinar el significado de un TOCALL, ALTNET u otro uso de Destination Address a partir de la combinación de bits C.

## ¿Cómo interpretar Destination Address?

Para un paquete moderno típico:

```text
SQ9MDD-4>APBOX0,WIDE1-1:...
```

podemos leer:

```text
SQ9MDD-4   Source Address
APBOX0     Destination Address utilizada como TOCALL
WIDE1-1    elemento de la ruta
```

Esta interpretación no puede aplicarse mecánicamente a todas las tramas APRS.

Destination Address puede cumplir varias funciones:

```text
TOCALL
dirección APRS genérica
Mic-E
ALTNET
mecanismos APRS históricos
```

La interpretación correcta depende, por tanto, del tipo de paquete y del contexto del protocolo.
