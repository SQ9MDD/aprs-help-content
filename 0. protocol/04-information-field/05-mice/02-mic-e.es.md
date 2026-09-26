---
title: "Mic-E: historia, aplicaciones y formato de datos"
description: "Codificación de posiciones APRS en la dirección de destino y el campo de información AX.25, historia de Mic-E, extensiones y reglas de decodificación."
---

# Mic-E: historia, aplicaciones y formato de datos

**Mic-E (Microphone Encoder)** es un formato de informes de posición APRS compactos. Su característica distintiva es que utiliza tanto el campo de información AX.25 como la dirección de destino. Los seis caracteres de esta dirección contienen la latitud codificada y otros datos; el campo de información completa el informe con longitud, rumbo, velocidad y símbolo. Mic-E no es un protocolo de enlace independiente ni una variante del formato *Compressed Position* basado en Base-91.

El formato permite transmitir muchos datos en una trama corta. Según *APRS Protocol Reference*, la trama Mic-E completa mínima, sin direcciones de repetidores digitales ni datos opcionales, ocupa 25 bytes, sin contar el FCS ni las banderas AX.25. Esta cifra corresponde a toda la trama, no solo al campo de información.

## Historia

Mic-E fue desarrollado por Bob Bruninga, WB4APR, y N3MIM en 1994. Inicialmente era un codificador de micrófono físico que permitía transmitir datos APRS mediante un transceptor. Al prototipo le siguió el kit TAPR Mic-E; el formato también se utilizó en dispositivos PIC Encoder.

Mientras se preparaba la especificación formal de APRS, ya se desarrollaban los equipos Kenwood TH-D7 y TM-D700. Por ello se introdujeron identificadores del tipo de dispositivo en el campo de texto opcional: `>` para el TH-D7 y `]` para el TM-D700. APRS 1.0, publicada en 2000, documentó la codificación de posiciones, los códigos de mensaje Mic-E, la telemetría y el texto de estado.

La aparición de más dispositivos puso de manifiesto las limitaciones del sistema original de identificación. El TM-D710 conservó el identificador de familia `]` y añadió un `=` final para distinguir el modelo. En 2007–2008 se amplió la identificación con categorías para otros dispositivos: `` ` `` indicaba equipos capaces de intercambiar mensajes y `'` identificaba rastreadores unidireccionales. Después se introdujo un par final de fabricante y versión (`Mv`). Los nuevos datos se añadieron al texto sin modificar la codificación básica de posición, conservando la compatibilidad con receptores antiguos.

*Mic-E TYPE CODES* documenta otros identificadores y dispositivos, con actualizaciones hasta el 4 de junio de 2019. Es una lista histórica, no una garantía de identificación completa de todos los modelos posteriores.

## Aplicaciones

Mic-E se ha utilizado en codificadores de micrófono, rastreadores autónomos y radios APRS. Sus informes compactos resultan adecuados para estaciones móviles que transmiten periódicamente posición, rumbo y velocidad. El código de mensaje Mic-E permite además comunicar un estado predefinido o personalizado.

El receptor, el digipeater y el IGate siguen procesando una trama AX.25. Sin embargo, el software APRS debe reconocer que la aparente dirección de destino contiene datos y no un identificador de aplicación TOCALL convencional. Por tanto, no debe interpretarse como el destino de una trama APRS ordinaria.

## Distribución de datos en la trama

Mic-E utiliza una trama UI AX.25 estándar. Los datos se distribuyen así:

| Campo | Información Mic-E |
| --- | --- |
| Dirección de destino, caracteres 1–3 | Tres primeros dígitos de latitud y bits de mensaje A/B/C |
| Dirección de destino, carácter 4 | Cuarto dígito de latitud e indicador N/S |
| Dirección de destino, carácter 5 | Quinto dígito de latitud y desplazamiento de longitud |
| Dirección de destino, carácter 6 | Sexto dígito de latitud e indicador E/W |
| SSID de la dirección de destino | Código histórico de ruta APRS |
| Campo de información, byte 1 | Identificador del tipo de datos Mic-E (DTI) |
| Campo de información, bytes 2–4 | Longitud |
| Campo de información, bytes 5–7 | Velocidad y rumbo |
| Campo de información, bytes 8–9 | Código de símbolo e identificador de tabla de símbolos |
| Bytes posteriores | Telemetría opcional o texto de estado con extensiones |

Los seis caracteres de la dirección de destino se codifican como caracteres válidos de dirección AX.25. Esto no significa que representen el indicativo de la estación destinataria. La dirección de origen sigue identificando a la estación transmisora.

## Latitud en la dirección de destino

Los seis dígitos de latitud corresponden al formato `DDMM.hh`: dos dígitos de grados, dos de minutos y dos de centésimas de minuto. Cada carácter de la dirección codifica un dígito y, según su posición, información adicional.

| Intervalo de caracteres | Dígito de latitud | Información adicional |
| --- | --- | --- |
| `0`–`9` | `0`–`9` | Bit `0`; en posiciones 4–6: S, desplazamiento `+0`, E, respectivamente |
| `A`–`J` | `0`–`9` | Bit `1` de mensaje personalizado en posiciones 1–3 |
| `P`–`Y` | `0`–`9` | Bit `1` de mensaje estándar; en posiciones 4–6: N, desplazamiento `+100`, W |
| `K`, `L`, `Z` | Sin dígito | Valores especiales relacionados con la ambigüedad de posición |

Los caracteres `A`–`K` no se utilizan en las posiciones 4–6. El significado exacto de los caracteres especiales depende de la posición. Por ejemplo, la dirección `S32U6T` codifica la latitud **33°25,64′ N**, los bits de mensaje `100` (estado estándar *Returning*), el desplazamiento de longitud `+0` y el hemisferio occidental. Es un ejemplo tomado directamente del capítulo 10 de la especificación.

### Código de mensaje Mic-E

Los tres primeros caracteres de la dirección contienen los bits A, B y C. Las combinaciones distintas de cero permiten transmitir un mensaje estándar o personalizado. Los tres bits a cero indican *Emergency*.

| A | B | C | Estándar | Personalizado |
| --- | --- | --- | --- | --- |
| 1 | 1 | 1 | M0: Off Duty | C0: Custom-0 |
| 1 | 1 | 0 | M1: En Route | C1: Custom-1 |
| 1 | 0 | 1 | M2: In Service | C2: Custom-2 |
| 1 | 0 | 0 | M3: Returning | C3: Custom-3 |
| 0 | 1 | 1 | M4: Committed | C4: Custom-4 |
| 0 | 1 | 0 | M5: Special | C5: Custom-5 |
| 0 | 0 | 1 | M6: Priority | C6: Custom-6 |
| 0 | 0 | 0 | Emergency | Emergency |

En los mensajes estándar, todos los bits activados deben utilizar codificación estándar; en los personalizados, codificación personalizada. La mezcla de ambas codificaciones para los bits activados produce un tipo de mensaje desconocido. La compatibilidad con mensajes personalizados es opcional y no existía en las unidades Mic-E originales. Un código de mensaje Mic-E no es un mensaje de texto APRS con mecanismo de confirmación.

### SSID de la dirección de destino

La especificación original permite codificar una ruta convencional o una de las rutas APRS genéricas históricas mediante el SSID de la dirección de destino. No debe confundirse este campo con un identificador de aplicación ni interpretarse la codificación histórica de rutas como una recomendación para configurar la red actual. La lista real de direcciones de digipeaters, cuando existe, sigue siendo un campo independiente de la trama AX.25.

## Campo de información

La parte básica del campo de información Mic-E consta de nueve bytes:

```text
DTI | d+28 | m+28 | h+28 | SP+28 | DC+28 | SE+28 | SYMBOL | TABLE
 1  |   2  |   3  |   4  |   5   |   6   |   7   |    8   |   9
```

Los bytes siguientes son opcionales. No todos los bytes de la parte básica son necesariamente caracteres ASCII imprimibles. Si la transmisión o una conversión a texto elimina caracteres de control, se desplazan los bytes restantes y la decodificación resulta incorrecta. La especificación exige descartar un paquete cuyo campo de información tenga menos de nueve bytes.

### DTI e incompatibilidad histórica

Originalmente, `` ` `` indicaba una posición GPS actual y `'` una posición antigua. Sin embargo, el Kenwood TM-D700 utilizaba el apóstrofo para una posición actual. La especificación documenta esta incompatibilidad y propone identificar el TM-D700 mediante `]` al principio del texto de estado. Las primeras unidades beta también utilizaban `0x1c` y `0x1d`.

No debe confundirse el **primer byte del campo de información (DTI)** con el **identificador de dispositivo**, que solo puede aparecer después de la parte básica de nueve bytes. Ambos lugares pueden contener `` ` `` o `'`, pero tienen significados distintos.

### Longitud

La longitud se codifica en los bytes `d+28`, `m+28` y `h+28`. El hemisferio E/W y el desplazamiento `+0` o `+100` deben obtenerse de la dirección de destino.

Decodificación de grados:

1. `d = valor_byte_2 - 28`.
2. Si la dirección indica desplazamiento `+100`, sumar `100`.
3. Si el resultado está entre `180–189`, restar `80`.
4. Si el resultado está entre `190–199`, restar `190`.

Estas correcciones poco habituales se deben a la codificación de los intervalos `0–9` y `100–109` grados. Un byte válido de grados tiene un valor decimal de `38–127`.

Decodificación de minutos:

1. `m = valor_byte_3 - 28`.
2. Si `m >= 60`, restar `60`.

Decodificación de centésimas de minuto: `h = valor_byte_4 - 28`. El resultado representa centésimas de minuto. Se deben conservar los valores originales de los bytes y validar los intervalos obtenidos.

### Velocidad y rumbo

Los tres bytes siguientes codifican velocidad en nudos y rumbo en grados. La especificación admite velocidades de `0–799` nudos y rumbos de `0–360°`; `0` representa un rumbo desconocido o indefinido y `360` indica el norte.

El decodificador reconstruye los valores después de restar `28` a cada uno de los tres bytes:

```text
sp = byte_5 - 28
dc = byte_6 - 28
se = byte_7 - 28

speed  = sp * 10 + floor(dc / 10)
course = (dc % 10) * 100 + se

if speed >= 800: speed -= 800
if course >= 400: course -= 400
```

En la práctica hay que considerar dos métodos históricos de codificación para parte de los valores `SP+28` y `DC+28`, descritos en las tablas de la especificación. El algoritmo anterior contempla ambas variantes mediante las correcciones finales. Los valores fuera de los intervalos permitidos deben rechazarse o marcarse como erróneos, no presentarse automáticamente como una posición válida.

La especificación incluye un ejemplo de **86 nudos y rumbo 194°**: `SP+28` puede ser `t` o `$`, `DC+28` puede ser `]` o `Y`, y `SE+28` vale `z`.

### Símbolo y tabla de símbolos

El octavo byte es el código de símbolo y el noveno identifica la tabla de símbolos o la superposición. Su orden es inverso al de la notación textual APRS habitual, en la que la tabla precede al código de símbolo. El documento sobre la evolución de Mic-E relaciona este orden con la incorporación posterior de la tabla alternativa al formato original.

## Ambigüedad de posición

Mic-E permite reducir deliberadamente la precisión de la posición transmitida. La ambigüedad se indica en los seis caracteres de latitud y debe aplicarse el mismo grado de ambigüedad a la longitud. En el ejemplo `T4SQZZ` de la especificación, los dos últimos dígitos de latitud son ambiguos; al decodificar la longitud deben ignorarse los dos últimos dígitos de precisión correspondientes. El software no debe mostrar estos datos como coordenadas exactas.

## Telemetría y texto de estado opcionales

Después de los nueve bytes básicos puede aparecer **telemetría Mic-E o texto de estado**. La interpretación depende del primer byte de esta parte opcional.

| Indicador | Significado según APRS 1.0 |
| --- | --- |
| `` ` `` | Dos valores de telemetría hexadecimales imprimibles; canales 1 y 3 |
| `'` | Cinco valores de telemetría hexadecimales imprimibles |
| `0x1d` | Cinco valores de telemetría binarios; unidades beta históricas |

En la variante imprimible, cada valor `0–255` ocupa dos caracteres hexadecimales. El ejemplo `'7200007100` de la especificación representa cinco valores: `114`, `0`, `0`, `113`, `0`.

Si la parte opcional es texto de estado, no puede empezar por los caracteres reservados como indicadores de telemetría, ya que se interpretaría incorrectamente. El texto puede contener un comentario, un localizador Maidenhead y altitud. La especificación también permite incluir una posición con formato APRS estándar dentro del texto de estado Mic-E; el receptor puede utilizarla en lugar de la posición codificada en Mic-E. Históricamente esto resultaba útil, entre otros casos, al utilizar un codificador sin receptor GPS.

### Localizador Maidenhead

El texto de estado puede incluir un localizador. Si va seguido de un comentario, la especificación exige separar adecuadamente el texto. Al reconocer el localizador hay que tener en cuenta un posible identificador de dispositivo anterior.

### Altitud en formato `aaa}`

La altitud Mic-E se codifica con tres caracteres Base-91 seguidos de `}`. El nivel de referencia está situado 10.000 m por debajo del nivel medio del mar. Para codificar la altitud en metros:

```text
v = altitud_m + 10000
caracter_1 = floor(v / (91 * 91)) + 33
caracter_2 = floor(v / 91) % 91 + 33
caracter_3 = v % 91 + 33
```

La decodificación invierte el procedimiento y resta `10000`. La especificación incluye el ejemplo de 200 pies (unos 61 m), codificados como `"4T}`. Las recomendaciones posteriores sitúan el campo opcional `aaa}` inmediatamente después del identificador de tipo de dispositivo, antes del resto del texto.

## Evolución del campo de texto

Los dispositivos originales transmitían texto de estado sin un identificador elaborado. Las extensiones posteriores se añadieron a esa misma zona para que los receptores antiguos pudieran seguir decodificando la posición básica. Los documentos de WB4APR describen estos elementos:

| Elemento | Significado | Posición |
| --- | --- | --- |
| `T` | Identificador opcional de tipo de dispositivo | Inicio del texto de estado |
| `aaa}` | Altitud opcional Base-91 | Inmediatamente después de `T`, si aparece |
| `FFF.FFFMHz` | Frecuencia opcional | Inicio del texto propiamente dicho, después de la posible altitud |
| Texto libre | Comentario de la estación | Después de los campos que requieren una posición fija |
| `/A=xxxxxx` | Notación antigua de altitud | Más adelante en el comentario, si se utiliza |
| `!DAO!` | Precisión ampliada de posición y datum | Se recomienda cerca del final del texto |
| `Mv` | Identificador de fabricante y versión | Final del texto en el sistema de identificación más reciente |

Esta tabla combina elementos de distintas etapas del desarrollo de Mic-E. No todos son obligatorios ni aparecen simultáneamente. Aquí `!DAO!` designa la familia de extensiones descrita en la documentación APRS 1.2, no un valor literal que deba transmitirse. Su significado exacto y validación deben seguir la documentación de dicha extensión.

Cuando aparecen altitud y frecuencia, el orden recomendado es `T`, `aaa}`, `FFF.FFFMHz`, seguido del texto y de los demás elementos. El documento de 2008 recomienda además un espacio después de la frecuencia para mantener la compatibilidad con los equipos Yaesu FTM-350 antiguos.

## Identificación del dispositivo

El identificador de dispositivo pertenece al **texto opcional**, no a la dirección de destino ni al DTI. La especificación original incluía, entre otros, `>` para el Kenwood TH-D7 y `]` para el TM-D700. Los acuerdos posteriores ampliaron estos identificadores:

| Forma | Interpretación en la documentación de WB4APR |
| --- | --- |
| `>...` | Familia Kenwood TH-D7 |
| `]...` | Kenwood TM-D700 |
| `]...=` | Kenwood TM-D710 |
| `>...=` | Kenwood TH-D72 |
| `>...^` | Kenwood TH-D74 |
| `` `...Mv `` | Otro dispositivo Mic-E capaz de intercambiar mensajes |
| `'...Mv` | Otro rastreador Mic-E unidireccional |

En esta tabla, los puntos suspensivos representan texto de longitud variable, no un número concreto de bytes. En dispositivos posteriores, la combinación del carácter inicial y `Mv` final puede identificar al fabricante y al modelo. *Mic-E TYPE CODES* enumera, entre otros, modelos Yaesu, Byonics, AnyTone y SCS, pero su última actualización es de 2019. Los identificadores ausentes de la tabla conocida no deben provocar el rechazo de una posición Mic-E válida.

También hay que mantener la compatibilidad al mostrar el texto: un identificador de tipo reconocido puede eliminarse del comentario visible para el operador y mostrarse por separado como información del dispositivo. No deben eliminarse indiscriminadamente caracteres finales desconocidos, pues podrían formar parte del comentario.

## Ejemplos de decodificación

### Dirección de destino `S32U6T`

Ejemplo de APRS Protocol Reference:

- Seis dígitos: `332564`, es decir, **33°25,64′**.
- El cuarto carácter `U` indica el hemisferio norte.
- El quinto carácter `6` indica desplazamiento de longitud `+0`.
- El sexto carácter `T` indica el hemisferio occidental.
- Bits de mensaje: `100`, estado estándar **M3: Returning**.

La dirección por sí sola no contiene la longitud completa: los valores numéricos deben obtenerse del campo de información.

### Longitud del campo de información

En el ejemplo de la especificación, los bytes `(`, `_`, `f` tienen los valores decimales `40`, `95` y `102`. Con desplazamiento `+100` e indicador W, el decodificador obtiene:

```text
grados:      40 - 28 + 100 = 112
minutos:     95 - 28 - 60 = 7
centesimas: 102 - 28 = 74
resultado:   112°07,74′ W
```

### Velocidad y rumbo del campo de información

El mismo ejemplo utiliza los bytes `n`, `"`, `O`:

```text
sp = 110 - 28 = 82
dc =  34 - 28 = 6
se =  79 - 28 = 51

speed  = 82 * 10 + 0 = 820; 820 - 800 = 20 nudos
course = 6 * 100 + 51 = 651; 651 - 400 = 251°
```

Resultado: **20 nudos y rumbo 251°**. Los bytes finales `j/` indican el símbolo de vehículo todoterreno de la tabla principal de símbolos. Los ejemplos ilustran la decodificación de partes individuales del formato; no deben combinarse en una trama nueva sin comprobar todos los campos.

## Recomendaciones de implementación

Un decodificador Mic-E debe procesar bytes, sin suponer que todo el campo de información es texto ordinario. Orden de procesamiento recomendado:

1. Validar la trama AX.25 y la longitud del campo de información (al menos nueve bytes).
2. Reconocer el DTI Mic-E y decodificar los seis caracteres de la dirección de destino, distinguiendo mensajes estándar, personalizados y desconocidos.
3. Reconstruir las coordenadas de ambos campos y tener en cuenta la ambigüedad de posición.
4. Decodificar velocidad, rumbo, símbolo y tabla de símbolos.
5. Reconocer la telemetría opcional o el texto de estado sin confundir sus indicadores con el DTI.
6. En el texto de estado, reconocer únicamente las extensiones y los identificadores de dispositivo cuya sintaxis se conozca; conservar los datos no reconocidos.
7. No modificar la dirección de destino codificada al retransmitir el paquete. Cambiar sus caracteres puede alterar las coordenadas, el código de mensaje o los indicadores de hemisferio.

Al convertir los datos a texto, hay que prestar especial atención a los bytes Mic-E no imprimibles. La pérdida de un solo byte puede impedir la decodificación correcta de todo el informe. El software también debe contemplar las desviaciones históricas de ciertos equipos, en vez de suponer que el DTI determina siempre por sí solo la vigencia de la posición GPS.

## Fuentes y alcance

- *APRS Protocol Reference*, APRS Protocol Version 1.0, Document Version 1.0.1, 29 de agosto de 2000, capítulo 10: **Mic-E Data Format**. Codificación básica de la dirección de destino, campo de información, mensajes, ambigüedad de posición, telemetría y texto.
- Bob Bruninga, WB4APR, [*Mic-E TEST EXAMPLES*](https://www.aprs.org/aprs12/mic-e-examples.txt), 10 de diciembre de 2008. Evolución del campo de texto, orden de las extensiones y ejemplos de compatibilidad con versiones anteriores.
- Bob Bruninga, WB4APR, [*Mic-E TYPE CODES*](https://www.aprs.org/aprs12/mic-e-types.txt), actualizado el 4 de junio de 2019. Identificadores históricos y posteriores de dispositivos.

Los documentos de 2008 y 2019 amplían temas que la especificación original de 2000 no cubre por completo. La descripción de identificadores no es un registro exhaustivo de todas las implementaciones actuales de Mic-E. Las reglas generales de extensiones APRS, como la sintaxis completa de `!DAO!` y la notación universal de frecuencia, son temas independientes; aquí solo se trata su ubicación en el texto de estado Mic-E.
