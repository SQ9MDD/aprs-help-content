---
title: Capas del protocolo APRS
description: Explicación de la relación entre APRS, AX.25, la capa de radio, el módem y APRS-IS.
template: doc
tableOfContents: true
---

APRS no es un único protocolo que abarque todo, desde la señal de radio hasta la aplicación del usuario.

En la práctica, el sistema está formado por varias capas que trabajan juntas.

En la ruta de radio pueden representarse de forma simplificada así:

```text
aplicación
   |
   v
datos APRS
   |
   v
AX.25
   |
   v
módem / modulación
   |
   v
radio
   |
   v
RF
```

Cada una de estas capas es responsable de algo diferente.

APRS define principalmente **el significado de la información**.

AX.25 proporciona la estructura de la trama utilizada para transportar esa información por radio.

El módem convierte los datos digitales en una señal que puede transmitirse a través del sistema de radio.

La radio transporta esa señal por el canal RF.

En el lado de Internet, la misma información APRS puede transportarse de otra forma:

```text
datos APRS
   |
   v
APRS-IS
   |
   v
TCP/IP
   |
   v
aplicaciones
```

Comprender esta división es muy importante porque muchos elementos visibles en un paquete APRS típico pertenecen en realidad a diferentes capas del sistema.

## APRS no es todo a la vez

En el lenguaje cotidiano, la palabra "APRS" se utiliza a menudo para describir todo el sistema:

- radio,
- modulación,
- tramas AX.25,
- posiciones y mensajes,
- digipeaters,
- IGates,
- APRS-IS,
- aplicaciones de Internet.

Es cómodo, pero técnicamente impreciso.

Por ejemplo, la transmisión:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

contiene elementos que pertenecen a diferentes capas.

De forma muy simplificada:

```text
SQ9MDD-7      origen AX.25
APRS          destination AX.25
WIDE1-1       ruta AX.25
!             identificador de tipo de datos APRS
5012.34N...   datos APRS
```

Además, una trama de radio real contiene información que no aparece en esta representación textual.

## Modelo simplificado de capas

Para APRS en un canal de radio puede utilizarse el siguiente modelo:

```text
+-----------------------------+
| aplicación de usuario       |
+-----------------------------+
| datos APRS                  |
+-----------------------------+
| AX.25 UI                    |
+-----------------------------+
| módem / modulación          |
+-----------------------------+
| radio                       |
+-----------------------------+
| canal RF                    |
+-----------------------------+
```

No se trata de un modelo OSI formal del protocolo APRS.

Es una forma práctica de mostrar dónde se encuentran los distintos elementos del sistema y de qué son responsables.

## La capa de radio

En el nivel más bajo se encuentra la transmisión física por radio.

Aquí importan, entre otros, los siguientes factores:

- frecuencia de trabajo,
- ancho de canal,
- potencia del transmisor,
- antena,
- propagación,
- nivel de señal,
- interferencias,
- uso compartido del canal por muchas estaciones.

En las redes APRS VHF europeas se utiliza muy frecuentemente la frecuencia:

```text
144.800 MHz
```

Sin embargo, la frecuencia en sí no forma parte del protocolo APRS.

APRS también puede transportarse en otras bandas y utilizando otros métodos de transmisión.

Por tanto, la frecuencia pertenece a la capa de radio, no a la estructura de datos APRS.

## Módem y modulación

Los datos digitales deben convertirse en una señal que pueda transmitirse por radio.

En APRS clásico sobre VHF se utiliza muy frecuentemente:

```text
1200 baud AFSK
```

con modulación basada en el estándar Bell 202.

Una implementación típica utiliza dos tonos de audio:

```text
1200 Hz
2200 Hz
```

Los datos digitales se convierten en la secuencia correspondiente de señales de audio, que después modulan el transmisor de radio.

En el lado receptor, el proceso funciona en sentido inverso.

```text
RF
 |
 v
radio
 |
 v
audio
 |
 v
módem
 |
 v
datos digitales
```

El módem puede ser:

- un dispositivo de hardware,
- parte de un equipo de radio,
- un TNC,
- software ejecutándose en un ordenador.

Dire Wolf es un ejemplo de módem por software.

Sin embargo, conviene recordar que:

**AFSK a 1200 baudios no es APRS.**

Es solo una de las formas de transportar los datos utilizados por APRS.

## AX.25

Por encima de la capa del módem se encuentra **AX.25**.

AX.25 es un protocolo de capa de enlace de datos utilizado en packet radio.

AX.25 define la estructura de la trama de radio, incluyendo, entre otras cosas:

- dirección destination,
- dirección source,
- direcciones opcionales de digipeaters,
- campo control,
- campo PID,
- campo information,
- comprobación de errores de la trama.

De forma simplificada:

```text
+-------------+
| Destination |
+-------------+
| Source      |
+-------------+
| Digipeaters |
+-------------+
| Control     |
+-------------+
| PID         |
+-------------+
| Information |
+-------------+
| FCS         |
+-------------+
```

APRS no crea desde cero su propia estructura completa de trama de radio.

En su lugar, utiliza AX.25 como mecanismo de transporte de datos.

## AX.25 UI

El tráfico APRS típico utiliza tramas AX.25 de tipo **UI**, donde UI significa:

**Unnumbered Information**

Este acrónimo no debe confundirse con *User Interface*.

Las tramas UI permiten enviar información sin establecer previamente una conexión AX.25 clásica.

Gracias a ello, una estación puede simplemente transmitir información como:

```text
posición
estado
objeto
meteorología
telemetría
```

y todas las estaciones que se encuentren dentro del alcance pueden recibirla.

Esto encaja muy bien con el carácter de difusión de APRS.

## ¿Dónde empieza realmente APRS?

En una trama APRS típica, los datos APRS propiamente dichos se encuentran en el campo **Information** de la trama AX.25.

Puede representarse así:

```text
AX.25
+------------------------------------------+
| direcciones | control | PID | Information |
+------------------------------------------+
                              |
                              v
                         datos APRS
```

El contenido de este campo define, entre otras cosas:

- posición,
- estado,
- mensaje,
- objeto,
- meteorología,
- telemetría,
- consulta,
- otros tipos de información APRS.

El primer carácter del campo de información suele actuar como **Data Type Identifier**, o DTI.

Por ejemplo:

```text
!
=
/
@
:
;
>
?
```

pueden indicar distintos tipos de datos.

El significado detallado de cada DTI se describe en una sección independiente de la documentación.

## Ejemplo de división de un paquete

Consideremos el paquete:

```text
SQ9MDD-7>APRS,WIDE1-1:!5012.34N/01956.78E>
```

Podemos dividirlo lógicamente de la siguiente manera:

```text
SQ9MDD-7
```

es la dirección de origen AX.25.

```text
APRS
```

es la dirección destination AX.25.

```text
WIDE1-1
```

es un elemento de la ruta AX.25.

```text
:
```

separa en la representación textual la cabecera del campo de información.

```text
!
```

es un Data Type Identifier de APRS.

```text
5012.34N/01956.78E>
```

es el contenido APRS propiamente dicho que describe la posición y el símbolo.

Por tanto, se ve claramente que no todos los elementos visibles del paquete pertenecen a la misma capa.

## Destination address

El campo destination forma parte de la cabecera AX.25.

En el uso clásico de AX.25, destination puede indicar la estación de destino.

En APRS, la situación es más compleja.

Muchos paquetes APRS utilizan la dirección destination para identificar el tipo de dispositivo o software.

Ejemplo:

```text
SQ9MDD-7>APRS:...
```

Esto no significa que el paquete esté dirigido a una estación con el indicativo `APRS`.

Del mismo modo, destination puede contener valores pertenecientes al sistema TOCALL.

Por eso, la dirección destination en APRS no debe interpretarse automáticamente como la dirección de un destinatario concreto.

Las reglas detalladas sobre destination address y TOCALL se describen por separado.

## El destinatario de un mensaje APRS

Esta distinción es especialmente importante en los mensajes.

Por ejemplo, un paquete puede tener este aspecto:

```text
SQ9MDD-7>APRS,WIDE1-1::SP9XYZ   :Test
```

El destination AX.25 puede seguir siendo:

```text
APRS
```

mientras que el destinatario real del mensaje APRS:

```text
SP9XYZ
```

se encuentra dentro del campo de información APRS.

Se trata de dos capas diferentes de direccionamiento.

## Ruta de digipeaters

Elementos como:

```text
WIDE1-1
WIDE2-1
```

no forman parte del payload APRS propiamente dicho.

Pertenecen a la lista de direcciones de digipeaters de la trama AX.25.

Esta capa permite a los digipeaters determinar si una trama puede retransmitirse y de qué manera.

En la representación textual lo vemos como:

```text
SOURCE>DEST,PATH:DATA
```

pero lógicamente:

```text
SOURCE
DEST
PATH
```

pertenecen a la cabecera AX.25,

mientras que:

```text
DATA
```

es el campo de información que contiene APRS.

## La representación textual no es la trama de radio sin procesar

Los paquetes APRS se muestran muy a menudo como texto:

```text
SOURCE>DEST,PATH:information
```

Es una representación extremadamente cómoda para usuarios, registros y aplicaciones.

Sin embargo, no es un registro exacto, byte por byte, de lo que se encuentra en el canal de radio.

Una trama AX.25 real contiene elementos que no son visibles directamente en la representación textual, entre ellos:

- direcciones AX.25 codificadas,
- bits de control,
- campo control,
- PID,
- FCS,
- mecanismos relacionados con la transmisión de la trama.

Por tanto:

```text
SQ9MDD-7>APRS,WIDE1-1:...
```

debe considerarse una representación legible del paquete, no el contenido literal de la transmisión de radio.

## APRS-IS

En el lado de Internet, los paquetes APRS no se transmiten como tramas de radio AX.25 sin procesar.

APRS-IS utiliza una representación textual de los paquetes similar a:

```text
SOURCE>DEST,PATH:information
```

Por tanto, un paquete puede ser recibido por un IGate a través de RF:

```text
AX.25
   |
   v
IGate
```

y después enviado a APRS-IS como representación textual:

```text
SOURCE>DEST,PATH:information
```

Al paquete pueden añadirse informaciones características de APRS-IS.

Un ejemplo son los **q-constructs**.

## Los q-constructs no forman parte de la ruta de radio

En APRS-IS puede verse un paquete como:

```text
SQ9MDD-7>APRS,WIDE1-1,qAR,SQ9MDD-4:...
```

El elemento:

```text
qAR
```

no fue transmitido por radio por la estación como otro elemento de la ruta normal de digipeaters.

Es información añadida en el entorno APRS-IS.

Del mismo modo:

```text
SQ9MDD-4
```

en esta parte puede identificar el IGate responsable de enviar el paquete a APRS-IS.

Por tanto, no debe interpretarse toda la ruta visible en APRS-IS como una copia exacta de la ruta presente en RF.

## RF y APRS-IS son entornos de transporte diferentes

La misma información APRS puede transportarse por diferentes caminos.

En RF:

```text
datos APRS
   |
   v
AX.25
   |
   v
módem
   |
   v
radio
```

En Internet:

```text
datos APRS
   |
   v
APRS-IS
   |
   v
TCP/IP
```

En ambos casos, el significado de la información APRS puede seguir siendo el mismo.

Lo que cambia es el método de transporte.

Esta distinción es importante.

**APRS es una capa de información, mientras que RF y APRS-IS son entornos diferentes por los que esa información puede transportarse.**

## El IGate como frontera entre entornos

Un IGate se encuentra entre la red de radio y APRS-IS.

En dirección RF -> Internet:

```text
RF
 |
 v
AX.25
 |
 v
IGate
 |
 v
APRS-IS
```

El IGate recibe la trama de radio, interpreta los elementos necesarios y envía a APRS-IS la representación adecuada del paquete.

Esquemáticamente:

```text
[RF / AX.25]
      |
      v
    IGate
      |
      v
 [APRS-IS]
```

Sin embargo, no se trata simplemente de reenviar bits de una interfaz a otra.

El IGate trabaja en la frontera entre dos entornos de transporte diferentes.

## Dirección APRS-IS -> RF

El paso en la dirección contraria es más complejo.

No se puede simplemente tomar cualquier línea de texto de APRS-IS y transmitirla sin cambios por radio.

APRS dispone de mecanismos que determinan cómo transferir determinada información desde APRS-IS hacia RF.

En particular, puede utilizarse el formato:

**third-party traffic**

Permite conservar información sobre el origen del paquete original.

Las reglas detalladas de este mecanismo se describen en las secciones dedicadas a formatos especiales y al funcionamiento de los IGates.

## Third-party traffic

El mecanismo third-party traffic crea una capa adicional de encapsulación.

De forma muy simplificada:

```text
AX.25
 |
 v
paquete APRS third-party
 |
 v
paquete original
```

Esto significa que un paquete procedente de otro entorno no se copia simplemente como un paquete RF normal.

Se coloca dentro de un formato APRS especial.

Gracias a ello, el receptor puede reconocer que la información fue reenviada por otro elemento de la infraestructura.

## ¿Qué pertenece a cada capa?

La siguiente tabla muestra varios elementos APRS habituales y su lugar dentro del sistema.

| Elemento | Capa |
|---|---|
| `144.800 MHz` | radio / RF |
| `1200 baud` | módem / transmisión |
| AFSK | modulación |
| Bell 202 | modulación |
| AX.25 | capa de enlace de datos |
| Source callsign | AX.25 |
| Destination address | AX.25 |
| `WIDE1-1` | ruta AX.25 |
| `WIDE2-1` | ruta AX.25 |
| UI | tipo de trama AX.25 |
| PID | AX.25 |
| FCS | AX.25 |
| `!` | APRS DTI |
| `=` | APRS DTI |
| `:` | APRS message DTI |
| posición | APRS |
| objeto | APRS |
| mensaje | APRS |
| telemetría | APRS |
| meteorología | APRS |
| TOCALL | uso de destination por APRS |
| APRS-IS | transporte de APRS por Internet |
| `qAR` | APRS-IS |
| TCP/IP | transporte por Internet |
| APRS.fi | aplicación / servicio |

Esta separación ayuda a comprender por qué algunos elementos del paquete aparecen únicamente en RF, otros únicamente en APRS-IS y otros conservan su significado en ambos entornos.

## Los mismos datos, transportes diferentes

Supongamos que la información APRS propiamente dicha describe la posición de una estación.

En RF puede transportarse así:

```text
posición APRS
     |
     v
   AX.25
     |
     v
   AFSK
     |
     v
    RF
```

Después de pasar por un IGate:

```text
posición APRS
     |
     v
  APRS-IS
     |
     v
   TCP/IP
```

El significado de la posición no tiene por qué cambiar.

Solo cambia el mecanismo utilizado para transportar la información.

## La aplicación está todavía más arriba

Al final se encuentra la aplicación del usuario.

Puede ser:

- un equipo de radio con decodificador APRS,
- un terminal APRS,
- una aplicación de ordenador,
- un mapa,
- un servicio de Internet,
- un sistema de monitorización,
- una base de datos.

La aplicación interpreta la información APRS y la presenta al usuario.

Por ejemplo:

```text
RF
 |
 v
AX.25
 |
 v
APRS
 |
 v
aplicación
 |
 v
mapa
```

o:

```text
APRS-IS
   |
   v
aplicación
   |
   v
mapa
```

Por tanto, el mapa es la capa final de presentación de la información, no el protocolo APRS en sí.

## Errores frecuentes

### APRS y AX.25 son lo mismo

No.

AX.25 proporciona, entre otras cosas, la estructura de la trama y el direccionamiento de la capa de enlace.

APRS utiliza AX.25 para transportar sus propios datos.

### AFSK a 1200 baudios es APRS

No.

AFSK a 1200 baudios es uno de los métodos de transmisión de datos.

APRS se encuentra por encima.

### WIDE1-1 forma parte de los datos APRS

No.

`WIDE1-1` se encuentra en la ruta de direcciones AX.25.

### Destination address siempre indica el destinatario APRS

No.

En APRS, destination también puede utilizarse para identificar un dispositivo o software.

### qAR forma parte de la ruta de radio

No.

`qAR` es un elemento relacionado con APRS-IS.

### Una línea de texto de un paquete es la trama de radio exacta

No.

```text
SOURCE>DEST,PATH:DATA
```

es una representación legible de la información contenida en la trama, pero no incluye todos los elementos de la transmisión AX.25 real.

### APRS-IS es una versión de AX.25 para Internet

No directamente.

APRS-IS transporta información APRS en un entorno de Internet utilizando su propio método de representación y distribución de paquetes.

## La imagen completa

Las capas de la transmisión por radio pueden resumirse así:

```text
+-----------------------------+
| Aplicación                  |
+-----------------------------+
| APRS                        |
| posiciones, mensajes,       |
| objetos, meteorología,      |
| telemetría                  |
+-----------------------------+
| AX.25 UI                    |
| direcciones, path, control, |
| PID                         |
+-----------------------------+
| módem / modulación          |
| p. ej. AFSK 1200            |
+-----------------------------+
| radio                       |
+-----------------------------+
| RF                          |
+-----------------------------+
```

En el lado de APRS-IS:

```text
+-----------------------------+
| Aplicación                  |
+-----------------------------+
| APRS                        |
+-----------------------------+
| APRS-IS                     |
+-----------------------------+
| TCP/IP                      |
+-----------------------------+
| red de Internet             |
+-----------------------------+
```

Un IGate puede funcionar entre estos entornos:

```text
             RF                         Internet

+--------------------------+       +----------------------+
| APRS                     |       | APRS                 |
+--------------------------+       +----------------------+
| AX.25                    |       | APRS-IS              |
+--------------------------+       +----------------------+
| módem                    |       | TCP/IP               |
+--------------------------+       +----------------------+
| radio                    |       | Internet             |
+--------------------------+       +----------------------+
             \                         /
              \                       /
               +-------- IGate ------+
```

## Puntos clave para recordar

**APRS no define toda la transmisión de radio.**

Utiliza otras capas, especialmente AX.25.

**AX.25 y APRS no son lo mismo.**

AX.25 transporta datos, mientras que APRS define el significado de la información contenida en el campo de datos.

**AFSK a 1200 baudios no es APRS.**

Es una forma de transportar tramas AX.25 por radio.

**WIDE1-1 y elementos similares pertenecen a la ruta AX.25.**

No forman parte del payload APRS propiamente dicho.

**Destination address es un elemento AX.25.**

APRS puede utilizarlo adicionalmente para identificar un dispositivo o una implementación de software.

**APRS-IS es un entorno de transporte diferente para la información APRS.**

No transmite tramas de radio sin procesar 1:1.

**Los q-constructs pertenecen a APRS-IS.**

No deben interpretarse como elementos normales de la ruta de radio.

**La representación textual de un paquete combina información de varias capas en una sola línea.**

Por tanto:

```text
SOURCE>DEST,PATH:DATA
```

es una representación muy cómoda para personas y aplicaciones, pero no es una imagen literal de toda la transmisión de radio.

## A continuación

Después de comprender las capas del sistema, se puede pasar a un análisis más detallado de un paquete individual.

Los siguientes temas deberían incluir:

- estructura de una trama AX.25,
- anatomía de la representación textual de un paquete APRS,
- source callsign y SSID,
- destination address,
- TOCALL,
- rutas de digipeaters,
- campo de información APRS,
- Data Type Identifier,
- diferencias entre un paquete RF y su representación en APRS-IS,
- q-constructs,
- third-party traffic.

Con esta base, los siguientes elementos del protocolo pueden analizarse sabiendo claramente **a qué capa del sistema pertenecen realmente**.
