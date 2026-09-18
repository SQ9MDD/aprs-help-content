---
title: Estación APRS portátil
description: APRS en un radiotransmisor portátil, uso en campo, equipos disponibles y diferencias entre un terminal APRS completo y un simple tracker.
template: doc
tableOfContents: true
---

APRS en un radiotransmisor portátil es una de las aplicaciones en las que las posibilidades del sistema resultan especialmente útiles.

En lugar de llevar un tracker, un ordenador y una radio por separado, podemos utilizar un único dispositivo pequeño tanto para comunicaciones de voz como para el intercambio de datos APRS.

Según las capacidades del equipo, podemos:

- transmitir nuestra propia posición,
- recibir posiciones de otras estaciones,
- ver la distancia y el rumbo hacia otro operador,
- enviar y recibir mensajes APRS,
- recibir objetos e información local,
- indicar la frecuencia en la que estamos disponibles,
- observar la actividad APRS cercana.

Esto hace que APRS en una radio portátil sea mucho más que un complemento de un mapa. Puede convertirse en un canal adicional de información que funciona independientemente de la red móvil.

## ¿Dónde resulta más útil el APRS portátil?

Las ventajas de APRS se ven especialmente bien durante el trabajo en campo.

### Montaña y senderismo

Durante una caminata podemos transmitir periódicamente nuestra posición para que otros radioaficionados que utilizan APRS sepan dónde estamos.

Si hay otra estación APRS cerca, la radio puede mostrar su posición, distancia y dirección.

Puede resultar muy útil durante:

- excursiones en grupo,
- activaciones SOTA,
- encuentros en campo,
- concursos,
- expediciones de radioaficionados,
- búsqueda de otros operadores cercanos.

APRS no sustituye a los equipos de navegación ni a los sistemas de emergencia, pero puede ser una fuente de información adicional muy útil.

### En el agua

La misma idea se aplica a la navegación, el kayak y otras actividades en el agua.

La posición de una estación puede transmitirse por radio sin necesidad de cobertura móvil.

Los operadores de un mismo grupo pueden ver las posiciones de los demás y utilizar al mismo tiempo la misma radio para comunicaciones de voz normales.

### Trabajo en campo

APRS también resulta muy práctico durante muchas otras actividades de radioaficionado fuera de casa.

Por ejemplo, al llegar a un lugar de pruebas o a un punto de encuentro podemos transmitir nuestra posición junto con información como:

```text
QRV 145.500 MHz
```

Otro radioaficionado que vea nuestra estación APRS sabrá no solo **dónde estamos**, sino también **en qué frecuencia puede llamarnos**.

Esta es una de las diferencias importantes entre APRS y un simple sistema de seguimiento de posición.

APRS pretende ayudar a los operadores a encontrarse y establecer comunicación.

## APRS sin acceso a Internet

No se necesita acceso a Internet para intercambiar información APRS directamente entre estaciones.

```text
Radio A
   |
   |  RF
   |
Radio B
```

Si ambas estaciones están dentro de su alcance de radio, pueden intercambiar datos directamente.

Si existe un digipeater entre ellas:

```text
Radio A
   |
   v
Digipeater
   |
   v
Radio B
```

el paquete puede ser retransmitido y alcanzar una zona mucho mayor.

Internet, APRS-IS y los iGate amplían las posibilidades del sistema, pero no son necesarios para el funcionamiento local de APRS.

Por eso APRS resulta especialmente interesante en actividades de campo.

## ¿Qué puede hacer una radio portátil con APRS?

Aquí aparece una diferencia muy importante entre dispositivos.

La afirmación:

> **esta radio soporta APRS**

no indica todavía qué funciones ofrece realmente.

Las capacidades pueden variar mucho.

### Terminal APRS completo

Las radios portátiles más avanzadas pueden ofrecer por sí solas gran parte de las funciones APRS.

Dependiendo del modelo, pueden incluir:

- GPS integrado,
- transmisión automática de posición,
- SmartBeaconing,
- recepción y decodificación de paquetes de otras estaciones,
- lista de estaciones recibidas,
- visualización de distancia y rumbo,
- mensajería APRS,
- estados,
- objetos,
- información de frecuencia,
- configuración de la ruta APRS,
- acceso a un TNC integrado.

En este caso la radio es un verdadero terminal APRS autónomo.

No necesitamos un teléfono ni un ordenador para ver otras estaciones o responder a un mensaje.

## Los clásicos: Kenwood y Yaesu

Durante muchos años el APRS portátil se ha asociado principalmente con equipos de **Kenwood** y **Yaesu**.

Kenwood ha desarrollado varias generaciones de radios con una integración APRS muy completa. Un ejemplo moderno es la serie TH-D75.

Estos equipos no solo pueden transmitir su propia posición. También pueden recibir datos de otras estaciones, mostrar su ubicación, manejar mensajes y realizar otras funciones APRS.

Yaesu también ofrece desde hace años radios portátiles con una implementación APRS avanzada, incluidas varias de sus series FT.

Con este tipo de equipos, APRS puede funcionar completamente dentro del propio radiotransmisor.

## Cada vez hay más equipos con APRS

El mercado de radios portátiles ha cambiado mucho.

APRS ya no es una función reservada únicamente a unos pocos equipos caros de fabricantes japoneses.

Cada vez más radios, incluidas muchas fabricadas por empresas chinas, ofrecen GPS y funciones que sus fabricantes describen como APRS.

Es una evolución positiva.

Una mayor variedad de equipos significa:

- menor barrera de entrada,
- más opciones de hardware,
- más usuarios de APRS,
- mayor interés por la transmisión de datos desde radios portátiles.

Sin embargo, hay un detalle muy importante.

## "Soporta APRS" no siempre significa lo mismo

Al comprar una radio, no conviene confiar únicamente en una especificación que diga:

> **APRS supported**

En un equipo puede significar un terminal APRS completo.

En otro puede significar únicamente la posibilidad de transmitir periódicamente su propia posición.

La diferencia es enorme.

### Tracker APRS

La implementación más sencilla puede verse así:

```text
GPS
 |
Radio
 |
posición APRS
 |
RF
```

La radio obtiene su posición del GPS y transmite periódicamente un beacon APRS.

Otras estaciones y los iGate pueden recibirlo.

Sin embargo, para el usuario de la radio las funciones APRS pueden terminar ahí.

El equipo puede no ser capaz de:

- decodificar paquetes APRS de otras estaciones,
- mostrar una lista de estaciones,
- mostrar sus posiciones,
- recibir mensajes,
- enviar mensajes,
- gestionar objetos APRS,
- realizar funciones más avanzadas del protocolo.

En la práctica, este tipo de dispositivo es principalmente un **tracker APRS integrado en una radio portátil**.

No hay nada malo en ello si es exactamente lo que necesitamos.

El problema aparece cuando lo compramos esperando un terminal APRS completo.

## APRS analógico y sistemas digitales

También conviene comprobar **cómo implementa APRS la radio**.

Algunos equipos pueden transmitir APRS AFSK clásico por un canal analógico.

Otros envían información de posición dentro de un sistema digital o dependen de infraestructura adicional.

Desde el punto de vista del usuario, el resultado puede parecer similar: la posición aparece en un mapa.

Sin embargo, técnicamente pueden ser sistemas completamente distintos.

Si queremos utilizar la red APRS local clásica en 2 m, debemos comprobar que la radio realmente soporta APRS a través de un canal analógico.

## ¿Qué conviene comprobar antes de comprar?

Si APRS es una de las razones para comprar una radio, vale la pena responder primero a algunas preguntas.

### ¿La radio transmite APRS clásico por RF?

No solo a través de Internet, una aplicación o una red digital.

### ¿Recibe APRS?

Esta es una de las diferencias más importantes entre un terminal APRS y un tracker.

### ¿Muestra las estaciones recibidas?

Lo ideal es poder ver:

- indicativo,
- distancia,
- dirección,
- símbolo,
- comentario o estado.

### ¿Soporta mensajes APRS?

Conviene comprobar por separado:

- recepción de mensajes,
- envío de mensajes,
- confirmaciones ACK.

### ¿Se puede configurar la ruta APRS?

Por ejemplo:

```text
WIDE1-1,WIDE2-1
```

No todos los equipos permiten al usuario controlar completamente los parámetros APRS.

### ¿Soporta SmartBeaconing?

Durante el uso a pie o en movimiento, una frecuencia de beacon inteligente puede reducir considerablemente el tráfico innecesario.

### ¿APRS funciona sin teléfono?

Si queremos trabajar de forma autónoma en campo, esta es una pregunta especialmente importante.

Algunos equipos necesitan una aplicación de teléfono para parte de sus funciones APRS.

### ¿El fabricante documenta realmente la implementación APRS?

Conviene leer el manual del equipo antes de comprarlo.

La simple presencia de la palabra `APRS` en la descripción de una tienda no aporta suficiente información.

## El teléfono como complemento de la radio

Un smartphone y APRS no tienen por qué excluirse.

Algunas radios pueden colaborar con un teléfono mediante Bluetooth.

El teléfono puede ofrecer entonces una forma más cómoda de utilizar:

- la interfaz,
- el mapa,
- la escritura de mensajes,
- la lista de estaciones recibidas.

La radio sigue realizando la transmisión por RF.

Puede ser un compromiso muy práctico entre un terminal APRS portátil clásico y una instalación de campo más compleja.

## APRS portátil es mucho más que un punto en el mapa

La forma más sencilla de utilizar APRS en una radio portátil es:

```text
GPS -> beacon -> mapa
```

pero las posibilidades del sistema son mucho mayores.

Un terminal APRS completo puede permitir que un operador situado en la montaña vea:

```text
SQ9ABC-7
3.2 km
dirección: NE
QRV 145.500 MHz
```

y poco después llame a esa estación por voz.

Puede recibir un mensaje corto de otro operador.

Puede mostrar un objeto que marque un lugar de encuentro.

Puede indicar que otro radioaficionado se encuentra a pocos kilómetros.

En ese momento APRS deja de ser simplemente un sistema de seguimiento de posición.

Se convierte en aquello para lo que fue diseñado: **un sistema local de intercambio de información en tiempo real entre radioaficionados**.

## ¿Por dónde empezar?

Si ya tienes una radio con soporte APRS completo:

1. configura tu indicativo y SSID,
2. activa el GPS,
3. configura la frecuencia APRS utilizada en tu región,
4. configura una ruta adecuada,
5. establece un intervalo de beacon razonable o SmartBeaconing,
6. empieza observando las estaciones recibidas,
7. prueba los mensajes APRS,
8. después adapta la configuración a la forma en la que realmente utilizas la radio.

En Polonia y en gran parte de Europa, el APRS clásico en la banda de 2 m utiliza:

```text
144.800 MHz FM
```

Si todavía estás eligiendo equipo, decide primero qué esperas de APRS.

Si solo quieres que otros vean tu posición, un tracker sencillo puede ser completamente suficiente.

Si quieres **utilizar APRS como sistema de comunicación e información**, busca un equipo que no solo transmita su propia posición, sino que también reciba y muestre datos de otras estaciones y soporte mensajes APRS.
