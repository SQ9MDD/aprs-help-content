---
title: ¿Qué es APRS?
description: Introducción a APRS como sistema de radio para intercambiar información breve y actual.
template: doc
tableOfContents: true
---

**APRS, Automatic Packet Reporting System**, es un sistema digital de radioaficionados para el intercambio de información en tiempo real. Fue diseñado para que los usuarios que se encuentran en una zona puedan intercambiar automáticamente información breve y actual que resulte útil para las comunicaciones y las actividades sobre el terreno.

APRS se asocia a menudo principalmente con la representación de la posición de las estaciones en un mapa. Sin embargo, esta es solo una de sus funciones.

La posición de una estación, un vehículo o un objeto es uno de los tipos de información que APRS puede transportar. El sistema también permite transmitir, entre otras cosas, estados, mensajes, información sobre la frecuencia de trabajo, objetos, datos meteorológicos, telemetría, boletines, consultas y avisos.

Por lo tanto, la característica más importante de APRS no es el propio reporte de posición, sino **poner automáticamente información actual a disposición de otros usuarios de la red**.

> APRS debe entenderse ante todo como un sistema de radio para intercambiar información breve y actual que puede ayudar a establecer una comunicación, mejorar la conciencia de la situación o facilitar una acción.

## Mucho más que posición

Uno de los malentendidos más comunes sobre APRS es considerarlo un sistema de seguimiento GPS.

Un tracker que transmite su posición es un dispositivo APRS típico, pero utiliza solo una parte de las posibilidades del protocolo.

En la misma red pueden aparecer, por ejemplo:

- posiciones de estaciones fijas y móviles,
- información sobre la frecuencia que se está monitorizando,
- estado del operador,
- mensajes de texto breves,
- mensajes de grupo y boletines,
- repetidores y otros objetos situados sobre el terreno,
- puntos de encuentro, eventos o actividades de campo,
- datos de telemetría,
- datos de estaciones meteorológicas,
- avisos meteorológicos y otra información relacionada con zonas geográficas.

Por tanto, una estación móvil puede informar a otras estaciones no solo de **dónde se encuentra**, sino también de qué frecuencia está monitorizando el operador o qué repetidor está utilizando en ese momento.

En la práctica, esta información puede ser mucho más útil que un simple punto en el mapa.

## Información sobre lo que está ocurriendo ahora

APRS fue diseñado pensando en información que tiene importancia **ahora**.

A diferencia de los sistemas clásicos de packet radio orientados a establecer una conexión entre dos estaciones y transferir una mayor cantidad de datos, el tráfico APRS típico se basa en transmisiones breves de tipo difusión.

Una estación transmite información que puede ser recibida al mismo tiempo por todas las estaciones que se encuentran dentro de su alcance de radio.

Gracias a ello, APRS es muy adecuado para crear una conciencia situacional local.

Un usuario puede saber, por ejemplo:

- quién se encuentra cerca,
- dónde están otras estaciones,
- qué estaciones son móviles,
- en qué frecuencias están trabajando los operadores,
- qué repetidores hay en las proximidades,
- si hay un evento o una actividad de radioaficionados en la zona,
- qué condiciones meteorológicas están reportando las estaciones locales,
- si se ha emitido un aviso,
- qué objetos han publicado otros usuarios de la red.

Esto no significa, por supuesto, que todos los receptores APRS tengan que mostrar todos estos tipos de datos. El alcance de la información depende de las capacidades del dispositivo o del software utilizado.

## APRS es un sistema de radio

APRS puede funcionar completamente sin acceso a Internet.

El intercambio de información más sencillo puede tener este aspecto:

```text
Estación A
   |
   | RF
   v
Estación B
```

La estación B recibe directamente la transmisión de la estación A y puede utilizar la información contenida en ella.

Si la cobertura directa no es suficiente, la red puede utilizar **digipeaters** que retransmiten determinados paquetes:

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

Por tanto, el acceso a Internet no es necesario para que APRS funcione.

Esta es una característica importante del sistema. La información puede intercambiarse localmente por radio incluso cuando ninguna de las estaciones tiene acceso a infraestructura de Internet.

## APRS-IS amplía la red de radio

El APRS moderno suele estar conectado a la red basada en Internet **APRS-IS**.

Para ello se utilizan estaciones denominadas **IGate**, es decir, Internet Gateway.

Una ruta típica de un paquete puede tener este aspecto:

```text
estación
  |
  | RF
  v
digipeater
  |
  | RF
  v
IGate
  |
  | Internet
  v
APRS-IS
  |
  v
aplicaciones y servicios APRS
```

Un IGate puede reenviar a APRS-IS la información recibida por radio. De esta forma, los datos pueden estar disponibles para aplicaciones que funcionan fuera de la cobertura de radio local.

Así es como los paquetes recibidos por la infraestructura local pueden aparecer posteriormente en servicios APRS de Internet.

Sin embargo, conviene mantener los conceptos en el orden correcto:

**APRS-IS forma parte de la infraestructura APRS, pero no es APRS en sí mismo.**

El intercambio básico de información puede seguir realizándose directamente por radio.

## APRS no es APRS.fi

Los servicios de Internet como APRS.fi son una forma muy útil de presentar datos de la red APRS, pero no son la propia red.

El mapa visible en un servicio de este tipo es únicamente una visualización de una parte de la información que ha sido reenviada a APRS-IS.

Un paquete puede cumplir perfectamente su función en la red de radio y no llegar nunca a Internet.

Por ejemplo, una estación móvil puede transmitir su posición junto con información sobre la frecuencia que está monitorizando. Un operador situado a varios kilómetros puede recibir el paquete y utilizar esa información para establecer una comunicación de voz.

Si no hay ningún IGate funcionando en la zona, el paquete no aparecerá en un servicio de Internet.

Eso no significa que APRS haya fallado.

Al contrario: la información ha llegado a un usuario para quien era útil.

Por tanto:

**la aparición de una estación en un mapa de Internet no debe considerarse el único criterio para determinar si APRS funciona correctamente.**

## El valor local de la información

APRS fue diseñado principalmente como un sistema de información táctica.

En este contexto, la palabra *táctica* no implica un uso militar. Se refiere a información útil para el usuario en su situación y entorno actuales.

Para un operador que viaja en automóvil, la información:

```text
SP9XYZ
145.550 MHz
```

puede ser más útil que un historial detallado del movimiento de esa estación durante las últimas horas.

Del mismo modo, un objeto que indica un repetidor local, un punto de encuentro, un punto de control o una actividad de campo tiene mayor valor para los usuarios que se encuentran cerca.

Por este motivo, APRS no debe considerarse un sistema destinado a enviar toda la información posible a todos los usuarios.

El objetivo es proporcionar **la información adecuada, a los usuarios adecuados, en el momento adecuado**.

## Un único canal compartido

El APRS clásico en VHF funciona normalmente a **1200 baudios**.

Se trata de un canal de radio compartido por todas las estaciones que se encuentran dentro de su alcance.

Cada transmisión ocupa una parte del tiempo de radio disponible. Además, un paquete puede ser repetido por uno o varios digipeaters.

Por este motivo, APRS requiere un uso razonable de la capacidad disponible del canal.

Más transmisiones no siempre significan más información.

Un beaconing excesivamente frecuente, rutas de digipeater innecesariamente largas o paquetes demasiado extensos pueden reducir la posibilidad de que otros usuarios utilicen el canal.

En una red bien diseñada, las transmisiones deberían realizarse **con la frecuencia necesaria, pero no con más frecuencia de la necesaria**.

Las reglas detalladas sobre rutas, digipeaters, colisiones y utilización del canal se describen en otras partes de la documentación.

## Difusión en lugar de una conexión clásica

La mayoría de las transmisiones APRS utilizan tramas AX.25 de tipo UI, es decir, *Unnumbered Information*.

Por tanto, no se establece previamente una conexión clásica entre emisor y receptor.

Una estación transmite un paquete y todas las estaciones que se encuentran dentro de su alcance pueden recibirlo.

Este enfoque encaja muy bien con la naturaleza de APRS.

Información como:

```text
estoy aquí
```

```text
estoy monitorizando esta frecuencia
```

```text
hay un repetidor en este lugar
```

```text
se ha emitido un aviso meteorológico
```

puede resultar útil para muchos usuarios al mismo tiempo.

Esto no significa que APRS no permita la comunicación dirigida a una estación concreta.

El protocolo dispone de un sistema de mensajes de texto breves y de mecanismos para confirmar su recepción. Sin embargo, estas funciones están construidas sobre el carácter de difusión de la red básica.

## ¿Qué puede ser una estación APRS?

Un nodo APRS no tiene que ser únicamente un tracker GPS instalado en un vehículo.

En la red pueden funcionar, entre otros:

- equipos portátiles,
- equipos móviles,
- estaciones base,
- trackers,
- ordenadores con módem por software,
- digipeaters,
- IGates,
- estaciones meteorológicas,
- dispositivos de telemetría,
- globos,
- estaciones portátiles,
- dispositivos APRS especializados.

Algunos dispositivos solo transmiten datos. Otros también pueden recibirlos, analizarlos y presentarlos al operador.

El uso más completo de APRS aparece cuando el operador no solo transmite su propia posición, sino que también utiliza la información generada por los demás usuarios de la red.

## Breve historia

APRS fue desarrollado por **Bob Bruninga, WB4APR**.

Las raíces del sistema se remontan a la década de 1980. Uno de sus primeros usos principales fue mostrar la posición y el estado de los participantes en actividades de campo.

Originalmente, las siglas APRS se desarrollaban como:

**Automatic Position Reporting System**

A medida que el sistema evolucionó, se hizo cada vez más evidente que la posición era solo uno de los muchos tipos de información intercambiados.

Por ello, el nombre pasó a desarrollarse como:

**Automatic Packet Reporting System**

Este cambio refleja muy bien la forma en que debe entenderse APRS.

No es únicamente un sistema de reporte de posición.

Es un sistema de reporte de **información**.

## APRS como herramienta de apoyo a las comunicaciones

Uno de los usos más interesantes de APRS es el apoyo a las comunicaciones de voz tradicionales.

Una estación puede transmitir, junto con su posición, información sobre la frecuencia que está monitorizando actualmente.

Un operador que se encuentre cerca puede ver no solo el indicativo de la estación, sino también información que le permite establecer contacto directamente.

Del mismo modo, APRS puede informar sobre repetidores locales, eventos, actividades u otras estaciones que se encuentran en la zona.

En este sentido, APRS no es un sistema que exista al margen de las comunicaciones clásicas de radioaficionados.

Puede actuar como una capa de información que ayuda a iniciar esas comunicaciones.

## ¿Cómo debe entenderse APRS?

La forma más sencilla de entender APRS es considerarlo un canal de radio compartido que transporta mensajes breves sobre la situación actual.

Cada estación puede añadir una pequeña parte de información a la imagen general:

```text
Estoy aquí.
```

```text
Estoy monitorizando aquí.
```

```text
Aquí hay un repetidor.
```

```text
Aquí se está celebrando un evento.
```

```text
Estas son las condiciones meteorológicas actuales.
```

```text
Este dispositivo tiene este estado.
```

```text
Hay un aviso activo para esta zona.
```

La combinación de estas informaciones crea una imagen actual de lo que está ocurriendo en la red local.

Esa es la esencia de APRS.

## Puntos clave para recordar

**APRS no es únicamente un sistema de seguimiento de posición.**

La posición es solo uno de muchos tipos de datos.

**APRS puede funcionar sin Internet.**

Una red de radio local puede ser el medio básico de comunicación.

**APRS-IS amplía las posibilidades del sistema, pero no es su esencia.**

Los servicios de Internet utilizan información procedente de la red APRS.

**APRS.fi no es APRS.**

Es una de las formas de presentar datos procedentes de APRS-IS.

**APRS se utiliza para distribuir información breve y actual.**

Su valor depende principalmente de si esa información resulta útil para otros usuarios de la red.

**APRS puede apoyar las comunicaciones de radio tradicionales.**

La información sobre posición, estado y frecuencia monitorizada puede ayudar directamente a establecer contacto.

## A continuación

Para comprender el funcionamiento del sistema con más detalle, conviene conocer después:

- la ruta de un paquete APRS entre estaciones,
- la relación entre APRS y AX.25,
- la estructura de un paquete APRS,
- los tipos básicos de datos APRS,
- el papel de los digipeaters,
- el papel de los IGates y APRS-IS,
- las limitaciones del canal de radio APRS.

Estos temas se describen en los siguientes artículos de la sección **Resumen del protocolo APRS**.

## Fuentes y lecturas adicionales

Para preparar este artículo se utilizaron principalmente:

- materiales y documentación APRS elaborados por Bob Bruninga, WB4APR,
- *APRS Protocol Reference*,
- el proyecto de documentación APRS Specification mantenido por John Langner, WB2OSZ,
- *What is APRS?*,
- *Understanding APRS Packets*,
- materiales relacionados con la especificación moderna APRS 1.2.

Los materiales de referencia utilizados por el proyecto APRSBox también están disponibles en el directorio `APRS-SPEC` del repositorio:

`https://github.com/SQ9MDD/APRSBox/tree/main/APRS-SPEC`
