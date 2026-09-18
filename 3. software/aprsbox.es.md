---
title: APRSBox
description: Consola APRS moderna para Linux y Raspberry Pi que integra Packet Routing, DIGI, iGate, APRS-IS, mensajería, mapa, alertas, estadísticas y análisis de propagación.
template: doc
tableOfContents: true
---

**APRSBox** es software de código abierto para construir una estación APRS completa bajo Linux. El proyecto está desarrollado por **SQ9MDD** y se distribuye con licencia **GPL-3.0**.

No es únicamente un programa iGate o digipeater. APRSBox reúne en una sola aplicación las funciones de una estación APRS doméstica, digipeater, iGate, cliente APRS-IS, monitor de tráfico, sistema de mensajería, mapa, alertas, estadísticas y herramientas de diagnóstico.

El proyecto está pensado principalmente para funcionamiento continuo en ordenadores pequeños como Raspberry Pi, aunque también puede ejecutarse en otros sistemas Linux.

Repositorio oficial:

https://github.com/SQ9MDD/APRSBox

## Carácter del proyecto

APRSBox puede considerarse una consola APRS moderna administrada desde un navegador. Una sola instalación puede realizar simultáneamente funciones que en una estación clásica suelen requerir varios programas independientes.

APRSBox puede trabajar, entre otras cosas, como:

- estación APRS doméstica,
- digipeater,
- fill-in digipeater,
- RX iGate,
- TX iGate controlado,
- messaging iGate,
- cliente APRS-IS,
- estación meteorológica,
- monitor del canal APRS local,
- terminal de alertas,
- analizador de propagación,
- nodo con múltiples interfaces de radio.

APRSBox no sustituye al módem de radio. Para comunicarse con el equipo de radio utiliza un TNC o módem externo que proporcione KISS, por ejemplo Dire Wolf, VP-Digi u otro dispositivo compatible.

## Arquitectura

La aplicación está escrita en Python y utiliza FastAPI.

El sistema se divide en dos procesos principales:

- `app.main` - interfaz web, configuración y administración,
- `app.core_main` - proceso APRS Core responsable del funcionamiento en tiempo real.

APRS Core se ocupa de la recepción y transmisión de tramas, decodificación AX.25 y APRS, Packet Routing, DIGI, APRS-IS, mensajería, planificadores, objetos, boletines, meteorología, estadísticas y otras tareas de tiempo real.

La configuración y los datos operativos se almacenan en SQLite. Separar la GUI de APRS Core reduce la influencia de la interfaz web sobre la ruta crítica de procesamiento de tramas.

## Interfaces y fuentes de datos

APRSBox puede utilizar varias interfaces simultáneamente.

Entre las interfaces compatibles se encuentran:

- KISS TCP,
- KISS Serial,
- APRS-IS,
- OpenWebRX mediante MQTT.

Una sola instalación puede recibir datos de varias fuentes, utilizar varios transmisores y enrutar el tráfico entre ellos de acuerdo con las reglas de Packet Routing.

## KISS TCP y KISS Serial

KISS TCP permite trabajar con módems y TNC que ofrecen KISS a través de una red IP. Ejemplos típicos son Dire Wolf, VP-Digi o un TNC físico remoto.

KISS Serial permite conectar un TNC directamente mediante un puerto serie. La configuración incluye la ruta del dispositivo y la velocidad del puerto, mientras que el runtime incorpora los mecanismos necesarios para un funcionamiento continuo estable.

Una interfaz KISS puede utilizarse tanto como fuente RX como destino TX.

## OpenWebRX MQTT

APRSBox dispone de una interfaz de recepción que utiliza MQTT de OpenWebRX.

Además de las tramas APRS clásicas, puede recibir datos de otros decodificadores disponibles en OpenWebRX, entre ellos:

- SONDE,
- ADS-B.

Los datos de radiosondas y ADS-B pueden representarse en el sistema como objetos APRS y mostrarse en la interfaz.

OpenWebRX MQTT es una fuente de recepción y no constituye un transmisor RF directo.

## APRS-IS

APRS-IS es una parte completa de la arquitectura de APRSBox.

La configuración incluye, entre otros elementos:

- servidor,
- puerto,
- login,
- passcode,
- filtro APRS-IS,
- diagnóstico de la conexión.

APRS-IS puede ser tanto una fuente de datos recibidos como un destino de Packet Routing.

## Packet Routing

Uno de los elementos más importantes de APRSBox es **Packet Routing**.

El administrador crea flujos según el modelo:

```text
fuente -> filtros -> acción / destino
```

Esto permite realizar, entre otras cosas:

```text
RF -> RF
RF -> APRS-IS
APRS-IS -> RF
Local TX -> APRS-IS
```

Estos flujos corresponden respectivamente a DIGI, RX iGate, TX iGate controlado y envío a APRS-IS de tramas generadas localmente.

Una regla también puede terminar registrando el evento o descartando deliberadamente la trama.

## Filtros DIGI y Packet Routing

Los flujos pueden utilizar varios filtros consecutivos.

Están disponibles mecanismos relacionados con:

- detección de duplicados,
- viscous delay,
- análisis de ruta,
- modo strict,
- recepción direct-only,
- protección DIGI,
- indicativo,
- tipo de paquete,
- símbolo APRS,
- distancia,
- limitación de frecuencia.

El orden de determinados filtros está controlado para reducir el riesgo de crear una configuración que provoque retransmisiones incorrectas o excesivas.

## Duplicate Filter y Viscous Delay

APRSBox incluye detección de duplicados utilizada durante el funcionamiento DIGI.

Puede combinarse con **viscous delay**, es decir, un retardo controlado de retransmisión. Un fill-in digipeater puede esperar antes de transmitir y cancelar la retransmisión si durante ese intervalo escucha la misma trama repetida por otra estación.

Esto reduce transmisiones innecesarias y la ocupación del canal.

## Protección DIGI y frescura de las tramas

APRSBox incorpora protecciones contra la retransmisión de tramas que un digipeater no debería repetir.

Se pueden comprobar, entre otros aspectos:

- elementos de ruta incorrectos,
- tramas ya repetidas por la estación local,
- paquetes third-party,
- mensajes y consultas APRS locales,
- situaciones en las que no debe producirse otra retransmisión.

El sistema también controla la antigüedad de las tramas en las colas. Un paquete que haya esperado demasiado por carga o por un problema de transporte puede descartarse en lugar de transmitirse más tarde como información obsoleta.

## RX iGate y TX iGate

En la dirección **RF -> APRS-IS**, APRSBox puede trabajar como RX iGate clásico. Esta ruta está diseñada para mantener baja latencia.

En la dirección **APRS-IS -> RF**, APRSBox ofrece un TX iGate controlado. La mensajería APRS y la información sobre si el destinatario fue escuchado localmente son especialmente importantes en este caso.

APRS-IS -> RF no es un reenvío incondicional de todo el tráfico de Internet. Las reglas y filtros protegen el canal local frente a carga innecesaria.

## Local TX e Internal TX

Las tramas generadas por APRSBox se tratan como una fuente lógica **Local TX**.

Esto incluye:

- beacon,
- status,
- WX,
- objetos,
- items,
- boletines,
- mensajes.

Local TX puede dirigirse a APRS-IS independientemente de la transmisión RF física.

También existe un **Internal TX** lógico. No realiza una transmisión física, pero permite introducir una trama generada localmente en el enrutamiento posterior.

## Estación propia, beacon y Proportional Pathing

APRSBox puede generar la posición de su propia estación.

Se configuran, entre otros elementos:

- indicativo y SSID,
- posición,
- símbolo APRS,
- comentario,
- ruta,
- intervalo de beacon,
- interfaz TX de destino.

El beacon también puede enviarse manualmente.

Está disponible **Proportional Pathing**, que permite enviar tramas directas con mayor frecuencia y utilizar rutas DIGI más amplias con menor frecuencia, por ejemplo:

```text
DIRECT -> ruta corta -> ruta completa
```

Esto ayuda a reducir la ocupación innecesaria del canal.

## APRS Status

APRS Status puede transmitirse periódicamente e independientemente del beacon de posición.

Esto permite publicar información adicional de la estación sin incluirla en cada trama de posición.

## Mensajes APRS

APRSBox dispone de una interfaz de conversaciones APRS.

Se admiten, entre otras funciones:

- mensajes numerados,
- mensajes sin número,
- ACK,
- REJ,
- reintentos de mensajes pendientes de ACK,
- eliminación de duplicados,
- mensajes recibidos por RF y APRS-IS,
- conversaciones con estaciones individuales,
- grupos de mensajes.

También se admiten identificadores alfanuméricos de mensajes APRS.

Un mensaje numerado recibido de nuevo no tiene que crear otra entrada en la conversación, pero puede recibir nuevamente un ACK.

## Mensajes de grupo y APRS Queries

Se pueden definir grupos APRS que la aplicación debe escuchar, por ejemplo:

- `ALL`,
- `QST`,
- `CQ`.

Los grupos para RF y APRS-IS pueden configurarse de forma independiente.

APRSBox también puede responder a determinadas consultas APRS estándar, entre ellas:

- `?APRS`,
- `?APRSD`,
- `?DX`.

Esto permite ofrecer información básica sobre la estación y la actividad local mediante el propio protocolo APRS.

## Objetos, items, boletines y anuncios

APRSBox puede gestionar objetos e items APRS.

Entre las operaciones disponibles se encuentran:

- definir posición y símbolo,
- añadir comentario,
- transmisión manual,
- transmisión periódica,
- establecer tiempo de validez,
- finalizar la transmisión al expirar,
- enviar información de eliminación del objeto.

También existe un planificador de boletines y anuncios. Las transmisiones pueden espaciarse para evitar que varios elementos activos generen un único burst repentino en el canal.

## Meteorología

APRSBox puede generar y procesar datos meteorológicos APRS.

Los datos WX se decodifican y se utilizan en las vistas de estaciones, el mapa y los filtros.

El parser admite los campos meteorológicos clásicos y extensiones adicionales utilizadas en APRS.

## Traffic Monitor

Traffic Monitor muestra en directo el tráfico que pasa por APRSBox.

Puede distinguir:

- RX,
- TX,
- tráfico de distintas interfaces,
- RF -> APRS-IS,
- APRS-IS -> RF,
- tramas generadas localmente.

Los filtros por dirección, interfaz y contenido de la trama convierten esta vista en un analizador útil del tráfico APRS local.

## Lista de estaciones

Las tramas recibidas se procesan para obtener el estado actual de cada estación.

La vista puede incluir:

- indicativo,
- última actividad,
- posición,
- distancia,
- símbolo,
- fuente de la trama,
- datos WX,
- velocidad y rumbo,
- información Mic-E,
- dispositivo o software identificado.

Las estaciones pueden filtrarse por tipo y fuente.

## Mapa

APRSBox incluye un mapa avanzado basado en Leaflet.

En el mapa pueden mostrarse:

- estaciones,
- objetos e items,
- trazas de estaciones móviles,
- cobertura PHG,
- cuadrícula Maidenhead,
- áreas NWS-WARN,
- áreas de alertas CAWF,
- PL-WARN,
- ES-WARN.

La vista puede filtrarse según las interfaces de origen.

## PHG, trazas y estaciones superpuestas

Si una estación transmite datos PHG, APRSBox puede mostrar su área aproximada de cobertura de radio.

Las estaciones móviles pueden mostrar su recorrido.

Cuando varias estaciones están en la misma posición o muy próximas, APRSBox puede agrupar marcadores y separar los símbolos superpuestos a un nivel de zoom adecuado para mantener accesible cada estación.

## Cuadrícula Maidenhead

El mapa puede mostrar una cuadrícula de localizadores Maidenhead.

El nivel de detalle cambia con el zoom, por lo que la capa es útil tanto para orientación general como para trabajo de operador más preciso.

## APRS Emergency

APRSBox detecta tramas relacionadas con **APRS Emergency** y las presenta en una parte específica de la interfaz.

El sistema puede:

- agrupar alertas por indicativo completo de origen,
- conservar el historial de tramas relacionadas,
- mostrar el número de eventos posteriores,
- presentar notificaciones globales,
- reproducir una señal sonora,
- permitir silenciar una alerta concreta de forma temporal o indefinida.

Eliminar una alerta de la lista no tiene por qué borrar sus tramas de origen del Traffic Monitor, por lo que el evento puede seguir analizándose.

## NWS-WARN

APRSBox admite **NWS-WARN**, el mecanismo de alertas por área utilizado en el ecosistema APRS estadounidense y asociado a los avisos del National Weather Service.

Una alerta puede incluir el tipo de peligro, el periodo de validez y la información sobre el área afectada.

APRSBox puede utilizar estos datos para visualizar la alerta directamente en el mapa, en lugar de limitarse a mostrar el texto del mensaje.

La compatibilidad con NWS-WARN mantiene la interoperabilidad con el ecosistema APRS existente y con aplicaciones que utilizan este mecanismo desde hace años.

## CAWF, Common APRS Warning Format

APRSBox también admite **CAWF, Common APRS Warning Format**.

CAWF está diseñado para distribuir alertas geográficas mediante APRS de forma independiente del sistema nacional que las origina.

El formato permite transmitir de manera normalizada, entre otros datos:

- tipo de peligro,
- nivel de severidad,
- área afectada,
- periodo de validez,
- identificador de la alerta,
- fuente de los datos.

Los datos de sistemas nacionales de alerta pueden normalizarse en un servidor warnHUB, transmitirse mediante APRS e interpretarse de forma coherente por un cliente compatible con CAWF.

## PL-WARN

En Polonia, CAWF es utilizado por **PL-WARN**.

warnHUB obtiene avisos oficiales, los normaliza al formato CAWF y los envía a la red APRS.

APRSBox incorpora las áreas administrativas de Polonia y puede asociar un identificador de área con la geometría correcta en el mapa.

De este modo, una alerta PL-WARN puede mostrarse como una zona geográfica resaltada y no únicamente como texto.

## ES-WARN

El mismo mecanismo está soportado para España mediante **ES-WARN**.

APRSBox incorpora las áreas de España necesarias para visualizar alertas CAWF.

PL-WARN y ES-WARN utilizan el mismo modelo CAWF, por lo que la aplicación puede presentar alertas de distintos países de forma coherente.

## Áreas de alerta en el mapa

Las alertas NWS-WARN y CAWF pueden representarse como capas del mapa.

En una misma vista el usuario puede observar:

- estaciones APRS,
- objetos,
- trazas de estaciones móviles,
- actividad local,
- áreas cubiertas por alertas.

Para CAWF, APRSBox dispone actualmente de áreas implementadas para Polonia y España, utilizadas respectivamente por PL-WARN y ES-WARN.

Esto permite que APRSBox actúe como terminal local de información de alertas recibidas mediante APRS-IS u otras fuentes configuradas.

## Radar de estaciones, notificaciones y webhooks

APRSBox incluye una función de radar de estaciones.

Se pueden observar indicativos seleccionados, también mediante máscaras, y reaccionar cuando aparezcan dentro de un radio definido.

Determinados eventos también pueden enviarse fuera de la GUI mediante mecanismos de notificación e integración, incluidos webhooks y Telegram.

## Condiciones de banda

APRSBox ofrece análisis automático de las condiciones de propagación basado en el tráfico APRS local.

El sistema construye una referencia local que describe qué estaciones y distancias se escuchan normalmente desde una ubicación determinada. La aparición anómala de estaciones más lejanas o de nuevas zonas geográficas puede indicar mejores condiciones de propagación.

El análisis puede funcionar de forma independiente para cada interfaz.

## Escala W0-W5 e historial de propagación

Las condiciones se presentan mediante una escala simplificada **W0-W5**.

La evaluación puede tener en cuenta:

- número de estaciones escuchadas,
- alcance local habitual,
- distancias,
- repetibilidad de recepciones lejanas,
- aparición de nuevas áreas,
- madurez de los datos acumulados.

El modelo no se basa únicamente en una sola estación más lejana.

APRSBox también conserva el historial de evaluaciones de propagación y datos de diagnóstico que ayudan a comprender la valoración actual.

## Estadísticas y TOP

APRSBox mantiene estadísticas de tráfico y actividad.

Entre los datos disponibles se encuentran:

- tipos de tramas APRS,
- tráfico directo y total,
- direcciones de routing,
- actividad de radio,
- usuarios más activos,
- dispositivos y aplicaciones identificados.

El sistema puede mostrar TOP de usuarios según `CALLSIGN-SSID` y TOP de dispositivos identificados mediante información como TOCALL y Mic-E.

## Dashboard y diagnóstico

El dashboard muestra el estado actual de toda la instalación.

Puede incluir:

- actividad RF,
- tráfico,
- estado de servicios,
- interfaces,
- APRS-IS,
- direcciones de routing configuradas,
- estación propia.

Packet Routing ofrece diagnóstico paso a paso. Se puede comprobar qué regla se activó, qué filtros se ejecutaron, dónde se rechazó un paquete, si llegó a TX y cuáles fueron los tiempos de ejecución, colas y workers.

## Múltiples interfaces, colas y pacing

APRSBox no presupone que una estación tenga un único módem.

Una sola instalación puede disponer de múltiples interfaces KISS y Packet Routing define qué receptor puede reenviar tráfico a qué transmisor.

Las colas TX están separadas por interfaz, por lo que un TNC lento o problemático no tiene por qué bloquear a los demás.

APRSBox también controla el espaciado entre tramas generadas localmente. Beacon, status, WX, objetos, boletines y mensajes pueden distribuirse en el tiempo en lugar de transmitirse como un único burst.

## Parser APRS

APRSBox dispone de su propia capa de decodificación APRS utilizada por el mapa, las vistas de estaciones, la mensajería, la meteorología y las estadísticas.

Se admiten, entre otros:

- posiciones APRS clásicas,
- posiciones comprimidas,
- Mic-E,
- position ambiguity,
- objetos,
- items,
- mensajes,
- ACK y REJ,
- query,
- status,
- telemetría,
- meteorología,
- PHG.

## Símbolos e interfaz web

La interfaz admite símbolos de las tablas primaria y alternativa de APRS junto con sus descripciones.

La GUI incluye:

- tema claro y oscuro,
- navegación adaptable,
- ayuda contextual,
- vistas de diagnóstico,
- tráfico y estado de estaciones actualizados dinámicamente.

La interfaz está traducida, entre otros idiomas, al polaco, inglés, español y alemán.

## Copias de seguridad, actualización, SQLite y HTTPS

La configuración de APRSBox puede exportarse y restaurarse.

Las instalaciones nativas disponen de actualización mediante GUI y pueden crear una copia de la base de datos antes de actualizar.

El diagnóstico de SQLite incluye información como tamaño de la base, WAL, número de páginas y comprobaciones de integridad.

Los datos runtime pueden limpiarse sin eliminar toda la configuración de la estación.

Una instalación nativa también puede configurarse para trabajar mediante HTTPS con certificado y clave privada propios.

## Instalación y Docker

El proyecto proporciona scripts de instalación para:

- Debian,
- Raspberry Pi OS y otros sistemas compatibles con Debian,
- Alpine Linux.

Se admiten tanto `systemd` como `OpenRC`.

APRSBox también puede ejecutarse en Docker. Los datos y logs pueden almacenarse en volúmenes. En un contenedor se deshabilitan algunas operaciones que administran directamente el host, y las actualizaciones se realizan sustituyendo la imagen y conservando los datos.

## Raspberry Pi y hardware de baja potencia

El proyecto se desarrolla pensando en ordenadores pequeños y funcionamiento continuo.

El código utiliza mecanismos como:

- bounded queues,
- workers de transmisión independientes,
- caché de configuración,
- traslado de operaciones pesadas fuera de la ruta crítica RX,
- agregación de estadísticas,
- limitación del procesamiento repetido de los mismos datos.

Esto permite que APRSBox funcione en hardware considerablemente menos potente que un PC moderno típico.

## Características principales

Entre las funciones más características de APRSBox se encuentran:

- Packet Routing,
- soporte para múltiples TNC,
- filtros DIGI avanzados,
- Duplicate Filter y Viscous Delay,
- RX iGate y TX iGate controlado,
- mensajería APRS,
- Proportional Pathing,
- mapa con PHG, trazas y cuadrícula Maidenhead,
- NWS-WARN,
- CAWF,
- PL-WARN con áreas de Polonia,
- ES-WARN con áreas de España,
- APRS Emergency,
- radar de estaciones,
- webhooks y notificaciones,
- análisis local de propagación,
- estadísticas de dispositivos y usuarios,
- diagnóstico avanzado de la estación.

El proyecto se desarrolla activamente, por lo que sus funciones pueden evolucionar más rápido que las descripciones de documentación externa.

## Información básica

**Nombre:** APRSBox  
**Autor / mantenedor principal:** SQ9MDD  
**Licencia:** GPL-3.0  
**Sistema:** Linux  
**Interfaz:** Web  
**Base de datos:** SQLite  
**Interfaz de radio:** KISS TCP / KISS Serial  
**Fuente RX adicional:** OpenWebRX MQTT  
**APRS-IS:** RX/TX  
**Instalación:** Debian, Raspberry Pi OS, Alpine Linux, Docker  
**Repositorio:** https://github.com/SQ9MDD/APRSBox
