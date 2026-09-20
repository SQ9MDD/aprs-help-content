---

title: Cómo añadir contenido a APRS.help
description: Cómo preparar un nuevo artículo y enviarlo al repositorio de APRS.help.
template: doc
tableOfContents: true
---------------------

APRS.help es un proyecto abierto y cualquier persona puede ayudar a ampliar su base de conocimientos.

El contenido mostrado en la página se almacena en el repositorio público de GitHub:

`https://github.com/SQ9MDD/aprs-help-content`

APRS.help sincroniza su contenido con este repositorio, por lo que añadir o modificar un artículo consiste en editar los archivos Markdown correspondientes y enviar los cambios mediante un **Pull Request**.

## Versiones de idioma obligatorias

Cada nuevo artículo debe prepararse simultáneamente en cuatro idiomas:

* polaco
* inglés
* español
* alemán

Para cada artículo deben crearse cuatro archivos correspondientes:

```text
nombre-del-articulo.pl.md
nombre-del-articulo.en.md
nombre-del-articulo.es.md
nombre-del-articulo.de.md
```

Ejemplo:

```text
digipeater.pl.md
digipeater.en.md
digipeater.es.md
digipeater.de.md
```

Los nombres de los archivos deben ser idénticos y diferenciarse únicamente por el código de idioma.

Un Pull Request que incluya un nuevo artículo debe contener las cuatro versiones lingüísticas.

Si falta cualquiera de las versiones obligatorias, el artículo se considera incompleto y no debería integrarse en el repositorio principal.

## Formato de los documentos

Los documentos deben prepararse en formato **Markdown** (`.md`).

Cada archivo debe comenzar con la información básica que describe el documento:

```yaml
---
title: Título del artículo
description: Breve descripción del contenido del artículo.
template: doc
tableOfContents: true
---
```

Los campos `title` y `description` deben traducirse en cada versión lingüística. El título de la página se muestra únicamente a partir del campo `title`, así que no añadas un segundo encabezado de nivel 1 (`# Título`) al principio del contenido del artículo.

Ejemplo de una versión en español:

```markdown
---
title: Digipeater APRS
description: Información básica sobre el funcionamiento de los digipeaters en la red APRS.
template: doc
tableOfContents: true
---

Un digipeater es una estación que retransmite paquetes APRS recibidos por radio.

## Cómo funciona un digipeater

Contenido del artículo...
```

Los archivos correspondientes en otros idiomas deben mantener la misma estructura del documento.

## Dónde colocar los archivos

Las cuatro versiones lingüísticas deben colocarse en el mismo directorio correspondiente al tema del artículo.

Ejemplo:

```text
4. sprzęt/
  modem-kiss.pl.md
  modem-kiss.en.md
  modem-kiss.es.md
  modem-kiss.de.md
```

Si no estás seguro de qué sección es la más adecuada, elige la temática más cercana. La ubicación puede corregirse durante la revisión del Pull Request.

## Imágenes, diagramas y capturas de pantalla

Guarda las imágenes específicas del artículo en un directorio `_img/` junto a los archivos Markdown. Los directorios cuyos nombres empiezan por `_` no se muestran en el menú de documentación.

```text
4. sprzęt/
  modem-kiss.pl.md
  modem-kiss.en.md
  modem-kiss.es.md
  modem-kiss.de.md
  _img/
    modem-kiss-conexion.png
```

Usa una ruta Markdown relativa dentro del artículo:

```markdown
![Diagrama de conexión del módem KISS](./_img/modem-kiss-conexion.png)
```

El mismo archivo de imagen puede utilizarse en todas las versiones lingüísticas, pero el texto alternativo entre corchetes debe traducirse. Usa nombres de archivo descriptivos; para fotos y capturas de pantalla utiliza PNG, JPEG o WebP, y para diagramas sencillos SVG siempre que sea posible.

## Cómo enviar nuevo contenido

El método recomendado es el flujo de trabajo estándar de GitHub:

1. Crea un fork del repositorio `SQ9MDD/aprs-help-content`.
2. Prepara el artículo en formato Markdown.
3. Crea las cuatro versiones obligatorias: `pl`, `en`, `es` y `de`.
4. Coloca todos los archivos en el directorio correspondiente.
5. Confirma los cambios en tu repositorio.
6. Crea un **Pull Request** hacia el repositorio principal de APRS.help.
7. Describe brevemente el contenido añadido o modificado.

Después de la revisión y aprobación, el Pull Request se integrará en el repositorio principal.

El servidor APRS.help sincroniza periódicamente su contenido con el repositorio, por lo que los cambios aceptados aparecerán posteriormente en la página.

## Modificación de artículos existentes

Un Pull Request también puede modificar contenido existente, por ejemplo:

* corregir un error técnico,
* aclarar una descripción,
* añadir un ejemplo,
* corregir una errata,
* actualizar información obsoleta,
* ampliar un artículo.

Si el cambio afecta al significado del contenido, deben actualizarse todas las versiones lingüísticas del documento.

## Algunas reglas

Al preparar contenido:

* escribe de forma clara y objetiva,
* divide los temas más amplios mediante encabezados,
* utiliza bloques de código para tramas APRS, configuraciones y comandos,
* mantén una estructura similar en todas las versiones lingüísticas,
* traduce también los títulos, descripciones y encabezados,
* cuando describas el comportamiento del protocolo, indica una fuente o documentación cuando sea posible,
* evita copiar grandes fragmentos de materiales de terceros,
* en temas discutibles, separa los requisitos del protocolo de las buenas prácticas y de la experiencia personal.

APRS.help pretende ser una fuente compartida, práctica y técnica de conocimientos sobre APRS, desarrollada por la comunidad de radioaficionados.

## ¿No conoces GitHub?

No pasa nada.

Para cambios sencillos puedes utilizar el editor de GitHub directamente desde el navegador. No es necesario instalar Git ni software adicional.

Lo más importante es preparar contenido útil y todas las versiones lingüísticas obligatorias. El mecanismo de Pull Request permite revisar conjuntamente los cambios antes de publicarlos y corregirlos si es necesario.
