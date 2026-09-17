---
title: Objetos de frecuencia
description: Objetos APRS que publican frecuencias locales de radio.
---

Los objetos de frecuencia publican recursos de radio locales útiles en el mapa APRS: un repetidor de voz recomendado, un nodo EchoLink o IRLP, una puerta de enlace Winlink o una red. Son objetos APRS normales con un nombre o comentario de frecuencia estructurado.

## Mantén la información local

Un objeto de frecuencia solo es útil donde un viajero pueda utilizarlo. Transmítelo con moderación y según el plan local de operación RF; los relés innecesarios aumentan la congestión de un canal compartido.

## Frecuencia en un comentario

La forma legible habitual incluye la frecuencia y `MHz`, con tono, desplazamiento y alcance opcionales:

```text
146.805MHz T107 R25m
```

`T107` describe un tono de 107.x Hz y `R25m` un alcance nominal de 25 millas. La convención completa también define CTCSS/DCS, modulación, desplazamiento y kilómetros. Es una convención para clientes APRS compatibles, no un sustituto del plan local de frecuencias.

## Frecuencia como nombre del objeto

Para un repetidor, la frecuencia puede aparecer en el nombre del objeto y unos caracteres adicionales le dan unicidad. El comentario lleva entonces tono, desplazamiento, alcance y una breve descripción.

```text
;147.105md*111111zDDMM.hhN/DDDMM.hhWrT107 R25m Repetidor local
```

Es solo un esquema ilustrativo. Las coordenadas, el símbolo, el sufijo del nombre y el intervalo reales deben elegirse para la estación correspondiente.

## Servicios especiales

EchoLink, IRLP, WiRES y Winlink pueden usar el mismo mecanismo de objetos. Sus convenciones de nombre ayudan a los clientes móviles a identificar el servicio, pero la disponibilidad y el estado deben proceder del operador o de una fuente local fiable.

## Fuentes

- [freqspec.txt — formatos de frecuencia y convenciones de objetos de servicio](/APRS-SPEC/freqspec.txt)
- [PROTOCOL.TXT — trama de objeto APRS](/APRS-SPEC/PROTOCOL.TXT)
