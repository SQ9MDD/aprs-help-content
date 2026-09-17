---
title: "Consultas APRS"
---

Una query de APRS es un mecanismo de consulta que permite a una estación solicitar a otra estación, servidor o servicio APRS una información concreta o una respuesta determinada. En lugar de esperar a que los datos necesarios aparezcan de forma natural en el tráfico APRS, el operador puede solicitarlos activamente cuando los necesita.

Las consultas son especialmente útiles porque APRS es principalmente un sistema de difusión. Las estaciones transmiten periódicamente posiciones, estados, datos meteorológicos, objetos, telemetría y otra información, pero eso no significa que todos esos datos estén disponibles exactamente en el momento en que el usuario los necesita. Una query permite obtener información bajo demanda sin aumentar la frecuencia de los beacons de toda la red.

Algunos ejemplos típicos son solicitar la posición actual de una estación, su estado, la versión del software, la lista de estaciones escuchadas directamente o información sobre el funcionamiento de un IGATE. Otras queries no se dirigen a una estación concreta, sino a servicios que funcionan dentro del ecosistema APRS, por ejemplo servidores de objetos, directorios, servicios de grupos, sistemas de búsqueda de indicativos, servicios meteorológicos o gateways hacia otros sistemas de comunicación.

Existen dos tipos básicos de consultas APRS:

consultas generales, dirigidas a todas las estaciones o a una determinada clase de estaciones,
consultas dirigidas, enviadas como mensaje APRS a un indicativo o a un nombre de servicio concreto.

Normalmente una query comienza con el carácter ?, seguido de un identificador del tipo de consulta. La respuesta depende de las funciones que implemente la estación o el servicio receptor. No todas las implementaciones APRS están obligadas a soportar todas las queries, por lo que distintos programas o dispositivos pueden ofrecer conjuntos de comandos diferentes.

Una característica importante de las queries es que están pensadas como solicitudes puntuales de información. No se utilizan para establecer una sesión conectada como en el packet radio clásico. Una estación envía la consulta y, si el receptor la reconoce y la soporta, devuelve el informe de posición, estado, mensaje u otro paquete APRS correspondiente.

Tampoco deben confundirse con los mensajes APRS normales que requieren confirmación. Las consultas APRS estándar no contienen un identificador de mensaje destinado al mecanismo ACK, y sus respuestas tampoco deberían generar confirmaciones adicionales. De este modo, una simple consulta no provoca una cadena innecesaria de tráfico.

En la práctica, las queries se utilizan cuando una información es útil bajo demanda, pero no tiene sentido transmitirla continuamente. En lugar de aumentar la frecuencia de transmisión de estados o listas de estaciones escuchadas, esos datos pueden solicitarse únicamente cuando realmente hacen falta. Esto encaja muy bien con la filosofía de APRS, especialmente en el canal de radio, donde es importante reducir el tráfico innecesario y utilizar el ancho de banda disponible solo cuando aporta un beneficio real.

El ecosistema APRS moderno ha ampliado mucho el concepto original de query. Por eso este documento incluye no solo comandos clásicos como ?APRSP, ?APRSS o ?APRSD, sino también consultas a servicios como ANSRVR, QRU, AVRS, WLNK-1, WHO-IS, WXBOT o WHERE-IS. Todos siguen la misma idea general: una estación APRS envía una petición breve y el sistema remoto devuelve información concreta o realiza una operación determinada.

## Notas generales

- UI-View distingue entre **mayúsculas y minúsculas**.
- Para consultas que incluyan un indicativo, debe utilizarse el **SSID si existe**.
- Este archivo fue preparado **exclusivamente a partir del PDF proporcionado**, sin utilizar otras fuentes.

---
## 1. Consultas APRS estándar
| Entrada | A | Significado / respuesta | Ejemplo |
|---|---|---|---|
| `?APRS?` | `callsign` | Solicita la lista de todas las consultas que la estación o sistema puede responder | `?APRS?` |
| `?WX?` | `CQ` | Consulta a todas las estaciones meteorológicas | `?WX?` |
| `!PROGRAMNAME.EXE` | `callsign` | UI-View puede ejecutar un programa del directorio RCOMMAND, por ejemplo `!Orbitron.exe` o `!Orbitron` | `!help` |
| `?ABOUT` | `callsign` | Versión del software de la estación, sistema operativo, carga de CPU, equivalente a `?APRSV` y `?VER` | `?APRST APRSIS32 Win v6.1 b7601 p2 9.1/6.4%` |
| `?APRSD` | `callsign` | Estaciones escuchadas directamente, sin saltos de digipeater | `?APRSD` |
| `?APRSH` | `callsign` | Estaciones escuchadas junto con el tipo de ruta del paquete: `I`, `G`, `D`, `d`, `R` | `?APRSH` |
| `?APRSH DF8LS-9` | `callsign` | Indica si la estación ha escuchado el indicativo especificado, la respuesta incluye estadísticas de las últimas 18 horas | `?APRSH DF8LS-9` |
| `?APRSL` | `callsign` | Estaciones escuchadas localmente durante los últimos 30 minutos, hasta 2 saltos | `?APRSL` |
| `?APRSM` | `callsign` | Consulta de mensajes, pregunta si la estación tiene mensajes en cola para mí | `?APRSM` |
| `?APRSO` | `callsign` | Consulta de objetos, envía los objetos activos creados por la estación, excluyendo los objetos de QRU-server | `?APRSO` |
| `?APRSP` | `callsign` | Consulta de posición, normalmente provoca el envío de un beacon por parte del receptor | `?APRSP` |
| `?APRSS` | `callsign` | Consulta de estado, por ejemplo el estado de los puertos de APRSIS32 | `?APRSS` |
| `?APRST` | `callsign` | Traza de los paquetes de la estación hacia la interfaz de Internet APRS-IS o por radio, equivalente a `?PING?` | `?APRST` |
| `?APRSV` | `callsign` | Versión del software, sistema, CPU, equivalente a `?ABOUT` y `?VER` | `?APRSV` |
| `?CPU` | `callsign` | Versión del software, sistema, carga de CPU de kernel y usuario | `?CPU` |
| `?DX` | `callsign` | Informe DX abreviado, la estación más lejana recibida durante la última hora | `?DX` |
| `?IGATE` | `callsign` | Actividad en los puertos IGATE, en APRSIS32 también informa del funcionamiento bidireccional | `?IGATE` |
| `?PING?` | `callsign` | Igual que `?APRST` | `?PING?` |
| `?VER` | `callsign` | Igual que `?ABOUT` y `?APRSV` | `?VER` |

### Significado de las abreviaturas en `?APRSH`

- `I` - paquetes procedentes de Internet, incluido el propio APRS-IS
- `G` - paquetes de estaciones pasadas por gateway, callsign-SSID después de la construcción `qAS`, `qAR`, etc.
- `D` - paquetes digipeatados cuya primera copia llegó desde Internet
- `d` - paquetes digipeatados escuchados localmente
- `R` - paquetes escuchados por radio

---
## 2. Funciones exclusivas de UI-View

| Entrada | A | Significado |
|---|---|---|
| `BCN` | `callsign` | Posición y texto de comentario como paquete beacon |
| `LGS` o `LG1` | `callsign` | Inicia el registro de todas las estaciones recibidas |
| `LGX` o `LG0` | `callsign` | Finaliza el registro de estaciones recibidas, el comando debe proceder de la propia estación |
| `QAS` | `callsign` | Todas las estaciones escuchadas |
| `QWS` | `callsign` | Estaciones meteorológicas escuchadas |

---

## 3. ANSRVR y CQSRVR
### ANSRVR
| Entrada | A | Significado |
|---|---|---|
| `?` | `ANSRVR` | Lista de grupos de interés disponibles, sin límite de tiempo o actividad |
| `? GROUP NAME` | `ANSRVR` | Número de miembros del grupo indicado |
| `D GROUP NAME` | `ANSRVR` | Descripción del grupo y lista de miembros |
| `L` | `ANSRVR` | Grupos a los que pertenezco |
| `J GROUP NAME` | `ANSRVR` | Unirse al grupo, membresía válida durante 12 horas |
| `U GROUP NAME` | `ANSRVR` | Abandonar el grupo |
| `CQ GROUP NAME Text...` | `ANSRVR` | Unirse al grupo y enviar un mensaje a sus miembros al mismo tiempo |

### Notas sobre `CQ GROUP NAME Text...`

- Si el grupo no existe, el remitente se convierte en su propietario.
- Al crear un grupo, el nombre puede tener como máximo **46 caracteres**.
- El propietario solo puede eliminar el grupo cuando ya no quedan miembros.

### CQSRVR

| Entrada | A | Significado |
|---|---|---|
| `INFO` | `CQSRVR` | Lista de grupos de corta duración, la membresía y el grupo caducan tras 12 horas sin actividad |

---

## 4. QRU-Server
### Consultas generales

| Entrada | A | Significado |
|---|---|---|
| `INFO` | `QRU` | Qué grupos de objetos QRU están disponibles dentro de mi radio geográfico, por defecto por ejemplo 50 km |
| `INFO 250` | `QRU` | Igual, pero para un radio de 250 km |
| `OBJECT GROUP NAME` | `QRU` | Qué objetos del grupo indicado están disponibles dentro de mi radio |
| `OBJECT GROUP NAME 150` | `QRU` | Qué objetos del grupo indicado están disponibles dentro de un radio de 150 km |

### Comportamiento de las respuestas QRU
- En una consulta por Internet se muestran todos los objetos.
- En APRSIS32 también pueden abrirse ventanas de mapa con su ubicación.
- En una consulta por radio se devuelve un mensaje final del tipo `Sent 5 RP70 Objects Max 5@50km`.
- Los objetos se envían después a través del IGATE y pueden aparecer en la pantalla de la radio, en la lista de estaciones o en un sistema de navegación.
- El documento indica que los objetos no aparecen en aprs.fi si la consulta se realizó por Internet, pero sí aparecen si se realizó por radio.

### Grupos de objetos QRU
| Código | Significado |
|---|---|
| `AERO` | Aerodrome |
| `AIRP` | Airport |
| `AMBU` | Ambulance |
| `CLUB` | Amateur Radio Club |
| `CIVD` | Civil Defense |
| `ECHO` | EchoLink |
| `FIRE` | Fire Department |
| `FOOD` | Restaurant |
| `FUEL` | Gas Station |
| `HELI` | Rescue Helicopter |
| `HOSP` | Hospital |
| `INFO` | Info Kiosk Collection |
| `LIFEBOAT` | Lifeboat |
| `POLI` | Police |
| `POST` | Post Office |
| `RAIL` | Railway |
| `RD2M` | 2m D-Star Repeater |
| `RD70` | 70cm D-Star Repeater |
| `RP10` | 10m Repeater |
| `RP23` | 23cm Repeater |
| `RP2M` | 2m Repeater |
| `RP6M` | 6m Repeater |
| `RP70` | 70cm Repeater |
| `SHOP` | Shopping Center |
| `SRAIL` | Steam Railway |
| `STOR` | Amateur Radio Shop |
| `T2SRV` | T2 Server |
| `VETE` | Veterinarian |

---
## 5. AVRS, Automatic Voice Relay System

| Entrada | A | Significado |
|---|---|---|
| `?` | `AVRS` | Dónde se encuentra la estación EchoLink, IRLP o Allstar más cercana respecto a mi posición |
| `? CALLSIGN-SSID` | `AVRS` | Dónde se encuentra la estación EchoLink, IRLP o Allstar más cercana respecto a la posición de la estación indicada |
| `CALLSIGN-SSID` | `AVRS` | Solicitud de la información necesaria para establecer un contacto de voz a través del nodo EchoLink más cercano |

### Nota

Para que AVRS funcione correctamente, al menos un beacon de posición debe haber llegado previamente a APRS-IS.

---
## 6. APRSlink, WLNK-1

APRSlink se utiliza para consultas de usuarios de Winlink que disponen de una dirección `callsign@winlink.org`.

| Entrada | A | Significado |
|---|---|---|
| `H` | `WLNK-1` | Ayuda |
| `I` | `WLNK-1` | Información sobre APRSlink |
| `?L` | `WLNK-1` | Ayuda para un comando concreto |
| `L` | `WLNK-1` | Lista de mensajes disponibles, normalmente los últimos 5 |
| `R<number>` | `WLNK-1` | Leer un mensaje concreto |
| `Y<number>` | `WLNK-1` | Responder a un mensaje concreto |
| `K<number>` | `WLNK-1` | Eliminar un mensaje concreto |
| `F<number>` | `WLNK-1` | Reenviar un mensaje concreto |
| `SP <email/callsign/alias> <subject>` | `WLNK-1` | Iniciar el envío de un correo más largo en varios pasos |
| `/EX` | `WLNK-1` | Finalizar y enviar el mensaje completo |
| `P` | `WLNK-1` | Reproducción del mensaje |
| `SMS ...` | `WLNK-1` | Enviar un mensaje de una sola línea |
| `A ALIAS=mail@provider.net` | `WLNK-1` | Crear o actualizar un alias |
| `A ALIAS=` | `WLNK-1` | Eliminar un alias |
| `AL` | `WLNK-1` | Lista de alias |
| `G<number>` | `WLNK-1` | Consultar los gateways RMS Packet más cercanos, por defecto 1 |

### Secuencia `SP`

1. Iniciar: `SP <email o callsign o alias> <subject>`
2. Enviar sucesivos fragmentos de texto
3. Finalizar con un mensaje separado `/EX`

El documento indica que, hasta enviar el comando de finalización, puede enviarse texto a `WLNK-1` sin identificación adicional.

---
## 7. WHO-IS, consultas de indicativos en QRZ.com

| Entrada | A | Significado |
|---|---|---|
| `callsign` | `WHO-IS` | Consulta corta: clase / nombre / país |
| `F callsign` | `WHO-IS` | Consulta completa: nombre / clase / calle / ciudad / país |

El documento indica que la consulta también es posible mediante `WHO-15`.

---
## 8. QRZ, consultas de objetos

| Entrada | A | Significado |
|---|---|---|
| `object name` | `QRZ` | Consulta corta de un objeto con antigüedad máxima de 2 horas: nombre del objeto y texto |
| `object*` | `QRZ` | Versión con comodín `*` al final, la respuesta contiene una selección de objetos disponibles |

---

## 9. METAR, consultas meteorológicas

| Entrada | A | Significado |
|---|---|---|
| `ICAO Code` | `WXBOT` | Consulta corta de la meteorología actual de un aeropuerto, SA/METAR |

Ejemplo del documento: `EDDL`.

---
## 10. WHERE-IS, posiciones, distancia, alertas

**Importante:** el documento indica que `WHERE` y `WHERE-IS` distinguen entre **mayúsculas y minúsculas**.

| Entrada | A | Significado |
|---|---|---|
| `callsign` | `WHERE` o `WHERE-IS` | Consulta corta de distancia, rumbo y hora del último informe |
| `where callsign` | `WHERE` o `WHERE-IS` | Igual que arriba |
| `dir callsign` | `WHERE` o `WHERE-IS` | Solo dirección y hora |
| `direction callsign` | `WHERE` o `WHERE-IS` | Forma completa de `dir` |
| `dis callsign` | `WHERE` o `WHERE-IS` | Solo distancia y hora |
| `distance callsign` | `WHERE` o `WHERE-IS` | Forma completa de `dis` |
| `dis callsign @km` | `WHERE` o `WHERE-IS` | Distancia en unidades concretas |
| `pos callsign` | `WHERE` o `WHERE-IS` | Solo coordenadas y hora |
| `loc callsign` | `WHERE` o `WHERE-IS` | Igual que `pos` |
| `position callsign` | `WHERE` o `WHERE-IS` | Forma completa de `pos` |
| `location callsign` | `WHERE` o `WHERE-IS` | Forma completa de `loc` |
| `last callsign` | `WHERE` o `WHERE-IS` | Solo fecha y hora del último informe |
| `alert callsign < 15 km` | `WHERE` o `WHERE-IS` | Alerta cuando la estación se acerque por debajo de la distancia indicada |
| `alert callsign > 15 km` | `WHERE` o `WHERE-IS` | Alerta cuando la estación se aleje por encima de la distancia indicada |
| `cancel callsign` | `WHERE` o `WHERE-IS` | Elimina la alerta guardada |
| `help` | `WHERE` o `WHERE-IS` | Muestra los comandos disponibles |

### Unidades en `WHERE` / `WHERE-IS`

Unidades disponibles:

- `@miles` o `@mi`
- `@nm`
- `@yards` o `@y`
- `@foot` o `@f`
- `@meters` o `@m`
- `@km`

### Limitaciones de las alertas

- Una alerta se aplica solo a **una estación**
- Una vez activada, la alerta se elimina
- El documento indica que el almacenamiento persistente y múltiple de alertas todavía no estaba disponible

---

## 11. SMSGTE

| Entrada | A | Significado |
|---|---|---|
| `@6135551234 free text...` | `SMSGTE` | Sistema que conecta APRS con teléfonos móviles |

### Nota

El documento indica que `SMSGTE` está disponible únicamente en **EE. UU. y Canadá**.

---
## 12. Resumen práctico breve

Los principales grupos de consultas del documento son:

- consultas APRS estándar a estaciones y sistemas
- funciones específicas de UI-View
- grupos ANSRVR y CQSRVR
- objetos y directorios QRU
- AVRS para localizar los nodos de voz más cercanos
- APRSlink para Winlink mediante `WLNK-1`
- WHO-IS y QRZ para información sobre indicativos y objetos
- WXBOT para METAR
- WHERE / WHERE-IS para posición, distancia y alertas
- SMSGTE como puente entre APRS y SMS
