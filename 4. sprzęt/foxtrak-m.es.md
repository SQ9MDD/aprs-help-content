---
title: "FoxTrak-M"
slug: "foxtrak-m"
type: "hardware"
category: "tracker"
manufacturer: "Fox Delta"
status: "legacy"
protocols:
  - "APRS"
  - "AX.25"
  - "NMEA 0183"
tags:
  - "tracker"
  - "GPS"
  - "1200-baud"
  - "Bell-202"
  - "PIC"
source:
  - "https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf"
---

## 1. Información básica

**FoxTrak-M** es un módulo compacto de tracker APRS de Fox Delta. Fue diseñado como un módulo pequeño para integrarlo en un dispositivo o proyecto propio, en lugar de como un equipo completo en una carcasa independiente.

El diseño se basa en microcontroladores **PIC16F84A o PIC16F628A**. La placa mide aproximadamente **7 × 4 cm** y utiliza conectores de pines de 0,1 pulgadas.

FoxTrak-M es una variante del tracker FoxTrak anterior. La principal diferencia de diseño es la sustitución de los conectores D-Sub por conectores de pines, lo que facilita la integración del módulo en otros equipos.

## 2. Uso previsto

FoxTrak-M está destinado principalmente a:

- transmisión automática de la posición de la estación mediante APRS,
- construcción de un tracker GPS móvil,
- integración de un tracker APRS como módulo en un equipo propio,
- funcionamiento con el terminal FoxView-2,
- experimentación con firmware propio para el microcontrolador PIC.

El dispositivo es un tracker de transmisión. No es un terminal APRS completo y no decodifica tramas APRS recibidas.

## 3. Funcionamiento

El tracker recibe del GPS datos de posición en formato **NMEA**, en particular sentencias:

```text
$GPRMC
```

A partir de esos datos genera una señal de packet radio de **1200 bps**, que puede aplicarse directamente a la entrada de micrófono del transmisor.

Para el funcionamiento básico no se requiere un TNC externo.

Los beacons automáticos se transmiten únicamente cuando el dispositivo recibe datos de posición válidos del GPS.

## 4. APRS y funciones compatibles

FoxTrak-M realiza la función básica de un tracker APRS, es decir, la transmisión periódica de informes de posición.

El firmware de DK7IN admite, entre otras funciones:

- intervalo fijo de beacons,
- cambio del intervalo de beacons según la velocidad,
- beacon adicional tras un cambio importante de dirección,
- TX Delay configurable,
- configuración del indicativo y otros parámetros APRS,
- generación de tonos de prueba para ajustar el trayecto de audio.

El dispositivo no recibe ni interpreta tráfico APRS del canal de radio.

## 5. Smart Beaconing

FoxTrak-M implementa una forma temprana de beaconing adaptativo. La frecuencia de envío de la posición puede cambiar en función de la velocidad.

Hay dos conjuntos de parámetros disponibles, seleccionables mediante un interruptor.

| Velocidad | Perfil rápido | Perfil lento |
|---|---:|---:|
| < 4 km/h | 25 min | 30 min |
| > 4 km/h | 4 min | 5 min |
| > 11 km/h | 120 s | 160 s |
| > 24 km/h | 60 s | 80 s |
| > 50 km/h | 30 s | 40 s |
| > 100 km/h | 20 s | 30 s |
| > 150 km/h | 10 s | 20 s |

Se tiene en cuenta la velocidad máxima alcanzada desde el envío del beacon anterior.

Si la velocidad supera **15 km/h**, el dispositivo también puede enviar un beacon tras un cambio significativo de dirección:

- más de **40°** en el perfil rápido,
- más de **60°** en el perfil lento.

Si la transmisión anterior se produjo hace menos de 10 segundos, el siguiente beacon se retrasará.

## 6. Interfaz GPS y configuración

La misma interfaz serie se utiliza para:

- recibir datos del receptor GPS,
- configurar parámetros almacenados en la memoria del PIC.

La documentación describe utilidades de configuración para:

- DOS,
- Windows,
- Linux.

También era posible utilizar el programa de configuración de TinyTrak.

Una versión de ejemplo del firmware se identificaba como:

```text
DK7IN V.1.6
```

El intervalo fijo de beacons puede ajustarse en pasos de 10 segundos, y el TX Delay con una resolución aproximada de 6,6 ms.

## 7. Trayecto de radio

FoxTrak-M genera modulación AFSK compatible con el sistema **Bell 202** utilizado en packet radio.

Las frecuencias nominales de los tonos son:

- **1200 Hz**
- **2200 Hz**

Para ajuste y diagnóstico, el dispositivo puede generar tonos de prueba.

Ejemplos de comandos de terminal:

```text
ESC T 0
```

genera un tono de 1200 Hz.

```text
ESC T 1
```

genera un tono de 2200 Hz.

```text
ESC T M
```

genera una señal mixta de 1200/2200 Hz.

La comunicación de configuración descrita en el manual utiliza:

```text
4800 baud, 8N1
```

## 8. Conexión con la radio

El conector de radio proporciona, entre otras, las siguientes señales:

| Señal | Función |
|---|---|
| Audio In | audio z odbiornika do trackera |
| CD | Carrier Detect, jeśli dostępny |
| GND | masa |
| SW1 | wejście przycisku natychmiastowej transmisji |
| Audio Out | audio trackera do wejścia mikrofonowego radia |
| +5V | zasilanie modułu |

El módulo permite ajustar el nivel de audio hacia y desde la radio.

También existe la opción de adaptar el control PTT para algunos equipos portátiles mediante una resistencia de 2,2 kΩ.

## 9. Conector GPS / PC

El segundo conector de 8 pines y 0,1 pulgadas se utiliza para comunicarse con el GPS o con un ordenador.

La documentación enumera las siguientes señales:

| Señal | Función |
|---|---|
| GND | masa |
| S0 | dane szeregowe OUT |
| SI | dane szeregowe IN |
| PI | PTT IN |
| PO | PTT OUT |
| A | niewykorzystywane w FoxTrak-M |
| B | niewykorzystywane w FoxTrak-M |

## 10. Indicadores y controles

FoxTrak-M dispone de LED que indican el estado del dispositivo.

**CD**

Se enciende cuando se detecta la transmisión de otra estación.

**GPS**

Se enciende cuando hay datos de posición GPS válidos. Parpadea cuando se reciben datos GPS pero la posición no es válida.

**PTT**

Se enciende mientras el transmisor está activado.

Al arrancar, los LED realizan una secuencia de inicialización, tras la cual puede transmitirse un beacon.

## 11. Alimentación

FoxTrak-M requiere alimentación externa:

```text
+5 V DC
```

El módulo no dispone de su propio regulador de 5 V.

Tampoco proporciona alimentación al receptor GPS, por lo que el GPS debe tener su propia fuente de alimentación.

## 12. Firmware

El firmware principal descrito en la documentación fue desarrollado por **DK7IN**.

El diseño de hardware permite sustituir el firmware y, si es necesario, reemplazar el PIC16F84A por un PIC16F628A.

FoxTrak-M fue diseñado no solo como un tracker listo para usar, sino también como una plataforma para experimentar con software APRS propio.

## 13. Limitaciones

Las principales limitaciones indicadas en la documentación son:

- no decodifica tramas APRS recibidas,
- no ofrece funciones de TNC completo,
- el dispositivo se centra en transmitir la posición GPS,
- requiere alimentación externa de 5 V,
- el GPS no se alimenta desde el módulo,
- el diseño se basa en microcontroladores PIC antiguos.

## 14. Aplicaciones

Aplicaciones típicas de FoxTrak-M:

- tracker APRS para vehículo,
- tracker integrado en otro dispositivo,
- estación móvil GPS/APRS,
- plataforma experimental APRS,
- proyecto educativo relacionado con AFSK, AX.25 y APRS.

## 15. Clasificación

| Característica | Valor |
|---|---|
| Tipo | hardware |
| Clase | tracker APRS |
| Dirección | TX |
| Posición | GPS |
| Datos GPS | NMEA, GPRMC |
| Capa de radio | AFSK 1200 bps |
| Modulación | Bell 202 |
| Microcontrolador | PIC16F84A / PIC16F628A |
| Alimentación | 5 V DC |
| Interfaz de radio | audio + PTT |
| Recepción APRS | no |
| Smart Beaconing | sí |
| Configuración desde PC | sí |

## 16. Importancia histórica

FoxTrak-M representa una clase de trackers APRS de hardware sencillos que permitían construir una estación móvil de posicionamiento sin utilizar un TNC completo ni un ordenador.

Su diseño muestra claramente el modelo clásico de un tracker APRS:

```text
GPS
  ↓
NMEA
  ↓
mikrokontroler
  ↓
APRS / AX.25
  ↓
AFSK 1200 bps
  ↓
radio
```

## 17. Fuentes

La base de esta descripción es la documentación del fabricante:

**Fox Delta, FD - FoxTrak-M, Technical information: Compact PIC 16F84A/628A APRS Tracker Module**

https://www.foxdelta.com/projects/ftmodule/foxtrak-m.pdf

El documento está fechado el 8 de junio de 2008.
