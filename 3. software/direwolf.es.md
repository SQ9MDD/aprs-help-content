---
title: Dire Wolf
description: Módem AX.25 y TNC por software para APRS con soporte de AFSK, KISS, FX.25, digipeater e IGate.
template: doc
tableOfContents: true
---

**Dire Wolf** es un módem packet por software, TNC y codificador/decodificador AX.25 que utiliza la interfaz de audio del ordenador y procesamiento digital de señales.

Permite recibir y transmitir tramas AX.25 sin necesidad de utilizar un TNC físico tradicional. Puede funcionar de forma independiente o como capa de radio para otras aplicaciones APRS.

Repositorio oficial del proyecto:

https://github.com/wb2osz/direwolf

## Para qué sirve Dire Wolf

En su forma más sencilla, Dire Wolf convierte un ordenador con una interfaz de audio en un módem de packet radio.

```text
Radio <-> audio/PTT <-> Dire Wolf <-> KISS/AGW <-> aplicación APRS
```

El programa recibe el audio de la radio, demodula la señal y decodifica las tramas AX.25. En sentido contrario genera el audio de transmisión y puede controlar el PTT de la radio.

Dire Wolf puede utilizarse como:

- TNC por software,
- codificador y decodificador AX.25,
- decodificador APRS,
- módem KISS para otras aplicaciones,
- digipeater APRS,
- IGate,
- tracker y generador de balizas,
- puerta de enlace APRStt.

## Alta eficacia de decodificación

Una de las ventajas más importantes de Dire Wolf es su muy buena eficacia al decodificar tramas AX.25.

No es simplemente un sustituto por software de un antiguo TNC físico. Dire Wolf utiliza procesamiento digital de señales y varias técnicas destinadas a mejorar la recepción en condiciones reales de radio.

Según la configuración puede utilizar, entre otras cosas:

- distintos perfiles de demodulador,
- filtrado digital,
- compensación automática de amplitud entre mark y space,
- varios demoduladores en paralelo,
- varios puntos de decisión,
- intentos paralelos de decodificar la misma señal mediante métodos diferentes.

Esto resulta útil con señales débiles, cadenas de audio imperfectas, distintas características de los equipos de radio y operación en HF.

Dire Wolf también ha sido comparado con TNC clásicos utilizando material de prueba como WA8LMF TNC Test CD. En la práctica puede decodificar correctamente algunas tramas que soluciones TNC más simples o antiguas no consiguen recibir.

Esto no elimina la importancia de una buena cadena de radio. Los niveles de audio correctos, el ancho de banda, la relación señal/ruido y la calidad del receptor siguen siendo importantes.

## AFSK a 1200 bit/s

El modo APRS más habitual en VHF y UHF es:

```text
MODEM 1200
```

Los tonos AFSK típicos son 1200 Hz y 2200 Hz.

Es el modo básico del APRS clásico en la banda de 2 m y normalmente funciona a través de conexiones de audio convencionales de la radio.

## AFSK a 300 bit/s en HF

Dire Wolf también admite AFSK a 300 bit/s, utilizado principalmente para packet radio y APRS en HF SSB.

```text
MODEM 300
```

Los tonos predeterminados son 1600 Hz y 1800 Hz.

En HF resulta especialmente útil la posibilidad de utilizar varios demoduladores en paralelo con pequeñas diferencias de frecuencia. Esto ayuda a tolerar pequeñas diferencias de sintonía entre estaciones y variaciones introducidas por la cadena SSB.

Ejemplo de una configuración más avanzada:

```text
MODEM 300 1600:1800 7@30 /4
```

Los parámetros concretos deben adaptarse a la cadena de radio y a la versión de Dire Wolf utilizada.

## PSK a 2400 y 4800 bit/s

Dire Wolf también admite modos PSK a 2400 y 4800 bit/s.

Son mucho menos habituales que el AFSK clásico a 1200 bit/s, pero pueden utilizarse en aplicaciones más especializadas de packet radio.

## 9600 bit/s

Dire Wolf admite transmisión a 9600 bit/s al estilo G3RUH.

No debe considerarse simplemente una versión más rápida del AFSK a 1200. La salida de altavoz y la entrada de micrófono normales suelen no proporcionar una cadena adecuada debido al filtrado, preénfasis y deénfasis.

En la práctica se necesita una cadena de datos suficientemente ancha y relativamente directa, por ejemplo una conexión 9600 packet, discriminator o direct audio.

Por ello, para una estación APRS típica conectada mediante el audio normal de la radio, 1200 AFSK sigue siendo la solución más práctica.

## FX.25

Dire Wolf también admite **FX.25**, una extensión de AX.25 que utiliza corrección de errores FEC.

FX.25 añade información de corrección Reed-Solomon a la transmisión. Gracias a ello, un receptor compatible con FX.25 puede recuperar una trama válida en determinados casos incluso cuando se han producido errores durante la transmisión.

Una característica importante de FX.25 es su compatibilidad con AX.25 convencional.

La recepción de FX.25 en Dire Wolf está activada automáticamente. La transmisión FX.25 debe habilitarse de forma explícita.

Ejemplo:

```text
FX25TX 1
```

Según la versión también se puede seleccionar una cantidad concreta de bytes de paridad, por ejemplo 16, 32 o 64.

FX.25 aumenta la cantidad de datos transmitidos por radio, por lo que no siempre es adecuado para un canal APRS muy ocupado. Sin embargo, es una opción interesante para packet radio y para trabajar en condiciones de radio más difíciles.

## Muy importante: FIX_BITS

Dire Wolf incluye un mecanismo llamado `FIX_BITS` que intenta recuperar una trama AX.25 recibida con un FCS no válido.

El mecanismo puede modificar bits de la trama recibida y comprobar si el resultado produce un FCS válido.

Puede ser interesante para experimentos con packet radio, pero requiere especial precaución en infraestructura APRS.

Una trama con FCS no válido fue recibida con un error. Intentar repararla puede hacer que se reenvíen datos modificados y que las aplicaciones posteriores ya no puedan distinguirlos de una trama recibida correctamente.

Un solo bit modificado puede afectar a:

- el indicativo,
- SSID,
- la ruta,
- la posición,
- un mensaje,
- telemetría,
- el identificador de mensaje,
- cualquier parte del payload APRS.

Para una infraestructura APRS típica, especialmente cuando Dire Wolf funciona como módem de un IGate o digipeater, es recomendable desactivar explícitamente este mecanismo:

```text
FIX_BITS 0
```

Así se mantiene una regla sencilla: solo se reenvían las tramas que realmente fueron recibidas con un FCS válido.

### FIX_BITS y FX.25

`FIX_BITS` y FX.25 son mecanismos diferentes.

FX.25 utiliza información FEC adicional transmitida intencionadamente por la estación emisora. El receptor dispone por tanto de redundancia matemática que permite corregir una cantidad determinada de errores.

`FIX_BITS`, en cambio, busca cambios de bits en una trama AX.25 convencional ya dañada que hagan que el FCS resulte válido.

Por tanto es posible utilizar:

```text
FIX_BITS 0
```

y seguir recibiendo y corrigiendo tramas FX.25.

## KISS y otras aplicaciones

Dire Wolf puede funcionar como TNC virtual y entregar las tramas recibidas a otras aplicaciones.

Admite, entre otras interfaces:

- KISS sobre TCP,
- KISS mediante puerto serie o pseudo-terminal,
- interfaz de red AGW.

Una arquitectura típica puede ser:

```text
Radio
  |
  | audio + PTT
  v
Dire Wolf
  |
  | KISS TCP
  v
APRSBox / YAAC / Xastir / otra aplicación
```

De este modo Dire Wolf puede ocuparse únicamente de la capa de radio y AX.25, mientras la lógica APRS permanece en otro programa.

El puerto KISS TCP predeterminado suele ser:

```text
8001
```

## Digipeater

Dire Wolf incorpora soporte para digipeating APRS.

Puede recibir tramas del canal de radio, analizar su ruta y retransmitir paquetes según las reglas configuradas.

Antes de poner en marcha un digipeater conviene comprender bien el funcionamiento de las rutas APRS, especialmente `WIDE1-1`, `WIDE2-1` y la necesidad de evitar retransmisiones innecesarias.

Un digipeater mal configurado puede aumentar de forma importante la ocupación del canal.

## IGate

Dire Wolf también puede funcionar como puerta de enlace entre la red APRS por radio y APRS-IS.

La dirección más sencilla es:

```text
RF -> Dire Wolf -> APRS-IS
```

Dire Wolf también dispone de funciones relacionadas con el envío de determinada información desde APRS-IS hacia RF.

El funcionamiento APRS-IS -> RF requiere especial precaución. El tráfico de Internet no debe retransmitirse al canal de radio sin restricciones adecuadas.

## Control de PTT

La transmisión normalmente requiere controlar el transmisor de radio.

Dire Wolf admite varios métodos de PTT, entre ellos:

- RTS y DTR del puerto serie,
- GPIO,
- interfaces basadas en CM108/CM119,
- Hamlib,
- determinadas interfaces de radio dedicadas.

Ejemplo utilizando RTS:

```text
PTT /dev/ttyUSB0 RTS
```

Según la interfaz puede ser necesaria una polaridad invertida.

## Interfaces de audio y SDR

Dire Wolf utiliza dispositivos de audio estándar del sistema operativo.

Puede trabajar con:

- tarjetas de sonido integradas,
- adaptadores de audio USB,
- interfaces de radio con códec de audio integrado,
- dispositivos de audio virtuales,
- receptores SDR.

Ejemplo de cadena de recepción SDR:

```text
RTL-SDR
   |
   v
rtl_fm / otro software SDR
   |
   v
Dire Wolf
   |
   v
KISS / APRS
```

Esto permite construir un receptor APRS sencillo o un IGate solo de recepción.

## Funcionamiento continuo

Dire Wolf es adecuado para instalaciones que funcionan de forma continua.

Puede ejecutarse en ordenadores pequeños como Raspberry Pi como módem, TNC, IGate, digipeater o capa de radio para otra aplicación.

Esta es una ventaja independiente del alto rendimiento de sus demoduladores.

## Ejemplo de configuración básica

Una configuración muy simplificada para una estación APRS típica a 1200 bit/s puede ser:

```text
ADEVICE plughw:0,0

CHANNEL 0
MYCALL SQ9ABC

MODEM 1200

FIX_BITS 0
```

Una instalación real requiere además configurar correctamente el dispositivo de audio, los niveles de señal, PTT, las interfaces KISS y las funciones específicas de la estación.

Normalmente conviene configurar y comprobar primero la recepción antes de habilitar la transmisión.

## Diagnóstico

Dire Wolf muestra en el terminal mucha información útil durante el arranque y la recepción de paquetes.

Puede mostrar:

- tramas AX.25 recibidas,
- contenido APRS decodificado,
- niveles de audio,
- información sobre el canal de recepción,
- información del demodulador,
- mensajes de diagnóstico.

Por ello también es una herramienta muy útil para ajustar la cadena de audio y diagnosticar problemas de recepción.

## Documentación

Fuentes principales:

- repositorio del proyecto: https://github.com/wb2osz/direwolf
- documentación del proyecto: https://github.com/wb2osz/direwolf/tree/master/doc
- documentación adicional: https://github.com/wb2osz/direwolf-doc
- configuración de ejemplo: https://github.com/wb2osz/direwolf/blob/master/conf/generic.conf

Al configurar una versión concreta de Dire Wolf debe utilizarse la documentación correspondiente a esa versión.
