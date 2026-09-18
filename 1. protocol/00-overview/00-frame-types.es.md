---
title: Tipos de tramas APRS
description: Referencia de los identificadores de tipo de datos (DTI) del campo de información APRS.
---

Una transmisión APRS utiliza normalmente una trama UI de AX.25. Su campo de información empieza con un **identificador de tipo de datos** (DTI): un carácter que indica cómo interpretar los datos que siguen.

```text
ORIGEN>DESTINO,RUTA:DTI datos
```

Esta página describe los tipos de datos APRS que transporta ese campo. No sustituye las reglas de direccionamiento AX.25, rutas, formatos de posición ni los campos concretos de meteorología y telemetría.

## Reglas importantes

- Normalmente el DTI es el primer carácter del campo de información. El formato histórico de posición `!` es una excepción: puede aparecer en cualquier lugar de los primeros 40 caracteres para admitir determinados digipeaters TNC antiguos.
- El DTI identifica el **formato**, no necesariamente un tipo de trama AX.25 distinto. Por ejemplo, mensajes, confirmaciones, boletines y anuncios usan todos `:`.
- Una posición puede ser sin comprimir, comprimida, codificada Mic-E o una sentencia GPS/NMEA en bruto. El DTI selecciona la familia de formato; los caracteres posteriores seleccionan la variante detallada.
- No deben transmitirse identificadores marcados como obsoletos, reservados o «no usar». Un receptor puede encontrarlos en tráfico antiguo.

## Tipos operativos principales

| DTI | Tipo | Contenido |
|---|---|---|
| `!` | Posición sin marca de tiempo, sin mensajería | Posición de una estación; también el formato histórico de estación meteorológica Ultimeter 2000. |
| `=` | Posición sin marca de tiempo, con mensajería | Posición de una estación que admite mensajería APRS. |
| `/` | Posición con marca de tiempo, sin mensajería | Posición con hora. |
| `@` | Posición con marca de tiempo, con mensajería | Posición con hora de una estación capaz de recibir mensajes. |
| `'` o `` ` `` | Posición Mic-E | Posición Mic-E compacta; parte de la información se codifica en la dirección de destino. También existen variantes antiguas; el TM-D700 usa `'` para datos Mic-E actuales. |
| `$` | Datos GPS/NMEA en bruto | Una sentencia GPS transportada directamente por APRS o el formato histórico Ultimeter 2000. |
| `;` | Objeto | Objeto de mapa con nombre, creado y controlado por una estación; sus datos incluyen estado activo o eliminado. |
| `)` | Elemento | Elemento de mapa con nombre más corto. Puede estar activo o eliminado, pero no tiene marca de tiempo ni campo de propietario. |
| `_` | Meteorología sin posición | Observaciones meteorológicas sin posición APRS. |
| `#` o `*` | Meteorología Peet Bros U-II | Datos meteorológicos en los formatos Peet Bros Ultimeter-II. |
| `T` | Telemetría | Telemetría no Mic-E: normalmente número de secuencia, valores analógicos y bits digitales. |
| `:` | Familia de mensajes | Destinatario y texto; también formato de confirmaciones, rechazos, boletines, anuncios y consultas dirigidas. |
| `>` | Estado | Estado de una línea o texto de misión de la estación, opcionalmente precedido por una hora UTC. |
| `<` | Capacidades de estación | Tokens de capacidad, normalmente enviados como respuesta a una consulta de IGate. |
| `?` | Consulta general | Solicitud de difusión de una sola vez, por ejemplo `?APRS?`, `?WX?` o `?IGATE?`. |
| `}` | Tráfico de terceros | Paquete APRS encapsulado que se reenvió por otra red, con la cabecera de ruta y la carga originales. |
| `{` | Datos definidos por el usuario | Extensión asignada o experimental: `{`, un ID de usuario de un carácter, un tipo de paquete de un carácter y datos de aplicación. |
| `,` | Datos inválidos o de prueba | Datos que no cumplen un formato APRS estándar, por ejemplo una corrección GPS inválida indicada por un tracker. |
| `%` | Radiogoniometría | Formato de radiogoniometría Agrelo DFJr/MicroFinder. |

## Informes de posición y ubicación

Los cuatro identificadores de posición ordinarios distinguen la presencia de una hora y la capacidad de mensajería APRS. Los prefijos muestran la diferencia; la sintaxis de coordenadas se define por separado.

| Prefijo | Significado |
|---|---|
| `!4903.50N/07201.75W...` | Posición sin hora y sin mensajería. |
| `=4903.50N/07201.75W...` | Posición sin hora, con mensajería. |
| `/092345z4903.50N/07201.75W...` | Posición con hora, sin mensajería. |
| `@092345z4903.50N/07201.75W...` | Posición con hora, con mensajería. |

Cada forma incluye un símbolo y puede llevar extensiones de posición o un comentario. Por tanto, posiciones comprimidas, rumbo/velocidad, altitud, alcance de radio, datos meteorológicos y otras extensiones no son DTIs independientes.

Mic-E también es un informe de posición, pero sus datos se codifican de forma compacta entre la dirección de destino y el campo de información. Un decodificador debe reconocer Mic-E, no tratar los datos como texto ordinario de latitud/longitud. Un informe GPS/NMEA en bruto que empieza por `$` es otro formato relacionado con posición y necesita un analizador de sentencias NMEA.

## Objetos y elementos de mapa

`; ` introduce un **objeto**. Incluye el nombre del objeto, un indicador de activo (`*`) o eliminado (`_`), hora, posición y símbolo. Un objeto puede representar un repetidor, un punto de evento, un incidente, una estación meteorológica o un servicio situado en el mapa por su propietario.

`) ` introduce un **elemento**. Incluye un nombre de elemento y un indicador de activo (`!`) o eliminado (`_`), seguido de datos de posición. Se utiliza para entidades de mapa breves cuando no se necesitan propietario ni hora.

Un objeto o elemento no es la estación que informa. El indicativo de origen identifica a la estación transmisora; el nombre de la carga identifica la entidad mostrada.

## Meteorología y telemetría

Los datos meteorológicos pueden enviarse de varias maneras:

- `_` inicia un informe meteorológico sin posición.
- Un informe de posición, objeto o elemento puede llevar datos meteorológicos; entonces conserva el DTI de posición, objeto o elemento, y no pasa a ser `_`.
- `#` y `*` identifican los formatos meteorológicos históricos Peet Bros U-II.

`T` inicia la telemetría estándar no Mic-E. El informe básico comienza por `T#` y contiene un número de secuencia, hasta cinco canales analógicos y ocho bits digitales. Los metadatos, parámetros, unidades e información de sentido de bits usan el formato de mensaje de telemetría y, por ello, comienzan por `:` en vez de `T`.

## Mensajes, boletines, consultas y respuestas

La familia de mensajes usa un destinatario de ancho fijo:

```text
:DESTINAT.:texto del mensaje{ID
```

El texto puede ser un mensaje entre estaciones, `ack`/`rej`, un boletín (destinatario como `BLN...`), un anuncio o una consulta dirigida. El identificador de mensaje se utiliza cuando se requieren confirmaciones; las consultas APRS son peticiones de una sola vez y no deben llevar identificador ni confirmarse.

`? ` es el DTI de una consulta general de difusión. Las consultas dirigidas usan `:` porque se envían a una estación concreta. Las respuestas no tienen un único DTI independiente: según los datos solicitados pueden ser una posición, objeto, informe meteorológico, estado, mensaje, traza, informe de estaciones escuchadas o informe de capacidades.

## Encapsulación y extensiones

`} ` significa que la carga contiene un paquete APRS que ha cruzado una red de terceros. Antes de decodificar los datos originales, un analizador debe extraer el origen, destino, ruta y DTI interno incrustados; no es simplemente un prefijo de comentario.

`{ ` se reserva para un formato documentado definido por el usuario. Tras el DTI, una cabecera de dos caracteres asigna los datos a un autor/aplicación y subtipo de paquete. `{{` es la forma explícitamente experimental. Los formatos no admitidos deben ignorarse de forma segura.

## Identificadores históricos, reservados y prohibidos

Forman parte de la referencia APRS 1.0.1 suministrada, pero no son una elección normal para transmisores nuevos.

| Identificador | Estado en la referencia |
|---|---|
| `0x1C`, `0x1D` | Datos Mic-E Rev-0 beta actuales y antiguos; identificadores históricos no imprimibles. |
| `[` | Baliza obsoleta de localizador Maidenhead. |
| `&` | Reservado para una característica de mapa. |
| `+` | Reservado para datos de refugio con hora. |
| `.` | Reservado para meteorología espacial. |
| `"`, `(`, `-`, `\`, `]`, `^` | Sin uso. |
| `A`–`S`, `U`–`Z`, `a`–`z`, `0`–`9` | No usar como DTI. |
| `|`, `~` | No usar: caracteres de conmutación de flujo TNC. |

## Base documental

Esta introducción sigue la *APRS Protocol Reference, APRS Protocol Version 1.0.1* suministrada (capítulo 5, «APRS Data Type Identifiers»). Las notas suministradas sobre meteorología, mensajes, analizador y formatos definidos por el usuario aclaran los formatos relacionados y los casos heredados.
