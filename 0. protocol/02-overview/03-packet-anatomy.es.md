---
title: Anatomía de un paquete APRS
description: Explicación detallada de la representación textual de un paquete APRS, los campos AX.25, la ruta y los datos APRS.
template: doc
tableOfContents: true
---

Un paquete APRS se presenta muy a menudo en una forma textual legible:

```text
SOURCE>DEST,PATH:DATA
```

Ejemplo:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

A primera vista parece un único formato.

En realidad, esta notación combina en una sola línea información procedente de varios elementos de la trama AX.25 y los datos APRS propiamente dichos.

De forma simplificada:

```text
SQ9MDD-7 > APRS , WIDE1-1 : !5012.34N/01956.78E>
^^^^^^^^   ^^^^   ^^^^^^^   ^^^^^^^^^^^^^^^^^^^^^
 SOURCE    DEST     PATH           DATA
  AX.25    AX.25    AX.25          APRS
```

Comprender esta división es la base para seguir analizando el protocolo.

## Forma general del paquete

La representación textual más habitual de un paquete APRS tiene la forma:

```text
SOURCE>DEST,PATH:INFORMATION
```

No todos los elementos tienen que estar siempre presentes.

Un paquete sin ruta puede tener este aspecto:

```text
SOURCE>DEST:INFORMATION
```

Ejemplo:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

Un paquete con ruta puede tener este aspecto:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:INFORMATION
```

Ejemplo:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:>QRV 145.550 MHz
```

Lógicamente, puede dividirse en:

```text
SOURCE
DEST
PATH
INFORMATION
```

Los tres primeros elementos están relacionados con el direccionamiento AX.25.

El campo `INFORMATION` contiene la información APRS propiamente dicha.

## Ejemplo paso a paso

Consideremos el paquete:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Podemos descomponerlo en las siguientes partes:

```text
SQ9MDD-7
```

origen del paquete,

```text
>
```

separador utilizado en la representación textual,

```text
APRS
```

dirección destination,

```text
,
```

separador antes de la ruta,

```text
WIDE1-1
```

ruta de digipeater,

```text
:
```

separador entre la cabecera y el campo de información,

```text
!
```

Data Type Identifier,

```text
5012.34N/01956.78E>
```

datos APRS propiamente dichos.

Por tanto, en una sola línea vemos al mismo tiempo información de la capa AX.25 y datos del protocolo APRS.

## SOURCE

El primer elemento es la dirección de origen:

```text
SOURCE
```

Ejemplo:

```text
SQ9MDD-7
```

En un uso APRS típico, se trata del indicativo de la estación junto con un SSID opcional.

Puede aparecer, por ejemplo, como:

```text
SQ9MDD
SQ9MDD-4
SQ9MDD-7
SQ9MDD-9
```

SOURCE identifica la estación que creó la trama AX.25.

Es un elemento del direccionamiento AX.25, no una parte del campo de información APRS.

## Indicativo y SSID

Una dirección AX.25 puede contener un indicativo y un SSID.

Ejemplo:

```text
SQ9MDD-7
```

puede leerse lógicamente como:

```text
callsign: SQ9MDD
SSID:     7
```

El SSID permite utilizar el mismo indicativo para varias estaciones lógicas.

Por ejemplo, un operador puede utilizar SSID separados para:

- una estación base,
- una estación móvil,
- un equipo portátil,
- un digipeater,
- un IGate,
- un tracker.

Existen convenciones históricas y prácticas sobre el significado de determinados SSID, pero no deben considerarse una regla absoluta del protocolo que defina la función del dispositivo.

Los detalles sobre indicativos y SSID se describen por separado.

## El separador `>`

Después de la dirección de origen aparece en la representación textual el carácter:

```text
>
```

Por ejemplo:

```text
SQ9MDD-7>APRS
```

Separa SOURCE de DEST.

Es un elemento de la representación textual del paquete.

El carácter `>` no debe interpretarse como un byte independiente situado en ese lugar de la trama AX.25 sin procesar transmitida por radio.

## DEST

Después del carácter `>` se encuentra la dirección destination.

Ejemplo:

```text
APRS
```

En conjunto:

```text
SQ9MDD-7>APRS
```

En AX.25 es la dirección destination.

En packet radio clásico puede indicar la estación de destino.

En APRS su significado suele ser diferente.

## Destination en APRS

En muchos paquetes APRS, el campo destination no identifica al destinatario real de la información.

Puede utilizarse, entre otras cosas, para identificar:

- el tipo de dispositivo,
- el fabricante,
- el software,
- la familia de aplicaciones,
- la forma en que se generó el paquete.

Para ello, APRS utiliza, entre otros mecanismos, el sistema **TOCALL**.

Algunos valores pueden tener este aspecto:

```text
APRS
APDWxx
APRSxx
```

Su significado exacto depende de la asignación TOCALL.

Por tanto, el paquete:

```text
SQ9MDD-7>APRS:...
```

no significa:

```text
SQ9MDD-7 envía datos a la estación APRS
```

Aquí, el campo destination es ante todo parte de la cabecera AX.25 y APRS también lo utiliza para identificación adicional.

## Destination no es el destinatario del mensaje APRS

Esta es una de las cosas más importantes que hay que recordar.

Consideremos:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Hola
```

El destination AX.25 es:

```text
APRS
```

pero el destinatario del mensaje es:

```text
SP9XYZ
```

El destinatario se encuentra dentro de los datos APRS.

Por tanto, tenemos dos conceptos diferentes:

```text
AX.25 destination
```

y:

```text
APRS message addressee
```

No deben confundirse.

## PATH

Después de destination puede aparecer una ruta:

```text
SOURCE>DEST,PATH:DATA
```

Ejemplo:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

La ruta puede contener uno o más elementos:

```text
WIDE1-1
WIDE2-1
```

En la representación textual se separan mediante comas.

## PATH es un elemento AX.25

La ruta no se encuentra dentro del payload APRS.

Representa la lista de direcciones de digipeaters AX.25.

Lógicamente:

```text
SOURCE>DEST,WIDE1-1,WIDE2-1:DATA
```

puede entenderse como:

```text
AX.25:
    SOURCE
    DEST
    DIGI 1
    DIGI 2

APRS:
    DATA
```

Esto es importante al analizar paquetes.

`WIDE1-1` no es una orden almacenada dentro de los datos APRS.

Forma parte del direccionamiento de la trama AX.25.

## Varios elementos de ruta

La ruta puede contener varias direcciones.

Ejemplo:

```text
SQ9MDD-7>APRS,WIDE1-1,WIDE2-1:...
```

o después de pasar por la infraestructura:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:...
```

El carácter:

```text
*
```

visible en la representación de monitor puede indicar que ese elemento de la ruta ya ha sido utilizado.

Los detalles del mecanismo H-bit, los alias `WIDE`, el New-N Paradigm y el procesamiento de rutas se describen en un artículo independiente.

## El separador `:`

El límite más importante en la representación textual de un paquete es:

```text
:
```

Ejemplo:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Todo lo que aparece antes del primer separador `:` forma la representación textual de la información relacionada con la cabecera.

Lo que aparece después es el campo de información.

Podemos escribir:

```text
SOURCE>DEST,PATH : INFORMATION
^^^^^^^^^^^^^^^^   ^^^^^^^^^^^
     header           data
```

Para el análisis APRS, el contenido APRS propiamente dicho comienza a la derecha de este separador.

## INFORMATION

El campo situado después del separador `:` contiene los datos transportados en el campo Information de AX.25.

Ejemplo:

```text
!5012.34N/01956.78E>
```

Aquí se encuentra el protocolo de datos APRS propiamente dicho.

El contenido puede representar, entre otras cosas:

- posición,
- mensaje,
- estado,
- objeto,
- item,
- meteorología,
- telemetría,
- consulta,
- capacidades de la estación,
- datos especiales.

La interpretación del campo depende principalmente de cómo comienza.

## Data Type Identifier

El primer carácter del campo APRS Information es muy a menudo el **Data Type Identifier**, o DTI.

Ejemplo:

```text
!5012.34N/01956.78E>
^
|
DTI
```

El DTI determina cómo debe interpretarse el resto de los datos.

Algunos DTI son:

| DTI | Significado general |
|---|---|
| `!` | posición sin marca de tiempo |
| `=` | posición sin marca de tiempo, con soporte de mensajería |
| `/` | posición con marca de tiempo |
| `@` | posición con marca de tiempo, con soporte de mensajería |
| `:` | mensaje |
| `;` | objeto |
| `)` | item |
| `>` | estado |
| `?` | consulta |
| `_` | datos meteorológicos sin posición |
| `T` | telemetría |
| `}` | third-party traffic |

No es una tabla completa de todos los valores posibles.

El artículo dedicado a los tipos de tramas APRS contiene una descripción completa de los DTI.

## El DTI no siempre basta para una interpretación completa

Reconocer el primer carácter es solo el comienzo del análisis del paquete.

Ejemplo:

```text
!5012.34N/01956.78E>
```

DTI:

```text
!
```

indica que se trata de una posición sin marca de tiempo.

Solo los caracteres siguientes determinan:

- latitud,
- tabla de símbolos,
- longitud,
- símbolo,
- posibles extensiones,
- comentario.

Por tanto, un parser APRS debe reconocer primero el tipo de datos y después aplicar las reglas correspondientes a ese tipo.

## Ejemplo de posición

El paquete:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

puede descomponerse de la siguiente forma:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

```text
!
```

DTI de posición sin marca de tiempo,

```text
5012.34N
```

latitud,

```text
/
```

identificador de la tabla de símbolos,

```text
01956.78E
```

longitud,

```text
>
```

código de símbolo.

En este ejemplo, el paquete completo puede verse así:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E >
SOURCE      DEST    PATH      DTI LATITUDE   LONGITUDE  SYMBOL
```

## El símbolo forma parte del formato de posición

En un paquete de posición APRS, el símbolo no se transmite como un nombre:

```text
car
house
repeater
```

En su lugar, lo determinan caracteres situados en posiciones concretas del formato de posición.

En una posición clásica sin comprimir, son importantes:

- el carácter de la tabla de símbolos,
- el código del símbolo.

Por tanto:

```text
/
```

dentro de un paquete de posición no es un separador textual como `>` o `:`.

Forma parte de los datos APRS y es importante para interpretar el símbolo.

## Ejemplo de mensaje

Consideremos:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

La división es la siguiente:

```text
SQ9MDD-7
```

SOURCE,

```text
APRS
```

DEST,

```text
WIDE1-1
```

PATH,

y el campo de información:

```text
:SP9XYZ   :Test
```

comienza con:

```text
:
```

que es el DTI de mensaje.

A continuación aparece el campo del destinatario:

```text
SP9XYZ
```

y el contenido:

```text
Test
```

Conviene observar los dos puntos diferentes:

```text
...WIDE1-1::SP9XYZ   :Test
           ^^
```

El primero:

```text
:
```

separa la cabecera de Information.

El segundo:

```text
:
```

es el DTI de mensaje APRS.

Esta repetición no es accidental.

## Ejemplo de estado

El paquete:

```text
SQ9MDD-7>APRS:>QRV 145.550 MHz
```

contiene:

```text
>
```

como primer carácter del campo de información.

Aquí `>` es el DTI de estado.

Esto muestra por qué el mismo carácter puede tener significados distintos según el lugar en el que aparece.

En la cabecera:

```text
SOURCE>DEST
```

`>` es un separador de la representación textual.

En Information:

```text
>QRV 145.550 MHz
```

`>` es un DTI APRS.

El parser debe tener siempre en cuenta el contexto.

## Ejemplo de objeto

Un paquete de objeto comienza en Information con:

```text
;
```

Esquemáticamente:

```text
SOURCE>DEST,PATH:;OBJECTNAM*...
                 ^
                 |
                DTI
```

Después del DTI aparecen campos específicos del objeto, entre ellos su nombre, estado, tiempo y posición.

La sintaxis detallada de los objetos se describe en una sección independiente de la documentación.

## Comentario

Muchos formatos APRS permiten incluir un comentario adicional después de los datos básicos.

Por ejemplo, un paquete de posición puede contener:

```text
!5012.34N/01956.78E>QRV 145.550 MHz
```

La primera parte define la posición y el símbolo.

La parte siguiente:

```text
QRV 145.550 MHz
```

es el comentario.

El comentario también puede contener determinadas extensiones de datos APRS.

Por ello, no todo lo que aparece después de la posición debe tratarse automáticamente como texto libre.

## Extensiones de datos

Después de la posición APRS básica puede aparecer información adicional.

Según el formato y el contexto, puede describir, entre otras cosas:

- rumbo y velocidad,
- altitud,
- PHG,
- alcance,
- frecuencia,
- tone,
- offset,
- DAO,
- otras extensiones.

Por tanto, un ejemplo puede ser mucho más elaborado que una posición simple:

```text
SOURCE>DEST,PATH:POSITION + EXTENSION + COMMENT
```

Las reglas exactas dependen del tipo de datos concreto y se describen en las secciones correspondientes de la documentación.

## La longitud del campo Information importa

APRS funciona en un entorno de ancho de banda limitado, especialmente en el canal VHF clásico de 1200 baudios.

Cada carácter adicional aumenta la duración de la transmisión.

Por tanto, un paquete bien diseñado debe transmitir la información necesaria sin ampliar innecesariamente el contenido.

Esto se aplica especialmente a:

- comentarios,
- beacons de posición,
- objetos,
- estados,
- mensajes.

Un paquete largo ocupa el canal durante más tiempo que uno corto.

## El texto mostrado en el monitor es formato TNC2

La forma:

```text
SOURCE>DEST,PATH:INFORMATION
```

se conoce habitualmente como formato de monitor TNC2 o representación TNC2.

Lo utilizan, entre otros:

- terminales,
- software APRS,
- registros,
- servidores APRS-IS,
- herramientas de diagnóstico.

Permite presentar una trama AX.25 de una forma fácil de leer por una persona.

Esto no significa que exactamente esa cadena de caracteres se haya transmitido por radio.

## ¿Qué contiene realmente una trama AX.25?

En RF, las direcciones no se transmiten como texto literal:

```text
SQ9MDD-7>APRS,WIDE1-1
```

Una trama AX.25 tiene su propia codificación binaria de direcciones y campos de control.

Incluye, entre otras cosas:

```text
Destination
Source
Digipeater addresses
Control
PID
Information
FCS
```

En APRS típico:

```text
Control = UI
PID     = no layer 3
```

El campo Information contiene los datos APRS.

La representación TNC2 oculta muchos detalles de la capa AX.25 para facilitar el análisis del paquete.

## FCS no es visible en TNC2

Una trama de radio real incluye un mecanismo de comprobación de errores, FCS.

Sin embargo, no aparece en una línea típica:

```text
SOURCE>DEST,PATH:DATA
```

El receptor, TNC o módem comprueba antes la validez de la trama.

Si la trama es aceptada y entregada a la aplicación, la representación de monitor normalmente no contiene FCS.

Es otro ejemplo de la diferencia entre la trama AX.25 real y su representación textual.

## Paquete recibido directamente

Ejemplo de un paquete visto directamente en RF:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Podemos interpretarlo como:

```text
SOURCE: SQ9MDD-7
DEST:   APRS
PATH:   WIDE1-1
DATA:   !5012.34N/01956.78E>
```

Es uno de los casos de análisis más sencillos.

## Paquete después de pasar por un digipeater

Después de la retransmisión, la representación puede contener información sobre el elemento de ruta utilizado.

Por ejemplo:

```text
SQ9MDD-7>APRS,SP9ABC-2*,WIDE2-1:!5012.34N/01956.78E>
```

El monitor puede mostrar entonces por dónde se repitió el paquete.

Esto no significa que los datos APRS:

```text
!5012.34N/01956.78E>
```

hayan cambiado.

Lo que cambió fue la información relacionada con el transporte AX.25.

## Paquete en APRS-IS

Después de ser reenviado por un IGate, el paquete puede tener este aspecto:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

Aquí vemos los elementos:

```text
qAR
SQ9MDD-4
```

que corresponden a APRS-IS.

Por tanto, toda la cadena entre destination y `:` no debe analizarse como si fuera únicamente una ruta AX.25 de radio.

En APRS-IS, la representación del paquete también puede contener información añadida por la infraestructura de Internet.

## La frontera entre RF y APRS-IS

Comparemos:

### Paquete RF

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

### Paquete visible en APRS-IS

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:!5012.34N/01956.78E>
```

La información APRS propiamente dicha:

```text
!5012.34N/01956.78E>
```

permanece igual.

Lo que cambia es la información que describe cómo llegó el paquete a ese entorno.

## Third-party packet

APRS también dispone de un formato que permite colocar un paquete completo dentro de otro paquete.

DTI:

```text
}
```

significa third-party traffic.

Esquemáticamente:

```text
SOURCE>DEST,PATH:}ORIGINAL>DEST,PATH:DATA
                 ^
                 |
          third-party DTI
```

Después del carácter `}` se encuentra la representación del paquete original.

Por tanto, tenemos una situación en la que dentro del campo Information de una trama se encuentra otra representación de paquete.

El parser debe reconocer este caso y procesar correctamente la información anidada.

## Un paquete puede contener información anidada

Third-party traffic muestra una característica importante de APRS.

No siempre se puede analizar un paquete con la simple suposición:

```text
encuentra el primer carácter
lee el resto como datos
```

Algunos tipos de datos crean una estructura interna adicional.

Por tanto, un parser correcto debe reconocer el formato derivado del DTI y solo entonces interpretar el contenido restante.

## Importancia de la posición del carácter

Al analizar APRS, un carácter importa no solo por su valor, sino también por el lugar en el que aparece.

Por ejemplo, el carácter:

```text
:
```

puede ser:

- un separador entre la cabecera e Information en la representación TNC2,
- un DTI de mensaje,
- un separador dentro del formato de mensaje.

El carácter:

```text
>
```

puede ser:

- un separador entre SOURCE y DEST,
- un DTI de estado,
- un código de símbolo en los datos de posición.

Por tanto, APRS no debe analizarse buscando caracteres individuales sin tener en cuenta la estructura.

## Orden de análisis

Un parser práctico puede funcionar lógicamente en el siguiente orden.

Primero hay que separar:

```text
HEADER:INFORMATION
```

Después, reconocer en la cabecera:

```text
SOURCE
DEST
PATH
```

Y después, en el campo Information:

```text
DTI
```

y seleccionar el parser de datos adecuado en función del DTI.

Esquema:

```text
paquete
  |
  +-> header
  |     |
  |     +-> source
  |     +-> destination
  |     +-> path
  |
  +-> information
        |
        +-> DTI
              |
              +-> parser de posición
              +-> parser de mensajes
              +-> parser de objetos
              +-> parser meteorológico
              +-> ...
```

Es un enfoque mucho más seguro que intentar interpretar todo el paquete como una única cadena de estructura fija.

## No todos los paquetes de posición tienen el mismo aspecto

Ejemplo:

```text
!5012.34N/01956.78E>
```

es una posición clásica sin comprimir.

APRS también admite otras formas de codificar la posición, entre ellas:

- posición con marca de tiempo,
- posición comprimida,
- Mic-E,
- extensiones de precisión.

Por tanto, un parser no debe suponer que una posición siempre contiene coordenadas legibles con la forma:

```text
DDMM.mmN
DDDMM.mmE
```

Los formatos de posición detallados se describen en una sección independiente.

## No todos los campos Information comienzan con un DTI simple

La mayoría de los formatos habituales pueden reconocerse fácilmente por el primer carácter.

Sin embargo, existen formatos históricos, formatos especiales y construcciones que requieren un análisis adicional.

Por tanto, una implementación completa de APRS debe tener en cuenta no solo los más comunes:

```text
!
=
:
;
>
```

sino todo el conjunto de formatos definidos por la especificación y las extensiones posteriores.

## ¿Qué ve el usuario y qué ve el módem?

El usuario puede ver:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

La aplicación APRS ve campos lógicos:

```text
source
destination
path
information
```

El parser APRS ve dentro de Information:

```text
DTI
payload
```

La capa AX.25 trabaja con los campos reales de la trama.

El módem trabaja con el flujo de bits y la señal.

La radio transporta finalmente la señal RF.

Por tanto, cada capa observa el mismo paquete desde una perspectiva distinta.

## Errores de interpretación frecuentes

### Todo lo que aparece antes de `:` es APRS

No.

Una gran parte de esa información describe la cabecera y la ruta AX.25.

### `WIDE1-1` forma parte del payload APRS

No.

Es un elemento de la ruta AX.25.

### Destination es el destinatario del mensaje

No siempre.

El destinatario del mensaje APRS se encuentra dentro de Information.

### Todos los caracteres `:` tienen el mismo significado

No.

Su significado depende del lugar en el que aparece.

### La forma textual del paquete es el contenido exacto de RF

No.

Es una representación de monitor de la trama AX.25 real.

### Todo lo que aparece entre destination y `:` procede de RF

No siempre.

En APRS-IS pueden aparecer elementos añadidos por la infraestructura, por ejemplo q-constructs.

### Todas las posiciones tienen la forma DDMM.mmN/DDDMM.mmE

No.

APRS dispone de varios formatos de posición.

## ¿Cómo leer un paquete APRS?

Para el paquete:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>QRV 145.550
```

conviene leerlo por etapas.

### 1. ¿Quién transmitió?

```text
SQ9MDD-7
```

### 2. ¿Cuál es el destination?

```text
APRS
```

### 3. ¿Cuál es la ruta?

```text
WIDE1-1
```

### 4. ¿Dónde comienza Information?

Después de:

```text
:
```

### 5. ¿Cuál es el DTI?

```text
!
```

es decir, una posición sin marca de tiempo.

### 6. ¿Cuáles son los datos propiamente dichos?

```text
5012.34N/01956.78E>
```

posición y símbolo.

### 7. ¿Hay información adicional?

```text
QRV 145.550
```

comentario.

Este enfoque permite analizar paso a paso incluso paquetes mucho más complejos.

## El paquete completo como capas

El mismo ejemplo puede representarse así:

```text
SQ9MDD-7 > APRS , WIDE1-1 : ! 5012.34N / 01956.78E > QRV 145.550
|          |      |          | |          |           | |
|          |      |          | |          |           | +-- comentario
|          |      |          | |          |           +---- símbolo
|          |      |          | |          +---------------- longitud
|          |      |          | +--------------------------- latitud
|          |      |          +----------------------------- DTI
|          |      +---------------------------------------- path
|          +----------------------------------------------- destination
+---------------------------------------------------------- source
```

A nivel de capas:

```text
AX.25
+---------------------------------------------------+
| SOURCE | DEST | PATH | INFORMATION               |
+---------------------------------------------------+
                       |
                       v
APRS
+---------------------------------------------------+
| DTI | datos del tipo de paquete | extensiones / comentario |
+---------------------------------------------------+
```

Este es el modelo más importante que conviene recordar.

## Puntos clave para recordar

**Un paquete APRS textual combina información AX.25 y APRS.**

La forma:

```text
SOURCE>DEST,PATH:DATA
```

no es un único formato plano.

**SOURCE, DEST y PATH pertenecen a la capa de direccionamiento AX.25.**

No son el payload APRS propiamente dicho.

**El campo Information comienza después del separador `:` en la representación TNC2.**

Aquí se encuentran los datos APRS.

**El primer carácter de Information suele ser el DTI.**

Determina cómo debe interpretarse el resto del paquete.

**Destination no tiene por qué ser el destinatario real.**

En APRS puede identificar un dispositivo o software.

**El destinatario del mensaje APRS se encuentra en Information.**

Es distinto del destination AX.25.

**La ruta de digipeaters no forma parte de los datos APRS.**

Pertenece a AX.25.

**La representación TNC2 no es la trama de radio sin procesar.**

Oculta algunos campos y detalles binarios de AX.25.

**Un paquete visible en APRS-IS puede contener información que no estaba presente en RF.**

Los q-constructs son un ejemplo.

**El significado de un carácter depende de su posición en la estructura.**

Por tanto, el paquete debe analizarse por capas y según el formato derivado del DTI.

## A continuación

Después de comprender la anatomía de un paquete individual, pueden estudiarse con más detalle sus distintos elementos.

Los siguientes temas incluyen:

- source callsign,
- SSID,
- destination address,
- TOCALL,
- rutas AX.25,
- mecanismo WIDE,
- Data Type Identifier,
- campo Information,
- timestamp,
- símbolos,
- extensiones de datos,
- formatos de posición,
- Mic-E,
- mensajes,
- objetos,
- telemetría,
- meteorología,
- third-party traffic,
- q-constructs.

A partir de este punto se pueden analizar los distintos tipos de APRS sin mezclar información perteneciente a AX.25, APRS y APRS-IS.
