---
title: Digipeating en APRS
description: Cómo funciona la retransmisión de paquetes APRS mediante digipeaters, qué significan el H-bit y el marcador *, cómo se procesan las rutas WIDEn-N y por qué la supresión de duplicados es esencial para el funcionamiento correcto de la red.
template: doc
tableOfContents: true
---

Un **digipeater** es una estación digital de retransmisión. Recibe una trama AX.25 completa, analiza su ruta y, si se cumplen las condiciones necesarias, la transmite de nuevo.

Por tanto, no funciona como un repetidor analógico que retransmite la señal de forma continua. Un digipeater trabaja según el principio **store and forward**:

```text
recepción de la trama
    ↓
comprobación de la ruta y de las reglas
    ↓
comprobación de duplicado
    ↓
modificación del campo de ruta
    ↓
retransmisión
```

Lo más habitual es que el paquete se retransmita por el mismo canal de radio, aunque un digipeater multipuerto también puede transferir tráfico entre canales distintos.

La decisión básica de retransmisión afecta al campo de direcciones AX.25 y no al contenido APRS del campo Information. Para gestionar correctamente la ruta, un digipeater no necesita comprender si el paquete contiene una posición, un mensaje, datos meteorológicos o telemetría.

## Nota importante sobre la especificación

El APRS Protocol Reference original describe el formato de las rutas y los mecanismos APRS, pero no contiene un algoritmo completo e inequívoco para el funcionamiento de un digipeater moderno.

Como consecuencia, a lo largo de los años han aparecido implementaciones que difieren en algunos detalles.

Este artículo toma como referencia:

- el modelo moderno y trazable **New-N**,
- las recomendaciones de APRS 1.1,
- el algoritmo de referencia descrito por John Langner WB2OSZ en el documento **APRS Digipeater Algorithm**.

Cuando el comportamiento de equipos antiguos u otras implementaciones puede ser distinto, se indica expresamente.

## Campo de ruta AX.25

Un paquete APRS de ejemplo puede tener este aspecto:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

Después de Source Address y Destination Address aparecen las direcciones de digipeater:

```text
WIDE1-1,WIDE2-1
```

AX.25 permite un máximo de ocho direcciones de digipeater en una sola trama.

Cada una de estas direcciones contiene, entre otros elementos, un **H-bit**, es decir, **Has Been Repeated**. Este bit indica si ese elemento de la ruta ya ha sido utilizado.

El digipeater toma su decisión a partir del **primer elemento de ruta no utilizado**.

Los elementos ya utilizados no se procesan de nuevo.

## H-bit y marcador `*`

En la representación textual de monitor, el estado del H-bit se muestra mediante:

```text
*
```

Ejemplo:

```text
SQ9MDD-7>APBOX0,SR5ABC*:...
```

significa que la dirección `SR5ABC` ya ha sido utilizada durante la retransmisión.

`*` no forma parte del indicativo ni del alias. Es una forma textual de representar el estado del campo de direcciones AX.25.

### Solo la última dirección utilizada lleva `*`

En el formato clásico de monitor, el carácter `*` se coloca junto a la **última dirección de digipeater utilizada**.

Si la ruta tiene este aspecto:

```text
SR5AAA,SR5BBB*
```

significa que se han utilizado:

```text
SR5AAA
SR5BBB
```

y que `SR5BBB` es el último elemento utilizado.

Una representación como:

```text
SR5AAA*,SR5BBB*
```

puede ser mostrada por algunos programas como visualización directa de los H-bits individuales, pero no es la convención estándar del formato clásico de monitor. En la representación estándar, las direcciones utilizadas anteriormente se deducen de la posición del último asterisco.

## Retransmisión por indicativo explícito

El caso más sencillo se produce cuando la estación de origen coloca en la ruta indicativos concretos de digipeaters:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB:...
```

`SR5AAA` ve su propio indicativo como el primer elemento de ruta no utilizado.

Después de la retransmisión obtenemos:

```text
SQ9MDD-7>APBOX0,SR5AAA*,SR5BBB:...
```

A continuación `SR5BBB` puede utilizar el siguiente elemento.

Tras su retransmisión, la representación estándar de monitor será:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

En el propio campo AX.25, el H-bit queda establecido para ambas direcciones utilizadas.

Este modo de funcionamiento es un encaminamiento a través de digipeaters indicados explícitamente.

## Alias

APRS no exige conocer los indicativos concretos de todos los digipeaters de la ruta. En su lugar se utilizan habitualmente alias.

Un digipeater puede responder a:

- su propio indicativo,
- un alias simple configurado,
- un alias del tipo **WIDEn-N**,
- alias locales o regionales construidos según reglas similares.

Si un alias debe ser trazable, un digipeater moderno debería dejar su propio indicativo en la ruta para que posteriormente pueda saberse por dónde pasó realmente el paquete.

Por ejemplo, un alias trazable:

```text
TEMP
```

puede ser sustituido tras la retransmisión por:

```text
SR5ABC*
```

También existen implementaciones de alias no trazables que únicamente marcan el alias como utilizado. Sin embargo, la práctica APRS moderna prefiere rutas de las que pueda reconstruirse el recorrido real del paquete.

## WIDEn-N

El mecanismo moderno más importante para el digipeating general en APRS es **WIDEn-N**.

Ejemplo:

```text
WIDE2-2
```

El primer número:

```text
2
```

define la clase de alias `WIDE2`.

El segundo número:

```text
2
```

es el contador de usos restantes de ese elemento de ruta.

De forma simplificada:

```text
WIDE2-2
      ^
      quedan 2 usos
```

Todo digipeater WIDEn-N que funcione correctamente reduce el segundo número durante la retransmisión.

### Cuando N es mayor que 1

El paquete:

```text
SQ9MDD-7>APBOX0,WIDE2-2:...
```

es recibido por el digipeater `SR5AAA`.

Después de la retransmisión:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

El digipeater:

1. añadió su indicativo a la ruta,
2. lo marcó como utilizado,
3. redujo el contador de `2` a `1`.

El elemento restante:

```text
WIDE2-1
```

pasa a ser el primer elemento de ruta no utilizado y puede ser atendido por otro digipeater.

### Cuando N es igual a 1

Si otro digipeater `SR5BBB` recibe:

```text
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:...
```

el contador queda agotado.

En el algoritmo de referencia no se deja un inútil:

```text
WIDE2-0
```

El alias se sustituye por el indicativo del digipeater.

En la representación estándar de monitor, el resultado será:

```text
SQ9MDD-7>APBOX0,SR5AAA,SR5BBB*:...
```

El paquete ha consumido toda la parte planificada `WIDE2-2`.

Algunas implementaciones antiguas pueden dejar un alias agotado con el H-bit activado, por lo que en registros reales pueden encontrarse otras variantes de representación.

### Cuando N es igual a 0

Un elemento como:

```text
WIDE2-0
```

está agotado y no debería provocar otra retransmisión.

En una ruta correctamente formada, este estado no debería aparecer como primera dirección no utilizada.

## WIDE1-1 y fill-in digipeater

El alias:

```text
WIDE1-1
```

tiene un papel especial en una arquitectura APRS típica.

Se utiliza principalmente para obtener el primer salto local mediante un **fill-in digipeater**, es decir, una estación de menor cobertura situada donde los usuarios no tienen un buen acceso directo a los digipeaters principales de gran cobertura.

Una ruta de ejemplo:

```text
WIDE1-1,WIDE2-1
```

puede procesarse del siguiente modo.

Paquete de origen:

```text
SQ9MDD-7>APBOX0,WIDE1-1,WIDE2-1:...
```

después del fill-in local `SR5FILL`:

```text
SQ9MDD-7>APBOX0,SR5FILL*,WIDE2-1:...
```

y después del digipeater principal `SR5WIDE`:

```text
SQ9MDD-7>APBOX0,SR5FILL,SR5WIDE*:...
```

Un fill-in debe configurarse de acuerdo con su función local. Un fill-in típico responde a `WIDE1-1`, pero no debería comportarse como un digipeater de gran cobertura que atiende cualquier `WIDEn-N`.

La elección de la ruta por parte del usuario es un tema separado. Aquí lo importante es cómo se procesan los distintos elementos de la ruta.

## El primer elemento no utilizado es la clave

El algoritmo básico de un digipeater no recorre arbitrariamente toda la ruta buscando algo que sepa procesar.

El elemento que se analiza en primer lugar es:

**la primera dirección de digipeater no utilizada.**

Para:

```text
SR5AAA*,WIDE2-1,SR5XYZ
```

el primer elemento no utilizado es:

```text
WIDE2-1
```

`SR5XYZ` aparece más adelante y, en funcionamiento normal, no debería "saltar" por encima de `WIDE2-1`.

Esta regla conserva el orden de ruta definido por el emisor y evita que se omitan elementos de forma arbitraria.

## Preemptive digipeating

Algunas implementaciones modernas ofrecen **preemptive digipeating** de forma opcional.

Este mecanismo permite que un digipeater encuentre su propio indicativo o un alias determinado más adelante en la parte no utilizada de la ruta y reaccione a pesar de los elementos anteriores.

Por ejemplo:

```text
WIDE1-1,SR5ABC
```

normalmente requeriría utilizar primero `WIDE1-1`.

Preemptive digipeating puede permitir que `SR5ABC` tome el paquete antes.

No es, sin embargo, una regla básica del digipeating APRS clásico. Modifica la semántica del orden de la ruta y debe utilizarse conscientemente. El comportamiento exacto depende de la implementación.

## Supresión de duplicados

El contador WIDEn-N y los H-bits por sí solos no bastan para proteger la red.

Un digipeater también debe detectar **duplicados**.

La misma transmisión original puede ser recibida por varios digipeaters vecinos y después volver a llegar a uno de ellos por rutas diferentes.

Sin memoria de los paquetes retransmitidos recientemente, la red empezaría rápidamente a generar copias repetidas de la misma información.

En el algoritmo de referencia de WB2OSZ, el digipeater recuerda los paquetes retransmitidos recientemente, normalmente durante aproximadamente:

```text
30 s
```

Si el mismo paquete vuelve a aparecer dentro de esa ventana, no se retransmite de nuevo.

### La ruta no puede formar parte de la comparación de duplicados

Esto es muy importante.

La misma trama puede tener rutas distintas después de pasar por diferentes digipeaters:

```text
SQ9MDD-7>APBOX0,SR5AAA*:...
```

y:

```text
SQ9MDD-7>APBOX0,SR5BBB*:...
```

y aun así pueden ser dos copias de la misma transmisión original.

Por tanto, el campo de ruta no puede decidir si un paquete es un duplicado.

El algoritmo de referencia de WB2OSZ compara:

- Source Address,
- Destination Address, ignorando su SSID,
- el campo Information,

e ignora las direcciones de digipeater.

Las implementaciones suelen guardar un hash de estos datos en lugar de toda la trama.

## Por qué un digipeater no debería modificar los datos del paquete

Durante el digipeating moderno normal, la parte que cambia es la **ruta de digipeaters**.

Un digipeater no debería modificar:

```text
Source Address
Destination Address
Information
```

Modificar cualquiera de estos campos significaría que la copia retransmitida deja de ser la misma trama desde el punto de vista de los mecanismos de detección de duplicados.

Esto es especialmente importante porque distintas copias de una misma transmisión pueden recorrer rutas diferentes y, aun así, deben reconocerse como el mismo paquete.

El mecanismo histórico de encaminamiento mediante Destination SSID fue una excepción a esta regla moderna, pero hoy está obsoleto y no debería ser la base de nuevas implementaciones.

## Protección contra bucles

Una red APRS que funcione correctamente utiliza varias protecciones independientes:

1. El **H-bit** marca los elementos de ruta ya utilizados.
2. La **primera dirección no utilizada** impone el orden de procesamiento.
3. El **contador WIDEn-N** limita el número de retransmisiones posteriores.
4. La **supresión de duplicados** impide volver a transmitir la misma transmisión recibida por otra ruta.
5. El **trazado con el propio indicativo del digipeater** permite ver el recorrido real y detectar comportamientos incorrectos de la red.

Ningún mecanismo individual sustituye a los demás.

## Digipeating traced y non-traced

Históricamente se han utilizado dos formas principales de gestionar los alias.

### Non-traced

El alias permanecía en la ruta y solo cambiaba su estado o contador.

Esta representación no permitía determinar de forma inequívoca qué digipeater físico había realizado la retransmisión.

### Traced

El digipeater inserta su propio indicativo en la ruta.

Ejemplo:

```text
WIDE2-2
```

después del primer salto:

```text
SR5AAA*,WIDE2-1
```

Esto permite al receptor reconstruir la ruta real recorrida por el paquete.

El **New-N Paradigm** moderno prefiere WIDEn-N trazable precisamente porque facilita el análisis, diagnóstico y gestión de la red.

## RELAY, WIDE y TRACE - mecanismos históricos

En documentación APRS antigua pueden aparecer rutas que contengan:

```text
RELAY
WIDE
TRACE
TRACEn-N
```

No deben confundirse con los actuales:

```text
WIDE1-1
WIDEn-N
```

Los antiguos alias `RELAY`, `WIDE` simple y `TRACE` fueron retirados dentro del **New-N Paradigm**, porque las implementaciones anteriores generaban un gran número de duplicados y no ofrecían un control suficiente del tráfico de red.

APRS 1.1 y las recomendaciones posteriores orientan las nuevas implementaciones hacia WIDEn-N trazable y valores pequeños de N justificados por las condiciones de la red local.

Los artículos y configuraciones que utilicen `RELAY,WIDE` deben considerarse material histórico.

## Limitación de valores N demasiado grandes

Un digipeater moderno no tiene por qué aceptar sin límites cualquier valor:

```text
WIDEn-N
```

recibido de un usuario.

Las implementaciones pueden aplicar límites y trampas locales para valores excesivamente grandes, de forma que una sola estación mal configurada no pueda ocupar innecesariamente una parte importante del canal compartido.

Por ejemplo, un paquete con una ruta muy agresiva puede ser:

- descartado,
- limitado a un solo salto,
- reescrito según la política local.

Esto protege un recurso compartido del canal y no cambia el significado básico de WIDEn-N.

## Longitud máxima de la ruta

AX.25 permite un máximo de ocho direcciones de digipeater.

Esto es importante para WIDEn-N trazable porque los digipeaters sucesivos pueden insertar sus indicativos en la ruta.

Si la lista de direcciones ya está llena, la implementación no puede seguir añadiendo nuevos elementos indefinidamente.

El algoritmo de referencia tiene en cuenta este límite al reescribir la ruta.

Los sistemas nuevos no deberían crear rutas artificialmente largas. En APRS práctico, el objetivo es conseguir acceso eficaz a la red local, no maximizar el número de saltos.

## Digipeating retrasado y "viscous"

Algunas implementaciones utilizan un mecanismo adicional de retransmisión retrasada.

El digipeater:

1. determina que el paquete cumple los requisitos para retransmitirse,
2. no lo transmite inmediatamente,
3. escucha durante un breve periodo para comprobar si otro digipeater retransmite la misma trama,
4. cancela su propia transmisión si escucha la copia adecuada.

Esta técnica puede reducir el número de retransmisiones simultáneas en la red.

No es un elemento básico del formato APRS ni de AX.25. Es una estrategia de implementación utilizada por algunos programas y dispositivos.

## ¿Qué modifica realmente un digipeater?

Para el digipeating moderno estándar, la regla más importante puede resumirse en una frase:

> **Un digipeater reenvía el mismo paquete, modificando únicamente la información necesaria para procesar la ruta AX.25.**

Ejemplo:

```text
antes:
SQ9MDD-7>APBOX0,WIDE2-2:!5213.45N/02100.12E>Test

después del primer digi:
SQ9MDD-7>APBOX0,SR5AAA*,WIDE2-1:!5213.45N/02100.12E>Test
```

No han cambiado:

```text
SQ9MDD-7                    Source Address
APBOX0                      Destination Address
!5213.45N/02100.12E>Test    Information
```

Solo ha cambiado la ruta:

```text
WIDE2-2
```

por:

```text
SR5AAA*,WIDE2-1
```

Esta separación es esencial para el funcionamiento correcto del encaminamiento, el trazado de la ruta y la supresión de duplicados.

## Fuentes

Este artículo se basa en:

- [APRS Digipeater Algorithm - WB2OSZ](https://github.com/wb2osz/aprsspec/blob/main/APRS-Digipeater-Algorithm.pdf)
- [APRS Digipeaters - WB2OSZ](https://raw.githubusercontent.com/wb2osz/direwolf-doc/main/APRS-Digipeaters.pdf)
- [Understanding APRS Packets](https://github.com/wb2osz/aprsspec/blob/main/Understanding-APRS-Packets.pdf)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [New-N Paradigm / Fixing Digipeaters](https://www.aprs.org/fix14439.html)
- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
