# netpractice
   
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


## Level 2

![Esquema Level 2](images/level2.png)

Descripción del ejercicio...

## Level 3

![Esquema Level 3](images/level3.png)

## Level 4

![Esquema Level 4](images/level4.png)


## Level 5

![Esquema Level 5](images/level5.png)


## Level 6

![Esquema Level 6](images/level6.png)

## Level 7

![Esquema Level 7](images/level7.png)


## Level 8

![Esquema Level 2](images/level8.png)

En este esquema a tener en cuenta que nos dan el destino de la ruta de internet con máscara /26 y en R2 nos dan la PE de la ruta default con una IP de ese mismo rango de red. Hago subneting en dicho rango y es necesario usar máscara /28 para que no haya solapamiento. En la interfaz R13 estamos obligados a poner la IP 161.246.151.62 que es la PE de la ruta del router R2. Aquí la duda puede ser que máscara utilizar. Si pusieramos máscara /26 las IPS de R13 y R21, 62 y 61 estarían en el rango 0-63, habría solapamiento de redes con los valores de las ips de las interfaces R22 y R23, 17 y 1 respectivamente. Por tanto es necesario /28. Con esta máscasra las Ips de R13 y R21, 62 y 61 están en la subred 48-63 y no se produce solapamiento con las otras subredes 0-15 y 16-31.


## Level 9

![Esquema Level 9](images/level9.png)

En este esquema no hago subneting, utilizo rangos de red diferentes para cada tramo. Como no podemos utilizar redes privadas al ser un esquema sin NAT, pongo rangos parecidos a los de las redes privadas, pero con pequeñas modificaciones, cambio 192.168.3.x, 10.0.0.x por 190.168.3.x, 50.0.0.x y 60.0.0.x para que sean rangos públicos. A tener en cuenta....En R1 tengo que añadir las rutas para llegar a cation y gluon, pero en las rutas de internet no tengo que añadir gluon al no necesitar acceso a internet. En las rutas de internet añado las de cation y meson que son los que necesitan acceso a internet. Esto no puede ser en un entorno real, Internet no tendría rutas específicas a hosts internos.

## Level 10

![Esquema Level 10](images/level10.png)

En este nivel dan muchos valores predeterminados, y sí realizo subneting. Lo más importante a tener en cuenta, en internet solo puedo añadir una ruta, por eso ponemos el rango con el que jugamos 153.223.108.0 con máscara /24 para que alcance todas las subredes. La única dificualtad es tener cuidado con la máscara que utilizamos en el rango de las interfaces R22 y H31, donde no hay nada predeterminado y podemos eligirlo. En los otros tramos si viene predeterminada la máscara, y el /25 de H21 y H11 hace que este segmento ya coja ips hasta la 127. El /26 de R23 y H41 nos ocupan las ips desde el 128 al 191. Si pongo un /28 en R22 y H31 no ontrolo muy bien si hay solapamiento, por eso elijo un /30 y pongo las ips del rango anterior a la red de las interfaces que comunican los routers R1 y R2. Pongo las ips 249 y 250 del tramo anterior al 251-255. Son subredes de 4 ips, y descartando la primera y última, que son utilizadas como ip de red y de broadcast respectivamente, se puede hacer sin mucho cálculo y con subneting este esquema que funciona.
