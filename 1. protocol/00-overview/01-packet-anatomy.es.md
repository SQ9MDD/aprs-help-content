---
title: Estructura de un paquete APRS
description: Una breve introducción a las partes de un paquete APRS.
---

Un paquete APRS incluye una estación de origen, un identificador de destino, una ruta opcional y un campo de información.

```text
SOURCE>DESTINATION,PATH:information field
```

El campo de información indica al software receptor si el paquete contiene una posición, un mensaje, un objeto, telemetría u otro tipo de datos APRS.

## Por qué es importante

Leer las partes por separado es la forma más rápida de diagnosticar un paquete que no se muestra como se esperaba.
