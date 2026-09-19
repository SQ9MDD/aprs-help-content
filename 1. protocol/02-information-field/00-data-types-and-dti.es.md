---
title: Tipos de datos y DTI
description: Cómo está estructurado el campo Information de APRS, qué tipos principales de datos define el protocolo, qué es el Data Type Identifier y por qué DTI no es lo mismo que un tipo de datos APRS.
template: doc
tableOfContents: true
---

Los datos APRS propiamente dichos se transportan en el campo **Information** de una trama AX.25 UI.

En una representación textual de monitor, como el formato TNC2, el campo Information es la parte situada después del separador `:`:

```text
SQ9MDD-4>APBOX0,WIDE1-1:!5213.45N/02100.12E...
                            ^^^^^^^^^^^^^^^^^^^^^^
                            campo Information
```

El propio separador `:` no es un byte adicional del campo Information de AX.25. Forma parte de la representación textual de la trama.

APRS no define un único formato universal para todo el campo Information. Una posición, un mensaje, un informe meteorológico, telemetría o un objeto se codifican de forma distinta. Por tanto, antes de interpretar los datos el receptor debe determinar con qué tipo y variante de datos está tratando.

El mecanismo básico para ello es el **Data Type Identifier**, abreviado **DTI**.

## Modelo general del campo Information

APRS Protocol Reference 1.0.1 describe el modelo general del campo Information como:

```text
DTI | APRS Data | APRS Data Extension | Comment
```

Esto no significa que todos los paquetes deban contener las cuatro partes.

En el modelo clásico:

- `DTI` ocupa 1 byte,
- `APRS Data` tiene una longitud dependiente del formato,
- la clásica `APRS Data Extension`, si aparece, ocupa 7 bytes,
- `Comment` es opcional y su longitud depende del formato concreto.

`APRS Data Extension` es un concepto de la especificación básica e incluye extensiones clásicas como `course/speed`, `PHG`, `RNG` o datos de señal DF. Sin embargo, esos 7 bytes no deben considerarse el único lugar posible para todas las extensiones posteriores de APRS. Con el tiempo aparecieron extensiones adicionales codificadas en el comentario, como `!DAO!` o Base91 Comment Telemetry.

El comentario tampoco es simplemente "el resto del texto" en cualquier paquete. En algunos formatos puede contener datos adicionales con una sintaxis definida.

## Tipos principales de datos APRS

APRS Protocol Reference distingue **10 tipos principales de datos APRS**. Es una clasificación semántica, es decir, responde principalmente a la pregunta:

**¿qué transporta este paquete?**

No es una lista de valores DTI. Un mismo tipo de datos puede usar varios DTI diferentes y algunos DTI han tenido significados históricos sobrecargados.

| Tipo de datos APRS | DTI y formatos típicos | Significado |
|---|---|---|
| **Position** | `!`, `=`, `/`, `@`, Mic-E `` ` `` y `'`, NMEA bruto `$` | Posición de la estación. Los cuatro DTI básicos de posición distinguen timestamp y capacidad de mensajería. Mic-E tiene su propia codificación y `$` puede transportar sentencias NMEA brutas. |
| **Direction Finding** | normalmente `!`, `=`, `/`, `@`; históricamente también `%` | Un informe DF estándar es una variante del informe de posición con símbolo adecuado y datos DF, por ejemplo `/BRG/NRQ`. `%` se utilizó en el formato Agrelo DFJr / MicroFinder. |
| **Objects and Items** | `;` para Object, `)` para Item | Describe un elemento distinto de la estación fuente. Object e Item tienen DTI separados y distinta sintaxis. |
| **Weather** | `_`, y también `!`, `=`, `/`, `@`; históricamente `!`, `#`, `$`, `*` para formatos WX brutos | `_` significa informe meteorológico sin posición. La meteorología también puede ir asociada a un informe de posición. Los antiguos formatos brutos de estaciones meteorológicas siguen apareciendo en la documentación, pero APRS 1.1 no los recomienda para nuevas implementaciones. |
| **Telemetry** | `T` en el formato clásico | La telemetría clásica usa paquetes `T#...`. También puede aparecer en otros lugares, históricamente en Mic-E o posteriormente como Base91 Comment Telemetry dentro del comentario de posición. |
| **Messages, Bulletins and Announcements** | `:` | Todos usan el mismo DTI. La sintaxis posterior determina si se trata de un mensaje normal, un bulletin o un announcement. |
| **Queries** | `?` para consultas generales; `:` para consultas dirigidas | Las consultas generales comienzan con `?`. Una consulta dirigida a una estación concreta se codifica como mensaje APRS y usa por ello DTI `:`. |
| **Responses** | sin un único DTI propio | Una respuesta utiliza el formato apropiado para los datos devueltos, por ejemplo posición, objeto, meteorología, estado o mensaje. Una respuesta Station Capabilities usa DTI `<`. |
| **Status** | `>` | Estado de una línea de la estación. Puede contener texto normal y, en formatos definidos, información adicional. |
| **Other** | entre otros `{`, `}`, `,` | Otros mecanismos, como User-Defined Format, third-party traffic y datos marcados explícitamente como invalid/test. |

Esta tabla muestra ya una característica fundamental de APRS:

**tipo de datos y DTI no forman una relación uno a uno.**

## ¿Qué es DTI?

El **Data Type Identifier** es el primer byte del campo Information y determina cómo debe interpretarse el contenido que sigue.

En la práctica, DTI actúa como primer selector del parser.

Por ejemplo:

```text
!5213.45N/02100.12E...
```

comienza con `!`, por lo que el parser espera un formato de posición sin timestamp y sin declaración de capacidad de mensajería APRS.

```text
>QRV 145.500 MHz
```

comienza con `>`, por lo que los datos siguientes se interpretan como status.

```text
;REPEATER*...
```

comienza con `;`, por lo que el resto sigue el formato de objeto APRS.

```text
:SP9XYZ  :Test
```

comienza con `:`, por lo que se interpreta según el formato de mensaje APRS.

DTI no es simplemente el nombre de un tipo de datos. Selecciona una variante concreta de sintaxis y a veces transporta además información adicional.

### El DTI debe ser el primer byte

APRS 1.0.1 original contenía una excepción para DTI `!` relacionada con antiguos digipeaters X1J. Según aquella regla, `!` podía aparecer incluso hasta la posición 40 del campo Information.

APRS 1.1 eliminó posteriormente esta excepción. Actualmente `!`, igual que los demás DTI, debe aparecer al principio del campo Information.

Las nuevas implementaciones no deberían generar la variante histórica X1J.

## Un tipo de datos, varios DTI

El ejemplo más claro es **Position**.

APRS tiene cuatro DTI básicos para informes normales de posición:

| DTI | Timestamp | APRS messaging |
|---|---|---|
| `!` | no | no |
| `=` | no | sí |
| `/` | sí | no |
| `@` | sí | sí |

Los cuatro pertenecen al mismo tipo principal de datos:

```text
Position
```

pero seleccionan variantes diferentes del formato.

Puede representarse como una matriz:

| | Sin timestamp | Con timestamp |
|---|---|---|
| Sin APRS messaging | `!` | `/` |
| Con APRS messaging | `=` | `@` |

En este caso DTI comunica al mismo tiempo:

1. que el paquete es un informe de posición,
2. si el formato incluye timestamp,
3. si la estación declara capacidad de mensajería APRS.

Esto muestra por qué llamar "tipo de trama" a cada DTI individual es una simplificación excesiva.

## Capabilities - dos conceptos diferentes

En APRS hay dos mecanismos relacionados con las capacidades de la estación que se confunden con facilidad.

### Messaging capability en el DTI de posición

Los DTI `=` y `@` significan un informe de posición **with APRS messaging**.

Los DTI `!` y `/` representan las variantes correspondientes **without APRS messaging**.

Esta información forma parte del significado del DTI concreto de posición. No es un bloque de datos separado añadido al informe.

### Station Capabilities y DTI `<`

Existe además un mecanismo independiente: el paquete formal **Station Capabilities**, cuyo DTI es:

```text
<
```

APRS101 describe capabilities como una lista de tokens separados por comas:

```text
TOKEN
```

o:

```text
TOKEN=VALUE
```

Ejemplo clásico:

```text
<IGATE,MSG_CNT=43,LOC_CNT=14
```

En APRS101 este paquete es una respuesta a la consulta `?IGATE?`.

Por tanto, no deben confundirse:

```text
= o @
```

es decir, la información de capacidad de mensajería APRS codificada en una variante de informe de posición,

con:

```text
<
```

es decir, un formato independiente de Station Capabilities.

Mic-E posee además sus propios códigos de tipo e identificación de dispositivo. Es otro mecanismo independiente y no debe mezclarse ni con DTI `<` ni con la messaging capability de los informes de posición.

## DTI no siempre determina toda la semántica

DTI es el punto de entrada principal del parser, pero eso no significa que un solo carácter determine siempre todo de manera inequívoca.

Ejemplos:

- `:` significa formato de mensaje, pero solo los campos siguientes distinguen un mensaje ordinario, bulletin, announcement o directed query,
- `!`, `=`, `/` y `@` pueden transportar una posición normal, datos DF o meteorología asociada a una posición,
- `!` y `$` tienen además antiguos significados sobrecargados relacionados con formatos brutos de estaciones meteorológicas,
- una respuesta a una query no tiene un único DTI común, porque la respuesta puede ser una posición, estado, meteorología, objeto o Station Capabilities,
- Mic-E utiliza simultáneamente DTI, el campo Information y datos codificados en Destination Address.

Por ello, un parser APRS correcto no puede terminar su análisis en un simple `switch(DTI)`. DTI selecciona una familia de reglas, pero un formato concreto puede requerir reconocimiento adicional.

## Tabla completa de DTI de APRS101

La tabla siguiente muestra los significados definidos en APRS Protocol Reference 1.0.1 junto con las notas modernas más importantes.

| DTI | Significado | Observaciones |
|---|---|---|
| `0x1c` | Current Mic-E Data, Rev 0 beta | Formato beta muy antiguo. |
| `0x1d` | Old Mic-E Data, Rev 0 beta | Formato beta muy antiguo. |
| `!` | Position sin timestamp, no APRS messaging | Históricamente también Ultimeter 2000 WX. Se eliminó la antigua excepción X1J que permitía `!` más adelante en el campo. |
| `"` | Unused | No usar como nuevo DTI. |
| `#` | Peet Bros U-II Weather Station | Formato WX bruto e histórico. Las nuevas implementaciones no deberían elegirlo. |
| `$` | Raw GPS data o Ultimeter 2000 | Para GPS, típicamente sentencias NMEA brutas. DTI históricamente sobrecargado por un formato WX. |
| `%` | Agrelo DFJr / MicroFinder | Formato histórico especializado de direction finding. |
| `&` | Reserved - Map Feature | Reservado en APRS101. |
| `'` | Mic-E | APRS101 lo describe como Old Mic-E, pero Kenwood TM-D700 lo usó para current Mic-E. En la práctica debe tratarse según las reglas Mic-E. |
| `(` | Unused | No usar como nuevo DTI. |
| `)` | Item | El formato Item está definido, pero APRS 1.1 no recomienda su uso en RF por problemas de compatibilidad. |
| `*` | Peet Bros U-II Weather Station | Formato WX bruto e histórico. |
| `+` | Reserved - Shelter data with time | Reservado en APRS101. |
| `,` | Invalid data o test data | Paquete marcado deliberadamente como datos no estándar o de prueba. |
| `-` | Unused | No usar como nuevo DTI. |
| `.` | Reserved - Space weather | Reservado en APRS101. |
| `/` | Position con timestamp, no APRS messaging | Uno de los cuatro DTI básicos de posición. |
| `0`-`9` | Do not use | No destinados al uso como DTI. |
| `:` | Message | El mismo DTI se usa para mensajes, bulletins, announcements y directed queries. |
| `;` | Object | Informe de objeto APRS. |
| `<` | Station Capabilities | Formato de tokens de capabilities, por ejemplo `<IGATE,MSG_CNT=43,LOC_CNT=14`. |
| `=` | Position sin timestamp, with APRS messaging | Uno de los cuatro DTI básicos de posición. |
| `>` | Status | Informe de status. |
| `?` | Query | General query. Las directed queries usan el formato de mensaje `:`. |
| `@` | Position con timestamp, with APRS messaging | Uno de los cuatro DTI básicos de posición. |
| `A`-`S` | Do not use | Rango reservado como no válido para nuevos DTI. |
| `T` | Telemetry data | Formato clásico de telemetría `T#...`. |
| `U`-`Z` | Do not use | Rango reservado como no válido para nuevos DTI. |
| `[` | Maidenhead grid locator beacon | Marcado como obsolete en APRS101. |
| `\` | Unused | No usar como nuevo DTI. |
| `]` | Unused | No usar como nuevo DTI. |
| `^` | Unused | No usar como nuevo DTI. |
| `_` | Weather Report sin posición | Informe meteorológico estándar sin posición. |
| `` ` `` | Current Mic-E Data | DTI estándar de Mic-E. APRS101 señala una excepción relacionada con TM-D700. |
| `a`-`z` | Do not use | No destinados al uso como DTI. |
| `{` | User-Defined APRS packet format | Mecanismo reservado para formatos definidos por aplicaciones. |
| `|` | Do not use como DTI | En APRS101 el carácter estaba relacionado con el histórico TNC stream switching. Extensiones posteriores pueden usar `|` dentro de los datos, por ejemplo Base91 Comment Telemetry. |
| `}` | Third-party traffic | Envoltura para un paquete APRS transportado mediante una red intermedia. El paquete interior contiene su propio encabezado y datos APRS. |
| `~` | Do not use como DTI | Históricamente relacionado con TNC stream switching. |

La tabla DTI de APRS101 refleja el estado del protocolo alrededor del año 2000. Addenda posteriores corrigieron algunas recomendaciones y marcaron ciertos mecanismos antiguos como obsoletos o no recomendados.

## Weather muestra por qué la tabla DTI no basta

La meteorología es un ejemplo especialmente bueno de la naturaleza por capas de APRS.

Un informe sin posición puede comenzar con:

```text
_
```

Los datos meteorológicos también pueden estar asociados a un informe de posición que comience con:

```text
!
=
/
@
```

Además, APRS101 definía formatos brutos de determinadas estaciones meteorológicas usando:

```text
!
#
$
*
```

APRS 1.1 recomienda que los nuevos dispositivos no transmitan formatos brutos de estaciones meteorológicas, sino que conviertan los datos a formatos APRS estándar.

Esto significa que el valor DTI por sí solo puede requerir examinar el contenido posterior y conocer variantes históricas.

## La telemetría tampoco siempre tiene DTI `T`

La telemetría APRS clásica tiene la forma:

```text
T#001,123,045,255,010,100,10110011
```

y utiliza DTI:

```text
T
```

Esto no significa que todos los datos telemétricos en APRS deban aparecer en un paquete `T`.

La telemetría histórica Mic-E utilizaba el mecanismo Mic-E y posteriormente se consideró obsoleta tras introducirse Mic-E Type Codes.

La posterior **Base91 Comment Telemetry** permite colocar datos telemétricos directamente en el comentario de un informe de posición, por ejemplo entre `|...|`. En ese caso el DTI de todo el paquete sigue siendo el DTI de posición, como `!`, `=` o Mic-E, mientras que la telemetría es una extensión situada más profundamente en los datos.

Es otro ejemplo de que:

**un tipo de datos APRS no es lo mismo que el DTI del primer byte.**

## User-Defined APRS packet format

El DTI:

```text
{
```

está reservado formalmente para **User-Defined APRS packet format**.

APRS101 define el comienzo de este tipo de paquete como:

```text
{UX...
```

donde:

- `{` es el DTI,
- `U` es un User ID de un byte,
- `X` es un identificador de un byte para un tipo de paquete concreto del autor,
- a continuación aparecen los datos del formato definido por el autor.

Ejemplo de APRS101:

```text
{Q1qwerty
```

significa:

```text
DTI:         {
User ID:     Q
Packet Type: 1
Data:        qwerty
```

Para experimentos se reservó un User ID igual a `{`, por lo que un paquete experimental puede comenzar por:

```text
{{
```

Ejemplo:

```text
{{zasdfg
```

En el registro posterior de formatos experimentales aparecieron entre otros encabezados:

```text
{BT
{BP
{Dx
{KY
{Q1
{Q2
{Sx
```

User-Defined Format no significa que todos los programas APRS deban entender el paquete. APRS101 asume explícitamente que estos formatos son opcionales y que una aplicación que no reconozca un User ID y Packet Type concretos debe ignorarlos de forma segura.

El mecanismo `{` es por tanto una forma controlada de ampliar APRS sin fingir que los datos no estándar pertenecen a uno de los formatos estándar.

## Third-party traffic y DTI `}`

El DTI:

```text
}
```

significa **third-party traffic**.

En este formato el campo Information contiene un paquete APRS original encapsulado junto con su encabezado y datos.

Esquemáticamente:

```text
}SOURCE>DEST,PATH:DTI...
```

El primer DTI del paquete exterior es `}`, pero el paquete interior tiene su propio DTI y debe interpretarse nuevamente según su formato.

Es un caso especial en el que, tras reconocer `}`, el parser vuelve a analizar un paquete APRS completo contenido dentro de los datos.

## Caracteres `|` y `~` - limitación histórica

APRS101 marca `|` y `~` como caracteres que no deben utilizarse como DTI, porque los TNC antiguos los empleaban para cambiar de stream.

En la especificación original esta limitación se trataba también de forma más amplia en relación con los comentarios.

Sin embargo, la especificación posterior Base91 Comment Telemetry utiliza deliberadamente:

```text
|...|
```

como delimitadores de datos telemétricos dentro de un comentario.

Los autores de esa extensión señalan que la restricción procedía del antiguo modo CONVerse de los TNC clásicos, mientras que en la práctica el carácter `|` atraviesa correctamente las redes APRS modernas y APRS-IS.

Por ello, un parser moderno no debería aplicar la antigua regla "el carácter `|` nunca puede aparecer en datos APRS". Sigue sin ser un DTI estándar, pero puede tener un significado definido dentro de un formato o extensión concreta.

## Regla práctica

La forma más segura de interpretar el campo Information es:

1. leer el primer byte como DTI,
2. seleccionar la familia de formato indicada por el DTI,
3. recordar que un tipo principal de datos puede tener varios DTI,
4. tener en cuenta la semántica adicional del DTI, como timestamp y APRS messaging en los informes de posición,
5. si el formato está sobrecargado o es histórico, examinar la sintaxis siguiente,
6. interpretar la clásica Data Extension solo cuando el formato concreto la permita,
7. después de los datos principales, tener en cuenta extensiones e información codificadas en el comentario,
8. para `{`, aplicar las reglas del User ID y Packet Type concretos,
9. para `}`, desempaquetar el paquete interior y comenzar de nuevo la interpretación de su DTI,
10. no asumir que un DTI desconocido puede interpretarse de forma segura como texto.

DTI es por tanto la primera clave para decodificar el campo Information, pero el significado completo de un paquete resulta de combinar:

```text
tipo principal de datos
+
DTI
+
variante de formato
+
capabilities opcionales
+
extensiones
+
comentario
```

## Fuentes

Este artículo se basa en:

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF)
- [APRS Specification Addendum 1.1](https://www.aprs.org/aprs11.html)
- [APRS 1.2 Addendum Proposals](https://www.aprs.org/aprs12.html)
- [APRS User Defined Data Formats List](https://www.aprs.org/aprs11/expfmts.txt)
- [APRS Documentation Project - APRS 1.2 compilation](https://github.com/wb2osz/aprsspec)
- [Base91 Comment Telemetry specification](https://github.com/hessu/aprs-specs/blob/master/aprs-base91-comment-telemetry.txt)
