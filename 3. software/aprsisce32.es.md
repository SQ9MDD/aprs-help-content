---
title: APRSISCE/32
description: Cliente APRS avanzado para Windows y Windows Mobile con mapas, RF, APRS-IS, mensajes, objetos, MultiLine, NWS-WARN, radiogoniometría, iGate y muchas otras funciones del protocolo APRS.
template: doc
tableOfContents: true
---

APRSISCE/32 es uno de los clientes APRS clásicos más avanzados.

El programa reúne en una sola aplicación:

- mapa APRS,
- conexión con APRS-IS,
- soporte de radio y TNC,
- mensajes,
- objetos e items,
- telemetría,
- meteorología,
- iGate,
- beaconing,
- seguimiento de estaciones,
- Direction Finding,
- objetos MultiLine y áreas,
- soporte NWS-WARN,
- filtrado avanzado APRS-IS.

El proyecto fue creado por **Lynn Deffenbaugh KJ4ERJ**.

Documentación:

https://aprsisce.wikidot.com/

Descargas:

https://aprsisce.wikidot.com/downloads

Grupo de usuarios y soporte:

https://groups.io/g/APRSISCE

APRSISCE/32 es especialmente interesante porque implementa muchas más partes del protocolo APRS que las posiciones y los mensajes simples.

## APRSISCE y APRSIS32

El nombre del proyecto incluye dos versiones principales.

### APRSISCE

APRSISCE estaba destinado a dispositivos con:

- Windows Mobile 5,
- Windows Mobile 6,
- Windows Mobile 6.1,
- Windows Mobile 6.5,
- Windows CE.

Esto permitía utilizar un cliente APRS completo en PDA y terminales móviles.

### APRSIS32

APRSIS32 es la versión para ordenadores Windows.

A pesar de su nombre, también funciona en sistemas Windows de 64 bits.

La página actual de descargas ofrece una versión funcional para Windows de 32 y 64 bits, incluidos Windows 10 y Windows 11.

Las versiones Windows Mobile y Windows CE tienen hoy principalmente interés histórico.

## Terminal APRS completo

El modo más simple es:

```text
APRS-IS
   |
   |
APRSIS32
   |
  mapa
```

Las posibilidades completas aparecen al conectar una radio:

```text
Radio
  |
TNC / módem
  |
APRSIS32
  |
APRS-IS
```

En esta configuración una sola aplicación puede:

- recibir estaciones por RF,
- mostrarlas en el mapa,
- transmitir beacons propios,
- gestionar mensajes,
- enviar paquetes a APRS-IS,
- transmitir tráfico seleccionado de APRS-IS a RF,
- crear y transmitir objetos.

## Mapas

Una de las funciones centrales de APRSISCE/32 es el mapa.

El programa utiliza mapas por teselas, principalmente OpenStreetMap.

También se pueden definir otras fuentes de mapas.

Las teselas se guardan localmente, por lo que las zonas descargadas pueden seguir disponibles cuando la conexión a Internet es limitada.

Es útil durante:

- trabajo de campo,
- eventos de radioaficionados,
- ejercicios,
- búsquedas,
- operación móvil.

## Estaciones en el mapa

APRSISCE/32 no se limita a mostrar un símbolo y un indicativo.

Dependiendo del contenido de la trama puede mostrar:

- indicativo,
- símbolo APRS,
- comentario,
- altitud,
- velocidad,
- rumbo,
- meteorología,
- trayectoria,
- frecuencia de trabajo,
- información sobre mensajes,
- precisión de posición.

También soporta **position ambiguity**.

## Seguimiento de estaciones

Una estación seleccionada puede seguirse en el mapa.

El programa puede mover automáticamente el mapa con la estación.

También existe:

```text
MultiTrack
```

para abrir una ventana independiente de seguimiento.

## Historial de movimiento

APRSISCE/32 registra posiciones sucesivas y puede construir una traza.

Las trazas también pueden guardarse como:

```text
GPX
```

## Scroller

Una función característica de APRSIS32 es el **Scroller** situado a la izquierda de la ventana principal.

Permite ver rápidamente qué está ocurriendo en la red.

Los símbolos pueden distinguir:

```text
*  paquete recibido directamente por RF
#  paquete relacionado con transmisión APRS-IS -> RF
@  paquete repetido por el digipeater local
```

Los colores y marcas adicionales ayudan a distinguir el tipo y origen del tráfico.

## Soporte de radio

APRSISCE/32 puede trabajar con muchos dispositivos y módems.

La documentación incluye:

- TNC clásicos,
- KISS TNC,
- Kenwood TM-D700,
- Kenwood TM-D710,
- Kenwood TH-D7,
- Kenwood TH-D72,
- Kenwood TH-D74,
- Argent OT-USB,
- TinyTrak4,
- AGWPE,
- UZ7HO SoundModem,
- Dire Wolf.

Se soportan puertos serie y conexiones de red.

## KISS

El programa puede trabajar directamente con dispositivos KISS.

Modos disponibles:

```text
KISS
Simply KISS
```

`Simply KISS` está pensado para equipos que arrancan directamente en modo KISS.

El modo KISS normal puede enviar comandos adicionales al TNC.

## AGW

APRSIS32 puede utilizar la interfaz AGW.

Esto permite trabajar con módems de software como:

- UZ7HO SoundModem,
- Dire Wolf.

Ejemplo:

```text
Radio
  |
tarjeta de sonido
  |
Dire Wolf
  |
AGW
  |
APRSIS32
```

o:

```text
Radio
  |
tarjeta de sonido
  |
UZ7HO SoundModem
  |
AGW
  |
APRSIS32
```

APRSIS32 puede actuar como interfaz completa y dejar la decodificación AFSK a un módem externo.

## Kenwood TM-D710

La integración con radios Kenwood con APRS integrado es especialmente amplia.

Con el TM-D710, APRSISCE/32 puede mostrar más información de la visible en la propia radio.

Puede decodificar, entre otras cosas:

- informes Direction Finding,
- objetos MultiLine,
- historial completo de trazas.

## APRS-IS

APRSISCE/32 ofrece conexión completa con APRS-IS.

Puede recibir simultáneamente:

```text
RF + APRS-IS
```

y combinar ambas fuentes en una sola imagen operativa.

## Filtros avanzados APRS-IS

El programa soporta filtros compatibles con javAPRSSrvr.

Se puede filtrar por:

- área,
- radio,
- indicativo,
- prefijo,
- digipeater,
- iGate,
- objetos,
- símbolo,
- tipo de paquete,
- mensajes de grupo.

Ejemplo:

```text
m/50
```

## iGate

APRSIS32 puede funcionar como iGate.

```text
RF
 |
Radio
 |
APRSIS32
 |
APRS-IS
```

También puede transmitir tráfico seleccionado de APRS-IS a RF.

Los puertos permiten configurar:

- RF to IS,
- IS to RF,
- Messages,
- Bulletins/Objects,
- Beacon,
- Telemetry,
- transmit enable.

## Digipeater

APRSIS32 también incluye función de digipeater.

Por ejemplo:

```text
WIDE1-1=WIDE1*
```

permite crear un digipeater local o temporal.

## Mensajes APRS

El programa incluye una interfaz avanzada de mensajes.

Soporta:

- mensajes de texto,
- identificadores,
- ACK,
- retransmisiones,
- conversaciones con varias estaciones.

También maneja correctamente rutas como:

```text
RF
 |
iGate
 |
APRS-IS
 |
iGate
 |
RF
```

incluido el retorno de ACK.

## Message-able

La función:

```text
View Message-able
```

puede destacar estaciones probablemente capaces de intercambiar mensajes APRS.

## Objetos

APRSISCE/32 ofrece soporte avanzado de objetos APRS.

Pueden representar:

- repetidores,
- frecuencias,
- puntos de encuentro,
- puntos de ayuda,
- peligros,
- estaciones técnicas,
- lugares de actividad.

Un objeto puede contener:

- posición,
- símbolo,
- comentario,
- ruta,
- intervalo,
- frecuencia,
- tono CTCSS,
- información QSY.

## Objetos de frecuencia

APRSISCE/32 interpreta APRS Frequency Specification.

Ejemplo:

```text
145.650MHz T077 -060
```

El cliente puede extraer:

- frecuencia,
- tono,
- dirección del offset,
- valor del offset.

## QRU, APRS Info-kiosk

Una función especialmente interesante es **QRU**.

Permite almacenar muchos objetos localmente y transmitirlos solo cuando son solicitados.

```text
estación móvil
     |
     | INFO
     v
servidor QRU
     |
     | lista de grupos
     v
estación móvil
```

Se pueden solicitar grupos como:

```text
FUEL
FOOD
HOSP
RP2M
```

y recibir objetos cercanos a la posición actual.

## MultiLine

APRSISCE/32 soporta la extensión **MultiLine**.

Permite transmitir:

- líneas,
- rutas,
- límites,
- polígonos,
- áreas.

El programa puede recibir y crear estos datos.

También soporta coordenadas comprimidas.

## Objetos de área

MultiLine permite representar zonas completas, por ejemplo:

- zona operativa,
- zona cerrada,
- área de búsqueda,
- recinto de evento,
- área de peligro,
- ruta.

```text
+-------------------+
|                   |
|   ZONA OPERATIVA  |
|                   |
+-------------------+
```

## NWS-WARN

Una de las funciones más avanzadas es el soporte de **National Weather Service**.

APRSISCE/32 puede:

- recibir objetos NWS,
- identificar el área de alerta,
- mostrar alertas en el mapa,
- usar shapefiles locales,
- asociar identificadores con límites reales.

Utiliza:

```text
SHP
SHX
DBF
```

para dibujar límites precisos.

## Por qué NWS-WARN es importante

```text
información APRS corta
        |
        v
identificador de área
        |
        v
shapefile local
        |
        v
área de alerta en el mapa
```

Es una forma muy eficiente de transmitir información geográfica compleja por un canal de baja velocidad.

## Direction Finding

APRS incluye formatos para radiogoniometría.

APRSISCE/32 puede interpretar y visualizar paquetes **Direction Finding**.

Puede mostrar:

```text
DF circles
```

en el mapa.

## Triangulación

Las mediciones de varios puntos pueden mostrarse juntas.

Útil para:

- localizar interferencias,
- fox hunting,
- localizar transmisores,
- ejercicios de campo.

## Meteorología

APRSISCE/32 soporta estaciones meteorológicas APRS.

También puede generar paquetes meteorológicos.

Si un programa externo actualiza:

```text
wxnow.txt
```

APRSISCE/32 puede crear una trama APRS Weather válida y transmitirla por APRS-IS, RF o ambos.

## Telemetría

El programa soporta telemetría APRS estándar.

## APRS Queries

Soporta consultas como:

```text
?APRS?
?IGATE?
?WX?
```

## Item-In-Message

También soporta:

```text
Item-In-Message
```

## Satélites

APRSISCE/32 puede trabajar con datos TLE y calcular posiciones de satélites.

También puede mostrar footprints como objetos MultiLine.

## Paquetes duplicados

Incluye mecanismos para detectar duplicados, especialmente importantes al seguir estaciones móviles.

## Beaconing

Las fuentes de posición pueden incluir:

- configuración fija,
- GPS,
- NMEA desde un puerto de radio,
- GPS USB,
- GPS Bluetooth.

## GPS

Soporta datos:

```text
NMEA
```

de receptores externos.

## Windows, Linux y Wine

APRSIS32 es una aplicación Windows.

También puede ejecutarse en Linux mediante:

```text
Wine
```

o dentro de una máquina virtual Windows.

## Estado actual del proyecto

APRSISCE/32 tiene una larga historia y muchas funciones avanzadas se desarrollaron hace años.

Sin embargo, el proyecto no está completamente muerto.

La página de descargas fue actualizada en 2025 y ofrece una versión funcional para Windows moderno.

## Compatibilidad con el protocolo APRS

APRSISCE/32 soporta, entre otras cosas:

- posiciones,
- posiciones comprimidas,
- símbolos y overlays,
- comentarios,
- mensajes,
- ACK,
- estados,
- telemetría,
- meteorología,
- objetos,
- items,
- frecuencias,
- APRS Queries,
- objetos NWS,
- MultiLine,
- áreas,
- Direction Finding,
- Item-In-Message.

Esto demuestra que APRS es mucho más amplio que un simple sistema de posición.

## ¿Para quién es APRSISCE/32?

Para operadores que quieren:

- observar tráfico APRS local,
- usar mapas,
- conectar su radio,
- intercambiar mensajes,
- ejecutar un iGate,
- crear objetos,
- crear áreas y rutas,
- experimentar con Direction Finding,
- analizar meteorología y alertas,
- trabajar con varios puertos RF,
- explorar funciones avanzadas de APRS.

## Resumen

APRSISCE/32 es uno de los clientes APRS clásicos más completos.

Combina funciones de operador, mapa e infraestructura.

Soporta funciones comunes y otras mucho menos habituales:

- objetos de área,
- MultiLine,
- Direction Finding,
- QRU,
- APRS Queries,
- NWS-WARN,
- shapefiles,
- telemetría,
- meteorología,
- satélites.

## Documentación

Fuentes principales:

- sitio del proyecto: https://aprsisce.wikidot.com/
- descargas: https://aprsisce.wikidot.com/downloads
- documentación de funciones: https://aprsisce.wikidot.com/en-contexthelp
- hardware y software: https://aprsisce.wikidot.com/doc:compatible-hardware-software
- filtros APRS-IS: https://aprsisce.wikidot.com/aprs-is-filters
- NWS: https://aprsisce.wikidot.com/en-nws
- grupo de soporte: https://groups.io/g/APRSISCE

Material práctico en polaco:

- búsqueda APRSIS32: https://hamspirit.pl/SQ9MDD/?s=aprsis
- primeros pasos y radio: https://hamspirit.pl/SQ9MDD/?p=1239
- digipeater temporal: https://hamspirit.pl/SQ9MDD/?p=1202
- objetos: https://hamspirit.pl/SQ9MDD/?p=1140
- objetos de área: https://hamspirit.pl/SQ9MDD/?p=1170
- QRU Info-kiosk: https://hamspirit.pl/SQ9MDD/?p=1384
- Direction Finding y triangulación: https://hamspirit.pl/SQ9MDD/?p=1090
