---
title: APRX
description: Daemon APRS ligero para sistemas POSIX que combina iGate, digipeater, múltiples interfaces de radio, telemetría y enrutamiento APRS-IS.
template: doc
tableOfContents: true
---

APRX es software especializado de infraestructura APRS, diseñado principalmente para funcionar como **iGate, digipeater o una combinación de ambas funciones**.

El programa se ejecuta como daemon en sistemas compatibles con POSIX, especialmente Linux, BSD y otros sistemas tipo Unix.

Repositorio oficial:

https://github.com/PhirePhly/aprx/

Página del proyecto:

https://thelifeofkenneth.com/aprx/

APRX está escrito en C y desde el principio fue diseñado como software ligero para funcionamiento continuo de infraestructura.

No es un módem de audio.

Para comunicarse con la radio utiliza un TNC externo, un módem KISS, una interfaz AX.25 del sistema operativo u otra fuente compatible de tramas ya decodificadas.

Una instalación típica puede ser:

```text
Radio
  |
TNC / módem KISS
  |
APRX
  |
APRS-IS
```

o como digipeater:

```text
Radio
  |
TNC / módem
  |
APRX
  |
Radio
```

En instalaciones más complejas APRX puede gestionar simultáneamente varios receptores, transmisores y canales.

## Autores e historia del proyecto

El autor de la primera y segunda generación de APRX fue **Matti Aarnio OH2MQK**.

El código desarrollado por Matti corresponde al periodo:

```text
2007-2014
```

En los encabezados del código fuente APRX se describe como:

```text
2nd generation APRS iGate and digi
```

Desde 2014 el mantenimiento y desarrollo posterior del proyecto pasó a **Kenneth W. Finnegan W6KWF**.

El repositorio actual:

https://github.com/PhirePhly/aprx/

contiene código de ambas etapas de desarrollo.

APRX es un proyecto maduro. El ritmo de cambios es hoy mucho menor que en proyectos nuevos, pero el software sigue presente en distribuciones Linux y continúa utilizándose en instalaciones APRS reales.

La versión APRX 2.9.1 está disponible, entre otros lugares, en Debian.

## Filosofía de APRX

Uno de los objetivos fundamentales de APRX fue crear software de infraestructura con pocos requisitos.

El código fuente describe el proyecto como una solución con el menor número posible de dependencias fuera del entorno estándar de bibliotecas UNIX.

Por ello APRX resulta adecuado para:

- ordenadores pequeños,
- routers,
- dispositivos embedded con Linux,
- Raspberry Pi,
- PCs antiguos,
- servidores funcionando 24/7.

El programa no necesita entorno gráfico.

Una instalación típica es simplemente:

```text
sistema
  |
daemon aprx
  |
/etc/aprx.conf
```

## APRX no es un módem

Esta diferencia es importante.

APRX no decodifica AFSK desde una tarjeta de sonido.

Necesita una fuente de tramas AX.25 ya decodificadas.

Por ejemplo:

```text
Radio
  |
VP-Digi
  |
KISS
  |
APRX
```

o:

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
```

o un TNC de hardware clásico.

APRX se ocupa de:

- routing de tramas,
- digipeating,
- iGate,
- APRS-IS,
- filtrado,
- beacons,
- telemetría,
- lógica de múltiples interfaces.

El módem se ocupa de la capa de radio.

## RX iGate

Uno de los usos más habituales de APRX es un **iGate solo de recepción**.

En este esquema:

```text
RF
 |
Radio
 |
TNC
 |
APRX
 |
APRS-IS
```

las tramas recibidas por radio se envían a APRS-IS.

Una configuración mínima necesita principalmente:

- indicativo propio,
- passcode APRS-IS,
- servidor APRS-IS,
- interfaz de radio.

Ejemplo:

```text
<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>
```

APRX puede así realizar un iGate sencillo sin activar transmisión RF.

## TX iGate

APRX también soporta transmisión desde APRS-IS hacia RF.

Esto permite construir un iGate bidireccional completo.

```text
APRS-IS
   |
   v
 APRX
   |
   v
  RF
```

Un TX iGate no debe retransmitir todo el tráfico de APRS-IS a la radio.

APRX permite limitar y filtrar este tráfico.

La fuente `APRSIS` puede definirse en la sección de digipeater como una fuente separada con:

```text
relay-type third-party
```

También se pueden aplicar:

- filtrado,
- rate limiting,
- viscous delay.

Esto permite controlar con precisión qué tráfico se envía de Internet a RF.

## Digipeater

APRX incluye un motor de digipeater avanzado.

Soporta, entre otras cosas:

```text
WIDEn-N
```

y alias AX.25 normales.

Una sección de digipeater tiene un transmisor, pero puede recibir paquetes desde varias fuentes.

Ejemplo:

```text
RX1 ----\
         \
RX2 ------> APRX ---> TX
         /
RX3 ----/
```

Esto permite construir sistemas con:

- varios receptores,
- receiver diversity,
- antenas direccionales separadas,
- distintos canales,
- un transmisor común.

Esta arquitectura con múltiples fuentes fue una de las características que diferenciaron APRX de controladores de digipeater más simples.

## New-N

APRX soporta el mecanismo APRS New-N.

Puede trabajar con rutas como:

```text
WIDE1-1
WIDE2-1
WIDE2-2
```

La configuración permite definir el número máximo de saltos solicitados y realizados.

También se pueden definir claves de alias personalizadas.

Así APRX puede funcionar como fill-in digipeater local o como parte de una infraestructura mayor.

## Viscous digipeating

Una función característica de APRX es **viscous digipeating**.

La idea es que el digipeater no tenga que repetir inmediatamente un paquete.

Primero espera durante un corto periodo.

Si durante ese tiempo escucha que otra estación ya ha repetido el paquete, puede cancelar su propia transmisión.

```text
paquete recibido
     |
     v
espera corta
     |
     +---- paquete oído de nuevo ---> DROP
     |
     +---- sin repetición ----------> TX
```

Esto ayuda a reducir duplicados en áreas donde se solapan las coberturas de varios digipeaters.

APRX soporta viscous delay tanto dentro de una misma interfaz como entre interfaces diferentes.

## Múltiples receptores

APRX también fue diseñado pensando en nodos grandes.

Puede recibir tramas simultáneamente desde múltiples interfaces.

```text
Radio RX Norte ---> TNC ---\
                            \
Radio RX Sur -----> TNC ----> APRX ---> Radio TX
                            /
Radio RX Local ---> TNC ---/
```

Esto permite construir nodos con varios receptores y un solo transmisor.

Las interfaces también pueden agruparse mediante:

```text
igate-group
```

lo que ayuda a manejar correctamente el iGate cuando hay varios receptores y transmisores en el mismo canal.

## Múltiples canales

APRX no está limitado a un solo canal de radio.

Se pueden configurar varias interfaces y varias secciones de digipeater.

Una sola instancia puede gestionar un nodo más complejo.

Por ejemplo:

```text
144.800 MHz ---> APRS
432 MHz     ---> Packet / APRS
otro RF     ---> canal local
```

La configuración concreta depende de los módems y puertos utilizados.

## Interfaces KISS

APRX puede trabajar con TNC clásicos conectados por puerto serie.

Se soporta:

```text
KISS
```

y varias extensiones relacionadas.

En la configuración aparecen, entre otras:

```text
KISS
XORSUM / BPQCRC
SMACK / CRC16
FLEXNET
```

Esto permite trabajar con diferentes generaciones y tipos de TNC y módems de hardware.

## Modo monitor TNC2

APRX también puede recibir datos en formato:

```text
TNC2
```

es decir, el formato de monitor textual conocido de los TNC clásicos.

Esto permite integrar fuentes que no ofrecen KISS pero sí pueden mostrar las tramas recibidas en modo monitor.

## Linux AX.25

En Linux APRX también puede utilizar interfaces AX.25 proporcionadas directamente por el kernel.

La configuración puede usar:

```text
ax25-device
```

APRX puede entonces recibir tramas desde la pila AX.25 del kernel.

No es obligatorio.

El proyecto puede funcionar también sin soporte AX.25 en el kernel.

## DPRS

APRX incluye soporte **D-PRS**.

Puede recibir datos DPRS y convertirlos a APRS.

El proyecto incluye funcionalidad de gateway:

```text
D-PRS -> APRS
```

pensada principalmente para integrar datos de posición procedentes de sistemas de radio digital.

## APRS-IS

APRX incluye su propio cliente APRS-IS.

Puede conectarse a servidores Tier2 y manejar varias definiciones de servidor.

Ejemplo:

```text
server rotate.aprs2.net
```

También pueden usarse filtros APRS-IS.

Ejemplo:

```text
filter "m/100"
```

que significa tráfico dentro de 100 km de la posición de la estación.

También pueden utilizarse otros filtros compatibles con javAPRSSrvr/APRS-IS.

## Filtrado de tráfico

APRX ofrece mecanismos de filtrado amplios.

Se puede filtrar por:

- origen,
- destino,
- ruta,
- payload,
- área geográfica.

Existen filtros con expresiones regulares y filtros geográficos.

Por ejemplo, puede bloquearse un indicativo o limitarse la retransmisión a un área concreta.

Ejemplo:

```text
filter -b/CALL
```

El filtrado puede aplicarse por separado a distintas fuentes del digipeater.

## Rate limiting

APRX incluye mecanismos para limitar el número de tramas retransmitidas.

Se puede establecer un límite global mediante:

```text
ratelimit
```

y un límite por indicativo origen mediante:

```text
srcratelimit
```

Esto ayuda a proteger el canal frente a una estación que genere demasiado tráfico.

## Beacons

APRX posee su propio generador de beacons.

Puede generar:

- posiciones,
- items,
- objetos,
- tramas APRS raw,
- datos leídos desde un archivo.

Ejemplo:

```text
beacon symbol "I&" $myloc comment "Tx-iGate"
```

Los beacons pueden enviarse por una interfaz seleccionada y con una ruta determinada.

APRX distribuye intencionadamente los tiempos de transmisión, en lugar de enviar todos los beacons exactamente a la vez.

Los intervalos también se aleatorizan ligeramente para reducir estadísticamente la probabilidad de colisiones.

## Telemetría

APRX tiene su propio sistema de telemetría de infraestructura.

Recoge datos de tráfico por interfaz.

Entre otros valores monitoriza:

```text
RX
DROP
TX
```

además de contadores de paquetes y bytes.

Los datos pueden agregarse y publicarse como telemetría APRS.

APRX puede enviarlos a APRS-IS y opcionalmente a RF.

## Monitor Erlang

Un elemento interesante es el **monitor erlang** integrado.

El nombre procede de la unidad utilizada para describir ocupación de canal.

APRX mide el tráfico real de las interfaces y recoge estadísticas en intervalos como:

```text
1 minuto
10 minutos
20 minutos
```

Esto permite observar la carga real del canal de radio.

Las estadísticas pueden guardarse en un archivo de estado para que reinicios cortos no borren necesariamente las mediciones actuales.

## Logging

APRX puede mantener logs separados para:

- tramas RF recibidas,
- conexiones APRS-IS,
- DPRS,
- estadísticas erlang.

Ejemplo:

```text
aprx-rf.log
aprx.log
erlang.log
```

Los logs pueden rotarse normalmente mediante el sistema operativo.

## Configuración

El archivo principal suele ser:

```text
/etc/aprx.conf
```

La sintaxis recuerda a la configuración de Apache.

Ejemplo:

```text
mycall SQ9ABC-1

<aprsis>
    passcode 12345
    server rotate.aprs2.net
</aprsis>

<interface>
    serial-device /dev/ttyUSB0 9600 8n1 KISS
</interface>
```

Las secciones incluyen, entre otras:

```text
<aprsis>
<logging>
<interface>
<beacon>
<telemetry>
<digipeater>
```

Puede haber múltiples interfaces, beacons y secciones de telemetría.

## APRX con Dire Wolf

Una combinación práctica es utilizar APRX como lógica de infraestructura y un módem externo como capa de radio.

```text
Radio
  |
Dire Wolf
  |
KISS
  |
APRX
  |
APRS-IS
```

Dire Wolf se ocupa de la modulación y demodulación AFSK.

APRX se ocupa de:

- digipeating,
- iGate,
- filtros,
- múltiples fuentes,
- telemetría,
- routing.

Esta separación de funciones encaja bien con la arquitectura de APRX, que no necesita un módem de audio propio.

## APRX con TNC de hardware

También puede utilizarse un dispositivo KISS de hardware:

```text
Radio
  |
VP-Digi / TNC-X / otro TNC
  |
KISS
  |
APRX
```

Gracias al estándar KISS, APRX no depende de un modelo concreto de módem.

## Requisitos bajos

APRX está escrito en C y no tiene interfaz gráfica.

Sus requisitos de hardware son pequeños.

Funciona bien en:

- Raspberry Pi,
- pequeños ordenadores x86,
- hardware antiguo,
- dispositivos embedded con Linux,
- máquinas virtuales.

En una instalación típica de infraestructura puede funcionar durante meses sin intervención del operador.

## Estabilidad y madurez

APRX tiene una larga historia.

Su arquitectura principal se creó hace muchos años y ha sido probada en numerosas instalaciones APRS reales.

La línea 2.9.1 sigue disponible en Debian.

El repositorio mantiene issues y pull requests, pero hoy el proyecto debe considerarse principalmente **software maduro de infraestructura**, no una aplicación que cambie rápidamente.

## Qué no hace APRX

APRX no es:

- un módem de audio,
- un cliente APRS gráfico,
- un mapa APRS,
- un programa de seguimiento de estaciones,
- una aplicación diaria de mensajería para el operador.

Su lugar principal es la infraestructura.

La forma más sencilla de verlo:

```text
radio
  |
módem / TNC
  |
APRX
  |
lógica de infraestructura APRS
  |
APRS-IS / RF
```

## ¿Para quién es APRX?

APRX funciona bien cuando se necesita:

- RX iGate,
- TX iGate,
- digipeater,
- digi + iGate combinado,
- nodo con múltiples receptores,
- instalación con varias interfaces de radio,
- routing entre puertos,
- filtrado de tráfico,
- telemetría de infraestructura,
- daemon muy ligero funcionando 24/7.

Es especialmente interesante cuando el operador quiere controlar en detalle la lógica de la infraestructura APRS y tratar el módem de radio como una capa separada.

## Licencia

APRX es software open source.

El proyecto se distribuye bajo:

```text
BSD 3-Clause
```

Esto permite analizar, modificar y utilizar el código según las condiciones de la licencia.

## Resumen

APRX es uno de los programas clásicos de infraestructura APRS.

Su mayor fortaleza no es una sola función, sino la posibilidad de construir un nodo completo alrededor de un único proceso:

```text
múltiples receptores
      |
      v
    APRX
   /    \
 digi   iGate
   \    /
    RF / APRS-IS
```

El programa combina:

- RX iGate,
- TX iGate,
- New-N digipeating,
- viscous digipeating,
- múltiples interfaces,
- KISS y Linux AX.25,
- filtrado,
- rate limiting,
- beacons,
- telemetría,
- monitorización de carga del canal,
- DPRS,
- APRS-IS.

Al mismo tiempo sigue siendo un daemon pequeño escrito en C, sin necesidad de entorno gráfico ni de un sistema grande.

Por eso APRX ha sido durante muchos años una opción popular para estaciones de infraestructura APRS que trabajan continuamente.

## Documentación

Fuentes principales:

- repositorio: https://github.com/PhirePhly/aprx/
- página del proyecto: https://thelifeofkenneth.com/aprx/
- configuración de ejemplo: https://github.com/PhirePhly/aprx/blob/master/aprx.conf.in
- configuración compleja: https://github.com/PhirePhly/aprx/blob/master/aprx-complex.conf.in
- Debian Sources: https://sources.debian.org/src/aprx/
