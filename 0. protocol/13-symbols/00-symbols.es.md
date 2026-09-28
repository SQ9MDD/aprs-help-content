---
title: Símbolos APRS
description: Codificación e interpretación de los símbolos APRS, tablas, superposiciones, aplicaciones prácticas y catálogo completo de 188 símbolos.
template: doc
tableOfContents: true
---

Un símbolo APRS no es simplemente una imagen que indica una posición en un mapa. También es **un medio compacto para transmitir información sobre el tipo, la función y las características de una estación o un objeto**. Un símbolo adecuado puede identificar un vehículo, un digipeater o una estación doméstica, mientras que una superposición permite precisar, por ejemplo, su fuente de alimentación, las funciones del equipo o la naturaleza de una actividad sobre el terreno.

APRS transmite **el código del símbolo, no su representación gráfica**. En los informes de posición habituales bastan dos caracteres: el identificador de tabla y el carácter del símbolo. La superposición no añade un tercer carácter, sino que ocupa el lugar del identificador de la tabla alternativa. La aplicación receptora interpreta la combinación según su propia tabla de símbolos. Así puede transmitirse información adicional sin aumentar en otro carácter la longitud del informe.

## Codificación del símbolo en un informe de posición

En un informe de posición sin comprimir, el carácter de la tabla aparece inmediatamente después de la latitud y el del símbolo, después de la longitud:

```text
SQ9MDD>APRS:!5003.50N/01956.00E>
                     ^         ^
                     |         |
                   tabla     símbolo
```

La pareja `/>` identifica un automóvil: `/` selecciona la tabla principal y `>` designa el símbolo de esa tabla. Esta línea es una representación textual de un paquete, no una transcripción de todos los bytes de la trama de radio AX.25.

| Elemento | Función |
|---|---|
| **Carácter de tabla** | Selecciona la tabla principal, la alternativa o una variante de símbolo alternativo con superposición. |
| **Carácter de símbolo** | Indica una posición en la tabla seleccionada y, junto con la superposición, determina el significado del código. |
| **Representación gráfica** | La almacena o genera la aplicación receptora; no se transmite en el paquete. |

Los símbolos también aparecen en otros formatos de informes de posición, entre ellos los comprimidos y Mic-E. Su ubicación y codificación pueden variar, pero siguen identificando un símbolo, no transmitiendo un mapa de bits.

## Tablas principal y alternativa

Las dos tablas APRS contienen 94 posiciones cada una:

- `/` selecciona la **tabla principal**, utilizada, entre otras cosas, para estaciones y vehículos habituales;
- `\` selecciona la **tabla alternativa**, que incluye también familias de símbolos ampliadas mediante superposiciones.

El significado se establece a partir de ambos caracteres. Por ejemplo, `/>` es un automóvil de la tabla principal, mientras que `\>` es el símbolo base de vehículo de la tabla alternativa. Del mismo modo, `/-` representa una casa de la tabla principal y `\-` es un símbolo alternativo cuyas superposiciones permiten describir las características de una estación doméstica.

## Superposiciones e información adicional

Una **superposición** (*overlay*) es una letra `A`-`Z` o una cifra `0`-`9` que **sustituye al carácter `\`** en el código de un símbolo alternativo. No es un campo adicional ni aumenta la longitud de la pareja de caracteres del símbolo.

```text
Tabla alternativa:      \-    casa (símbolo base)
Con superposición S:    S-    casa con energía solar

                        ^
                        superposición en lugar del carácter de tabla
```

El símbolo base define la categoría y la superposición puede indicar una variante concreta. Su significado **depende del carácter del símbolo**: `S-` se refiere a la alimentación de una casa, `S;` identifica una actividad SOTA y `S#` describe una función de un digipeater. No existe un diccionario universal en el que una letra tenga el mismo significado para todos los iconos.

La ampliación de 2007 contempla superposiciones para todos los símbolos alternativos, pero **no todas las combinaciones tienen un significado definido**. No se les debe atribuir una interpretación propia ni suponer que otras aplicaciones reconocerán una combinación sin definir. El mecanismo admite numerosas combinaciones, pero su utilidad depende de definiciones acordadas y de su implementación.

En origen, la superposición se concibió como una cifra o una letra dibujada sobre el icono base. Sin embargo, la documentación también permite utilizar una imagen completamente distinta para una combinación concreta si transmite su significado con mayor claridad.

## Aplicaciones prácticas de los símbolos y las superposiciones

### Estación doméstica: fuente de alimentación y presencia del operador

![Símbolo base de casa de la tabla alternativa](./_img/verG/a12.gif)

La familia del símbolo alternativo de casa `\-` ilustra especialmente bien que un icono puede transmitir algo más que el tipo de objeto. Según la lista de ampliaciones proporcionada, existen las siguientes combinaciones:

| Código | Significado |
|---|---|
| `\-` | Casa, símbolo base alternativo; utilizado históricamente para estaciones de HF. |
| `B-` | Alimentación por baterías o funcionamiento fuera de la red eléctrica. |
| `C-` | Combinación de fuentes de energía alternativas. |
| `E-` | Alimentación de emergencia ante una interrupción del suministro eléctrico. |
| `G-` | Energía geotérmica. |
| `H-` | Energía hidráulica. |
| `S-` | Energía solar. |
| `W-` | Energía eólica. |
| `O-` | Operador presente en la estación. |
| `5-`, `6-` | Frecuencia de red inusual en esa zona: 50 o 60 Hz, respectivamente. |

Ejemplo de informe de posición de una estación doméstica con superposición `S`:

```text
SQ9MDD>APRS:!5003.50NS01956.00E-
```

En esta representación, `S`, situado después de la latitud, sustituye a `\` y el `-` final selecciona el símbolo de casa. **Los dos caracteres `S-` bastan para declarar una alimentación solar.** No se trata de otro campo, una ampliación del comentario ni un tercer carácter del símbolo.

Esta información declara las características de la estación, no mide su estado actual. `E-` no demuestra que la alimentación de emergencia esté funcionando en ese momento; del mismo modo, `S-` no constituye telemetría de producción energética. Un código solo admite una superposición. Si importan varias características, deben completarse mediante un comentario, telemetría u otro mecanismo APRS previsto para ello.

**Nota histórica:** algunas listas antiguas asignaban `C-` a un radioclub. En la revisión proporcionada de `symbols-new.txt` de 2017, `C-` pasó a indicar una combinación de fuentes de energía alternativas y **el radioclub se trasladó a `Ch`** (familia de edificios `\h`). Conviene tener en cuenta este cambio al comparar programas y tablas antiguos.

### Digipeater: información sobre sus funciones

![Símbolo base de digipeater de la tabla alternativa](./_img/verG/a02.gif)

Una superposición de digipeater puede indicar su función, no solo el tipo de dispositivo:

| Código | Significado según la lista de ampliaciones |
|---|---|
| `/#` | Símbolo genérico de digipeater de la tabla principal. |
| `1#` | Digipeater WIDE1-1. |
| `A#` | Digipeater con entrada alternativa, por ejemplo en otra frecuencia. |
| `E#` | Digipeater con alimentación de emergencia. |
| `I#` | Digipeater que también incorpora la función IGate. |
| `V#` | Digipeater que utiliza el mecanismo Viscous. |

`I#` no significa lo mismo que el símbolo independiente de una pasarela APRS-IS. El primer código describe un digipeater con una función adicional; el segundo pertenece a otra familia de símbolos de pasarelas. La elección depende de la función que queramos destacar en el mapa.

### IGate: dirección y forma de retransmisión de datos

![Símbolo base de pasarela de la tabla alternativa](./_img/verG/a05.gif)

La familia `\&` puede indicar las capacidades de una pasarela:

| Código | Significado |
|---|---|
| `I&` | IGate genérico; la documentación recomienda una superposición más específica cuando sea posible. |
| `R&` | IGate que solo recibe de RF, sin reenviar mensajes hacia RF. |
| `T&` | IGate transmisor con trayectos de mensajes limitados a un salto. |
| `2&` | IGate transmisor con trayectos de dos saltos; la lista señala que generalmente no se recomienda. |

Por tanto, el icono puede distinguir una estación receptora de una pasarela que también retransmite mensajes hacia RF. No obstante, el símbolo expresa la función declarada, no confirma el estado actual de la conexión APRS-IS ni verifica la configuración.

### Actividades sobre el terreno y eventos

Las distintas superposiciones de la familia de operación portátil identifican clases de actividad:

| Código | Significado |
|---|---|
| `/;` | Símbolo básico de operación portátil / campamento. |
| `F;` | Field Day. |
| `I;` | IOTA (*Islands on the Air*). |
| `S;` | SOTA (*Summits on the Air*). |
| `W;` | WOTA (*Wainwrights on the Air*). |

Este ejemplo muestra superposiciones cuyo significado no está relacionado con el equipo ni con la alimentación. Un mismo icono base puede informar sobre el tipo de actividad en que participa la estación.

### Vehículos e instalaciones especiales

En la familia de vehículos `\>`, pueden distinguirse `B>` para un vehículo a baterías, `P>` para un híbrido enchufable y `S>` para un vehículo alimentado por energía solar. En la familia de refugios `\z`, `Ez` indica una instalación con alimentación de emergencia y `Tz` un punto de triaje médico (*triage*). Son ejemplos de cómo comunicar una característica o función concreta sin ampliar la representación del símbolo.

Al elegir estos símbolos, debe declararse la función real del objeto. No conviene utilizar iconos de servicios de emergencia, incidentes o peligros solo por su aspecto gráfico.

## Cómo elegir e interpretar los símbolos

Un símbolo debe corresponder ante todo a **la función actual de la estación o del objeto**. Entre las opciones habituales están `/[` para una persona a pie, `/>` para un automóvil, `/b` para una bicicleta, `/-` para una estación doméstica, `/#` para un digipeater, `/r` para un repetidor y `/_` para una estación meteorológica. Es conveniente utilizar una superposición para añadir detalles solo cuando la combinación tenga un significado documentado.

Para leer un informe de posición sin comprimir, hay que localizar el carácter de tabla inmediatamente después de la latitud y el carácter de símbolo después de la longitud. A continuación se interpreta **la pareja de caracteres como un único código**. Por ejemplo, `!5003.50N/01956.00E>` contiene el símbolo de automóvil `/>`, mientras que `!5003.50NS01956.00E-` contiene `S-`, que indica una estación doméstica alimentada con energía solar.

Es importante distinguir el símbolo de los demás datos del informe. Identifica una categoría o una característica del objeto, pero no sustituye el comentario, la telemetría ni los datos sobre su estado real. Si una característica es relevante para la operación, también debería poder ser comprendida por usuarios cuyas aplicaciones no admitan las superposiciones más recientes.

## Por qué un mismo símbolo puede verse de distintas formas

Las representaciones gráficas de los símbolos se almacenan o generan localmente en cada aplicación. Un programa con una tabla antigua puede mostrar una nueva combinación como simple icono base, omitir la letra superpuesta o representarla de otro modo que un cliente moderno. Incluso los programas plenamente compatibles pueden elegir estilos gráficos distintos sin alterar el significado del código.

La documentación de ampliaciones destaca la importancia de la compatibilidad: los cambios demasiado frecuentes en los significados y la falta de soporte de superposiciones pueden provocar interpretaciones distintas del mismo paquete. Por eso, el software debería **conservar los dos caracteres recibidos del símbolo**, aunque todavía no sea capaz de mostrar gráficamente una combinación. Una superposición desconocida no debería provocar el rechazo de un informe de posición válido.

En aplicaciones operativas no debe confiarse únicamente en un icono poco habitual. Los datos importantes, como la función del objeto, la frecuencia o la disponibilidad de servicios, también pueden incluirse en un comentario APRS adecuado.

## Tabla completa de símbolos

El siguiente catálogo incluye las 188 posiciones de las tablas proporcionadas: 94 de la tabla principal y 94 de la alternativa. Las filas están emparejadas por el mismo carácter de símbolo. Las imágenes proceden del conjunto local `_img/verG` y representan los símbolos base, no todas las posibles variantes con superposición.

**Leyenda:** «libre» significa una posición sin asignación en la lista citada. «Base para superposiciones» indica que el significado del símbolo puede especificarse mediante una letra o una cifra.

| Código principal | Icono | Significado | Código alternativo | Icono | Significado |
|---|:---:|---|---|:---:|---|
| `/!` | ![Policía / sheriff](./_img/verG/00.gif) | Policía / sheriff. | `\!` | ![Alarma de emergencia](./_img/verG/a00.gif) | Alarma de emergencia; base para superposiciones. |
| `/"` | ![Reservado (antes, lluvia)](./_img/verG/01.gif) | Reservado (antes, lluvia). | `\"` | ![Reservado](./_img/verG/a01.gif) | Reservado. |
| `/#` | ![Digipeater](./_img/verG/02.gif) | Digipeater. | `\#` | ![Digipeater con superposición / estrella verde](./_img/verG/a02.gif) | Digipeater con superposición / estrella verde. |
| `/$` | ![Teléfono](./_img/verG/03.gif) | Teléfono. | `\$` | ![Banco o cajero automático](./_img/verG/a03.gif) | Banco o cajero automático. |
| `/%` | ![DX Cluster](./_img/verG/04.gif) | DX Cluster. | `\%` | ![Central eléctrica](./_img/verG/a04.gif) | Central eléctrica; base para superposiciones. |
| `/&` | ![Pasarela HF](./_img/verG/05.gif) | Pasarela HF. | `\&` | ![Pasarela](./_img/verG/a05.gif) | Pasarela; superposiciones IGate. |
| `/'` | ![Avión pequeño](./_img/verG/06.gif) | Avión pequeño. | `\'` | ![Lugar de accidente / incidente](./_img/verG/a06.gif) | Lugar de accidente / incidente. |
| `/(` | ![Estación satelital móvil](./_img/verG/07.gif) | Estación satelital móvil. | `\(` | ![Nubosidad](./_img/verG/a07.gif) | Nubosidad; base para variantes de nubes. |
| `/)` | ![Silla de ruedas](./_img/verG/08.gif) | Silla de ruedas. | `\)` | ![Firenet MEO / observación de la Tierra MODIS](./_img/verG/a08.gif) | Firenet MEO / observación de la Tierra MODIS. |
| `/*` | ![Moto de nieve](./_img/verG/09.gif) | Moto de nieve. | `\*` | ![Libre](./_img/verG/a09.gif) | Libre. |
| `/+` | ![Cruz Roja](./_img/verG/10.gif) | Cruz Roja. | `\+` | ![Iglesia](./_img/verG/a10.gif) | Iglesia. |
| `/,` | ![Boy Scouts](./_img/verG/11.gif) | Boy Scouts. | `\,` | ![Girl Scouts](./_img/verG/a11.gif) | Girl Scouts. |
| `/-` | ![Casa / QTH VHF](./_img/verG/12.gif) | Casa / QTH VHF. | `\-` | ![Casa (históricamente, estación HF)](./_img/verG/a12.gif) | Casa (históricamente, estación HF); base para una familia de superposiciones, incluido `O-` para operador presente. |
| `/.` | ![Marca X](./_img/verG/13.gif) | Marca X. | `\.` | ![Posición ambigua (signo de interrogación grande)](./_img/verG/a13.gif) | Posición ambigua (signo de interrogación grande). |
| `//` | ![Punto rojo](./_img/verG/14.gif) | Punto rojo. | `\/` | ![Destino / waypoint](./_img/verG/a14.gif) | Destino / waypoint. |
| `/0` | ![Círculo, símbolo obsoleto](./_img/verG/15.gif) | Círculo, símbolo obsoleto. | `\0` | ![Círculo](./_img/verG/a15.gif) | Círculo; IRLP, EchoLink, WiRES y superposiciones. |
| `/1` | ![Libre / históricamente un círculo numerado](./_img/verG/16.gif) | Libre / históricamente un círculo numerado. | `\1` | ![Libre](./_img/verG/a16.gif) | Libre. |
| `/2` | ![Libre / históricamente un círculo numerado](./_img/verG/17.gif) | Libre / históricamente un círculo numerado. | `\2` | ![Libre](./_img/verG/a17.gif) | Libre. |
| `/3` | ![Libre / históricamente un círculo numerado](./_img/verG/18.gif) | Libre / históricamente un círculo numerado. | `\3` | ![Libre](./_img/verG/a18.gif) | Libre. |
| `/4` | ![Libre / históricamente un círculo numerado](./_img/verG/19.gif) | Libre / históricamente un círculo numerado. | `\4` | ![Libre](./_img/verG/a19.gif) | Libre. |
| `/5` | ![Libre / históricamente un círculo numerado](./_img/verG/20.gif) | Libre / históricamente un círculo numerado. | `\5` | ![Libre](./_img/verG/a20.gif) | Libre. |
| `/6` | ![Libre / históricamente un círculo numerado](./_img/verG/21.gif) | Libre / históricamente un círculo numerado. | `\6` | ![Libre](./_img/verG/a21.gif) | Libre. |
| `/7` | ![Libre / históricamente un círculo numerado](./_img/verG/22.gif) | Libre / históricamente un círculo numerado. | `\7` | ![Libre](./_img/verG/a22.gif) | Libre. |
| `/8` | ![Libre / históricamente un círculo numerado](./_img/verG/23.gif) | Libre / históricamente un círculo numerado. | `\8` | ![Nodo de red 802.11 u otra red](./_img/verG/a23.gif) | Nodo de red 802.11 u otra red. |
| `/9` | ![Libre / históricamente un círculo numerado](./_img/verG/24.gif) | Libre / históricamente un círculo numerado. | `\9` | ![Gasolinera](./_img/verG/a24.gif) | Gasolinera. |
| `/:` | ![Incendio](./_img/verG/25.gif) | Incendio. | `\:` | ![Libre (el granizo pasó a las superposiciones)](./_img/verG/a25.gif) | Libre (el granizo pasó a las superposiciones). |
| `/;` | ![Campamento / operación portátil](./_img/verG/26.gif) | Campamento / operación portátil. | `\;` | ![Parque o zona de pícnic](./_img/verG/a26.gif) | Parque o zona de pícnic; admite superposiciones para eventos. |
| `/<` | ![Motocicleta](./_img/verG/27.gif) | Motocicleta. | `\<` | ![Aviso / una sola bandera meteorológica](./_img/verG/a27.gif) | Aviso / una sola bandera meteorológica. |
| `/=` | ![Locomotora](./_img/verG/28.gif) | Locomotora. | `\=` | ![Familia libre de símbolos con superposiciones](./_img/verG/a28.gif) | Familia libre de símbolos con superposiciones. |
| `/>` | ![Automóvil](./_img/verG/29.gif) | Automóvil. | `\>` | ![Vehículo](./_img/verG/a29.gif) | Vehículo; base para superposiciones. |
| `/?` | ![Servidor de archivos](./_img/verG/30.gif) | Servidor de archivos. | `\?` | ![Punto de información](./_img/verG/a30.gif) | Punto de información. |
| `/@` | ![Punto de posición prevista (H/C)](./_img/verG/31.gif) | Punto de posición prevista (H/C). | `\@` | ![Huracán / tormenta tropical](./_img/verG/a31.gif) | Huracán / tormenta tropical. |
| `/A` | ![Puesto de asistencia](./_img/verG/32.gif) | Puesto de asistencia. | `\A` | ![Recuadro](./_img/verG/a32.gif) | Recuadro; DTMF, RFID, XO y otras superposiciones. |
| `/B` | ![BBS / PBBS](./_img/verG/33.gif) | BBS / PBBS. | `\B` | ![Libre (la ventisca pasó a una superposición)](./_img/verG/a33.gif) | Libre (la ventisca pasó a una superposición). |
| `/C` | ![Canoa](./_img/verG/34.gif) | Canoa. | `\C` | ![Guardacostas](./_img/verG/a34.gif) | Guardacostas. |
| `/D` | ![Libre](./_img/verG/35.gif) | Libre. | `\D` | ![Depósito / terminal](./_img/verG/a35.gif) | Depósito / terminal; base para superposiciones. |
| `/E` | ![Símbolo de ojo](./_img/verG/36.gif) | Símbolo de ojo; evento o punto de atención. | `\E` | ![Humo y otros códigos de visibilidad](./_img/verG/a36.gif) | Humo y otros códigos de visibilidad. |
| `/F` | ![Vehículo agrícola / tractor](./_img/verG/37.gif) | Vehículo agrícola / tractor. | `\F` | ![Libre (la lluvia engelante pasó a una superposición)](./_img/verG/a37.gif) | Libre (la lluvia engelante pasó a una superposición). |
| `/G` | ![Localizador Maidenhead de seis caracteres](./_img/verG/38.gif) | Localizador Maidenhead de seis caracteres. | `\G` | ![Libre (los chubascos de nieve pasaron a una superposición)](./_img/verG/a38.gif) | Libre (los chubascos de nieve pasaron a una superposición). |
| `/H` | ![Hotel](./_img/verG/39.gif) | Hotel. | `\H` | ![Calima](./_img/verG/a39.gif) | Calima; también base para símbolos de peligros. |
| `/I` | ![Estación TCP/IP en la red de radio](./_img/verG/40.gif) | Estación TCP/IP en la red de radio. | `\I` | ![Chubascos de lluvia](./_img/verG/a40.gif) | Chubascos de lluvia. |
| `/J` | ![Libre](./_img/verG/41.gif) | Libre. | `\J` | ![Libre (el relámpago pasó a una superposición)](./_img/verG/a41.gif) | Libre (el relámpago pasó a una superposición). |
| `/K` | ![Escuela](./_img/verG/42.gif) | Escuela. | `\K` | ![Radio Kenwood](./_img/verG/a42.gif) | Radio Kenwood. |
| `/L` | ![Usuario de ordenador conectado a APRS](./_img/verG/43.gif) | Usuario de ordenador conectado a APRS. | `\L` | ![Faro](./_img/verG/a43.gif) | Faro. |
| `/M` | ![MacAPRS](./_img/verG/44.gif) | MacAPRS. | `\M` | ![MARS](./_img/verG/a44.gif) | MARS; superposiciones según el tipo de servicio. |
| `/N` | ![Estación del National Traffic System](./_img/verG/45.gif) | Estación del National Traffic System. | `\N` | ![Boya de navegación](./_img/verG/a45.gif) | Boya de navegación. |
| `/O` | ![Globo](./_img/verG/46.gif) | Globo. | `\O` | ![Cohete aficionado / familia de globos con superposiciones](./_img/verG/a46.gif) | Cohete aficionado / familia de globos con superposiciones. |
| `/P` | ![Policía](./_img/verG/47.gif) | Policía. | `\P` | ![Aparcamiento](./_img/verG/a47.gif) | Aparcamiento. |
| `/Q` | ![Libre](./_img/verG/48.gif) | Libre. | `\Q` | ![Terremoto](./_img/verG/a48.gif) | Terremoto. |
| `/R` | ![Vehículo recreativo / autocaravana](./_img/verG/49.gif) | Vehículo recreativo / autocaravana. | `\R` | ![Restaurante](./_img/verG/a49.gif) | Restaurante. |
| `/S` | ![Transbordador espacial](./_img/verG/50.gif) | Transbordador espacial. | `\S` | ![Satélite / PACSAT](./_img/verG/a50.gif) | Satélite / PACSAT. |
| `/T` | ![SSTV](./_img/verG/51.gif) | SSTV. | `\T` | ![Tormenta eléctrica](./_img/verG/a51.gif) | Tormenta eléctrica. |
| `/U` | ![Autobús](./_img/verG/52.gif) | Autobús. | `\U` | ![Soleado](./_img/verG/a52.gif) | Soleado. |
| `/V` | ![ATV / quad](./_img/verG/53.gif) | ATV / quad. | `\V` | ![Ayuda a la navegación VORTAC](./_img/verG/a53.gif) | Ayuda a la navegación VORTAC. |
| `/W` | ![Estación del National Weather Service](./_img/verG/54.gif) | Estación del National Weather Service. | `\W` | ![Estación NWS con superposiciones](./_img/verG/a54.gif) | Estación NWS con superposiciones. |
| `/X` | ![Helicóptero](./_img/verG/55.gif) | Helicóptero. | `\X` | ![Farmacia](./_img/verG/a55.gif) | Farmacia. |
| `/Y` | ![Velero](./_img/verG/56.gif) | Velero. | `\Y` | ![Radios y dispositivos APRS](./_img/verG/a56.gif) | Radios y dispositivos APRS. |
| `/Z` | ![WinAPRS](./_img/verG/57.gif) | WinAPRS. | `\Z` | ![Libre](./_img/verG/a57.gif) | Libre. |
| `/[` | ![Persona / estación a pie](./_img/verG/58.gif) | Persona / estación a pie. | `\[` | ![Nube pared](./_img/verG/a58.gif) | Nube pared; también símbolo de persona con superposición. |
| `/\` | ![Triángulo, radiogoniometría](./_img/verG/59.gif) | Triángulo, radiogoniometría. | `\\` | ![Nuevo símbolo GPS con superposiciones](./_img/verG/a59.gif) | Nuevo símbolo GPS con superposiciones. |
| `/]` | ![Correo / oficina de correos](./_img/verG/60.gif) | Correo / oficina de correos. | `\]` | ![Libre](./_img/verG/a60.gif) | Libre. |
| `/^` | ![Avión grande](./_img/verG/61.gif) | Avión grande. | `\^` | ![Aviación](./_img/verG/a61.gif) | Aviación; otros tipos de aeronaves con superposiciones. |
| `/_` | ![Estación meteorológica](./_img/verG/62.gif) | Estación meteorológica. | `\_` | ![Estación meteorológica / digi verde con superposición](./_img/verG/a62.gif) | Estación meteorológica / digi verde con superposición. |
| <code>/&#96;</code> | ![Antena parabólica](./_img/verG/63.gif) | Antena parabólica. | <code>&#92;&#96;</code> | ![Lluvia](./_img/verG/a63.gif) | Lluvia; tipos de precipitación mediante superposiciones. |
| `/a` | ![Ambulancia](./_img/verG/64.gif) | Ambulancia. | `\a` | ![ARRL, ARES, Winlink, D-STAR y otras superposiciones](./_img/verG/a64.gif) | ARRL, ARES, Winlink, D-STAR y otras superposiciones. |
| `/b` | ![Bicicleta](./_img/verG/65.gif) | Bicicleta. | `\b` | ![Libre (el polvo o la arena pasaron a una superposición)](./_img/verG/a65.gif) | Libre (el polvo o la arena pasaron a una superposición). |
| `/c` | ![Puesto de mando de incidentes](./_img/verG/66.gif) | Puesto de mando de incidentes. | `\c` | ![Protección civil](./_img/verG/a66.gif) | Protección civil; RACES, SATERN y otras superposiciones. |
| `/d` | ![Bomberos](./_img/verG/67.gif) | Bomberos. | `\d` | ![Aviso DX por indicativo](./_img/verG/a67.gif) | Aviso DX por indicativo. |
| `/e` | ![Caballo / equitación](./_img/verG/68.gif) | Caballo / equitación. | `\e` | ![Aguanieve](./_img/verG/a68.gif) | Aguanieve. |
| `/f` | ![Camión de bomberos](./_img/verG/69.gif) | Camión de bomberos. | `\f` | ![Nube embudo](./_img/verG/a69.gif) | Nube embudo. |
| `/g` | ![Planeador](./_img/verG/70.gif) | Planeador. | `\g` | ![Banderas de temporal](./_img/verG/a70.gif) | Banderas de temporal. |
| `/h` | ![Hospital](./_img/verG/71.gif) | Hospital. | `\h` | ![Tienda / feria de radioaficionados](./_img/verG/a71.gif) | Tienda / feria de radioaficionados; `Ch` indica radioclub. |
| `/i` | ![IOTA (Islands on the Air)](./_img/verG/72.gif) | IOTA (Islands on the Air). | `\i` | ![Recuadro / punto de interés](./_img/verG/a72.gif) | Recuadro / punto de interés. |
| `/j` | ![Jeep](./_img/verG/73.gif) | Jeep. | `\j` | ![Obras en la carretera](./_img/verG/a73.gif) | Obras en la carretera. |
| `/k` | ![Camión](./_img/verG/74.gif) | Camión. | `\k` | ![Vehículo especial, SUV, ATV o 4×4](./_img/verG/a74.gif) | Vehículo especial, SUV, ATV o 4×4. |
| `/l` | ![Portátil](./_img/verG/75.gif) | Portátil. | `\l` | ![Área: rectángulo, círculo, línea o triángulo](./_img/verG/a75.gif) | Área: rectángulo, círculo, línea o triángulo. |
| `/m` | ![Repetidor Mic-E](./_img/verG/76.gif) | Repetidor Mic-E. | `\m` | ![Indicador de valores](./_img/verG/a76.gif) | Indicador de valores. |
| `/n` | ![Nodo de red](./_img/verG/77.gif) | Nodo de red. | `\n` | ![Triángulo con superposición](./_img/verG/a77.gif) | Triángulo con superposición. |
| `/o` | ![Centro de operaciones de emergencia (EOC)](./_img/verG/78.gif) | Centro de operaciones de emergencia (EOC). | `\o` | ![Círculo pequeño](./_img/verG/a78.gif) | Círculo pequeño. |
| `/p` | ![ROVER / perro](./_img/verG/79.gif) | ROVER / perro. | `\p` | ![Libre (la nubosidad parcial pasó a una superposición)](./_img/verG/a79.gif) | Libre (la nubosidad parcial pasó a una superposición). |
| `/q` | ![Localizador Maidenhead (descripción de 128 m)](./_img/verG/80.gif) | Localizador Maidenhead (descripción de 128 m). | `\q` | ![Libre](./_img/verG/a80.gif) | Libre. |
| `/r` | ![Repetidor](./_img/verG/81.gif) | Repetidor. | `\r` | ![Aseos](./_img/verG/a81.gif) | Aseos. |
| `/s` | ![Barco con motor](./_img/verG/82.gif) | Barco con motor. | `\s` | ![Barco / embarcación con superposición](./_img/verG/a82.gif) | Barco / embarcación con superposición. |
| `/t` | ![Área de descanso para camiones](./_img/verG/83.gif) | Área de descanso para camiones. | `\t` | ![Tornado](./_img/verG/a83.gif) | Tornado. |
| `/u` | ![Camión de 18 ruedas](./_img/verG/84.gif) | Camión de 18 ruedas. | `\u` | ![Camión con superposición](./_img/verG/a84.gif) | Camión con superposición. |
| `/v` | ![Furgoneta](./_img/verG/85.gif) | Furgoneta. | `\v` | ![Furgoneta con superposición](./_img/verG/a85.gif) | Furgoneta con superposición. |
| `/w` | ![Estación de agua](./_img/verG/86.gif) | Estación de agua. | `\w` | ![Inundación, avalancha o deslizamiento de tierra](./_img/verG/a86.gif) | Inundación, avalancha o deslizamiento de tierra. |
| `/x` | ![xAPRS / Unix](./_img/verG/87.gif) | xAPRS / Unix. | `\x` | ![Accidente u obstáculo en la carretera](./_img/verG/a87.gif) | Accidente u obstáculo en la carretera. |
| `/y` | ![Antena Yagi en el QTH](./_img/verG/88.gif) | Antena Yagi en el QTH. | `\y` | ![Skywarn](./_img/verG/a88.gif) | Skywarn. |
| `/z` | ![Libre](./_img/verG/89.gif) | Libre. | `\z` | ![Refugio con superposición](./_img/verG/a89.gif) | Refugio con superposición. |
| `/{` | ![Libre](./_img/verG/90.gif) | Libre. | `\{` | ![Libre (la niebla pasó a una superposición)](./_img/verG/a90.gif) | Libre (la niebla pasó a una superposición). |
| <code>/&#124;</code> | ![Conmutador de flujos TNC](./_img/verG/91.gif) | Conmutador de flujos TNC. | <code>&#92;&#124;</code> | ![Conmutador de flujos TNC](./_img/verG/a91.gif) | Conmutador de flujos TNC. |
| `/}` | ![Libre](./_img/verG/92.gif) | Libre. | `\}` | ![Libre](./_img/verG/a92.gif) | Libre. |
| `/~` | ![Conmutador de flujos TNC](./_img/verG/93.gif) | Conmutador de flujos TNC. | `\~` | ![Conmutador de flujos TNC](./_img/verG/a93.gif) | Conmutador de flujos TNC. |

| Archivo de imagen adicional | Icono | Observaciones |
|---|:---:|---|
| `x.gif` | ![Cruz Roja](./_img/verG/x.gif) | Copia adicional de la imagen de la Cruz Roja; su código de símbolo en la tabla principal es `/+`. |

## Fuentes y notas sobre los catálogos

- [APRS Symbols - master symbol list](http://www.aprs.org/symbols/symbolsX.txt) - tablas principal y alternativa, incluidas las marcas de los símbolos que admiten superposiciones.
- [APRS symbol overlays and extensions](http://www.aprs.org/symbols/symbols-new.txt) - ejemplos y significados asignados a las superposiciones de casas, digipeaters, IGate, vehículos y otras familias.
- [Overlay Extension to APRS Symbol Set](http://www.aprs.org/symbols/symbols-overlays.txt) - justificación de la ampliación del conjunto de símbolos y criterios de compatibilidad con versiones anteriores.
- [Background on Updating APRS Symbols](http://www.aprs.org/symbols/symbols-background.txt) - representación de los símbolos y limitaciones del software antiguo.

La tabla completa anterior contiene **los símbolos base** y sus descripciones preparadas para este artículo. Las variantes con superposiciones deben consultarse por separado en la lista de ampliaciones. Si una tabla antigua discrepa de una lista posterior, comprueba la fecha de revisión de cada definición, especialmente en códigos cuyo significado ha cambiado, como `C-`.
