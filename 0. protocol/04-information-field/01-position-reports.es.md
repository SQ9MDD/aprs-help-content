---
title: "Informes de posición"
---


Los informes de posición son uno de los tipos de datos fundamentales de APRS. Transmiten la ubicación de una estación y, opcionalmente, información sobre su movimiento, características o estado. La posición puede proceder de un receptor GNSS, introducirse manualmente o programarse como ubicación fija. Tanto las estaciones móviles como las fijas transmiten informes de posición.


## Formatos de los informes de posición


APRS define varias formas de transmitir una posición:

- **Uncompressed Position**: coordenadas geográficas representadas como texto legible.

- **Compressed Position**: coordenadas codificadas en Base91 para reducir la longitud del informe.

- **Mic-E**: formato que codifica parte de la información de posición en la dirección de destino AX.25 y el resto en el campo de información.


La especificación también describe posiciones transmitidas mediante sentencias NMEA e informes que utilizan un localizador Maidenhead. Estos formatos tienen identificadores y reglas de interpretación propios.


## Identificadores de los informes de posición


En los informes clásicos y comprimidos, el primer carácter del campo de información, denominado Data Type Identifier (DTI), identifica el tipo de informe:

| DTI | Función |
|---|---|
| `!` | Posición sin marca de tiempo; la estación no declara capacidad de mensajería APRS. |
| `=` | Posición sin marca de tiempo; la estación declara capacidad de mensajería APRS. |
| `/` | Posición con marca de tiempo; la estación no declara capacidad de mensajería APRS. |
| `@` | Posición con marca de tiempo; la estación declara capacidad de mensajería APRS. |


El DTI no determina si las coordenadas están comprimidas. El formato se reconoce por la estructura de los datos siguientes. Mic-E utiliza identificadores propios: `'` y `` ` ``.


## Estructura del informe


Según el formato, un informe de posición puede contener los siguientes elementos:

| Elemento | Función |
|---|---|
| DTI | Identificador del tipo de datos. |
| Timestamp | Marca de tiempo opcional. |
| Position | Coordenadas geográficas. |
| Symbol | Identificación del símbolo gráfico de la estación. |
| Position extension | Extensión de posición opcional. |
| Comment | Comentario e información adicional. |


La presencia, el orden y la codificación de los campos dependen del formato del informe.


### Ejemplo de informe clásico


```text
SQ9MDD>APRS:!5003.50N/01956.00E-
```

En el ejemplo, `!` indica un informe sin marca de tiempo, `5003.50N` y `01956.00E` son las coordenadas, y `/` y `-` especifican la tabla y el código del símbolo.


## Información adicional


Los informes de posición también pueden contener datos complementarios. La especificación contempla, entre otros:

- **Course/Speed**: rumbo y velocidad.

- **PHG**: potencia del transmisor, altura de la antena, ganancia y directividad.

- **RNG**: alcance declarado de la estación.

- **BRG/NRQ**: información utilizada en radiogoniometría.

- **Altitude**: altitud, incluida la extensión `/A=`.


La disponibilidad y la codificación dependen del formato; no todas las extensiones son válidas en todos los formatos.


## Precisión y ambigüedad de la posición


La resolución de las coordenadas no equivale a la precisión real de la medición. APRS define **Position Ambiguity**, que permite indicar deliberadamente un área en lugar de un punto exacto. La extensión **DAO** permite transmitir una precisión adicional de las coordenadas.

Estos mecanismos tienen finalidades distintas y deben interpretarse por separado.


## Posición de la estación frente a objetos y elementos


Un informe de posición ordinario describe la estación identificada por la dirección de origen de la trama. APRS también admite **Objects** e **Items**, que permiten comunicar la posición de un objeto o elemento con nombre, independientemente del indicativo de la estación transmisora.

Los objetos y elementos utilizan mecanismos de codificación de posición APRS, pero tienen formatos y reglas de identificación propios. Se documentan por separado.


## Documentación detallada


Otros artículos tratan las posiciones clásicas, marcas de tiempo, Position Ambiguity, extensiones de posición, altitud y precisión, símbolos, comentarios y formatos comprimidos, incluido Mic-E.


## Referencia


[APRS Protocol Reference, Version 1.0.1](https://www.aprs.org/doc/APRS101.PDF).