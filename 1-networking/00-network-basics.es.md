---
title: Función y elementos de la red APRS, fundamentos de funcionamiento
description: Visión general de la estructura de la red APRS, las funciones de sus elementos, el flujo de información y los principios de funcionamiento de los dispositivos intermediarios.
template: doc
tableOfContents: true
---

APRS forma una red distribuida de intercambio de información. Los datos pueden recibirse directamente por radio, retransmitirse mediante digipeaters y transferirse entre la parte de radio de la red y APRS-IS.

La red no requiere un elemento intermediario central. Dos estaciones dentro de su alcance radioeléctrico directo pueden intercambiar información directamente. Los digipeaters amplían el alcance útil de la red de radio, mientras que los iGate conectan los segmentos RF con APRS-IS.

Los distintos elementos de la red realizan funciones diferentes. Un mismo dispositivo puede realizar varias de ellas simultáneamente, pero cada función tiene su propio ámbito de actuación.

## Elementos de la red APRS

Las funciones básicas presentes en una red APRS son:

- estación final,
- digipeater,
- iGate,
- APRS-IS,
- aplicaciones y servicios que utilizan datos APRS.

Estas funciones describen la tarea realizada dentro de la red, no un tipo concreto de dispositivo.

Una misma instalación puede, por ejemplo, generar sus propios paquetes APRS, retransmitir tráfico como digipeater y enviar los paquetes recibidos a APRS-IS como iGate. Sin embargo, estas funciones siguen siendo independientes.

## Estación final

Una estación final es una fuente o un destinatario de información APRS.

Puede generar, entre otros, datos de posición, estado, meteorología, telemetría, objetos o mensajes. También puede recibir e interpretar datos generados por otras estaciones.

La comunicación no tiene que pasar por una infraestructura intermediaria. Si dos estaciones se encuentran dentro de su alcance radioeléctrico directo, un paquete puede recibirse sin intervención de un digipeater, un iGate o APRS-IS.

## Digipeater

Un digipeater es un elemento de la parte de radio de la red APRS. Su función es retransmitir paquetes de acuerdo con el mecanismo de rutas de APRS.

Esto permite que un paquete llegue más allá del alcance radioeléctrico directo de la estación de origen.

Durante la retransmisión, el digipeater realiza los cambios previstos para la ruta del paquete. La retransmisión no puede utilizarse para modificar arbitrariamente el resto de los datos del paquete. Los datos de origen siguen siendo datos procedentes de la estación que generó el paquete.

Si un dispositivo que trabaja como digipeater genera su propio beacon, estado u otro paquete APRS, se trata de un nuevo paquete originado por esa estación, no de una ampliación del contenido del paquete de otra estación.

Los detalles del procesamiento de rutas y de los algoritmos de digipeating se describen por separado.

## iGate

Un iGate es una pasarela entre la parte de radio de la red APRS y APRS-IS.

La dirección básica de funcionamiento es:

```text
RF -> APRS-IS
```

Un iGate también puede realizar el reenvío controlado de tráfico seleccionado en la dirección:

```text
APRS-IS -> RF
```

Ambas direcciones están sujetas a reglas diferentes.

Al igual que un digipeater, un iGate no puede modificar arbitrariamente los datos de un paquete reenviado. La información asociada con la introducción de un paquete en APRS-IS se coloca en la parte de la ruta destinada a este fin, entre otros mecanismos mediante un `q` construct. No se añade a los datos de origen de la estación.

Las reglas detalladas de gating, los `q` constructs y el tráfico APRS-IS -> RF se describen por separado.

## APRS-IS

APRS-IS es la parte de Internet de la infraestructura APRS. Conecta servidores, clientes, aplicaciones e iGate, permitiendo distribuir paquetes más allá de la cobertura radioeléctrica local.

Un paquete recibido localmente por RF puede ser introducido en APRS-IS por un iGate y quedar disponible para sistemas situados en otras ubicaciones.

APRS-IS no es un sistema independiente y ajeno a la parte radioeléctrica de APRS. Su funcionamiento está relacionado con el tráfico RF y los datos procedentes de APRS-IS pueden, en casos definidos, volver a transmitirse por radio.

## Aplicaciones y servicios

Los datos APRS pueden ser recibidos y utilizados por aplicaciones y servicios especializados.

Entre otras funciones, pueden:

- presentar el estado actual de la red,
- mostrar posiciones y objetos,
- gestionar mensajes,
- registrar datos,
- procesar meteorología y telemetría,
- proporcionar otras funciones basadas en datos APRS.

No todas las aplicaciones que utilizan APRS-IS forman parte de la infraestructura que transporta paquetes. Debe distinguirse un sistema que consume datos de un digipeater, iGate o servidor APRS-IS que participa directamente en su distribución.

## Flujo de información

La comunicación más sencilla es directa:

```text
estación A -> RF -> estación B
```

Con un digipeater:

```text
estación A -> RF -> digipeater -> RF -> estación B
```

Un paquete recibido por un iGate puede enviarse a APRS-IS:

```text
estación A -> RF -> iGate -> APRS-IS
```

Dentro de APRS-IS, el mismo paquete puede estar disponible para muchos clientes y servicios:

```text
                         -> cliente
                        /
RF -> iGate -> APRS-IS ---> servicio
                        \
                         -> otro sistema APRS
```

En casos definidos, el tráfico también puede reenviarse desde APRS-IS a RF:

```text
APRS-IS -> iGate -> RF -> estación
```

No todos los paquetes pasan por todos los elementos de la red. Son distintas rutas posibles de distribución de la información.

## Fuente de información y elementos intermediarios

Debe distinguirse la fuente de la información de los elementos que participan en su reenvío.

Un paquete puede pasar por un digipeater, un iGate y APRS-IS, pero estos elementos no se convierten por ello en la fuente de los datos contenidos en el paquete.

La información necesaria para el funcionamiento de la red, por ejemplo la relacionada con la ruta de radio utilizada o con el punto de entrada en APRS-IS, dispone de sus propios mecanismos y lugares destinados a este fin.

Un elemento intermediario realiza únicamente los cambios derivados de la función que implementa. Su propia información técnica no debe trasladarse a los datos de origen de otra estación.

## Reenvío y generación de paquetes

Reenviar un paquete existente y generar un paquete nuevo son dos operaciones diferentes.

```text
reenviar un paquete existente
```

no es lo mismo que:

```text
generar un paquete propio
```

Un digipeater que retransmite un paquete o un iGate que lo envía a APRS-IS actúa como elemento intermediario.

Si un dispositivo genera su propia información APRS, se crea un nuevo paquete originado por ese dispositivo.

La distinción sigue siendo importante incluso cuando varias funciones son realizadas por el mismo programa, ordenador o equipo de radio.

## Funciones y límites de actuación

Una estación final, un digipeater, un iGate y APRS-IS realizan funciones diferentes.

Una estación final genera o recibe información APRS.

Un digipeater retransmite paquetes dentro de la red de radio y realiza los cambios previstos para la ruta de retransmisión.

Un iGate reenvía paquetes entre RF y APRS-IS según las reglas definidas para esta función. La información relativa al transporte dentro de APRS-IS se almacena en los elementos de la ruta destinados a este fin, entre otros mediante `q` constructs.

APRS-IS distribuye paquetes dentro de la parte de Internet del sistema.

El hecho de que un dispositivo pueda realizar técnicamente varias funciones no fusiona sus reglas de funcionamiento. Para cada paquete es relevante la función con la que el dispositivo lo procesa.

En particular, la infraestructura intermediaria no puede modificar arbitrariamente los datos procedentes de la estación de origen. Los cambios realizados durante el reenvío de un paquete se limitan a los campos y mecanismos previstos para la función de red que se está realizando.

## Canal de radio compartido

La parte radioeléctrica de APRS utiliza un medio compartido de capacidad limitada.

Cada transmisión ocupa el canal y afecta a la posibilidad de transmitir y recibir de las demás estaciones. Esto se aplica a los paquetes generados por las estaciones, a las retransmisiones realizadas por los digipeaters y al tráfico introducido en RF por los iGate.

Por tanto, la configuración de cada elemento afecta no solo al alcance de un paquete individual, sino también al funcionamiento de toda la red local.

Los mecanismos detallados de limitación del tráfico y los principios de diseño de la infraestructura se describen en artículos posteriores de la sección **Red**.

## La red APRS como conjunto

Una posible ruta de un paquete puede representarse de la siguiente forma:

```text
                      +-> estación
                      |
estación -> digipeater +-> iGate -> APRS-IS -> aplicaciones y servicios
                      |
                      +-> otras estaciones RF
```

No es una topología obligatoria.

APRS puede funcionar localmente solo por RF, puede utilizar uno o varios digipeaters, puede estar conectado a APRS-IS o puede utilizar varios de estos mecanismos simultáneamente.

La división básica de funciones sigue siendo:

- una estación crea o recibe información,
- un digipeater amplía su distribución por RF,
- un iGate conecta RF con APRS-IS,
- APRS-IS distribuye la información en la parte de Internet de la red,
- las aplicaciones y los servicios utilizan los datos disponibles.

Cada uno de estos elementos realiza las operaciones previstas para su función. Las reglas detalladas de funcionamiento de los digipeaters, iGate y APRS-IS se desarrollan en las siguientes partes de la sección **Red**.

## Fuentes

- [APRS Documentation Project](https://github.com/wb2osz/aprsspec)
- [APRS-IS](https://www.aprs-is.net/)
- [APRS-IS IGate Details](https://www.aprs-is.net/IGateDetails.aspx)
