# netpractice

## Introducción

Este repositorio contiene la resolución práctica de los 10 niveles del proyecto **NetPractice** de 42. Cada nivel introduce conceptos progresivos de redes TCP/IP, desde direccionamiento básico hasta enrutamiento complejo con subneting.

La documentación incluye:
- **Esquema visual de subneting y máscaras de red** como referencia rápida
- **Explicación de cada nivel** con enfoque práctico, introduciendo la teoría necesaria y los conceptos clave de cada esquema
- **Consejos y soluciones** orientados a la resolución eficiente de los ejercicios

El objetivo es comprender no solo cómo completar cada nivel, sino **por qué** funciona cada solución.

---

## Máscaras de Red / Subneting
   
   ![Máscaras de Subneting](images/net3_dark.png)

   ## Índice de Ejercicios

- [Level 1](#level-1)
- [Level 2](#level-2)
- [Level 3](#level-3)
- [Level 4](#level-4)
- [Level 5](#level-5)
- [Level 6](#level-6)
- [Level 7](#level-7)
- [Level 8](#level-8)
- [Level 9](#level-9)
- [Level 10](#level-10)


## Level 1

![Esquema Level 1](images/level1.png)

Tenemos que poner las IPs de la interfaz D1 y A1, en dos redes diferentes. D1 pertenece a una red con máscara /16 (clase B 255.255.0.0), por lo que solo es necesario que coincidan las Ips de los dos primeros octetos (211.191). En los dos siguientes podemos elegir cualquier valor entre 0-255, descartando el 0 que es el valor de red y el 255, que es el de broadcast. 

En la otra red la máscara es /24 (clase C 255.255.255.0), tienen que coincidir los valores de los 3 primeros octetos (104.95.23) y en el último elegimos cualquier valor entre 0-255 descartando primero y último.

## Level 2

![Esquema Level 2](images/level2.png)

En este esquema nos ponen un rango que no podemos usar en una de las redes: 127.0.0.0/8, porque es el rango de loopback (dirección reservada para comunicación interna del propio dispositivo, como 127.0.0.1 "localhost"). Tenemos que borrarlo completamente y elegir otro rango válido, por ejemplo 10.0.0.0.

**Red con máscara /30:**
La máscara /30 nos la dan predeterminada y no es modificable. Esta máscara divide la red en subredes de solo 4 IPs cada una. En la primera subred (0-3) podemos usar las IPs 1 y 2, descartando la primera (0 - red) y la última (3 - broadcast).

**Red con máscara /28:**
Tenemos que poner en la interfaz B1 la misma máscara que A1, que es /28 (255.255.255.224). Una máscara /28 crea subredes de 16 IPs cada una. Como calcular el rango exacto no es fácil bajo presión, podemos elegir para A1 la IP anterior o posterior a la de B1 y probar.

En este caso, con /28 la subred donde está la IP de B1 (222) es el rango 208-223. Elegir la IP siguiente (223) nos daría error al ser la dirección de broadcast, por eso elegimos la anterior: 221.

**Consejo práctico:** Si no puedes hacer estos cálculos rápidamente en la evaluación, siempre puedes probar con la IP inmediatamente anterior o posterior. Si una no funciona, la otra debería ser válida, como ocurre en este caso.

## Level 3

![Esquema Level 3](images/level3.png)

Tenemos 3 hosts conectados a un switch. Nos dan la máscara /25 (255.255.255.128) en uno de ellos y la IP en otro (104.198.130.125). 

**Concepto clave - Todos los hosts en un switch deben estar en la misma red.**

Calcular los rangos de las subredes con /25 es sencillo (puedes consultar el [esquema de máscaras y subneting](#máscaras-de-red--subneting) al inicio). La máscara /25 divide el último octeto en 2 subredes:
- Primera subred: 0-127 
- Segunda subred: 128-255

Como nos dan la IP 104.198.130.125, estamos en la primera subred (0-127). Para los otros dos hosts debemos elegir IPs también en este rango. Podemos usar la 126 para un host, pero no podemos utilizar la 127 al ser la IP de broadcast de esta subred. Por tanto, para el tercer host elegimos la 124 (anterior a la 125).

En resumen, en esta subred podríamos elegir cualquier IP desde 1 hasta 126 (descartando 0 para red y 127 para broadcast), siempre que no se repitan.

## Level 4

![Esquema Level 4](images/level4.png)

Tenemos dos hosts conectados a un switch, y este conectado a un router con 3 interfaces. Nos dan valores predeterminados en dos interfaces del router, y debemos completar la tercera evitando solapamiento de redes.

**Concepto clave - Evitar solapamiento con subneting:**
Las tres interfaces del router deben estar en redes diferentes. Analizamos qué rangos están ocupados:

- **Interfaz R2**: Máscara /25 e IP terminada en 1 → Ocupa el rango 0-127 (128 IPs)
- **Interfaz R3**: Máscara /26 e IP 244 → Ocupa el rango 192-255 (64 IPs)
- **Rango libre**: 128-191

**Solución para R1 y los hosts:**
Podemos usar el rango libre (128-191) con máscara /26. Por ejemplo:
- **R1** (interfaz del router): 129
- **Hosts**: Nos dan la IP 132 para uno. Para el otro host podemos usar 130. Ambos con máscara /26.

Todas las IPs en esta red deben estar en el mismo rango 128-191 y usar la misma máscara /26.


## Level 5

![Esquema Level 5](images/level5.png)

En este esquema tenemos dos hosts conectados directamente a dos interfaces de un router.

**Concepto clave - Rutas estáticas:**
Cada interfaz del router está en un rango de red distinto, y es necesario añadir **rutas estáticas** en cada host para que puedan alcanzar la red del otro lado del router. 

Una ruta tiene dos componentes:
- **Destino** (izquierda): La red a la que quiero llegar
- **Puerta de enlace/Gateway** (derecha): La IP del router por el que debo salir

La puerta de enlace siempre es la dirección de la interfaz del router al que estoy conectado, y la IP del host debe estar en ese mismo rango de red.

**Configuración de rutas y redes:**

- **Ruta en MachineA:**
  Podemos especificar la ruta de dos formas:
  - Ruta específica: `130.116.39.0/16 → 83.218.90.126`
  - Ruta por defecto: `default → 83.218.90.126` (significa: todo lo que no esté en mi red, envíalo por esta puerta de enlace). **Es más práctico usar `default ó 0.0.0.0/0` , ya que es más genérico y evitamos posibles errores al especificar la ruta exacta.**
  
  **Interfaz A1**: Máscara /25 e IP del router 126 → Rango 0-127. El host puede usar cualquier IP del rango (por ejemplo, 125).

- **Ruta en MachineB:**
  Tenemos como valor fijo el destino `default`. Debemos añadir la puerta de enlace, que es la IP de la interfaz del router conectada a esta red:
  - `default → 130.116.39.254`
  
  **Interfaz B1**: Máscara /18 e IP del router 130.116.39.254 → El subneting está en el segundo octeto, donde tenemos 4 subredes de 64 valores cada una (0-63, 64-127, 128-191, 192-255). Al tener en el router la IP con valor 39 en el segundo octeto, estamos en el rango 0-63. 
  
  **Consejo práctico:** En casos donde el subneting no se hace en el último octeto, lo más fácil es mantener el mismo valor en ese octeto (39) y en el último usar cualquier IP del rango 1-253 (evitando 254 que ya tiene el router y 255 de broadcast).

## Level 6

## Level 6

![Esquema Level 6](images/level6.png)

En este esquema tenemos un host conectado a un switch, a su vez conectado a un router, y este conectado a Internet. Introducimos el concepto de enrutamiento desde Internet hacia redes internas.

**Configuración de rutas:**

- **Ruta en HostA:**
  No nos dan ningún valor predeterminado. Debemos configurar:
  - **Destino**: `default` (cualquier red externa)
  - **Gateway/PE**: Debe ser la IP de la interfaz R1 del router (en el mismo rango que la interfaz A1)
  
  También completamos la máscara en A1: /25 (255.255.255.128).

- **Ruta en el Router de Internet:**
  **Concepto clave - Internet no puede usar rutas `default`:**
  
  Aquí **no podemos poner `default` como destino** porque Internet no "sabe" a dónde llegar si no especificamos la red. Debemos indicar explícitamente la red de destino (la red del HostA) con su dirección de red y máscara.
  
  HostA tiene máscara /25 y está en el segundo segmento del último octeto (128-255). Técnicamente, la dirección de red específica sería:
  - `111.124.199.128/25`
  
  Sin embargo, **como consejo práctico**, es más seguro especificar todo el rango del tercer octeto con /24 para evitar errores de cálculo:
  - `111.124.199.0/24` (abarca ambas subredes: 0-127 y 128-255)
  
  Esta ruta más amplia funciona perfectamente y se evitan errores.

  **Gateway/PE**: La IP de la interfaz del router que conecta con Internet, que ya viene dada.

## Level 7

![Esquema Level 7](images/level7.png)

Tenemos dos hosts conectados a dos routers que están conectados entre sí. Nos dan las IPs de las dos interfaces del router R1.

**Concepto clave - Comunicación entre routers:**
En este nivel debemos configurar correctamente las rutas para que ambos hosts puedan comunicarse a través de los dos routers intermedios.

**Configuración de Router R1:**

- **Interfaz R11** (conectada a HostA): IP 110.198.14.1
  - Esta IP es la que debemos usar como **gateway/PE** en la ruta del HostA
  - **Ruta en HostA**: `default → 110.198.14.1`

- **Interfaz R12** (conectada a R2): IP 110.198.14.254
  - Para evitar que ambas interfaces estén en la misma red, hacemos **subneting con máscara /25**
  - Esto crea dos subredes: 0-127 y 128-255
  - R11 (IP .1) queda en la primera subred (0-127)
  - R12 (IP .254) queda en la segunda subred (128-255)

- **Ruta en R1**: 
  - **Destino**: Red de HostB (o `default`)
  - **Gateway/PE**: 110.198.14.253 (IP de la interfaz R21 del router R2, en el mismo rango 128-255)

**Configuración de Router R2:**

- **Interfaz R21** (conectada a R1): IP 110.198.14.253
  - Debe ser la IP que pusimos como gateway en la ruta de R1
  - Máscara /25 para estar en la subred 128-255

- **Interfaz R22** (conectada a HostB): 
  - **Solución 1 (más fácil)**: Usar una red completamente diferente, por ejemplo 50.0.0.x
    - No requiere cálculos de subneting
    - Es la solución mostrada en la captura
  - **Solución 2 (alternativa)**: Seguir con el mismo rango 110.198.14.x pero con una máscara diferente para mayor segmentación

- **Ruta en R2**: 
  - **Destino**: Red de HostA (o `default`)
  - **Gateway/PE**: 110.198.14.254 (IP de la interfaz R12 de R1)

**Consejo práctico:** Usar rangos de red completamente diferentes para cada segmento (como en la solución 1) simplifica el ejercicio y evita errores de cálculo en subneting.

## Level 8

![Esquema Level 2](images/level8.png)


En este esquema nos dan el destino de la ruta de internet con máscara /26 y en R2 nos dan la puerta de enlace (PE) de la ruta default con una IP de ese mismo rango de red. Hago subneting en dicho rango y es necesario usar máscara /28 para que no haya solapamiento. 

En la interfaz R13 estamos obligados a poner la IP 161.246.151.62 que es la PE de la ruta del router R2. Aquí la duda puede ser qué máscara utilizar. Si pusiéramos máscara /26 (64 IPs, rango 0-63), las IPs de R13 y R21 (62 y 61) estarían en el mismo rango que las interfaces R22 y R23 (17 y 1), produciendo solapamiento de redes. 

Por tanto es necesario /28 (16 IPs por subred). Con esta máscara las IPs de R13 y R21 (62 y 61) están en la subred 48-63 y no se produce solapamiento con las otras subredes: 0-15 y 16-31.


## Level 9

![Esquema Level 9](images/level9.png)

En este esquema no hago subneting, utilizo rangos de red diferentes para cada tramo. Hay muy pocos valores que nos dan predeterminados, y para mi la dificultad mayor es que esta libertad de elección de rutas me hizo por concepto y por costumbre utilizar redes privadas, cuando no podemos al no ser esquemas con NAT (Network Address Translation, que traduce direcciones privadas a públicas para salir a Internet).

**Punto clave - Redes públicas vs privadas:**
Como no hay NAT , no podemos utilizar redes privadas (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), ya que Internet no enruta tráfico a estas direcciones. Por tanto, uso rangos públicos similares a los privados:
- 192.168.3.x → 190.168.3.x
- 10.0.0.x → 50.0.0.x y 60.0.0.x

**Configuración de rutas:**
- En R1: Añado rutas para llegar a cation y gluon (ambos hosts internos).
- En Internet: Solo añado rutas hacia cation y meson, que son los hosts que necesitan acceso a Internet. Gluon no necesita acceso a Internet, por lo que no es necesario que Internet tenga una ruta hacia él.

**Nota importante:** Esto no sería realista en un entorno real, ya que Internet no tendría rutas específicas hacia hosts internos. En la práctica, esto  se resolvería con NAT y redes privadas.

## Level 10

![Esquema Level 10](images/level10.png)

En este nivel dan muchos valores predeterminados y sí realizo subneting. 

**Restricción clave - Ruta única en Internet:**
En Internet solo puedo añadir una ruta, por eso uso el rango 153.223.108.0 con máscara /24 para que alcance todas las subredes del esquema.

**Análisis del espacio de direcciones (153.223.108.0/24):**
Los valores predeterminados ya ocupan gran parte del espacio:
- /25 (H21-H11): IPs 0-127 (128 direcciones)
- /26 (R23-H41): IPs 128-191 (64 direcciones)
- Espacio disponible: 192-255

**Solución para R22-H31:**
La única dificultad es elegir la máscara para las interfaces R22 y H31, donde no hay valores predeterminados. Si pusiera /28 (16 IPs) no controlo bien si hay solapamiento con el rango 192-255, por eso elijo /30 (4 IPs) que me da mayor control.

Uso las IPs 249 y 250, que están en el rango justo anterior a las IPs 251-254 que son usadas para la comunicación entre routers R1 y R2, con máscara /30, y son predeterminadas. Con /30 tengo subredes de 4 IPs cada una, y descartando la primera (red) y la última (broadcast), puedo asignar las dos del medio sin solapamiento.

Esta solución funciona sin necesidad de cálculos complejos y evita conflictos entre subredes.
