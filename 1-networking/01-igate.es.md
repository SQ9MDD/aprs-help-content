---
title: "01. IGate e intercambio de datos con APRS-IS"
description: Función de IGate, direcciones de transferencia, q construct y mecanismos de entrega de tráfico APRS-IS independientes de los filtros.
---

Un IGate (Internet Gateway) conecta la red de radio APRS con APRS-IS. Recibe paquetes por radio y los envía a la red de Internet. Un IGate bidireccional también puede transmitir por RF determinados paquetes recibidos de APRS-IS. Sin embargo, no es un puente transparente: cada dirección tiene sus propias reglas de transferencia y los servidores APRS-IS desempeñan un papel importante en el lado de Internet.

## De RF a APRS-IS

La función principal de un IGate es poner a disposición de APRS-IS los paquetes recibidos por radio. Entre ellos se encuentran posiciones, mensajes, objetos, telemetría y datos meteorológicos. El IGate transfiere los paquetes válidos conservando su contenido y la ruta de radio, de acuerdo con las reglas que evitan reintroducir los mismos datos en la red.

Según las reglas de IGate, no deben transferirse a APRS-IS, entre otros:

- tramas AX.25 sin los campos correctos de control UI (`0x03`) y PID (`0xF0`);
- tramas recibidas en modo `PASSALL`, que no garantiza la integridad de los datos;
- consultas APRS generales que comienzan por `?`;
- paquetes que contienen `TCPIP` o `TCPXX` en su ruta y, conforme a las reglas aplicables, `NOGATE` o `RFONLY`;
- paquetes en formato third-party que contienen `TCPIP` o `TCPXX` en la cabecera interna.

En un paquete third-party que no contenga estos marcadores, deben eliminarse adecuadamente la cabecera de radio externa y el marcador third-party antes de transferirlo a APRS-IS.

Un IGate no debe reescribir arbitrariamente la ruta del paquete. Registra su entrada desde RF a APRS-IS en el lugar previsto para ello.

## q construct: identificación del origen del paquete

`q construct` es un mecanismo de cabecera utilizado **exclusivamente en APRS-IS**. Permite reconocer cómo entró el paquete en la red, identificar el punto de entrada y ayudar a detectar bucles. No forma parte de la ruta de radio AX.25 y nunca debe transmitirse por RF.

Ejemplo de paquete recibido por RF:

```text
SQ9ABC>APRS,WIDE1-1:!5000.00N/01900.00E-
```

Después de transferirlo mediante el IGate bidireccional `SQ9MDD-4`, su cabecera en APRS-IS puede ser:

```text
SQ9ABC>APRS,WIDE1-1,qAR,SQ9MDD-4:!5000.00N/01900.00E-
```

`qAR` identifica un paquete transferido desde RF por un IGate capaz de reenviar mensajes a esa estación. `SQ9MDD-4` identifica el punto de entrada. Esta notación no demuestra por sí sola que el mensaje vaya a transmitirse realmente por RF: también depende de las reglas del IGate y de la situación de la red.

Las construcciones principales que aparecen en APRS-IS son:

| Construcción | Significado |
| --- | --- |
| `qAR` | Paquete transferido desde RF por un IGate que declara poder reenviar mensajes a esa estación. |
| `qAO` | Paquete transferido desde RF sin posibilidad de reenviar mensajes a esa estación, especialmente por un IGate solo receptor. |
| `qAC` | Paquete procedente directamente de un cliente con autenticación verificada correctamente, marcado por el servidor. |
| `qAS` | Paquete recibido por el servidor sin un q construct existente o generado por el propio servidor. |
| `qAU` | Paquete recibido directamente mediante UDP. |
| `qAI` | Construcción utilizada para rastrear la ruta de un paquete por los servidores APRS-IS. |

La documentación también recoge construcciones asociadas a métodos antiguos de transferencia, como `qAr` y `qAo`, así como mecanismos obsoletos de autenticación no verificada. Las mayúsculas y minúsculas de q construct son significativas.

No debe interpretarse `qAR` como prueba de que el IGate dispone de un transmisor físicamente operativo, ni `qAO` únicamente como ausencia de transmisor. Un IGate bidireccional también puede utilizar `qAO` para una estación a la que no reenviará mensajes.

## De APRS-IS a RF

La transferencia de datos desde Internet al canal de radio es mucho más selectiva. No debe retransmitirse todo el flujo APRS-IS, ya que saturaría rápidamente el canal local.

La aplicación principal de un IGate bidireccional es entregar mensajes a estaciones dentro de su cobertura de radio. Según los criterios básicos, el IGate transfiere un mensaje y los paquetes de posición asociados cuando se cumplen las condiciones correspondientes:

- el destinatario ha sido escuchado por RF dentro de un intervalo configurado y está dentro del área de servicio del IGate, definida, por ejemplo, por el número de saltos DIGI o la distancia;
- el remitente del mensaje no ha sido escuchado recientemente por RF en el área local;
- el paquete del remitente no contiene marcadores que impidan la transferencia, especialmente `TCPXX`, `NOGATE` o `RFONLY`;
- el destinatario no ha sido detectado recientemente como estación disponible directamente por Internet.

Los intervalos exactos, el área de servicio de radio y otras restricciones dependen de la configuración del IGate. El operador también puede establecer criterios propios para transferir determinados paquetes, como ciertos objetos. **Recibir un paquete de APRS-IS no significa que el IGate lo transmita automáticamente por RF.**

## Filtro APRS-IS y tráfico entregado automáticamente

Un IGate suele conectarse al puerto filtrado `14580` de APRS-IS. El filtro del servidor determina un **flujo de datos adicional** que el cliente desea recibir. No sustituye los mecanismos básicos del servidor encargados de la comunicación con las estaciones atendidas por el IGate.

Ejemplo de filtro:

```text
filter m/10
```

`m/10` define un área de 10 km de radio alrededor de la última posición conocida del indicativo utilizado para iniciar sesión en APRS-IS. No representa el alcance de recepción por radio del IGate, ni un área alrededor de todas las estaciones escuchadas, ni un límite absoluto para los datos entregados por el servidor. Si el servidor desconoce la posición del indicativo de acceso, el filtro carece de un punto de referencia establecido.

Un filtro equivalente con centro fijo sería, por ejemplo:

```text
filter r/50/19/50
```

Incluye posiciones y objetos en un radio de 50 km desde 50°N, 19°E, así como mensajes dirigidos a estaciones situadas en esa área. En ambos casos, la suscripción adicional funciona **junto al** flujo básico del servidor, no en su lugar.

### ¿Qué entrega el servidor independientemente del filtro adicional?

La documentación de filtrado APRS-IS identifica tres categorías importantes de tráfico que se entregan de forma predeterminada en el puerto filtrado:

- **Mensajes APRS** dirigidos al cliente conectado y a las estaciones cuyos paquetes ese cliente ha transferido desde RF a APRS-IS.
- **Posiciones asociadas de los remitentes de mensajes**: el siguiente informe de posición disponible de la estación que envió uno de esos mensajes. Esto no implica entregar automáticamente todo su historial de posiciones.
- **Paquetes `TCPIP` de estaciones transferidas por el cliente**: este mecanismo no requiere ningún mensaje previo. Puede hacer que lleguen paquetes posteriores de una estación cuyo tráfico el IGate introdujo anteriormente en APRS-IS, aunque esté fuera del radio del filtro.

El último punto es especialmente importante al interpretar registros reales. No todos los paquetes recibidos desde fuera de `m/10` son mensajes o posiciones de sus remitentes. El servidor también puede entregar tráfico de Internet procedente de estaciones vinculadas al IGate por transferencias anteriores desde RF.

Los filtros inclusivos son acumulativos: puede entregarse un paquete que coincida con cualquiera de las reglas activas. Los filtros de exclusión restringen las suscripciones adicionales, pero no desactivan la gestión estándar de mensajes. El filtrado se aplica al flujo **del servidor al cliente**. No limita los paquetes que el IGate envía a APRS-IS.

## ¿Cómo afecta la recepción de radio a larga distancia al flujo APRS-IS?

La cobertura de radio de un IGate es variable. En condiciones de propagación mejoradas puede recibir una estación situada a cientos de kilómetros, directamente o mediante digipeaters. En ambos casos, el paquete puede transferirse correctamente a APRS-IS. El radio configurado en el filtro de la conexión a Internet no limita este proceso.

Consideremos un IGate con el filtro `m/10`:

1. El IGate recibe por RF un paquete de una estación lejana, por ejemplo gracias a la propagación troposférica o a una ruta mediante DIGI, y lo transfiere a APRS-IS.
2. El servidor incluye esa estación en los mecanismos de gestión del tráfico transferido por el cliente.
3. Si después la estación envía paquetes directamente a APRS-IS con una ruta de Internet `TCPIP`, el servidor puede entregárselos también a ese IGate independientemente de `m/10`. No hace falta ningún mensaje APRS.
4. Si otra estación envía un mensaje a la estación transferida previamente por el IGate, el servidor también entrega ese mensaje y el siguiente informe de posición disponible de su remitente. Este también puede encontrarse muy lejos del área del filtro.
5. Recibir estos datos de APRS-IS no significa que el IGate los retransmita automáticamente por RF. La transferencia IS a RF se rige por reglas independientes.

Por tanto, un filtro geográfico pequeño puede coexistir con la llegada ocasional de paquetes de estaciones mucho más lejanas. No es una ampliación del radio de `m/10`, sino el funcionamiento paralelo de las funciones básicas del servidor APRS-IS. El fenómeno puede hacerse más visible tras períodos de recepción de radio a larga distancia, cuando el IGate transfiere paquetes de estaciones que normalmente no escucha.

### Dos situaciones diferentes que pueden confundirse

**Paquetes de una estación transferida previamente por el IGate.** El IGate recibe por RF a `SQ9ABC` y transfiere su paquete a APRS-IS. Si después `SQ9ABC` envía su propio paquete directamente a APRS-IS como `TCPIP`, este puede volver al flujo del IGate fuera de su filtro geográfico. No es necesaria ninguna correspondencia con otra estación.

**Paquetes asociados a un mensaje dirigido a una estación transferida previamente.** El IGate recibe a `SQ9ABC` por RF. La estación `EA1XYZ` envía mediante APRS-IS un mensaje dirigido a `SQ9ABC`. El servidor entrega el mensaje al IGate y le envía el siguiente paquete de posición disponible de `EA1XYZ`, aunque el remitente esté muy lejos del área del filtro.

El primer mecanismo afecta a los paquetes `TCPIP` de la estación transferida anteriormente por el cliente. El segundo afecta a los mensajes dirigidos a esa estación y a las posiciones del **remitente del mensaje**. Esta distinción explica por qué pueden aparecer paquetes fuera del filtro sin correspondencia APRS previa visible.

### ¿Cómo interpretar estos paquetes en la práctica?

En el flujo APRS-IS pueden coexistir paquetes seleccionados por `m/10`, paquetes `TCPIP` de estaciones transferidas previamente por el IGate, mensajes con posiciones asociadas y datos admitidos por otras reglas activas. Estos mecanismos funcionan en paralelo.

Una cabecera como:

```text
SQ9ABC>APRS,TCPIP*,qAC,T2SERVER:!5000.00N/01900.00E-
```

indica cómo entró el paquete en APRS-IS. `qAC` por sí solo **no indica** por qué un servidor concreto lo entregó a un cliente concreto. Una sola entrada del registro tampoco permite determinar si llegó gracias al filtro geográfico, a una transferencia anterior de la estación por el IGate o a otra regla.

Si en el registro aparecen estados, telemetría y objetos junto con posiciones lejanas, no deben atribuirse automáticamente todos a la gestión de mensajes. Pueden incluir paquetes `TCPIP` de estaciones transferidas anteriormente por el IGate o datos admitidos por otros filtros activos. La documentación no respalda que escuchar una estación una sola vez provoque incondicionalmente la entrega de todo el tráfico de su entorno durante un período determinado.

Para el operador, la regla esencial es: **el filtro geográfico define el tráfico solicitado adicionalmente, mientras el servidor sigue atendiendo a las estaciones cuyos paquetes el IGate introdujo en APRS-IS**. Por eso recibir paquetes de fuera de `m/10` puede ser un comportamiento normal de la red.

## Formato third-party al transferir a RF

Un paquete recibido de APRS-IS no puede transmitirse por RF con su ruta de Internet y q construct. El IGate utiliza el formato third-party: una trama de radio externa que contiene el paquete original como datos.

Esquema:

```text
IGATECALL>APRS,GATEPATH:}FROMCALL>TOCALL,TCPIP,IGATECALL*:datos_originales
```

La cabecera interna `TCPIP,IGATECALL*` identifica el origen del paquete. Antes de transmitirlo debe eliminarse su ruta APRS-IS. Así, otro IGate que reciba esa transmisión por radio reconocerá que el paquete procede de Internet y no volverá a introducirlo en APRS-IS.

El marcador `}` inicia el paquete third-party interno. No debe confundirse con q construct, que solo existe en el lado de Internet.

## Documentación

- [APRS-IS: IGate Details](https://www.aprs-is.net/IGateDetails.aspx) - criterios de transferencia y formato third-party.
- [APRS-IS: q Construct](https://www.aprs-is.net/q.aspx) - significado y uso de las construcciones q.
- [APRS-IS: Server Design](https://www.aprs-is.net/ServerDesign.aspx) - reglas fundamentales del servidor, incluida la gestión obligatoria de mensajes.
- [APRS-IS: Server-side Filter Commands](https://www.aprs-is.net/javAPRSFilter.aspx) - funcionamiento de los filtros y paquetes entregados independientemente de ellos.
