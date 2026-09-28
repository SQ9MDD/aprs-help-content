---
title: Rutas APRS en la práctica
---

La ruta APRS determina qué digipeaters pueden retransmitir una trama de radio y en qué orden. Puede indicar estaciones concretas o utilizar alias admitidos por varios digipeaters. El recorrido real de la transmisión depende tanto de la ruta indicada como de la configuración de las estaciones y de sus zonas de cobertura.

Este artículo aborda el direccionamiento clásico AX.25, el mecanismo `WIDEn-N`, las rutas con y sin trazado, los alias regionales y para eventos, el enfoque actual de las retransmisiones mediante digipeaters fill-in y el caso particular de los digipeaters por satélite. Los ejemplos muestran posibles transformaciones de la trama suponiendo determinadas configuraciones de los digipeaters. No implican que los alias presentados funcionen en toda la red APRS.

## 1. Estructura y representación de la ruta

La ruta se encuentra en el campo de direcciones de la trama AX.25, después de las direcciones de destino y origen. En la representación legible TNC2, sus elementos se separan mediante comas:

```text
SQ9MDD-9>APRS,WIDE2-1:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SP2-2:...
```

`SQ9MDD-9` es el origen de la trama, mientras que `APRS` es un ejemplo de dirección de destino (TOCALL), no el nombre de un digipeater. La ruta está formada únicamente por las direcciones situadas después de la primera coma. Si no hay ninguna, la trama se transmite sin solicitar retransmisiones:

```text
SQ9MDD-9>APRS:...
```

Los equipos pueden denominar esta configuración `DIRECT`. Sin embargo, no es un salto adicional ni un alias que deba incluirse en la trama. Las estaciones lejanas pueden recibir una trama sin ruta, y los IGates que la escuchen directamente pueden enviarla a APRS-IS.

En el procesamiento normal, el digipeater examina el **primer elemento no utilizado** de la ruta. Los elementos posteriores no están activos hasta que se hayan procesado los anteriores. Existen excepciones, como los mecanismos configurados expresamente para *preemptive digipeating*, que se explican más adelante.

### Límites de AX.25

Cada dirección de una trama AX.25 por radio ocupa siete bytes: seis para el indicativo o alias (rellenados con espacios si es más corto) y uno que contiene, entre otros datos, el SSID de cuatro bits y los indicadores de dirección. El SSID de AX.25 admite el intervalo `0..15`. El formato tradicional utilizado por APRS permite un máximo de ocho direcciones de digipeaters. Eso no significa que deban utilizarse las ocho: cada posición adicional aumenta la longitud de la trama y el trazado puede consumir otras posiciones libres.

Al diseñar alias deben distinguirse dos casos:

- Un alias simple, como `ARISS` o `RAJD`, cabe en los seis caracteres del campo de dirección.
- En un alias de tipo `n-N`, como `RAJD2-2`, la parte `RAJD2` ocupa el campo de nombre de seis bytes (`RAJD` más el dígito `n`), mientras que `-2` es el SSID que actúa como contador `N`.

Por tanto, si `n` tiene un solo dígito, el nombre base de este tipo de alias no debe superar los cinco caracteres. La representación textual no elimina las limitaciones del campo binario de direcciones AX.25.

### El asterisco y el bit H

Cada dirección de digipeater dispone de su propio bit **H** (*has been repeated*), que indica que ese elemento de la ruta ya se ha utilizado. En la representación habitual de monitorización TNC2, el asterisco aparece **únicamente después del último elemento utilizado**; se sobreentiende que todos los anteriores también se han utilizado:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...   # antes de la retransmisión
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...  # después del primer digi
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...  # después del segundo digi
```

La última línea no significa que `SR5AAA` no haya retransmitido la trama. En AX.25 binario, ambos bits H están activados. Algunos programas de diagnóstico muestran un asterisco en todas las direcciones utilizadas, pero esa no es la representación abreviada TNC2 habitual. El carácter textual `*` representa el bit H; no se transmite como parte de la dirección AX.25.

## 2. Origen del New-N Paradigm

Las redes APRS antiguas utilizaban, entre otros, los alias `RELAY`, `WIDE`, `TRACE` y `TRACEn-N`. Permitían ampliar la cobertura, pero algunas implementaciones de aquella época generaban demasiados duplicados de las mismas tramas. Además, el `WIDEn-N` original a menudo no registraba los indicativos de los digipeaters, lo que dificultaba el análisis del tráfico y la planificación de la red.

La iniciativa **New-N Paradigm**, iniciada a finales de 2004, reorganizó estos mecanismos:

- El antiguo `RELAY` fue sustituido por `WIDE1-1`, manteniendo la posibilidad de que las estaciones móviles utilizaran digipeaters fill-in domésticos sencillos.
- Se extendió el uso de `WIDEn-N`, con su contador de retransmisiones restantes, en lugar del antiguo alias individual `WIDE`.
- `WIDEn-N` comenzó a procesarse en modo de trazado, implementado históricamente, entre otros mecanismos, mediante `UITRACE`.
- Se conservó el mecanismo sin trazado `UIFLOOD`, entre otros usos, para redes regionales `SSn-N`.
- Limitar los valores excesivos de los contadores y eliminar los duplicados se convirtió en una parte esencial de la configuración de los digipeaters.

Los nombres `UITRACE` y `UIFLOOD` proceden de determinadas implementaciones de TNC. Otros programas pueden ofrecer funciones equivalentes con nombres diferentes. En la actualidad, `RELAY`, el antiguo `WIDE` y `TRACE` deben considerarse principalmente elementos históricos o de configuraciones heredadas, no equivalentes intercambiables del `WIDEn-N` moderno.

## 3. Enrutamiento mediante indicativos concretos y alias simples

La ruta más sencilla indica directamente un digipeater. Los indicativos sucesivos establecen el orden de retransmisión:

```text
SQ9MDD-9>APRS,SR5AAA,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA*,SR5BBB:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...
```

`SR5BBB` no retransmitirá la trama en la primera etapa por el mero hecho de haber recibido la transmisión. Primero debe procesarse la dirección `SR5AAA`. Las rutas explícitas sirven para establecer deliberadamente un recorrido a través de estaciones concretas, por ejemplo, en comunicaciones entre puntos.

Un digipeater también puede atender un **alias simple**, como `RAJD`. Al recibir una trama dirigida mediante `RAJD`, puede sustituir el alias por su propio indicativo o conservarlo e insertar su indicativo por separado. La transformación depende de la implementación y de la configuración:

```text
SQ9MDD-9>APRS,RAJD:...
SQ9MDD-9>APRS,SR5AAA*:...       # alias sustituido por el indicativo
```

En la segunda variante, la identificación de la estación puede añadirse por separado, consumiendo otro campo de dirección. Un alias simple no contiene un contador de retransmisiones restantes. Por ello, no debe suponerse que funciona como `WIDE2-2` ni que todas las estaciones que reconocen el mismo alias aplican reglas de retransmisión idénticas.

## 4. Cómo interpretar `n-N`

En la familia de alias `WIDEn-N`, `n` es el dígito anterior al guion y `N` es el valor del SSID posterior:

```text
WIDE2-2
    ^ ^
    n N
```

`n` identifica la clase del alias y su número inicial de saltos declarado; **`N` cuenta las retransmisiones que aún quedan**. Normalmente la trama inicia su recorrido con `n = N`, pero `WIDE2-1` también es válido: pertenece a la familia `WIDE2`, aunque solo solicita un salto más.

Cada retransmisión que cumple las reglas reduce `N` en uno. Una secuencia simplificada sin identificar los digipeaters sucesivos sería:

```text
WIDE2-2 -> WIDE2-1 -> WIDE2*
SP2-2   -> SP2-1   -> SP2*
```

Una vez agotado el contador, el elemento puede aparecer sin `-0`, porque en la representación textual se omite el SSID igual a cero. El bit H lo marca entonces como utilizado. Las distintas implementaciones conservan o sustituyen de forma diferente los alias agotados; por eso, los registros reales no siempre muestran exactamente los mismos campos.

`WIDE2-2` no garantiza **solo dos transmisiones en toda la red**. Permite como máximo dos retransmisiones sucesivas *en una determinada rama de la ruta del paquete*. Si varios digipeaters escuchan la trama original, cada uno puede crear su propia rama y el número total de transmisiones será mayor.

## 5. Rutas con trazado (trace)

En una ruta con trazado, cada digipeater deja información que permite identificarlo. Es una característica esencial del uso actual de `WIDEn-N` conforme al New-N Paradigm: permite reconstruir el trayecto seguido por una copia recibida de la trama.

Ejemplo de una rama de trazado `WIDE2-2`:

```text
SQ9MDD-9>APRS,WIDE2-2:...
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...
```

En este ejemplo, los digipeaters insertan sus indicativos y el alias agotado permanece en la ruta. Otra implementación correctamente configurada puede sustituir el alias por el último indicativo y producir una representación final más corta, como `SR5AAA,SR5BBB*`. Por tanto, al analizar registros es necesario tener en cuenta el comportamiento del programa concreto y no suponer que todas las cabeceras presentan el mismo formato.

El trazado facilita el diagnóstico, pero cada indicativo insertado consume otros siete bytes del campo de direcciones. En rutas largas puede agotarse el espacio para nuevas direcciones.

## 6. Rutas sin trazado (flood)

En el modo sin trazado, un digipeater reduce el contador del alias, pero **no inserta su indicativo en la ruta**. No se trata de otro protocolo ni de un formato particular del campo de información APRS. Es una forma de procesar las direcciones en los digipeaters, asociada históricamente a `UIFLOOD`.

Por ejemplo, consideremos una red con un alias regional `SP` configurado para funcionar sin trazado:

```text
SQ9MDD-9>APRS,SP2-2:...
SQ9MDD-9>APRS,SP2-1:...
SQ9MDD-9>APRS,SP2*:...
```

Las tres líneas pueden corresponder a la misma trama retransmitida por distintas estaciones. La cabecera final no revela qué digipeaters participaron. Un receptor que escucha `SP2-1` no puede determinar únicamente por la ruta qué estación realizó el salto anterior.

Características principales del modo flood:

- La ruta no crece con el indicativo de otro digi después de cada retransmisión.
- La ausencia de un trazado completo dificulta reconstruir el recorrido de la transmisión.
- Siguen siendo necesarios los límites de los contadores y la eliminación de duplicados.
- Flood no significa difusión ilimitada: su cobertura depende del grupo de estaciones que atienden el alias y de sus configuraciones.

### Flood con identificación parcial

El funcionamiento sin trazado no implica necesariamente perder toda la información sobre las estaciones intermedias. Algunas configuraciones históricas de `UIFLOOD` con la opción `ID` permitían conservar, entre otros datos, los indicativos del primer y del último digipeater que procesaban una ruta regional. En una ruta mixta `WIDE1-1,SSn-N`, el primer indicativo también puede proceder del elemento `WIDE1-1` procesado por separado.

Este mecanismo **no proporciona un trazado completo**. El método de identificación y la posición de los alias conservados dependen del TNC concreto. Tampoco debe identificarse automáticamente cualquier alias regional con flood: exactamente el mismo alias puede configurarse para funcionar con trazado.

## 7. Rutas de uno o varios elementos y digipeaters fill-in

El número de elementos es la cantidad de posiciones de la ruta separadas por comas. El número de saltos depende de sus contadores y de las reglas de procesamiento. Un solo elemento puede solicitar más de una retransmisión:

| Ruta | Número de elementos | Saltos solicitados en una rama |
| --- | ---: | ---: |
| `WIDE2-1` | 1 | 1 |
| `WIDE2-2` | 1 | 2 |
| `SP2-2` | 1 | 2 |
| `WIDE1-1,WIDE2-1` | 2 | 2 |
| `WIDE1-1,WIDE2-2` | 2 | 3 |
| `SP1-1,SP2-2` | 2 | 3, si se admiten ambos elementos |

En el procesamiento normal, el segundo elemento solo entra en funcionamiento cuando se agota el primero. Esto no significa que cada elemento tenga que ser atendido por una *categoría* diferente de dispositivos: lo determinan los alias configurados.

### Origen de `WIDE1-1`: fill-in locales para estaciones móviles

En configuraciones APRS antiguas, las estaciones móviles utilizaban, entre otras posibilidades, rutas que comenzaban con `RELAY`. Una estación doméstica cercana con un TNC sencillo configurado como **digi fill-in** podía realizar el primer salto, incluso sin las funciones de un digipeater regional completo. Era especialmente útil para estaciones móviles y portátiles de menor potencia, con antenas menos eficientes o que circulaban por zonas con lagunas de cobertura. Sin embargo, las rutas históricas que combinaban `RELAY` y `WIDE` favorecían una cantidad excesiva de duplicados.

Con la introducción del New-N Paradigm, `RELAY` fue sustituido por **`WIDE1-1`**. La solución tenía en cuenta las posibilidades de los dispositivos domésticos sencillos que ya existían, incluidos los diseños mini-digi. Esos TNC no necesitaban comprender el algoritmo `WIDEn-N` ni reducir su contador: bastaba con reconocer el alias exacto `WIDE1-1`, retransmitir una vez la trama y marcar ese elemento como utilizado. El resto de la ruta quedaba a cargo de un digipeater completo con soporte de `WIDEn-N`.

De ahí procede la construcción de una ruta destinada a móviles que utilizan fill-in locales:

```text
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1:...          # transmite la estación móvil
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...           # fill-in doméstico, primer salto
SQ9MDD-9>APRS,SR5AAA,SR5BBB,WIDE2*:...      # digi regional, segundo salto
```

Aquí, `SR5AAA` representa un fill-in sencillo que sustituye `WIDE1-1` por su indicativo, mientras que `SR5BBB` procesa el `WIDE2-1` restante. Un dispositivo más avanzado puede conservar también el alias `WIDE1` ya utilizado, con lo que la cabecera final resulta más larga. En cambio, un digipeater sencillo que trata `WIDE1-1` como un alias ordinario puede marcarlo como utilizado sin cambiar su SSID; en ese caso, es el bit H, y no necesariamente la representación visible `WIDE1-0`, el que da por terminado el primer elemento.

**Un fill-in clásico y sencillo que solo admite `WIDE1-1` no debería procesar `WIDE2-1` ni los demás elementos de la ruta más amplia.** Su función es reenviar una sola vez el paquete desde una laguna local de cobertura hacia un digipeater regional. Instalar estos repetidores donde las estaciones móviles ya tienen buen acceso a la red regional añade copias innecesarias de las tramas al canal compartido. Los fill-in modernos pueden aplicar otras reglas, condicionando la transmisión tanto a la forma de recepción como al tráfico observado; se explican a continuación.

Sin embargo, `WIDE1-1` no está reservado exclusivamente para fill-in sencillos. Un digipeater regional completo también puede procesar el alias si escucha directamente la estación móvil. En ese caso, el primer salto de `WIDE1-1,WIDE2-1` se consume sin participación de una estación doméstica; **esto no añade otro salto a los dos solicitados por la ruta**.

### Por qué `WIDE1-1` no era una ruta recomendada para estaciones domésticas

Hay que distinguir entre **un fill-in doméstico que atiende el alias `WIDE1-1`** y **una estación doméstica que transmite sus propias balizas con `WIDE1-1` en la ruta**. En las recomendaciones originales del New-N Paradigm, el primer caso estaba destinado principalmente a los móviles, mientras que las estaciones fijas ordinarias debían utilizar rutas `WIDEn-N` con un número de saltos adaptado a la región, históricamente a menudo `WIDE2-2` en las condiciones descritas por los autores de la iniciativa. `WIDE1-1` no se diseñó como primer elemento predeterminado de la ruta de las estaciones domésticas.

El motivo es la topología. Una estación fija suele tener una ubicación estable y una instalación de antena más favorable, por lo que a menudo puede alcanzar directamente un digipeater regional. Añadir `WIDE1-1` activa también fill-in cercanos que la estación no necesita, cuyas retransmisiones pueden solaparse con la del digipeater regional. Cambiar `WIDE2-2` por `WIDE1-1,WIDE2-1` no aumenta el número de saltos permitido, pero abre el primero a otro grupo de retransmisores.

No es una prohibición impuesta por AX.25. Una estación fija excepcional, situada en una laguna real de cobertura, puede utilizar técnicamente un fill-in si lo justifican la topología local y los acuerdos entre operadores. Sin embargo, hay que distinguir esa excepción del **propósito y las recomendaciones originales**: `WIDE1-1` se introdujo para ayudar a las estaciones móviles mediante digipeaters locales sencillos, no como ruta universal para todos los equipos APRS.

La ruta `SP1-1,SP2-2` funciona de manera análoga en cuanto al orden de los elementos y los contadores **siempre que** la red local disponga de las reglas adecuadas para ambos. La mera presencia de `SP1-1` no convierte al primer digipeater en un fill-in. Esa función requiere una coordinación específica entre operadores cuando se utilizan alias regionales.

### Fill-in actuales: `direct-only` y `viscous delay`

La construcción histórica `WIDE1-1,WIDE2-1` resolvía un problema concreto: un mini-digi doméstico sencillo reconocía un solo alias y retransmitía la trama sin poder determinar si un digipeater regional mayor ya lo había hecho. El software moderno también puede decidir si debe retransmitir según el origen de la trama y el tráfico observado en el canal. Esto no modifica las reglas de direccionamiento AX.25, sino que permite aprovechar los mecanismos disponibles de manera más selectiva.

Dos técnicas complementarias son:

- **`direct-only`**: el digipeater considera únicamente las tramas recibidas directamente del emisor y no las copias que otro digi ya ha retransmitido. Así se evita que el repetidor local se convierta automáticamente en otro eslabón de cada ruta que encuentra.
- **`viscous delay`**: el digipeater retiene durante un breve intervalo configurado una trama que cumple las condiciones. Si durante ese tiempo escucha una retransmisión equivalente de otro digipeater, puede cancelar su propia transmisión. Si no observa ninguna retransmisión, envía la trama pendiente conforme a sus reglas.

Estas técnicas no constituyen un nuevo formato de ruta. APRX ya documentaba el *viscous digipeater* en 2009, y su modo `directonly` puede combinarse con `viscous-delay`. Lo importante, por tanto, no es la fecha de creación de los algoritmos, sino la posibilidad de utilizarlos en lugar de la retransmisión incondicional propia de los mini-digi sencillos.

Por ejemplo, un fill-in local inteligente se puede **configurar deliberadamente** para procesar `WIDE2-2` recibido de forma directa, con retardo y control de duplicados. Si el digi regional retransmite antes la trama y la estación local escucha esa retransmisión durante la espera, el fill-in renuncia a su propio TX. Si el digi regional no reenvía la trama, el fill-in local puede realizar el primer salto, dejando `WIDE2-1` para el procesamiento posterior. Es un ejemplo de una posible política de red, **no el comportamiento predeterminado de todos los digipeaters**:

```text
SQ9MDD-9>APRS,WIDE2-2:...  # transmisión móvil

# Caso A: el digi regional recibe directamente la estación
# El digi regional retransmite; el fill-in escucha la copia y cancela su TX.

# Caso B: el digi regional no recibe directamente la estación
# El fill-in no escucha otra copia y retransmite después del retardo:
SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...
```

En una red configurada así, no solo importa **qué alias se ha indicado**, sino también **si un digipeater concreto necesita realmente transmitir**. En ese caso, el elemento inicial independiente `WIDE1-1` puede dejar de ser necesario. No obstante, sigue siendo imprescindible cuando los dispositivos locales solo admiten ese alias. Del mismo modo, `direct-only` y `viscous delay` no autorizan automáticamente a retransmitir una trama sin ruta: el digi debe tener una regla de direccionamiento apropiada o un comportamiento especial configurado expresamente.

El retardo no garantiza eliminar todos los duplicados. Si el fill-in no escucha la transmisión del digipeater regional, no puede deducir por ello que esa retransmisión no se haya producido. Además, un TX retardado aumenta el tiempo de entrega, y un número excesivo de repetidores similares todavía puede sobrecargar el canal compartido. Los parámetros y alias admitidos deben elegirse según la topología real de la red.

De ello se desprende un cambio importante de enfoque: **la recomendación histórica de rutas para móviles era una forma de adaptarse a las limitaciones de la infraestructura de entonces, no una necesidad permanente del protocolo**. En redes con digipeaters inteligentes, la política de retransmisión puede tener más importancia que la división tradicional entre una ruta especial para móviles con fill-in y otra para estaciones que acceden directamente a un digi regional. Sin embargo, el campo de ruta sigue determinando qué retransmisiones están permitidas.

## 8. Alias regionales y para eventos

Un alias regional permite definir un *grupo lógico de digipeaters* encargado de gestionar un tráfico determinado. El concepto clásico `SSn-N` surgió para que las tramas pudieran alcanzar zonas lejanas de una región sin implicar a toda la red `WIDEn-N` vecina. Las convenciones de nombres y las configuraciones varían según el país.

Ejemplos de posibles representaciones:

```text
SP2-2
WM2-2
```

En ambos casos, `SP` y `WM` son los nombres base de los alias, no límites administrativos que el protocolo reconozca automáticamente. Solo funcionan donde los operadores hayan configurado su soporte. Además, un mismo alias puede procesarse en modo trace o flood, dependiendo de la configuración.

### Alias para eventos, ejercicios y actividades

El mismo mecanismo puede utilizarse para un rally, ejercicios de comunicaciones, una actividad de radioaficionados o una red temporal de campo. Supongamos que varios digipeaters coordinados atienden el alias base `RAJD` en modo sin trazado:

```text
SQ9MDD-9>APRS,RAJD2-2:...
SQ9MDD-9>APRS,RAJD2-1:...
SQ9MDD-9>APRS,RAJD2*:...
```

Se trata de **un ejemplo de diseño**, no de un alias APRS existente y universalmente admitido. Al terminar el evento, los operadores pueden desactivar `RAJD` sin afectar al funcionamiento normal de `WIDEn-N`. Si es necesario conocer el recorrido de los mensajes, el mismo alias acordado puede procesarse en modo de trazado.

Un ejemplo histórico de uso similar es `TEMPn-N`, descrito en el New-N Paradigm para digipeaters temporales empleados, entre otras ocasiones, durante Field Day y en emergencias. Esto no significa que todos los dispositivos APRS tengan habilitado de fábrica el alias `TEMP`.

Para crear un alias hay que acordar, como mínimo, su nombre, las estaciones participantes, el modo de trazado, los valores de contador permitidos, el filtrado de duplicados y el período de funcionamiento. También deben evitarse las colisiones de nombres con la red local y con las reglas predeterminadas del software utilizado.

**La separación mediante un alias es lógica, no radioeléctrica.** En la misma frecuencia, cada retransmisión adicional sigue ocupando el canal compartido. Un digipeater configurado con varios alias también puede reenviar otras tramas conforme a sus demás reglas. El carácter local del alias no garantiza por sí mismo aislamiento del tráfico ni confidencialidad.

## 9. Alias satelitales

Un digipeater instalado en un satélite o a bordo de la Estación Espacial Internacional también puede direccionarse mediante el campo de ruta AX.25. En este contexto son especialmente importantes **los alias simples y los indicativos de estaciones concretas**, no las rutas terrestres complejas `WIDEn-N`.

| Dirección en la ruta | Características |
| --- | --- |
| `ARISS` | Alias compartido admitido por la ISS y algunos otros satélites, según su configuración actual. |
| `APRSAT` | Alias compartido histórico descrito en materiales APRS; no debe suponerse que todos los satélites lo admiten actualmente. |
| `RS0ISS`, `NA1SS` | Indicativos utilizados por la estación de la ISS; la posibilidad de utilizarlos como direcciones digi depende de los equipos y la configuración activos. |
| Indicativo de un satélite concreto | Dirección indicada en la documentación del repetidor correspondiente, como `W3ADO-1` o `PCSAT-1` para NO-44. |

Ejemplo de uso de un alias compartido con un satélite que lo admita:

```text
SQ9MDD-7>APRS,ARISS:...
```

Aquí `ARISS` es una única dirección simple. No debe añadirse sin una justificación específica una ruta terrestre `WIDE1-1,WIDE2-1`. Después de la retransmisión satelital, numerosas estaciones terrestres y pasarelas satelitales pueden recibir la trama, pero eso no cambia el significado del propio alias.

Los materiales históricos de APRS describían los alias compartidos `ARISS`, `APRSAT` y `WIDE`, así como experimentos con retransmisiones satelitales más complejas. No representan configuraciones universales actuales. La lista de AMSAT del **7 de septiembre de 2026** incluía para la ISS, entre otros, `RS0ISS`, `NA1SS` y `ARISS`, y también direcciones específicas de otros satélites. Antes de transmitir, hay que comprobar el estado actual del satélite concreto, la dirección admitida, la frecuencia y el tipo de modulación; aparecer en la lista no garantiza la disponibilidad del servicio durante un pase determinado.

## 10. Limitación de duplicados y retransmisiones excesivas

El contador `N` limita la longitud de una rama de la ruta, pero no el número total de copias en la red. Si `SR5AAA` y `SR5BBB` escuchan directamente una trama `WIDE2-2`, ambos pueden realizar el primer salto. Después, distintos digipeaters vecinos pueden retransmitir esas copias por segunda vez. Por tanto, solicitar dos saltos no equivale a realizar únicamente dos transmisiones RF.

Los digipeaters correctamente configurados deben detectar duplicados retransmitidos recientemente, normalmente a partir del origen, el destino y el campo de información, independientemente de los cambios de ruta. El algoritmo exacto, el tiempo de retención y las excepciones dependen de la implementación. Sin embargo, la eliminación de duplicados no evita todas las colisiones: dos estaciones que reciben simultáneamente la primera copia pueden decidir transmitir de forma independiente.

También son importantes las siguientes medidas:

- Limitar los valores `n` y `N` admitidos, lo que incluye rechazar o reducir rutas excesivamente largas (*trapping*).
- Comprobar que el contador sea coherente, por ejemplo, impidiendo `WIDE1-7` dentro de las reglas `WIDEn-N`.
- Evitar que retransmita una trama un digipeater cuyo indicativo ya figura en la parte utilizada de la ruta.
- Controlar la frecuencia de las balizas propias y las retransmisiones innecesarias en el canal compartido.

Un contador demasiado elevado en un alias regional también puede sobrecargar la red dentro de esa región. La elección de un valor mayor o menor exige conocer la topología real y los acuerdos locales, no únicamente el alcance nominal del transmisor.

## 11. Preemptive digipeating

Normalmente, un digipeater procesa únicamente el primer elemento no utilizado de la ruta. Algunas implementaciones ofrecen **preemptive digipeating**, es decir, la capacidad de reconocer su propio indicativo o un alias especial en una posición posterior y modificar adecuadamente los elementos anteriores.

Este mecanismo puede resultar útil en redes especiales diseñadas expresamente, cuando una trama llega directamente a una estación situada más adelante en la ruta prevista. No debe suponerse que forma parte del comportamiento de cualquier digipeater APRS. El resultado depende de la implementación y del modo seleccionado para omitir posiciones anteriores. Todos los ejemplos ordinarios de este artículo suponen un procesamiento sin preemption.

## 12. `RFONLY`, `NOGATE` y el paso a APRS-IS

Al final de una ruta pueden aparecer estos indicadores:

```text
SQ9MDD-9>APRS,WIDE2-1,RFONLY:...
SQ9MDD-9>APRS,WIDE1-1,WIDE2-1,NOGATE:...
```

`RFONLY` y `NOGATE` son **indicadores destinados a las pasarelas**, no solicitudes adicionales de retransmisión. No aumentan el número de saltos. Su presencia en el campo de ruta no elimina los límites de AX.25 relativos al número y la longitud de las direcciones.

La especificación APRS-IS los menciona como motivos para no reenviar una trama desde RF hacia Internet, pero **el tratamiento de ambos indicadores por parte de un IGate es opcional**. Por tanto, no puede garantizarse que todas las pasarelas bloqueen el paquete. Tampoco proporcionan privacidad a la transmisión por radio.

Después de reenviar correctamente una trama a APRS-IS, la pasarela añade el *q-construct* correspondiente, por ejemplo `qAR` seguido de su propio indicativo o `qAO` en el caso de una pasarela exclusivamente receptora. Son elementos de la cabecera de Internet, **no forman parte de la ruta AX.25 por radio**. No deben aparecer en una trama emitida directamente por un transmisor APRS. Los detalles corresponden al artículo específico sobre el funcionamiento de los IGates.

## 13. Interpretación de tramas de ejemplo

| Representación | Qué puede deducirse |
| --- | --- |
| `SQ9MDD-9>APRS:...` | El emisor no solicitó retransmisiones por digi. Sigue siendo posible la recepción directa por un IGate. |
| `SQ9MDD-9>APRS,WIDE2-1:...` | Se solicita un salto adicional mediante una estación compatible con la familia `WIDE2`. |
| `SQ9MDD-9>APRS,SR5AAA*,WIDE2-1:...` | `SR5AAA` es la última dirección utilizada y el elemento `WIDE2-1` sigue activo. |
| `SQ9MDD-9>APRS,SR5AAA,SR5BBB*:...` | Ambas direcciones indicadas están utilizadas; el asterisco aparece solo en la última. |
| `SQ9MDD-9>APRS,SP2-1:...` | Queda un salto para el alias `SP`, pero en modo flood no puede recuperarse el indicativo del digi anterior. |
| `SQ9MDD-9>APRS,SP2*:...` | El elemento `SP2` está agotado; eso no determina cuántas copias recibieron otras estaciones. |
| `SQ9MDD-9>APRS,ARISS:...` | El emisor indicó el alias simple `ARISS`; su funcionamiento depende de la configuración actual del satélite receptor. |
| `SQ9MDD-9>APRS,WIDE2-1,NOGATE:...` | Se solicita un salto y se indica la petición de no enviar la trama a APRS-IS. |

Al analizar paquetes conviene separar tres cuestiones: **qué ruta indicó el emisor**, **cómo la transformó realmente el digipeater receptor** y **qué añadió después la infraestructura APRS-IS**. De lo contrario, es fácil confundir la ausencia de identificación en flood con una recepción directa, o interpretar varias copias de un mismo mensaje como saltos sucesivos de una única rama.

## Documentación y fuentes

- [Bob Bruninga, *Fixing the APRS Network: The New n-N Paradigm*](https://www.aprs.org/fix14439.html) - historia y reglas de `WIDEn-N`, `SSn-N`, `UITRACE`, `UIFLOOD` y `TEMPn-N`, además de las recomendaciones originales para estaciones móviles, fijas y fill-in.
- [Bob Bruninga, *MD/VA Digipeater Plan*](https://www.aprs.org/digis/digis-md.html) - distinción histórica detallada entre las rutas recomendadas para móviles que necesitan fill-in y las de las estaciones fijas.
- [aprs.fi, *How APRS paths work*](https://blog.aprs.fi/2020/02/how-aprs-paths-work.html) - interpretación de rutas AX.25 y ejemplos reales de `WIDEn-N` y fill-in.
- [APRX, *Viscous Digipeater*](https://github.com/PhirePhly/aprx/blob/master/ViscousDigipeater.README) - retardo de tramas, detección de duplicados y cancelación de transmisiones de fill-in.
- [APRX, *aprx(8)*](https://manpages.debian.org/testing/aprx/aprx.8.en.html) - modos `directonly` y `viscous-delay` y sus parámetros.
- [Argent Data Systems, *Digipeater Setup*](https://argentdata.com/support/digipeater_setup/) - tratamiento de alias, eliminación de duplicados y preemptive digipeating en una implementación concreta.
- [APRS-IS, *IGate Details*](https://www.aprs-is.net/IGateDetails.aspx) - reglas de pasarela, `NOGATE`, `RFONLY` y q-constructs.
- [AMSAT, *Live Digipeater Satellites*](https://www.amsat.org/live-digipeater-satellites/) - direcciones y parámetros de digipeaters satelitales; los datos operativos deben verificarse antes de utilizarlos.
- [APRS-AX.25](https://wiki.sral.fi/wiki/APRS-AX.25.en) - campos de direcciones y bits H utilizados en tramas de radio APRS.
