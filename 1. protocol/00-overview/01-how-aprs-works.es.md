---
title: ¿Cómo funciona APRS?
description: Resumen del recorrido de la información en APRS, desde la transmisión por radio, pasando por digipeaters e IGates, hasta APRS-IS y las aplicaciones.
template: doc
tableOfContents: true
---

APRS es un sistema distribuido.

No existe un único punto central por el que tenga que pasar toda la información. Un paquete puede ser recibido directamente por otra estación, repetido por un digipeater, enviado a APRS-IS por un IGate o llegar al mismo tiempo a varios elementos de la infraestructura.

Por eso, APRS se entiende mejor no como una única ruta lineal, sino como **una red de radio en la que una sola transmisión puede ser utilizada por muchos receptores**.

La ruta más sencilla de un paquete puede tener este aspecto:

```text
Estación A
   |
   | RF
   v
Estación B
```

Pero en una red más grande, el mismo paquete puede llegar simultáneamente a varios receptores:

```text
                  -> Estación B
                 /
Estación A ------+-> Digipeater
                 \
                  -> IGate
```

Cada uno de estos elementos cumple una función diferente.

## El caso más sencillo: de estación a estación

Para que APRS funcione no se necesita Internet, un servidor ni un digipeater.

Si dos estaciones se encuentran dentro de su alcance directo de radio, una puede transmitir un paquete APRS y la otra puede recibirlo directamente.

```text
Estación A
   |
   | RF
   v
Estación B
```

La Estación A puede transmitir, por ejemplo:

- su posición,
- estado,
- frecuencia monitorizada,
- un mensaje,
- datos de telemetría,
- información meteorológica.

Si la Estación B puede recibir e interpretar ese tipo de información, puede utilizarla inmediatamente.

En esta etapa, el paquete no tiene que llegar a ningún otro lugar.

Esto ya es APRS funcionando correctamente.

## ¿Qué se transmite realmente?

Un paquete APRS típico en la capa de radio se transporta en una trama **AX.25 UI**, donde UI significa *Unnumbered Information*.

De forma simplificada, contiene entre otras cosas:

- el indicativo de la estación de origen,
- el campo destination,
- la ruta,
- el campo de información APRS.

En representación textual, un paquete puede tener este aspecto:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

En esta etapa, sin embargo, lo más importante no es cómo está construido exactamente el paquete, sino qué ocurre con él después de ser transmitido.

La estructura detallada de las tramas AX.25 y de los datos APRS se describe en artículos posteriores.

## Una transmisión puede tener muchos receptores

Una transmisión de radio no se dirige a un único dispositivo físico del mismo modo que una conexión clásica punto a punto.

Si varias estaciones se encuentran dentro del alcance del transmisor, todas pueden recibir el mismo paquete.

Por ejemplo:

```text
                -> Estación B
               /
Estación A ----+-> Digipeater
               \
                -> IGate
```

La misma transmisión individual de la Estación A puede ser recibida por:

- otra estación de usuario,
- un digipeater,
- un IGate,
- varios de estos dispositivos al mismo tiempo.

Esto no significa que el paquete tenga que pasar después por todos estos elementos.

Cada receptor puede realizar su propia tarea independientemente de los demás.

## La función del digipeater

Un **digipeater** es una estación que puede recibir un paquete APRS y volver a transmitirlo por radio.

Su función principal es ampliar el alcance radioeléctrico de la información.

Ejemplo:

```text
Estación A
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Estación B
```

La Estación B puede estar fuera del alcance directo de la Estación A y aun así recibir su paquete gracias a la retransmisión.

Sin embargo, un digipeater no repite automáticamente todas las tramas que recibe.

La decisión de retransmitir depende, entre otras cosas, de:

- la ruta del paquete,
- la configuración del digipeater,
- los mecanismos de eliminación de duplicados,
- la política local de la red.

Las redes APRS modernas utilizan normalmente mecanismos de ruta basados, entre otros, en `WIDE1-1` y `WIDE2-n`.

Las reglas detalladas sobre rutas y digipeaters se describen por separado.

## Un digipeater no es un IGate

Un digipeater y un IGate son dos funciones diferentes.

Un digipeater trabaja principalmente en la parte de radio:

```text
RF -> RF
```

Recibe un paquete por radio y, bajo determinadas condiciones, lo vuelve a transmitir por radio.

Un IGate, en cambio, conecta la red de radio con APRS-IS:

```text
RF -> Internet
```

Un mismo dispositivo puede desempeñar ambas funciones a la vez, pero no es obligatorio.

Por tanto, una estación puede ser:

- solo digipeater,
- solo IGate,
- digipeater e IGate al mismo tiempo.

Distinguir estas funciones es importante para comprender toda la infraestructura APRS.

## La función del IGate

Un **IGate**, es decir, Internet Gateway, recibe paquetes del canal de radio local y puede enviarlos a la red APRS-IS.

Ejemplo:

```text
Estación A
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

Si el IGate recibe un paquete de la Estación A, puede enviarlo a APRS-IS.

A partir de ese momento, la información puede estar disponible para:

- clientes APRS,
- servicios cartográficos,
- bases de datos,
- aplicaciones de monitorización,
- otros sistemas que utilizan APRS-IS.

Un IGate no es necesario para que APRS funcione localmente.

Su función principal es conectar la red de radio local con la infraestructura de Internet.

## La función de APRS-IS

**APRS-IS, APRS Internet System**, es una infraestructura global de Internet utilizada para distribuir datos APRS.

Entre otras cosas, APRS-IS recibe paquetes enviados por IGates.

Un esquema simplificado puede tener este aspecto:

```text
Red RF
   |
   v
IGate
   |
   v
APRS-IS
   |
   +-> aplicaciones APRS
   |
   +-> servicios cartográficos
   |
   +-> sistemas de monitorización
   |
   +-> otros servicios
```

APRS-IS permite que las aplicaciones reciban datos procedentes de muchas áreas geográficas diferentes.

Esto no significa que APRS-IS sustituya a la red de radio.

La amplía.

## Una ruta típica de un paquete

Consideremos un ejemplo sencillo.

La estación:

```text
SQ9MDD-7
```

transmite un paquete APRS por radio.

El paquete puede ser recibido por una estación local:

```text
SQ9MDD-7
   |
   | RF
   v
Estación B
```

En ese momento, la información ya es útil.

Al mismo tiempo, el mismo paquete puede ser recibido por un digipeater:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
```

Si la ruta y la configuración lo permiten, el digipeater retransmite el paquete:

```text
SQ9MDD-7
   |
   | RF
   v
Digipeater
   |
   | RF
   v
Estación C
```

El paquete también puede ser recibido por un IGate:

```text
SQ9MDD-7
   |
   | RF
   v
IGate
   |
   | Internet
   v
APRS-IS
```

Y después los datos pueden ser obtenidos por una aplicación:

```text
SQ9MDD-7
   |
   v
IGate
   |
   v
APRS-IS
   |
   v
Aplicación APRS
```

Puede seguir siendo el mismo paquete original.

## Un paquete no tiene que pasar por todas las etapas

Es muy importante no considerar esta ruta como una cadena obligatoria:

```text
Estación -> Digipeater -> IGate -> APRS-IS
```

Es solo una de las rutas posibles.

Un paquete puede recibirse directamente:

```text
Estación A -> Estación B
```

Puede ser recibido por un IGate sin pasar por un digipeater:

```text
Estación A -> IGate -> APRS-IS
```

Puede permanecer únicamente en la red de radio:

```text
Estación A -> Digipeater -> Estación B
```

También puede llegar a varios receptores al mismo tiempo:

```text
                     -> Estación B
                    /
Estación A -> Digipeater -> Estación C
       \            \
        \            -> IGate 2
         \
          -> IGate 1
```

Por tanto, APRS no tiene una única ruta obligatoria para los paquetes.

## Una trama puede llegar por muchos caminos

En una red real, la misma trama puede ser recibida por varios digipeaters y varios IGates.

Por ejemplo:

```text
                    -> IGate 1
                   /
Estación A -> DIGI +-> IGate 2
        \          \
         \          -> Estación B
          \
           -> IGate 3
```

Además, algunos IGates pueden recibir directamente la transmisión original.

Como resultado, la misma información puede llegar a la infraestructura por más de una ruta.

Esto es normal en APRS.

## Duplicados

Como una transmisión puede ser recibida y reenviada por muchos elementos de la infraestructura, APRS tiene que gestionar los duplicados.

La misma trama puede, por ejemplo:

- ser recibida directamente por un IGate,
- volver a ser recibida después de una retransmisión por un digipeater,
- ser recibida por un segundo IGate,
- ser enviada a APRS-IS desde varios lugares.

Por eso, los digipeaters, IGates y servidores pueden utilizar mecanismos para reconocer y descartar repeticiones.

Sin estos mecanismos, una sola transmisión podría multiplicarse innecesariamente en la red.

Las reglas detalladas de gestión de duplicados dependen del elemento concreto de la infraestructura y se describen más adelante en la documentación.

## De RF a APRS-IS

La dirección:

```text
RF -> APRS-IS
```

es una de las funciones principales de un IGate.

Los paquetes recibidos localmente por radio pueden enviarse a APRS-IS, donde pasan a estar disponibles para aplicaciones de Internet.

Esto no significa que todas las tramas recibidas tengan que ser enviadas.

Un IGate puede aplicar reglas y filtros específicos según su configuración y el tipo de tráfico.

## De APRS-IS a RF

La dirección opuesta:

```text
APRS-IS -> RF
```

requiere mucha más precaución.

El canal de radio tiene una capacidad limitada, por lo que no se puede trasladar sin más todo el tráfico de APRS-IS a RF.

Solo determinadas informaciones pueden enviarse a radio de acuerdo con las reglas de funcionamiento del IGate.

Un ejemplo típico es un mensaje dirigido a una estación local que ha sido escuchada recientemente por ese IGate.

La ruta puede tener entonces este aspecto:

```text
APRS-IS
   |
   v
IGate
   |
   | RF
   v
Estación local
```

El tráfico en dirección Internet -> RF requiere control porque cada paquete ocupa tiempo en el canal de radio compartido.

Las reglas detalladas de gating APRS-IS -> RF se describen por separado.

## Third-party traffic

En algunas situaciones, un paquete procedente de otra parte del sistema tiene que volver a colocarse en RF.

APRS dispone para ello de un mecanismo especial denominado **third-party traffic**.

No se trata simplemente de copiar un paquete de texto desde APRS-IS y transmitirlo sin cambios por radio.

La información original se coloca dentro de una estructura especial que conserva información sobre su origen.

Este mecanismo es importante, entre otras cosas, para el envío controlado de determinado tráfico desde APRS-IS hacia RF.

La sintaxis detallada de third-party traffic se describe en la sección dedicada a formatos especiales.

## ¿Qué funciona sin Internet?

Mucho.

Ejemplo:

```text
HT
 |
 | RF
 v
Digipeater
 |
 | RF
 v
Radio móvil
```

Si el operador del equipo móvil recibe el paquete de la estación portátil, APRS ha cumplido su función.

No se necesita APRS-IS.

No se necesita un mapa de Internet.

No se necesita ningún servidor.

**No tener Internet no significa no tener APRS.**

## ¿Qué funciona sin un digipeater?

Si las estaciones están dentro de su alcance directo, no se necesita un digipeater.

```text
Estación A
   |
   +-----> Estación B
   |
   +-----> IGate
```

La Estación B puede utilizar la información localmente, mientras que el IGate puede enviarla de forma independiente a APRS-IS.

Un digipeater solo es necesario cuando la retransmisión amplía realmente el alcance útil de la red.

## ¿Qué funciona sin un IGate?

También puede funcionar una red de radio local completa.

```text
Estación A
   |
   v
Digipeater
   |
   v
Estación B
```

Las estaciones pueden intercambiar posiciones, estados, mensajes y otra información sin ninguna conexión con APRS-IS.

Los paquetes no aparecerán entonces en servicios de Internet, pero la función local de APRS sigue siendo plenamente útil.

## La recepción directa es importante

En la práctica, un paquete recibido directamente puede ser más valioso que la información disponible a través de Internet.

Si una estación cercana transmite:

```text
SP9XYZ
145.550 MHz
```

el operador puede utilizar inmediatamente esta información para establecer una comunicación.

No tiene que esperar a que el paquete:

- sea recibido por un IGate,
- llegue a APRS-IS,
- sea almacenado por un servicio de Internet,
- sea recuperado por una aplicación.

La ruta de radio local es un elemento fundamental de APRS.

## Errores de concepto frecuentes

### Todos los paquetes tienen que pasar por un digipeater

No.

Si el receptor se encuentra dentro del alcance directo del transmisor, puede recibir el paquete sin ninguna retransmisión.

### Un digipeater envía paquetes a Internet

No necesariamente.

La función principal del digipeater es retransmitir paquetes por radio.

La conexión con APRS-IS corresponde a la función IGate.

### Un IGate tiene que ser también un digipeater

No.

Un IGate puede limitarse a recibir tráfico RF y enviarlo a APRS-IS.

### Si un paquete no aparece en APRS.fi, APRS no ha funcionado

No.

El paquete puede haber sido recibido correctamente y utilizado localmente por otras estaciones.

### APRS-IS devuelve todo el tráfico a la radio

No.

El tráfico desde APRS-IS hacia RF tiene que estar limitado y controlado.

### Un paquete tiene una única ruta determinada

No.

La misma transmisión puede ser recibida por muchas estaciones y llegar a la infraestructura por diferentes rutas.

## La imagen completa

Un modelo simplificado del funcionamiento de APRS puede representarse así:

```text
                         +-> Estación local
                         |
[Estación APRS] -- RF ---+-> [Digipeater] -- RF --> otras estaciones
                         |
                         +-> [IGate]
                               |
                               | Internet
                               v
                            [APRS-IS]
                               |
                 +-------------+-------------+
                 |             |             |
                 v             v             v
             aplicaciones     mapas       servicios
```

Lo más importante, sin embargo, es que ninguna de estas rutas es obligatoria.

APRS puede funcionar como:

```text
estación -> estación
```

```text
estación -> digipeater -> estación
```

```text
estación -> IGate -> APRS-IS
```

o por todas estas rutas al mismo tiempo.

## Puntos clave para recordar

**APRS es un sistema distribuido.**

Una transmisión puede ser recibida por muchas estaciones al mismo tiempo.

**Un digipeater amplía la cobertura de radio.**

Su función principal es retransmitir paquetes RF.

**Un IGate conecta la red de radio local con APRS-IS.**

No tiene por qué actuar también como digipeater.

**APRS-IS amplía el alcance de la información más allá de la red de radio local.**

Sin embargo, no sustituye la comunicación RF básica.

**Un paquete no necesita llegar a Internet para cumplir su función.**

La recepción local de la información puede ser su objetivo más importante.

**Una trama puede llegar por muchas rutas.**

Por eso, los duplicados y su eliminación son una parte natural del funcionamiento de la red.

**El tráfico de Internet hacia RF debe estar controlado.**

El canal de radio tiene capacidad limitada y no puede tratarse como una copia de APRS-IS.

## A continuación

Después de comprender cómo viaja la información por la red, conviene estudiar los siguientes elementos del protocolo:

- la relación entre APRS y AX.25,
- la estructura de la trama AX.25,
- la estructura del paquete APRS,
- las direcciones de origen y los SSID,
- la dirección destination y TOCALL,
- las rutas de los digipeaters,
- las diferencias entre RF y APRS-IS,
- los mecanismos de third-party traffic,
- los q-constructs,
- las reglas detalladas de funcionamiento de los IGates.

Solo al combinar estos elementos aparece la imagen completa de cómo la información APRS se mueve entre estaciones y las distintas partes de la infraestructura.
