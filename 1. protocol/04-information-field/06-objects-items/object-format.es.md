---
title: Objetos APRS
description: Cómo APRS representa un objeto con nombre en el mapa.
---

Un objeto APRS es un informe de mapa con nombre que una estación transmite en nombre de otra cosa: un repetidor, sensor meteorológico, punto de un evento, vehículo u otro recurso. Emplea la misma información de posición que un informe de posición normal, precedida por un nombre de nueve caracteres.

## Forma básica

```text
;OBJECT___*DDHHMMzDDMM.hhN/DDDMM.hhW$comentario
```

- `;` identifica un informe de objeto.
- `OBJECT___` es un nombre fijo de nueve caracteres; los nombres más cortos se completan con espacios.
- `*` marca un objeto activo.
- Los campos de posición restantes siguen las reglas habituales de APRS.

Los nombres distinguen mayúsculas y minúsculas. Elige un nombre claro y estable para que los receptores reconozcan los informes posteriores como actualizaciones del mismo objeto.

## Actualizar y eliminar

Envía un informe nuevo con el mismo nombre para cambiar su posición o comentario. Se puede marcar un objeto como eliminado sustituyendo el marcador activo por `_`. Los receptores deben dejar de mostrarlo y conservar el informe como historial cuando su software lo permita.

Solo la estación responsable debe eliminar un objeto. Así se evita que informes en conflicto hagan aparecer y desaparecer un objeto activo.

## ¿Objeto o elemento?

Usa un **objeto** cuando el nombre tiene exactamente nueve caracteres y resulta útil una marca de tiempo. Un **elemento** APRS tiene un nombre de longitud variable y no incluye una marca de tiempo, por lo que encaja mejor con informes sencillos y de corta duración.

## Fuentes

- [PROTOCOL.TXT — formato, sustitución y eliminación de objetos](/APRS-SPEC/PROTOCOL.TXT)
- [Objects.txt — comportamiento y presentación de objetos](/APRS-SPEC/Objects.txt)
