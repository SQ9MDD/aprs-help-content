---
title: "01. Informes meteorológicos APRS"
description: "Informes WX: historia, formatos, campos obligatorios, mediciones no disponibles, metodología de medición y calidad de los datos CWOP/MADIS."
---

APRS permite transmitir mediciones meteorológicas actuales directamente por radio y a través de APRS-IS. Un informe WX puede incluir la posición de la estación, datos de viento, temperatura, precipitaciones, humedad y presión atmosférica. El protocolo también contempla parámetros adicionales y formatos utilizados por equipos antiguos.

Los datos meteorológicos no tienen un identificador DTI único y exclusivo. Pueden formar parte de un informe de posición normal, de un objeto APRS o de un informe independiente sin posición. La interpretación depende de la combinación del DTI, la estructura del informe y el símbolo de la estación.

## Evolución de los informes WX

APRS ya transmitía mediciones meteorológicas mucho antes de que aparecieran los servicios meteorológicos modernos en Internet destinados a los radioaficionados. Las primeras soluciones funcionaban, entre otros equipos, con las estaciones Peet Bros ULTIMETER y Davis. También era posible el funcionamiento remoto: una estación meteorológica, un TNC y un transceptor podían transmitir las lecturas sin necesidad de mantener un ordenador encendido continuamente. Su utilidad no se limitaba a observar el tiempo local, sino que también incluía el intercambio de informes en redes de observadores como SKYWARN.

La evolución del formato muestra cómo APRS se adaptó a los equipos disponibles y a nuevas necesidades:

- **Década de 1990** - APRS admitía datos procedentes de distintos modelos de estaciones meteorológicas, incluidos los formatos sin procesar de los fabricantes. El documento `WX.TXT` registra un cambio de formato introducido en APRSdos 793 en junio de 1997 que no era retrocompatible con la forma anterior de presentar los datos.
- **Año 2000** - *APRS Protocol Reference 1.0.1* organizó los informes meteorológicos en tres formatos: sin procesar, sin posición y completo, que incluye tanto la posición como las mediciones.
- **2001 y aclaraciones posteriores de APRS 1.1** - Se precisaron, entre otras cuestiones, la diferencia entre la ausencia de una medición y el valor cero, el significado de los contadores de precipitación y la ambigüedad del campo `s`, que puede indicar la velocidad del viento o la nieve caída según la variante del informe.
- **Desde julio de 2001** - Los datos de CWOP, surgido del entorno de los radioaficionados y de APRSWXNET, alimentan el sistema MADIS de NOAA. De este modo, los informes de estaciones meteorológicas de aficionados comenzaron a incorporarse a un conjunto más amplio de observaciones meteorológicas.
- **2006** - Se documentó el uso de APRS para informar sobre niveles de agua y riesgos de inundación. Aparecieron símbolos para estaciones hidrométricas y una propuesta para transportar mediciones adicionales dentro de los campos meteorológicos.
- **Marzo de 2011** - Tras el accidente nuclear de Fukushima Daiichi, Bob Bruninga propuso ampliar el formato WX para incluir mediciones de radiación. Un documento del 24 de marzo de 2011 describe el campo `Xxxx` y una mayor unificación de la identificación de sensores y situaciones de peligro.

La historia del campo de radiación muestra una característica importante de APRS: el formato WX comenzó a considerarse también un medio para transportar mediciones ambientales, más allá de la meteorología convencional. Sin embargo, hay que distinguir los campos de la especificación básica de las propuestas posteriores. Que una ampliación esté documentada no significa que todas las aplicaciones la implementen.

## Tipos de informes meteorológicos

*APRS Protocol Reference 1.0.1* distingue tres formatos:

| Formato | Características |
| --- | --- |
| **Complete Weather Report** | Datos meteorológicos y posición en un único paquete. Es el formato recomendado para nuevas implementaciones. |
| **Positionless Weather Report** | Datos meteorológicos sin coordenadas. El receptor debe conocer previamente la posición de la estación mediante otro paquete. |
| **Raw Weather Report** | Datos sin procesar en el formato de un instrumento meteorológico concreto. Es una solución histórica, no recomendada para nuevos transmisores. |

Unir la posición y las mediciones actuales en un solo paquete reduce la dependencia de transmisiones anteriores. Esto es especialmente importante en el canal de radio, donde no está garantizada la recepción de todas las tramas.

## Informe meteorológico completo

En su forma básica, un Complete Weather Report es un informe de posición con un símbolo meteorológico seguido de datos WX. Se puede utilizar cualquiera de los cuatro DTI estándar para posiciones:

| DTI | Marca de tiempo | Compatibilidad declarada con mensajes APRS |
| --- | --- | --- |
| `!` | no | no |
| `=` | no | sí |
| `/` | sí | no |
| `@` | sí | sí |

### ¿Qué elementos son obligatorios en el informe completo?

En un Complete Weather Report con posición **sin comprimir**, son obligatorios la estructura de posición definida por el DTI elegido, el símbolo meteorológico, el campo de siete caracteres para dirección y velocidad del viento `ddd/sss` y el campo de temperatura `txxx`. Esto significa que **los campos deben estar presentes, pero no es obligatorio disponer de todos los sensores**. Si falta una medición, se conserva la posición del campo y se rellenan sus caracteres con puntos o espacios en la cantidad adecuada.

| Elemento | ¿Obligatorio en un informe completo sin comprimir? | Si no hay lectura |
| --- | --- | --- |
| DTI y posición válida | Sí | La posición define esta variante; no puede sustituirse por puntos WX. |
| Símbolo de estación meteorológica | Sí | Normalmente `/_` o `\_`; los dos caracteres del símbolo son importantes. |
| `ddd/sss` - dirección y velocidad del viento | Sí | `.../...` o tres espacios a cada lado de la barra. |
| `txxx` - temperatura | Sí | `t...` o `t` seguido de tres espacios. |
| `gxxx` - racha de viento | No, según aclaraciones posteriores del formato completo | Se puede omitir o utilizar `g...`. |
| Precipitaciones, humedad, presión y demás campos | No | El campo se omite o se sustituyen sus dígitos por puntos/espacios. |

Es importante distinguir el tratamiento de las rachas: en un **informe sin posición**, `gxxx` pertenece al conjunto inicial obligatorio, mientras que las aclaraciones posteriores del formato completo exigen principalmente `ddd/sss` y `txxx`. En las tablas y los ejemplos de APRS101 también aparece con frecuencia `gxxx` cuando hay datos de rachas disponibles.

Ejemplo de una trama completa en formato monitor:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_220/004g005t068r000p015P012h72b10132
```

La parte situada después de los dos puntos constituye el campo Information. Sus elementos principales son:

```text
! | 5003.50N | / | 01956.75E | _ | 220/004 | g005t068r000p015P012h72b10132
```

| Elemento | Significado |
| --- | --- |
| `!` | DTI de un informe de posición sin marca de tiempo. |
| `5003.50N` | Latitud. |
| `/` | Identificador de la tabla primaria de símbolos. |
| `01956.75E` | Longitud. |
| `_` | Código del símbolo de estación meteorológica. |
| `220/004` | Dirección y velocidad del viento. |
| `g005...` | Resto de datos meteorológicos. |

Los caracteres `/` y `_` de la parte correspondiente a la posición forman conjuntamente el símbolo `/_`. No debe confundirse el código de símbolo `_` con el DTI `_`, que aparece al principio de un informe independiente sin posición.

### Campos de medición y unidades

El formato WX clásico utiliza campos cortos de longitud fija. En un informe completo, la dirección y la velocidad del viento se codifican sin prefijos alfabéticos, mediante la extensión de siete bytes `ddd/sss`.

| Campo | Medición | Unidad y codificación |
| --- | --- | --- |
| `ddd/sss` | Dirección y velocidad media del viento | Grados y mph; velocidad promediada durante 1 minuto. **Campo obligatorio.** |
| `gxxx` | Racha de viento | mph; velocidad máxima de los últimos 5 minutos. Opcional en el informe completo. |
| `txxx` | Temperatura | °F; admite valores negativos, p. ej. `t-07`. **Campo obligatorio.** |
| `rxxx` | Precipitación de la última hora | Centésimas de pulgada. |
| `pxxx` | Precipitación de las últimas 24 horas | Centésimas de pulgada; ventana móvil de 24 horas. |
| `Pxxx` | Precipitación desde medianoche | Centésimas de pulgada. |
| `hxx` | Humedad relativa | Porcentaje; `h00` significa 100 %. |
| `bxxxxx` | Presión atmosférica | Décimas de hPa (mbar). |

Los valores se transmiten en las unidades definidas por el protocolo, con independencia de las unidades utilizadas por la aplicación para mostrarlos. El receptor puede presentar la temperatura en °C, el viento en km/h y las precipitaciones en milímetros, pero eso no modifica la codificación del informe.

En el ejemplo anterior:

| Datos | Valor interpretado del informe |
| --- | --- |
| Viento | 220°, 4 mph |
| Racha | 5 mph |
| Temperatura | 68°F (20°C) |
| Precipitación de la última hora | 0 |
| Precipitación de las últimas 24 horas | 0,15 pulgadas |
| Precipitación desde medianoche | 0,12 pulgadas |
| Humedad | 72 % |
| Presión | 1013,2 hPa |

Los campos `r`, `p` y `P` describen intervalos de tiempo diferentes. En particular, `p` no representa la precipitación del día natural anterior, ni `P` es intercambiable con `p`. La forma de calcular estos valores y las diferencias entre las mediciones de viento APRS y CWOP se explican en el apartado sobre metodología de medición.

### Mediciones no disponibles: puntos, espacios y campos omitidos

APRS distingue entre un **valor realmente igual a cero** y una **medición no disponible**. Si el informe contiene un campo definido, pero la estación carece del sensor correspondiente o la lectura está temporalmente indisponible, los caracteres numéricos pueden sustituirse por puntos (`.`) o espacios. La longitud del campo no cambia. Aclaraciones posteriores del autor del protocolo recomiendan los puntos porque facilitan la lectura.

| Situación | Ejemplo | Interpretación |
| --- | --- | --- |
| No hay anemómetro | `.../...` | Se desconocen la dirección y la velocidad del viento. |
| Dirección no disponible, velocidad conocida | `.../004` | Se conoce la velocidad de 4 mph, pero no la dirección. |
| No hay termómetro | `t...` | Temperatura desconocida. |
| No hay datos de rachas, pero el campo está presente | `g...` | Se desconocen las rachas; no equivale a 0 mph. |
| No hay lectura de presión, pero el campo está presente | `b.....` | Presión desconocida. |
| No hay lectura de pluviómetro, pero el campo está presente | `r...` | No hay datos de precipitación de la última hora. |
| Se ha medido ausencia de lluvia | `r000` | El resultado de la medición es exactamente 0,00 pulgadas. |

**No es necesario transmitir los campos opcionales vacíos.** Tanto `r...` como omitir `r` indican que no se dispone de esa lectura, mientras que `r000` es un resultado de medición concreto. Sin embargo, no se pueden eliminar los campos obligatorios solo porque falte el sensor correspondiente.

Ejemplo de informe mínimo sin comprimir de una estación equipada únicamente con un pluviómetro:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_.../...t...r012
```

Después del símbolo `_` aparecen los campos obligatorios `.../...` y `t...`. Se omite el campo opcional `g` porque la estación no mide las rachas. La única medición disponible es `r012`: durante la última hora han caído 0,12 pulgadas de lluvia. Es una situación válida: **el formato exige la presencia de determinados campos, pero no obliga a instalar todos los sensores**.

En cambio, la siguiente trama:

```text
SQ9MDD>APRS:!5003.50N/01956.75E_r012
```

no es equivalente. Omite los campos exigidos en este tipo de informe sin comprimir y no debería generarse como un Complete Weather Report válido.

Después de los elementos obligatorios, los parámetros adicionales no tienen que estar todos presentes ni aparecer siempre en el mismo orden. El analizador debe reconocer sus identificadores y longitudes fijas, en lugar de esperar la secuencia completa de todas las mediciones posibles.

## Posición, tiempo y objetos

Un informe completo puede incluir una posición sin comprimir o comprimida. En la variante sin comprimir, la extensión `ddd/sss` aparece inmediatamente después del símbolo meteorológico. En la variante comprimida, los datos del viento ocupan los campos correspondientes de la posición comprimida, por lo que no debe añadirse de nuevo la extensión de siete bytes `ddd/sss`. Los ejemplos de este artículo donde los valores del viento se sustituyen por puntos se refieren al formato sin comprimir, no a los bytes de compresión.

Un informe con marca de tiempo puede tener este aspecto:

```text
SQ9MDD>APRS:@282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

El DTI `@` indica una variante de posición con marca de tiempo que declara compatibilidad con los mensajes APRS. `282000z` significa el día 28 del mes a las 20:00 UTC.

También se pueden asociar las mediciones a un objeto APRS, por ejemplo, cuando una estación publica los datos de un sensor remoto:

```text
SQ9MDD>APRS:;WX-KRAKOW*282000z5003.50N/01956.75E_220/004g005t068h72b10132
```

En este caso, el informe comienza con el DTI `;` y el nombre `WX-KRAKOW` identifica el objeto. Las coordenadas y los datos meteorológicos describen el objeto, no necesariamente la ubicación de la estación que transmite el paquete.

## Informe meteorológico sin posición

Un Positionless Weather Report empieza con el DTI `_`. A continuación aparece una marca de tiempo de ocho cifras `MMDDHHMM` y después los campos de medición. En esta variante, la dirección y la velocidad del viento se indican mediante las letras `c` y `s`, en lugar del formato `ddd/sss`.

Ejemplo de APRS Protocol Reference:

```text
_10090556c220s004g005t077r000p000P000h50b09900wRSW
```

| Fragmento | Significado |
| --- | --- |
| `_` | DTI de informe meteorológico sin posición. |
| `10090556` | 9 de octubre, 05:56. |
| `c220s004` | Viento de 220°, 4 mph. |
| `g005t077` | Racha de 5 mph, temperatura de 77°F. |
| `r000p000P000` | Tres mediciones independientes de precipitación. |
| `h50b09900` | Humedad del 50 %, presión de 990,0 hPa. |
| `wRSW` | Identificador histórico del programa y del equipo meteorológico. |

En esta variante, la **secuencia inicial obligatoria** es: `_` + marca de tiempo de ocho cifras `MMDDHHMM` + `cxxx` + `sxxx` + `gxxx` + `txxx`. Debe respetarse el orden de estos campos. Los demás parámetros pueden añadirse después, en orden variable, o bien omitirse por completo. Aquí `s` significa velocidad del viento, no nieve caída.

Ejemplo de una estación equipada exclusivamente con un pluviómetro, siguiendo la estructura de APRS101:

```text
_10090556c...s...g...t...P012
```

Los campos `c`, `s`, `g` y `t` **deben aparecer** aunque no se disponga de ninguna de estas mediciones. `P012` indica 0,12 pulgadas de precipitación desde medianoche. Si la dirección real del viento fuese 0°, debería utilizarse `c000`, no `c...`.

El paquete no contiene coordenadas. Para mostrar la estación meteorológica en el mapa, el receptor necesita conocer su ubicación a partir de un informe de posición recibido anteriormente. Por ese motivo, las recomendaciones posteriores de APRS prefieren el informe completo que transporta a la vez la posición y las mediciones actuales.

## Informes históricos con datos sin procesar

Las estaciones meteorológicas antiguas podían transmitir los datos en su propio formato, sin convertirlos al formato WX genérico. APRS101 enumera los identificadores siguientes:

| DTI | Formato histórico del dispositivo |
| --- | --- |
| `!` | Ultimeter 2000 |
| `#` | Peet Bros U-II |
| `$` | Ultimeter 2000 |
| `*` | Peet Bros U-II |

Ejemplo de un informe sin procesar Peet Bros U-II incluido en la documentación:

```text
#50B7500820082
```

Algunos de estos identificadores coinciden con los DTI de otros tipos de datos APRS. Por tanto, para reconocer correctamente el formato también hay que comprobar la sintaxis que sigue al identificador. Al diseñar un transmisor nuevo, deben convertirse las lecturas del dispositivo al formato WX completo en lugar de transmitir el formato sin procesar del fabricante.

## Campos meteorológicos adicionales

Además de las mediciones básicas, la documentación contempla otros campos. No todos los equipos y aplicaciones los admiten.

| Campo | Significado | Observaciones |
| --- | --- | --- |
| `Lxxx` | Irradiancia solar | 0-999 W/m². |
| `lxxx` | Irradiancia solar | 1000 W/m² o más; se añaden 1000 al número de tres cifras. |
| `sxxx` | Nieve caída en las últimas 24 horas | Pulgadas; en el informe completo, `s` no entra en conflicto con la posición del campo de velocidad del viento. |
| `#xxx` | Contador sin procesar del pluviómetro | No tiene una unidad universal; su interpretación depende del dispositivo. |

Por ejemplo, `L700` significa 700 W/m², mientras que `l123` significa 1123 W/m². En el informe sin posición, el identificador `s` ya se utiliza para la velocidad del viento, por lo que no debe añadirse allí un campo de nieve caída que provoque esa colisión.

### Estaciones hidrométricas: ampliación de 2006

En junio de 2006 se documentó el uso de APRS para transmitir niveles de agua y señalar inundaciones. Se introdujeron los símbolos `/w` (estación hidrométrica) y `\w` (inundación), además de proponerse la posibilidad de incorporar mediciones del nivel del agua a los datos meteorológicos convencionales.

Un ejemplo del formato que utilizaban históricamente las estaciones hidrométricas de la red FIRENET es este objeto APRS:

```text
;09428508 *061713z3401.40N/11424.75Ww3.57gh/82cfs
```

En esta trama, `3.57gh` describe la altura registrada por el medidor y `82cfs` el caudal en pies cúbicos por segundo. Se trata de una **descripción textual del objeto**, no del campo `Fxxxx` de un informe WX convencional. La actualización de marzo de 2011 indica que este era el formato realmente utilizado en aquel momento, a pesar de propuestas anteriores para ampliar el formato meteorológico.

La propuesta de incorporar mediciones adicionales dentro de WX incluía los siguientes campos:

| Campo | Significado en la documentación de la ampliación |
| --- | --- |
| `Fxxxx` | Nivel de agua respecto a un nivel de referencia, en décimas de pie, con posibilidad de valores positivos y negativos. |
| `Vxxx` | Tensión de alimentación en décimas de voltio, p. ej. `V128` = 12,8 V. |
| `Zxx` | Código del tipo de dispositivo previsto en la descripción ampliada del sensor. |

Para una estación meteorológica equipada con un medidor de nivel de agua, se proponía conservar la estructura WX convencional, por ejemplo `.../...t...V128F+123` (en este caso, 12,3 pies por encima del nivel de referencia), en vez de definir un informe completamente nuevo. Las descripciones de 2011 distinguen entre estaciones hidrométricas que transmiten sus propios datos textuales y estaciones meteorológicas que incluyen campos de medición ampliados. El símbolo de estación hidrométrica por sí solo no garantiza la presencia de campos WX.

### Fukushima y la propuesta de medir la radiación de 2011

Tras el accidente de la central nuclear de Fukushima Daiichi en marzo de 2011 surgió también la necesidad de transmitir lecturas de radiación. Bob Bruninga hizo referencia expresa a los acontecimientos de Japón en el documento *APRS 1.2.1 Weather Updates to the Spec* del 24 de marzo de 2011. La solución propuesta aprovechaba el formato de informe meteorológico existente en lugar de crear un método de transmisión completamente independiente.

El nuevo campo `Xxxx` debía codificar la tasa de dosis de radiación en nanosieverts por hora (`nSv/h`). Después de la letra `X` se escribirían tres cifras: dos cifras significativas y un exponente de base diez. Por ejemplo:

| Campo | Interpretación | Resultado |
| --- | --- | --- |
| `X123` | 12 × 10³ nSv/h | 12 µSv/h |
| `X456` | 45 × 10⁶ nSv/h | 45 mSv/h |

Al mismo tiempo, se propusieron superposiciones sobre los símbolos existentes para identificar el tipo de sensor o el peligro: el símbolo meteorológico normal para lecturas de radiación de fondo, la superposición `R` para una estación que monitoriza la radiación y una superposición apropiada sobre el símbolo de peligro cuando se supere el umbral establecido. De ese modo, se podría aprovechar el mecanismo existente de visualización de sensores en el mapa y diferenciar una medición de la indicación de una situación peligrosa.

**Es importante tener en cuenta el estado de esta solución:** el documento de marzo de 2011 describe `Xxxx` como una *propuesta* de ampliación. No debe presuponerse que todas las aplicaciones actuales admiten este campo y las superposiciones propuestas, ni considerarlo un componente obligatorio de APRS101. Del mismo modo, la utilización de `Fxxxx`, `Vxxx` y `Zxx` requiere comprobar la compatibilidad real del software receptor.

## Símbolos e identificación de datos meteorológicos

El símbolo clásico de estación meteorológica es `/_`, y la tabla alternativa permite utilizar `\_`. Las aclaraciones de APRS 1.1 también incluyen `/W` y `\W` como símbolos alternativos relacionados con estaciones meteorológicas. El documento de 2011 propone unificar la identificación de sensores mediante símbolos meteorológicos con superposiciones y símbolos de peligro. Esa propuesta posterior no modifica las reglas de decodificación de los campos WX básicos.

Esto no significa que cualquier paquete que contenga el carácter `_` sea un informe WX. En un informe de posición, hay que identificar el código de símbolo en el lugar correspondiente y verificar la sintaxis de los datos que le siguen. En un informe sin posición, `_` desempeña otra función: es el primer byte del campo Information, es decir, el DTI.

## CWOP: de las estaciones APRS a las observaciones meteorológicas profesionales

El formato WX también se utiliza fuera de las redes de radioaficionados. Un ejemplo es el **Citizen Weather Observer Program (CWOP)**, surgido de APRSWXNET y de la comunidad de radioaficionados. El programa permite que voluntarios aporten mediciones de sus estaciones meteorológicas privadas a un conjunto compartido de datos meteorológicos. Pueden participar tanto radioaficionados como observadores que envían informes directamente por Internet, sin necesidad de transmisión por radio.

Desde el 1 de julio de 2001, las observaciones de CWOP alimentan **MADIS (Meteorological Assimilation Data Ingest System)**, desarrollado por la NOAA estadounidense. MADIS integra mediciones de numerosas fuentes independientes, normaliza sus formatos, unidades y marcas de tiempo, y realiza controles automáticos de calidad. Los resultados de estos controles se adjuntan a las observaciones, de modo que sus destinatarios puedan tener en cuenta la fiabilidad de cada lectura.

En una estación de radioaficionado, un informe WX puede llegar a APRS-IS por radio a través de un IGate. Las estaciones CWOP también pueden enviar sus datos mediante una conexión apropiada a Internet. El flujo simplificado para **las estaciones participantes en CWOP** es el siguiente:

```text
Estación WX -> radio -> IGate -> APRS-IS --+
                                          +-> CWOP / APRSWXNET -> NOAA MADIS
Estación WX -> Internet -------------------+                         |
                                                                    +-> servicios meteorológicos
                                                                    +-> instituciones de investigación
                                                                    +-> universidades y otros destinatarios
```

Es un esquema funcional, no una descripción de todas las conexiones internas. La forma en que MADIS obtiene los datos ha cambiado con el tiempo: desde 2023, NOAA indica que los obtiene directamente de servidores APRSWXNET y CWOP, en lugar de hacerlo a través del servicio findU como antes.

Los datos de CWOP están disponibles para numerosos usuarios del ámbito meteorológico, entre ellos las oficinas de predicción del **National Weather Service (NWS)** estadounidense, centros de investigación, universidades y entidades privadas. Pueden complementar las observaciones de estaciones profesionales y contribuir a la vigilancia meteorológica local, la verificación de pronósticos y la modelización. El NWS también señala el uso de estas observaciones en la elaboración de previsiones y avisos meteorológicos. Esto no significa, sin embargo, que cada lectura individual se utilice en todas esas aplicaciones.

### Importancia de la calidad y el registro de la estación

El valor práctico de estos informes no depende únicamente de que la sintaxis WX sea correcta. También son imprescindibles una ubicación adecuada de los sensores, unidades y tiempos de medición correctos, coordenadas actualizadas de la estación y medidas para evitar errores de medición. El control de calidad de MADIS permite detectar algunas anomalías y marcar datos sospechosos, pero no sustituye una instalación correcta de la estación meteorológica.

**No todos los informes meteorológicos visibles en APRS-IS se incorporan automáticamente a MADIS.** Para participar en CWOP es necesario registrarse y configurar correctamente el método de envío de datos. NOAA proporciona un formulario independiente para nuevos participantes y para actualizar las estaciones existentes, incluidos los radioaficionados que utilizan sus indicativos.

CWOP muestra la importancia más amplia del formato WX: un paquete APRS codificado correctamente puede ser algo más que información mostrada en un mapa; también puede formar parte de un sistema de recopilación y distribución de mediciones utilizadas por la meteorología profesional.

## Metodología de medición: compatibilidad APRS y calidad de datos CWOP

Una trama WX construida correctamente no garantiza que los datos sean fiables. La especificación APRS describe el formato y el significado de los campos, mientras que la [guía CWOP de 2005](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf) establece recomendaciones sobre metodología de medición, prestaciones de los instrumentos y condiciones de instalación. Ambos documentos deben leerse conjuntamente, pero no deben confundirse sus requisitos. El objetivo de una implementación propia debería ser generar informes APRS correctos y observaciones de la mayor calidad posible para CWOP/MADIS, no prometer que superarán siempre los controles de calidad.

### Viento: dos métodos diferentes para calcular los valores

| Parámetro | APRS WX clásico | Recomendaciones de la guía CWOP de 2005 |
| --- | --- | --- |
| Velocidad media del viento | Media del último minuto | Media de los últimos 2 minutos |
| Dirección del viento | Dirección de procedencia del viento, en grados | Dirección media de los últimos 2 minutos, respecto al norte geográfico |
| Racha `gxxx` | Velocidad máxima de los últimos 5 minutos | Lectura máxima de velocidad de los últimos 10 minutos |
| Muestreo | `WX.TXT` describe un ejemplo histórico que utiliza cuatro muestras separadas por 15 segundos para calcular la media de un minuto | La guía CWOP recomienda leer los sensores al menos cada 5 segundos |

Esta diferencia está documentada. Los autores de la guía CWOP incluyeron en su lista de cambios propuestos para APRS la ampliación de los periodos de **1 a 2 minutos** y de **5 a 10 minutos**. Por tanto, los periodos de CWOP no deben presentarse como si fueran las definiciones adoptadas por APRS101. Además, los campos `ddd/sss` y `gxxx` no incluyen metadatos que indiquen al receptor el periodo de medición aplicado. En un programa destinado a ambos usos conviene conservar las muestras originales y calcular resultados independientes para un perfil APRS y otro perfil de medición CWOP. El perfil utilizado en el informe transmitido debe seleccionarse deliberadamente y documentarse.

Para calcular la velocidad media basta con utilizar la ventana temporal correspondiente. Sin embargo, las direcciones no deben promediarse mediante una media aritmética normal: las lecturas de 359° y 1° representan viento del norte, no de 180°. Es necesario aplicar una media circular o un algoritmo vectorial adecuado, teniendo en cuenta cómo entrega las mediciones el sensor. Con viento en calma y una dirección poco fiable, debe evitarse aparentar una precisión inexistente. Conviene recordar también que el máximo de muestras instantáneas descrito en la guía CWOP puede no coincidir con otras definiciones de racha, por ejemplo la máxima media de 3 segundos utilizada en la metodología de la OMM (WMO).

### Precipitaciones: tres intervalos independientes

La base más segura para los cálculos es un historial continuo de incrementos de precipitación con su marca de tiempo, por ejemplo, los eventos de un pluviómetro de balancín. A partir de esos datos, el generador calcula por separado `rxxx` para los últimos 60 minutos, `pxxx` para una ventana móvil de 24 horas y `Pxxx` para el periodo transcurrido desde la **medianoche local de la estación**. No debe calcularse `p` a partir de `P` ni sustituirse la ventana móvil por la precipitación acumulada desde el inicio del día actual. Los resultados se codifican en centésimas de pulgada, independientemente de las unidades utilizadas por el sensor.

Después de reiniciar el dispositivo o perder parte del historial, no debe transmitirse una suma incompleta como si cubriese todo el periodo exigido. Si el archivo no permite reconstruir una ventana determinada, es necesario marcar esa medición como no disponible u omitir el campo opcional. Si se reinicia el contador del pluviómetro, hay que diferenciar la precipitación real del salto provocado por el reinicio. También importan la zona horaria correcta de la estación y la conservación de las marcas de tiempo de cada incremento.

### Temperatura, humedad y presión

APRS define las unidades y la codificación de estos campos, pero no les impone una única ventana de promediado. La guía CWOP de 2005 **recomienda** una temperatura media de los últimos 5 minutos y una humedad media del último minuto, utilizada para calcular el punto de rocío. Se trata de recomendaciones de calidad, no de bytes obligatorios adicionales del informe WX. Cada resultado debe corresponder al momento real de medición y no a un último valor conservado arbitrariamente.

El campo `bxxxxx` requiere especial atención: APRS especifica su unidad (décimas de hPa), pero una codificación correcta no determina por sí sola **qué presión** proporciona el instrumento. La guía CWOP de 2005 señala como parámetro previsto el *altimeter setting* (QNH), es decir, la presión reducida mediante el método adecuado, no la lectura sin corregir a la altura del sensor. Tampoco debe considerarse que QNH sea idéntico a la presión meteorológica al nivel del mar (QFF) sin comprobarlo. Antes de enviar datos a CWOP, hay que verificar el tipo de valor exportado por la estación, su calibración y las recomendaciones del software utilizado.

### Instalación de sensores y control de calidad

Ningún algoritmo puede corregir los errores causados por la instalación inadecuada de los dispositivos. La guía CWOP recomienda un termómetro ventilado y protegido de la radiación, situado aproximadamente a 1,5 m sobre un terreno representativo; un anemómetro, idealmente a 10 m de altura, en una zona lo más despejada posible; y un pluviómetro nivelado y protegido de las perturbaciones del flujo de aire. En entornos edificados pueden ser inevitables algunas concesiones, pero conviene documentarlas. Los metadatos de la estación, especialmente las coordenadas y la altitud, deben corresponder al lugar real de medición.

[MADIS](https://madis.ncep.noaa.gov/madis_qc.shtml) realiza controles de rangos de valores, coherencia interna, evolución temporal y coherencia espacial, dependiendo del parámetro y del nivel de control disponible. Los resultados se expresan mediante indicadores de calidad asociados a las observaciones. No se trata de una prueba universal que certifique toda la estación de manera permanente: incluso una medición correcta puede marcarse como sospechosa, y una trama sintácticamente válida puede contener valores erróneos. El operador debería analizar periódicamente la información de calidad, comparar sus mediciones con estaciones de referencia adecuadas y comprobar la calibración.

### Recomendaciones para desarrolladores de software WX

Conviene separar tres fases de procesamiento: **adquisición de muestras**, **cálculo de observaciones** y **codificación APRS**. Así, un cambio en la frecuencia de transmisión no modifica accidentalmente los periodos de promediado y el mismo flujo de mediciones puede alimentar distintos perfiles de observación. En particular:

1. Conserva la marca de tiempo de cada muestra, su unidad original, un indicador de validez y un historial suficiente para la ventana de medición más larga que se utilice.
2. Detecta datos ausentes, pérdidas de comunicación con el sensor, reinicios del contador y ventanas incompletas tras el arranque; no conviertas estas situaciones en mediciones iguales a cero.
3. Calcula las estadísticas a partir de los datos originales y solo después redondea y convierte a las unidades de la trama APRS. Evita las conversiones repetidas y los redondeos intermedios.
4. Conserva la documentación de la metodología utilizada, los periodos de promediado y la configuración de los sensores. Facilitará la interpretación correcta de los datos y el diagnóstico de posibles indicadores de calidad de MADIS.

La mera compatibilidad con el protocolo no garantiza la incorporación de los datos a CWOP ni una evaluación favorable de todas las observaciones. También son necesarios el registro de la estación, un canal de envío adecuado, mediciones fiables y la supervisión continua de su calidad.

## Notas de implementación

Con independencia de la metodología de medición, los generadores y analizadores WX deben respetar la sintaxis del protocolo. Son especialmente importantes las siguientes reglas:

1. Da preferencia al Complete Weather Report, que transporta posición y mediciones en una sola transmisión.
2. Respeta las unidades del protocolo en la entrada y la salida. Realiza las conversiones métricas únicamente para mostrar los datos o antes de codificar los valores que se van a transmitir.
3. Distingue `r`, `p` y `P` como tres intervalos distintos de medición de precipitaciones.
4. Valida la **presencia de los campos obligatorios** por separado de la disponibilidad de las propias mediciones. En un informe completo sin comprimir, conserva `ddd/sss` y `t`; en un informe sin posición, conserva la marca de tiempo y `c`, `s`, `g` y `t`.
5. No confundas una medición no disponible con el valor cero; admite puntos, espacios y omisiones de campos permitidas. Da preferencia a los puntos porque mejoran la legibilidad de los volcados de texto.
6. Reconoce las distintas codificaciones del viento en informes completos, sin posición y con posición comprimida.
7. Separa los campos WX clásicos de las ampliaciones posteriores y permite ignorar los campos desconocidos. No presupongas que todos los clientes admiten `Xxxx`, propuesto en 2011.
8. No confundas un informe de medición con un aviso meteorológico. Las notificaciones de peligro, incluidos NWS-WARN y otros sistemas de alerta, utilizan mecanismos independientes.
9. No cambies el significado de los campos solo porque la aplicación se integre con CWOP. Documenta el perfil de medición y distingue los requisitos de formato APRS de las recomendaciones de calidad de datos.

## Fuentes

- [APRS Protocol Reference 1.0.1](https://www.aprs.org/doc/APRS101.PDF), capítulo 12: Weather Reports.
- [APRS 1.1: Weather Specification Comments](https://www.aprs.org/aprs11/spec-wx.txt), WB4APR, actualizado el 24 de marzo de 2011.
- [APRS 1.2.1: Weather Updates to the Spec](https://www.aprs.org/aprs12/weather-new.txt), WB4APR, 24 de marzo de 2011; también contiene propuestas de ampliación.
- [Water Gauges in APRS](https://www.aprs.org/aprs12/watergage.txt), WB4APR, 2006; actualizado el 24 de marzo de 2011.
- [OIEA: información sobre el accidente de Fukushima Daiichi](https://www.iaea.org/newscenter/news/fukushima-nuclear-accident-update-log-20), documentación de los sucesos de marzo de 2011.
- WB4APR, *WX.TXT: Using APRS in Weather and SKYWARN Applications*, versión 8.3.5, 10 de marzo de 1999, actualizada el 18 de agosto de 2010 (documento histórico).
- [NOAA MADIS: Citizen Weather Observer Program Data](https://madis.ncep.noaa.gov/madis_cwop.shtml), objetivos de CWOP, historia y tratamiento de los datos.
- [NOAA MADIS: APRSWXNET/CWOP Snow Project](https://madis.ncep.noaa.gov/snow_project.shtml), destinatarios y ejemplos de utilización de las observaciones.
- [NOAA NWS: Join CWOP](https://www.weather.gov/pub/JoinCWOP), aplicaciones de los informes en la predicción y los avisos meteorológicos.
- [NOAA MADIS: Registration and Update Form](https://madis.ncep.noaa.gov/cwop_signup.shtml), condiciones de registro de estaciones.
- [CWOP Weather Station Siting, Performance, and Data Quality Guide](https://www.weather.gov/media/epz/mesonet/CWOP-OfficialGuide.pdf), versión 1.0 del 8 de marzo de 2005; recomendaciones de medición y lista histórica de cambios propuestos para APRS.
- [NOAA MADIS: Quality Control](https://madis.ncep.noaa.gov/madis_qc.shtml), descripción general del control de calidad y los indicadores de observaciones.
- [NOAA MADIS: Meteorological Surface Quality Control](https://madis.ncep.noaa.gov/madis_sfc_qc.shtml), alcance y niveles del control de calidad de las observaciones de superficie.
- [WMO Guide to Meteorological Instruments and Methods of Observation](https://www.weather.gov/media/epz/mesonet/CWOP-WMO8.pdf), referencia sobre definiciones internacionales de medición del viento y las rachas.
- [NOAA MADIS: Recent Updates](https://madisqa.ncep.noaa.gov/madis_recent.shtml), cambios en el método de adquisición de datos CWOP en 2023.
