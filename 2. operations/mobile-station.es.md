---
title: Estación APRS móvil
description: Cómo elegir e instalar el equipo de una estación APRS móvil.
template: doc
tableOfContents: true
---

Una estación APRS móvil informa la posición de un vehículo durante el desplazamiento y puede recibir estaciones y mensajes APRS. Puede ser un tracker sencillo que solo transmite la posición o un terminal de radio completo con pantalla.

Lo más importante es contar con un equipo bien elegido y correctamente instalado. La configuración de tramas, rutas y beaconing depende de la red local; estos temas se explican en la sección de protocolo.

En Polonia, el canal APRS principal en la banda de 2 m es **144,800 MHz FM**.

## ¿De qué se compone una estación?

La configuración más sencilla tiene cuatro elementos:

1. **Radio o transmisor**
2. **Tracker APRS o TNC**
3. **Receptor GPS**
4. **Antena y alimentación**

Puede tener este aspecto:

```text
GPS
 |
Tracker / TNC
 |             \
 |              \ audio + PTT
Radio 144,800 MHz
 |
Antena del vehículo
```

Muchos transceptores APRS actuales integran tracker, TNC y GPS en una sola carcasa. En ese caso, radio, antena y alimentación son suficientes para una estación completa.

## Tres variantes básicas

### Tracker y radio convencional

Esta es la configuración más sencilla cuando la estación está destinada principalmente a transmitir una posición. El tracker recibe datos GPS, crea un paquete APRS, envía audio a la entrada de micrófono de la radio y activa el PTT.

```text
GPS → tracker → radio → antena
```

Este conjunto es compacto, consume poca energía y puede funcionar sin ordenador. Su limitación habitual es la falta de recepción cómoda y de mensajería APRS.

### Radio con APRS integrado

Un transceptor con TNC y GPS propios es la solución más cómoda para quien desea transmitir su posición y utilizar APRS sin un ordenador adicional. Además de la posición propia, puede mostrar en pantalla las estaciones móviles y fijas recibidas, su distancia, dirección y los últimos datos recibidos.

En la práctica, un transceptor de este tipo facilita establecer contactos. Se puede detectar una estación que circula cerca, comprobar su indicativo y dirección y después llamarla por voz en una frecuencia incluida en una trama APRS o en la información de un objeto. Los objetos APRS se pueden utilizar de la misma forma, por ejemplo para consultar datos de un repetidor local, su frecuencia o el tono de acceso.

El terminal APRS integrado normalmente permite además:

- recibir y consultar mensajes APRS,
- enviar un mensaje corto a una estación elegida,
- recibir un boletín APRS local,
- consultar las estaciones y objetos recibidos,
- mostrar datos básicos de posición sin iniciar el teléfono.

Las funciones disponibles y la forma de presentar los datos dependen del modelo. La pantalla de una radio no sustituye a un mapa completo, pero resulta muy práctica para comprobar rápidamente quién está activo cerca e intercambiar mensajes breves durante una parada.

Antes de comprar, conviene comprobar que la radio dispone de:

- GPS integrado o entrada para un receptor externo,
- módem AFSK de 1200 baudios y TNC,
- compatibilidad con datos packet en la banda de 2 m,
- conector de datos o posibilidad de usar un TNC externo,
- una interfaz legible y segura de usar en un vehículo.

Algunos ejemplos de transceptores compatibles con APRS son el portátil **Kenwood TH-D75E/D75A** y los móviles **Kenwood TM-D710G**, **Yaesu FTM-300DE/DR** y **Yaesu FTM-500DE/DR**. Son ejemplos de clases de equipos, no una lista de compra: algunos modelos pueden estar descatalogados y la disponibilidad y la variante de bandas dependen del mercado.

### Radio, TNC y aplicación

También es posible conectar una radio convencional a un TNC externo, una tableta, un teléfono o un ordenador pequeño. La aplicación actúa entonces como terminal APRS, mientras que el TNC convierte los datos digitales en audio AFSK y controla el PTT.

```text
GPS / teléfono
      |
Aplicación APRS
      |
     TNC
      |
Radio → antena
```

Esta solución ofrece esencialmente las mismas funciones básicas de APRS que una radio con terminal integrado: posición, lista de estaciones, mensajes, boletines y objetos. En una pantalla más grande se añade además un mapa, que permite ver de inmediato la posición de la estación propia, otros usuarios, repetidores y demás objetos APRS.

Ofrece mucha flexibilidad, pero también más cables, conexiones y posibles puntos de fallo. Es la mejor opción cuando se necesita un mapa, una pantalla grande o escribir mensajes con más comodidad durante una parada.

## Radio

Para APRS móvil se necesita una radio FM que funcione en 2 m. No requiere compatibilidad APRS de fábrica si funciona con un tracker o TNC.

Al elegir una radio, presta especial atención a:

- acceso a la entrada de micrófono, salida de audio y PTT,
- un conector DATA, PACKET o de accesorios, que simplifique conectar un tracker,
- posibilidad de seleccionar una potencia de transmisión adecuada,
- alimentación estable de 12 V,
- montaje y manejo cómodos sin apartar la atención de la carretera.

Las radios móviles instaladas permanentemente suelen ser las más prácticas, pero también se puede usar un transceptor portátil con un tracker sencillo. En ese caso hay que cuidar especialmente el cable, el adaptador para el conector de accesorios y la refrigeración de la radio durante la transmisión.

## Tracker y TNC

Un **tracker** está dedicado al envío automático de una posición. Por lo general tiene entrada GPS, salida de audio, control PTT y memoria de configuración. No todos los trackers reciben y decodifican tráfico APRS; antes de comprar hay que comprobar claramente si el equipo es solo transmisor o también receptor.

Un **TNC** es un módem de packet radio. Puede trabajar con una aplicación APRS o con una radio y ordenador. Además de transmitir, normalmente permite recibir y decodificar tramas, pero sus funciones dependen del modelo y el software concretos.

En los equipos móviles encontrarás normalmente:

- un tracker de hardware con GPS o entrada NMEA,
- un TNC externo conectado por USB, Bluetooth o Wi-Fi,
- un TNC integrado en la radio,
- un módem por software que funciona en un teléfono u ordenador con interfaz de audio.

En 2 m, APRS utiliza normalmente AFSK a 1200 baudios. Por tanto, un tracker o TNC externo debe ser compatible con este modo y con los niveles de audio utilizados por la radio.

## GPS

El GPS puede estar integrado en la radio o el tracker, conectarse como receptor independiente o ser proporcionado por un teléfono.

La solución más fiable es un receptor que envíe datos de posición actuales directamente al equipo APRS. Los trackers antiguos suelen usar comunicación NMEA a través de un puerto serie. Antes de la instalación, comprueba la compatibilidad del conector, la tensión de alimentación y el formato de datos.

El receptor debe tener una vista del cielo lo más despejada posible. En un coche se puede colocar bajo el parabrisas o usar una antena GPS en la ubicación recomendada por el fabricante. No lo cubras con piezas metálicas ni lo ocultes profundamente bajo el salpicadero.

## Antena

En APRS móvil, la antena suele ser más importante que una alta potencia de transmisión. Una antena de 2 m bien instalada mejora tanto el alcance de transmisión como la recepción de estaciones y digipeaters.

El mejor lugar habitual es el techo del coche. La carrocería metálica proporciona entonces un plano de tierra para la antena. Una antena en la tapa del maletero, un soporte o una barra de techo puede funcionar correctamente, pero su diagrama y alcance pueden ser diferentes.

Durante la instalación, presta atención a:

- un montaje estable, resistente a vibraciones y condiciones meteorológicas,
- cable coaxial sin daños,
- un conector estanco en el exterior del vehículo,
- tender el cable sin pellizcarlo con una puerta,
- separación de otras antenas y obstáculos metálicos.

Antes de un viaje largo conviene comprobar la ROE. Una ROE alta puede indicar un problema de antena, cable o conector y someter al transmisor a un esfuerzo innecesario.

## Conexión con la radio

Un tracker o TNC se conecta a la radio mediante tres señales básicas:

| Señal | Función |
|---|---|
| Audio a la radio | Envía modulación AFSK a la entrada de micrófono o DATA |
| Audio desde la radio | Envía el audio recibido al TNC si la estación debe recibir |
| PTT | Activa el transmisor de la radio |

Muchas radios disponen de un conector DATA o PACKET específico. Normalmente es una mejor elección que el conector de micrófono, porque proporciona niveles de audio más adecuados y evita algunos ajustes adicionales.

Las conexiones de audio requieren ajustar los niveles. Un nivel demasiado bajo puede impedir la decodificación o recepción de paquetes; uno demasiado alto distorsionará la modulación. Verifica el ajuste durante la recepción real y una transmisión de prueba, siguiendo el manual del equipo.

## Alimentación e instalación en el vehículo

Es preferible alimentar una radio móvil desde la instalación de 12 V mediante cables de sección adecuada y un fusible situado cerca de la fuente de alimentación. La radio consume mucha más corriente al transmitir que al recibir, por lo que el cable de alimentación y la toma de accesorios deben estar dimensionados para esa demanda.

Un tracker, TNC y GPS pueden requerir 5 V, USB o un regulador de tensión independiente. No supongas que todos los conectores de la radio pueden alimentar un dispositivo adicional; consulta en la documentación la tensión y corriente disponibles.

Monta los dispositivos y cables de forma segura, fuera de las zonas de despliegue de los airbags. La pantalla, el micrófono y los controles no deben limitar la visibilidad ni dificultar la conducción.

## Antes del primer viaje

Antes de la instalación permanente, prueba el conjunto en una mesa o con el vehículo detenido. Comprueba, en este orden:

1. que la radio trabaja en el canal APRS correcto,
2. que el GPS proporciona una posición actual,
3. que el tracker o TNC activa correctamente el PTT,
4. que el nivel de audio no sobremodula el transmisor,
5. que la antena y los cables están en buen estado,
6. que los equipos siguen funcionando de forma estable al arrancar el motor.

Los ajustes de indicativo, símbolo, beaconing y ruta dependen de las reglas APRS y de la cobertura local. Configúralos según los artículos de la sección de protocolo y comprueba, tras los primeros trayectos, que las posiciones recibidas corresponden a la ruta real.

## ¿Por dónde conviene empezar?

El camino más sencillo hacia APRS móvil es:

```text
1. Una antena de 2 m
2. Una radio en 144,800 MHz
3. Un tracker con GPS o una radio con APRS integrado
4. Alimentación y montaje fiables
5. Prueba de audio, PTT y recepción
6. Configuración APRS según la práctica de la red local
```

Solo después de una prueba satisfactoria merece la pena ampliar la estación con una pantalla de mapa, mensajería APRS, un TNC adicional o integración con teléfono.
