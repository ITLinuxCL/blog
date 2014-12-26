---
layout: post
title: "Tutorial: Cálculo de direcciones IP"
date: 2014-12-24 09:37
comments: true
categories: [linux, howtos]
description: Guía para entender como realizar calculos de direccionamiento IP
author: Nicolás Fuenzalida
---

Aunque las direcciones IPv4 disponibles ya están a punto de acabarse, **sólo hay disponibles 3.432.192 de ip**, igual sigue siendo importante saber calcular la cantidad de hosts disponibles en una red, que a pesar de ser una información básica que se debe manejar, se me olvidó.

Empecemos este tutorial con los siguientes el siguiente segmento 192.168.3.0/24 red:

* **red**: 192.168.3.0
* **netmask**: 255.255.255.0

### ¿Cuántos hosts caben en esta red red?

Primero quiero que observes lo siguiente y luego comenzarás a entender como se realia el cálculo.

```
/32 = 11111111.11111111.11111111.11111111 = 255.255.255.255
/24 = 11111111.11111111.11111111.00000000 = 255.255.255.0
```


**Donde:**

 - ```/{CIDR}``` : Representa los bits activados que se usará para la red y lo restante para los hosts
 - ```11111111.11111111.11101100.00000000``` : Esto se le llama octetos, está dado de 4 octectos por 8 bits, lo cual multiplicado sería: 8 (bits) x 4 (octetos) = 32 bits y es lo que está soportado por IPv4.

Para calcular la cantidad de hosts que representa ```/24```, se restó:

```
32 - (la mascara de red o prefijo) = 32 - 24 = 8 bits de hosts
```

como ejemplo para la regla general se utiliza :

```
2 ^ 8 - 2 # traducido sería: 2 elevado a 8 menos 2
```

 - ```2```: que es la realación de 0 y 1.
 - ```8```: bits de hosts, este se obtuvo de la resta 32 - máscara de red.
 - ```-2```: se resta la ip de broadcast y la dirección de red.

Entonces sería: ```2 ^ 8 - 2 = 254``` ip disponibles para la red con CIDR /24. que daría algo como esto:

- ```192.168.0.0``` dirección de red
- ```192.168.0.1``` hasta ```192.168.0.254``` direcciones ip disponibles
- ```192.168.0.255``` dirección de broadcast

Y para saber la máscara de esta red, es más fácil aún, sólo tienes que dejar activados los bits, de la resta de ```32 - 8```.

Máscara de red:
```
11111111.11111111.11111111.00000000 = 255.255.255.0
```
