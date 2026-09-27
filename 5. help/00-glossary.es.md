---
title: "Glosario de términos y abreviaturas"
description: "Terminología básica de radiocomunicaciones, APRS y transmisión de paquetes explicada para quienes comienzan con APRS."
sidebar:
  order: 0
---

La documentación de APRS utiliza términos de radiocomunicaciones, informática y transmisión de paquetes. Este glosario explica los conceptos que aparecen al leer artículos, configurar estaciones y analizar el tráfico. Las entradas están agrupadas por temas. Las siglas conservan su desarrollo original y las definiciones explican su significado práctico.

## 1. Fundamentos de radiocomunicación

**Antena** - elemento que irradia y recibe ondas de radio. Su diseño, ubicación y diagrama de radiación influyen en la eficacia de la comunicación.

**CTCSS (Continuous Tone-Coded Squelch System)** - sistema que abre selectivamente el silenciador mediante un tono continuo de baja frecuencia transmitido junto con la señal. No proporciona confidencialidad.

**Frecuencia** - número de ciclos de una onda por segundo, expresado en hercios (Hz). En Polonia, el APRS convencional en la banda de 2 m suele operar en 144,800 MHz.

**DCS (Digital-Coded Squelch)** - sistema que abre selectivamente el silenciador mediante un código digital transmitido.

**Dúplex** - modo de operación con vías separadas para transmisión y recepción. El dúplex completo permite transmitir y recibir simultáneamente; el semidúplex exige alternar ambas operaciones.

**FM (Frequency Modulation)** - modulación en la que la información modifica la frecuencia instantánea de la portadora. Se utiliza en transceptores analógicos, también para transportar audio AFSK.

**Canal de radio** - frecuencia determinada o conjunto de parámetros de funcionamiento, como frecuencia, tipo de emisión y ajustes adicionales.

**Modulación** - variación de un parámetro elegido de la señal portadora para transmitir información.

**Potencia del transmisor** - potencia entregada por el transmisor, normalmente expresada en vatios (W). La potencia por sí sola no determina el alcance de una estación.

**Repetidor de radio** - estación que recibe y retransmite una señal para ampliar la cobertura. Los repetidores de voz suelen utilizar frecuencias de recepción y transmisión distintas.

**Propagación** - forma en que se desplazan las ondas de radio. La frecuencia, el terreno, las antenas y las condiciones atmosféricas influyen en las trayectorias y el alcance. Una propagación favorable permite recibir estaciones APRS lejanas.

**PTT (Push To Talk)** - botón o señal de control que pone un transceptor en transmisión. En una estación controlada por ordenador, el PTT puede accionarse mediante una interfaz física.

**RF (Radio Frequency)** - radiofrecuencia. En documentación APRS, «red RF» suele designar la parte radioeléctrica del sistema, a diferencia de APRS-IS.

**RX (Receive)** - recepción; designación del receptor, de la vía de recepción o de la operación de recibir.

**Símplex** - en sentido estricto, transmisión unidireccional. En la práctica de radioaficionados, «comunicación símplex» también suele significar comunicación directa y alterna en la misma frecuencia, sin repetidor.

**Squelch (silenciador)** - circuito que silencia el audio del receptor cuando la señal no cumple las condiciones configuradas. Un umbral excesivo puede dificultar la recepción de paquetes.

**TX (Transmit)** - transmisión; designación del transmisor, de la vía de transmisión o de la operación de enviar.

**UHF (Ultra High Frequency)** - frecuencias de 300 MHz a 3 GHz, incluida la banda de radioaficionados de 70 cm.

**VHF (Very High Frequency)** - frecuencias de 30 a 300 MHz, incluida la banda de radioaficionados de 2 m.

**VOX (Voice Operated Exchange)** - circuito que inicia automáticamente la transmisión al detectar una señal de audio suficiente. Su tiempo de reacción es importante al transmitir datos.

**Alcance radioeléctrico** - área en la que puede recibirse una estación. Depende de las antenas, la potencia, el terreno, las interferencias y la propagación.

## 2. Equipos e interfaces

**CAT (Computer Aided Transceiver)** - control del transceptor desde un ordenador, por ejemplo para cambiar la frecuencia o consultar parámetros. Las funciones dependen del equipo.

**DTR (Data Terminal Ready)** - señal de control de una interfaz serie que puede accionar el PTT mediante un circuito adecuado.

**GNSS (Global Navigation Satellite System)** - denominación general de los sistemas de navegación por satélite, utilizados, entre otras cosas, para determinar la posición de un tracker APRS.

**GPS (Global Positioning System)** - uno de los sistemas GNSS. Coloquialmente, «GPS» también se aplica a receptores que utilizan varios sistemas de navegación por satélite.

**Interfaz** - método o circuito de conexión entre dispositivos. Una interfaz de radio puede transportar audio entre el ordenador y el transceptor y controlar el PTT.

**Tarjeta de sonido** - dispositivo que convierte señales analógicas en digitales y viceversa. Junto con un módem por software, permite transmitir y recibir AFSK.

**Módem (Modulator-Demodulator)** - dispositivo o programa que convierte datos en una señal adecuada para el medio de transmisión y realiza el proceso inverso. En APRS convencional, el módem AFSK genera audio a partir de los datos y decodifica el audio recibido.

**Puerto serie** - interfaz que transmite los datos secuencialmente, bit a bit. Puede conectar un TNC, un receptor GNSS o un circuito de control.

**Transceptor** - equipo que integra transmisor y receptor de radio. No todos los transceptores incorporan módem o TNC.

**RTS (Request To Send)** - señal de control de una interfaz serie utilizada frecuentemente para accionar el PTT mediante un circuito adecuado.

**SDR (Software Defined Radio)** - radio definida por software, en la que el procesamiento digital por software implementa parte de las funciones del receptor o transmisor.

**Terminal** - programa o dispositivo para intercambiar datos con otro sistema. En radio por paquetes puede trabajar con un TNC.

**TNC (Terminal Node Controller)** - controlador de comunicación por paquetes, físico o por software. En una configuración AX.25 típica, gestiona tramas y trabaja con un módem. No todos los módems son TNC completos.

**UART (Universal Asynchronous Receiver-Transmitter)** - circuito que implementa comunicación serie asíncrona, habitual en microcontroladores.

**USB (Universal Serial Bus)** - interfaz utilizada, entre otras cosas, para conectar tarjetas de sonido, adaptadores serie, receptores GNSS y transceptores.

## 3. Conceptos básicos de APRS

**APRS (Automatic Packet Reporting System)** - sistema de intercambio automático de información mediante transmisión por paquetes. Admite posiciones, mensajes, objetos, meteorología y telemetría, entre otros datos.

**Beacon (baliza)** - paquete informativo transmitido normalmente de forma automática, por ejemplo con la posición o el estado de la estación. No constituye un tipo de trama APRS independiente.

**Boletín (Bulletin)** - anuncio APRS destinado a varios destinatarios y transmitido mediante el formato de mensajes APRS.

**Comentario** - texto adicional adjunto a determinados informes APRS, por ejemplo a un informe de posición.

**Objeto (Object)** - información APRS con nombre, normalmente relativa a un lugar o evento y publicada por otra estación. Puede representar, por ejemplo, un repetidor o la ubicación de un evento.

**Elemento (Item)** - formato simplificado de información APRS con nombre, distinto del formato de objeto.

**Paquete** - unidad de datos transmitida por una red. En conversaciones sobre APRS, «paquete» y «trama» a veces se utilizan indistintamente, aunque su significado preciso depende de la capa del protocolo.

**Informe de posición** - datos APRS con coordenadas geográficas y, según el formato, hora, símbolo, rumbo, velocidad, altitud o comentario.

**SSID (Secondary Station Identifier)** - identificador adicional en una dirección AX.25, con valores de 0 a 15. Distingue estaciones que comparten indicativo. No todos los sufijos textuales presentes en APRS-IS son SSID AX.25.

**Estación APRS** - dispositivo o aplicación que intercambia información APRS, como un tracker, estación fija, DIGI o IGate.

**Estado (Status)** - información textual sobre el estado o la actividad de una estación, transmitida mediante el formato APRS previsto para ello.

**Símbolo APRS** - representación gráfica de una estación u objeto, definida en los datos APRS y mostrada en mapas.

**Telemetría** - medidas o estados de un dispositivo transmitidos a distancia, como tensión, temperatura o señales digitales.

**Tracker (rastreador)** - dispositivo o aplicación que publica automáticamente su posición, normalmente determinada por un receptor GNSS.

**Mensaje APRS** - mensaje de texto breve transmitido mediante un formato APRS definido. Los mensajes dirigidos a destinatarios concretos pueden utilizar identificadores y acuses de recibo.

**Indicativo (Callsign)** - identificador de estación de radio asignado conforme a la normativa aplicable; en APRS de radioaficionados constituye la base del direccionamiento.

## 4. Elementos de la red APRS

**APRS-IS (APRS Internet System)** - infraestructura de internet para intercambiar datos APRS entre clientes, IGates y servidores.

**DIGI (Digipeater, Digital Repeater)** - estación repetidora digital que recibe paquetes por radio y los retransmite según las reglas de la ruta y su configuración.

**Paquete duplicado** - otra copia de un paquete ya recibido. Puede producirse cuando varias estaciones reciben y reenvían la misma transmisión.

**Hop (salto)** - una etapa de reenvío de un paquete. En APRS por radio, suele representar una retransmisión mediante un digipeater.

**IGate (Internet Gateway)** - pasarela entre la red APRS por radio y APRS-IS. Envía a internet los paquetes recibidos por radio; una pasarela bidireccional también puede transmitir a RF determinados datos de APRS-IS.

**Cliente APRS** - aplicación o dispositivo que recibe, muestra o envía datos APRS mediante un medio compatible.

**Retransmisión** - nueva transmisión de un paquete, por ejemplo mediante un digipeater, conforme a las reglas de reenvío.

**Servidor APRS-IS** - servidor que distribuye datos APRS por internet, atiende clientes y, según su función, se conecta con otros servidores.

**Ruta APRS (Path)** - campo de direcciones que identifica estaciones o alias implicados en el reenvío de un paquete por radio.

**WIDE1-1** - alias de ruta habitual que permite una retransmisión por un digipeater configurado adecuadamente.

**WIDE2-2** - alias WIDEn-N cuyo contador inicial permite dos etapas de retransmisión mediante digipeaters compatibles. No garantiza que el paquete se repita realmente dos veces.

## 5. Transmisión de datos y protocolos

**AFSK (Audio Frequency-Shift Keying)** - representación de datos mediante cambios de frecuencia de una señal de audio. El APRS convencional de 1200 baudios utiliza AFSK compatible con Bell 202.

**ALOHA** - método de acceso a un medio compartido en el que las estaciones intentan transmitir sin asignación centralizada de intervalos. Compartir el canal y carecer de garantía de entrega en APRS por radio hace posibles las colisiones.

**AX.25** - protocolo de transmisión por paquetes para radioaficionados que define, entre otras cosas, el direccionamiento y la estructura de las tramas. APRS utiliza principalmente tramas UI.

**Baud (baudio)** - unidad de velocidad de modulación que indica símbolos por segundo. No siempre equivale al número de bits por segundo.

**Bit/s (bps)** - número de bits transmitidos por segundo.

**CRC (Cyclic Redundancy Check)** - método para calcular un valor de comprobación que permite detectar errores en los datos transmitidos.

**DTI (Data Type Identifier)** - identificador del tipo de datos, normalmente el primer carácter del campo de información APRS, que indica cómo interpretar su contenido.

**FCS (Frame Check Sequence)** - secuencia de comprobación de trama. En AX.25 utiliza CRC para detectar errores de transmisión.

**FEC (Forward Error Correction)** - corrección de errores mediante datos adicionales transmitidos junto con la información, sin necesidad de retransmitir.

**FSK (Frequency-Shift Keying)** - modulación en la que los símbolos se representan mediante distintas frecuencias de señal.

**FX.25** - extensión de AX.25 que incorpora FEC. Permite recuperar algunas transmisiones dañadas si el receptor admite FX.25.

**KISS (Keep It Simple, Stupid)** - protocolo sencillo entre una aplicación y un TNC para transportar tramas y determinadas órdenes de control. Una interfaz KISS por sí sola no garantiza que el dispositivo implemente completamente AX.25.

**Mic-E** - formato compacto de APRS en el que parte de la información de posición y estado también se codifica en el campo de dirección AX.25.

**Payload (carga útil)** - datos transportados en una capa determinada del protocolo. En descripciones APRS suele referirse al contenido del campo de información de la trama.

**Trama** - unidad de datos de la capa de enlace. Una trama AX.25 contiene, entre otros elementos, direcciones, campo de control, campo de información y secuencia de comprobación.

**TCP/IP** - familia de protocolos de red utilizada, entre otras cosas, para comunicar clientes con APRS-IS.

**TOCALL** - nombre habitual del campo de dirección de destino APRS, cuyos valores a menudo identifican el programa o dispositivo transmisor. No todos los valores de destino identifican un producto.

**UI Frame (Unnumbered Information Frame)** - trama AX.25 que transporta datos sin establecer conexión ni confirmar cada trama en la capa de enlace. Es la base del APRS convencional.

## 6. Uso y configuración de estaciones

**APRS Passcode** - código utilizado en el inicio de sesión tradicional de APRS-IS. Se calcula a partir del indicativo y no constituye una protección criptográfica sólida.

**DCD (Data Carrier Detect)** - señal o mecanismo que detecta una transmisión de datos, utilizado, entre otras cosas, para evaluar la ocupación del canal.

**Filtro APRS-IS** - conjunto de reglas que limita los datos enviados a un cliente, por ejemplo según ubicación o indicativo.

**Host (equipo anfitrión)** - ordenador o dispositivo que proporciona un servicio, como un servidor KISS TCP.

**KISS Serial** - transporte de tramas y órdenes KISS mediante una interfaz serie.

**KISS TCP** - transporte de datos KISS mediante una conexión TCP, que permite comunicar una aplicación con un TNC a través de una red informática.

**Puerto TCP** - número que identifica un servicio TCP en un dispositivo. Depende de la configuración del servicio.

**Proportional Pathing** - método para enviar informes de posición sucesivos mediante distintas rutas, utilizando menos a menudo las rutas de retransmisión más largas para reducir la carga de la red.

**q-construct** - elemento especial añadido a la representación textual de un paquete en APRS-IS. Aporta información sobre cómo entró el paquete en la red de internet o cómo se reenvió. No es una ruta de retransmisión de radio AX.25.

**SmartBeaconing** - método que adapta la frecuencia de los informes de posición al movimiento de la estación, especialmente a la velocidad y los cambios de rumbo.

**TX Delay** - tiempo reservado para que el transmisor y la vía de recepción de la otra estación estén preparados antes de enviar los datos propiamente dichos de la trama. El significado exacto del ajuste depende del módem o TNC.

**TX Tail** - tiempo adicional durante el que se mantiene la transmisión después de terminar los datos, si el módem o TNC lo contempla.

## 7. Diagnóstico y funcionamiento

**Búfer** - área de memoria que almacena datos temporalmente antes de procesarlos o transmitirlos.

**Duplicados** - varias copias de la misma información. En el diagnóstico hay que distinguir varias recepciones de una sola transmisión de un nuevo envío realizado por la estación de origen.

**Colisión de paquetes** - solapamiento de transmisiones que impide o dificulta la decodificación correcta.

**Cola** - mecanismo que almacena paquetes pendientes de procesamiento o transmisión.

**Registro (Log)** - historial cronológico de eventos utilizado para analizar el funcionamiento de una estación y diagnosticar problemas.

**Monitor de paquetes** - herramienta que muestra tramas recibidas o enviadas, sus direcciones, rutas y contenido.

**Latencia** - tiempo transcurrido entre determinadas etapas del procesamiento o la transmisión de un paquete.

**Nivel de audio** - nivel de la señal entregada al módem o transmisor. Un nivel demasiado bajo o alto puede provocar problemas de decodificación.

**Sobremodulación o saturación** - distorsión de la señal por superar el nivel permitido en una etapa de procesamiento.

**RSSI (Received Signal Strength Indicator)** - indicador de intensidad de la señal de radio recibida. Su escala y método de medida dependen del equipo.

**SNR (Signal-to-Noise Ratio)** - relación entre la potencia de la señal útil y la del ruido, normalmente expresada en decibelios.

**Ocupación del canal** - situación en la que una transmisión en curso utiliza el canal. Un TNC puede detectar la ocupación antes de transmitir.

## 8. Términos que no deben confundirse

**Módem y TNC** - un módem convierte señales en datos y viceversa. Un TNC gestiona la comunicación por paquetes, como las tramas AX.25, y puede integrar un módem o trabajar con él.

**DIGI e IGate** - un DIGI retransmite paquetes por radio. Un IGate conecta la red de radio con APRS-IS. Un mismo dispositivo puede cumplir ambas funciones, pero son papeles distintos.

**Baud y bit/s** - baud cuenta símbolos por segundo y bit/s cuenta bits por segundo. Con un bit por símbolo, los valores numéricos pueden coincidir.

**GPS y GNSS** - GPS es uno de los sistemas GNSS. Un receptor multisistema también puede utilizar otras constelaciones de satélites.

**SSID y sufijo textual** - el SSID AX.25 va de 0 a 15. Otros sufijos de identificadores textuales de internet no se convierten por ello en SSID AX.25 y pueden no ser aptos para reenviarse a RF.

**APRS y APRS-IS** - APRS define un sistema de intercambio de información que puede utilizar distintos medios. APRS-IS es su infraestructura de distribución de datos por internet.
