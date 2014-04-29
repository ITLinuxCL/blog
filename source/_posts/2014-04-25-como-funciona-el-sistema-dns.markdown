---
layout: post
title: "Como funciona el sistema DNS"
date: 2014-04-25 17:55
comments: true
keywords: linux, admin, dns, devops
categories: [linux, admin, dns, devops]
description: Guía para entender como funciona el sistema de nombres de dominio (DNS) y la resolución de direccions IP
author: Patricio Bruna
---
{% img right /images/DNS.jpg 325 %}
El Sistema de Nombres de Dominio (DNS por su siglas en inglés) en uno de los servicios más críticos de Internet. Sin DNS, practicamente no podríamos acceder a la web. Antes de entrar en los detalles de como DNS funciona, una pequeña introducción se hace necesaria.

_Observación: Si te sientes superior, puedes saltarte esta sección ;)_

### Introducción

Cuando tu visitas www.itlinux.cl, el tráfico que se origina en tu computador atraviesa el _backbone_ de tu ISP (Proveedor de Servicio de Internet) y este lo envía a su proveedor, y así sucesivamente hasta que llega a la red de IT Linux. En la Internet actual basada en TCP/IP, el tránsito desde tu computador hasta nuestra red es controlado por un conjunto de _routers_ intermediarios que determinan las rutas según la dirección IP del servidor web de IT Linux.

Esto significa que necesitamos primero necesitamos conocer la dirección IP del servidor web de IT Linux para poder visitar la página. ¿Te imaginas tener que recordar cientos de direcciones IP para poder navegar la web? ¿Qué tal si existiera un mecanismo que hiciera este trabajo más fácil?

Bueno, para ello está el servicio DNS, el que mantiene registro de todos los nombres de dominios y su dirección IP correspondiente. Así que si nos queremos comunicar con un servidor web podemos usar su nombre de dominio, por ejemplo: www.example.com. El servidor DNS controla la conversión del nombre de dominio a su dirección IP, para que nuestro computador se pueda comunicar con el servidor web de destino utilizando la dirección IP.

### Terminología

_Observación: Si te sientes superior, igual lee esta parte_

Esta sección es una lista larga de definiciones. Para poder tener un conocimiento acabado de la operación de DNS es importante entender estos términos. De todas formas no tienes para que aprenderlos de memoria, siempre puedes marcar esta página como favorito y volver cuando necesites refrescar tu memoria.

Volviendo al tema, todos los nombres DNS son jerárquicos. Los documentos que definen el estándar RFC puedes ubicarlos en [http://www.zoneedit.com/doc/rfc/](http://www.zoneedit.com/doc/rfc/). El más importante de todos debe ser [RFC 1035](http://tools.ietf.org/html/rfc1035).

#### Sintaxis DNS
Como dijimos, todos los nombres DNS son jerárquicos y generalmente están divididos en 3 partes: **host.domain.tld**. Si tomamos como ejemplo el nombre de dominio www.example.com, tenemos que:

* **www** es la parte que indica cual es el host,
* **.example** indica cual es el dominio, y
* **.com** es el dominio de nivel principal (TLD)

La sección que viene explica esto en más detalle.

<!-- more -->

#### Servidores y Zonas Raíces
Los [servidores raíz](http://en.wikipedia.org/wiki/Root_name_server) y las [zonas raíz](http://en.wikipedia.org/wiki/DNS_root_zone) están en el nivel más alto del mundo jerárquico de DNS. Las zonas raíz mantienen los dominios de nivel principal. Los servidores usados para administrar estas zonas se llaman los servidores raíz. Hay un conjunto de 13 servidores raíz, cuyos nombres son del tipo a.root-servers.net, b.root-server.net, c.root-servers.net, d.root-servers.net, hasta llegar a m.root-servers.net. El número 13 se refiere a la cantidad de entidades y no en realidad a la cantidad de servidores. Cada entidad puede contener cientos de dispositivos y servidores en sus data centers, por motivos de disponibilidad, balanceo de carga y redundancia. Los servidores raíz comparten una base de datos distribuida y se comunican entre ellos para recibir actualizaciones de esta db.


#### Dominio de nivel principal (TLD)
Un dominio de nivel prinicipal, o TLD para los amigos, es la parte que se encuentra más a la derecha en un nombre DNS, por ejemplo: .com, .org, .net, .gov, .mil. Todos los nombres de dominio deben ser registrado bajo algún TLD. los TLDs son administrados por las Zonas Raíz.


#### Dominio
Un dominio es una "cadena de texto" registrada bajo un TLD que será usada por una organización o usuario como parte su nombre. DNS es flexible en cuanto a lo que viene bajo la jerarquía de un dominio. En cuanto a la sintáxis, la parte que refleja el nombre del dominio es escrito a la izquierda del TLD. Por ejemplo, en www.example.com, la parte **example** es el nombre del dominio. Los Dominios son mantenidos por la Autoridad de Nombres de Dominio, en el caso de Chile por ejemplo esta autoridad es el NIC.

#### Sub Dominio
Un sub dominio es un parámetro opcional que está escrito a la izquierda del dominio. El usuario es libre de administrar sub-dominios dentro de un dominio. Los registros de sub dominios son administrados por el servidor DNS que administra el dominio. Por ejemplo, en www.branch-1.example.com el texto branch-1 es un sub dominio.

#### Nombre de Host (Hostname)
El Nombre de Host generalmente es la parte ubicada más a la izquierda del dominio, e indica cual es el servidor que esta hospedando los servicios. Los administradores tienen completo acceso a esta parte, y pueden delegar nombres como les plazca. Por ejemplo, en www.example.com and ftp.example.com, los textos www y ftp son nombres de hosts, e indican servidores web y ftp.

#### Authoritative Name Server
El Authoritative Name Server de un dominio es responsable de administrar todos los registros DNS relevantes del dominio. Naturalmente es imposible, como también ineficiente que los servidores raiz administren todos los mapeos DNS y registros para cada dominio existente. En vez de eso, los servidores de autoridad de cada dominio deben administrar todos los registros de sus respectivos dominios. Los servidores raiz manejan solo la información de los servidores autorizdad de los dominios de primer nivel.


### Como funciona DNS
{% img right http://farm8.staticflickr.com/7382/13299249873_0083c3fa97_z.jpg 300 %}

Asumamos que un usuario intenta visitar www.example.com. Luego que el usuario ingresa la URL en el navegador y presiona Enter, la página se carga en tu navegador. Para que esto suceda, deben ocurrir los siguientes eventos:

1. El computador del usuario revisa el cache interno de DNS para resolver la dirección IP. Si no se encuentra, el usuario re-envia la consulta al servidor DNS local (configurado en el PC). Generalmente este servidor DNS está ubicado localmente si se trata de una empresa, o lo entrega el ISP si es un usuario de hogar.

2. El servidor DNS local revisa su cache de DNS en búsqueda de la dirección IP. Si no se encuentra, el servidor DNS local consulta a los servidores raíz cual es el servidor autoridad del dominio en cuestión. Este tipo de consultas se llama **consulta recursiva**.

3. El servidor raiz responde con la información necesaria del servidor autoridad.

4. El servidor DNS local ahora pregunta directamente por la dirección IP de www.example.com al servidor DNS autoridad del dominio example.com, el cual debe tener todos los registros DNS del dominio.

5. El servidor autoridad del dominio example.com responde la consulta entregando la dirección 100.100.100.100 para el host ```www```.

6. El servidor DNS local almacena esta respuesta para futuras consultas, y entrega la dirección IP al computador que originó la consulta.

7. El computador también almacena la dirección IP en su cache. Luego el navegador web genera una solicitud HTTP para el host ```www``` del dominio ```example.com```, el cual se encuentra en  100.100.100.100.

8. El servidor ```www``` responde a la solicitud y entrega la página al navegador web.

Raya para la suma, navegar en Internet es fácil gracias al servicio entregado por los servidores DNS. Pero como pudimos ver, en silencio se ejecutan un serie de actividades.


_Disclaimer_: Esta fue una traducción de [http://xmodulo.com/2014/03/how-dns-works.html](http://xmodulo.com/2014/03/how-dns-works.html)



























