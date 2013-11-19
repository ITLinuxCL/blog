---
layout: post
title: "Un cuento de Análisis y mejora de rendimiento de Varnish Cache"
date: 2013-11-18 18:28
comments: true
categories: [devops, varnish]
description: Una historia de la vida de real sobre como analizar los logs de Varnish Cache y mejorar su desempeño.
author: Patricio Bruna 
---
Tenemos un excelente cliente con un par de portales web que suelen ser "víctimas" de un alto tráfico, uno de ellos es un noticiero, así que cuando algo pasa recibe hordas de visitantes. A principio de año comenzamos a trabajar con ellos para mejorar el desempeño de su plataforma web que ya estaba operando con una configuración básica de Varnish.

{% img center /images/varnish_503.png %}

Pasaron los meses y todo funcionaba correctamente. Los problemas de indisponibilidad del sitio debido a un aumento repentino de visitas cada vez que ocurría una noticia importante habían desaparecido, y el sitio en general respondía más rápido gracias al caché de Varnish. 

Pero todo cambio cuando se publicaron las listas de Vocales de Mesa.

Los Vocales de Mesa en Chile son las personas que reciben los votos en las elecciones de autoridades del país. Una vez que se publicó la lista el flujo de visitantes aumentó de forma radical y el sitio comenzó a funcionar de forma inestable, entregando muchos errores _503: Service Unavailable_. 

El primer síntoma que notamos fue que Varnish dejaba pasar muchas solicitudes de la misma página a los servidores que web, lo cual no debería ocurrir, ya que supuestamente sólo la primera solicitud va al servidor web y todas las siguientes se responden del cache de Varnish.

Aquí es donde comienza nuestro viaje para _debuguear_ Varnish a través de sus logs.

<!--more -->

### 1. Detectar las solicitudes que pasan
Lo primero que hicimos fue usar [Varnish Dashboard](/blog/2013/04/09/varnish-dashboard-metricas-en-tiempo-real/), que herramienta más buena :). Esto nos permitió ver en tiempo real y fácilmente como se estaba comportando el cache de Varnish.

Una vez que tuvimos la certeza de que el cache no funcionaba como debía y que muchas conexiones pasaban a los servidores web nos volcamos a descubrir cuales eran las solicitudes que más pedían los visitantes del sitio. Para ello usamos el comando ```varnishtop```:

```bash
# Listar lo más pedido por los visitantes
$ varnishtop -i RxURL

# Listar las solicitudes que más pasan a los serviores web
$ varnishtop -i TxURL

```

Un ejemplo del resultado del comando anterior sería:
{% img left /images/varnishtop_rxurl.png %}

__varnishtop__ es un comando similar a la herramienta ```top``` del sistema operativo y permite ver varias estadísticas de Varnish ordenadas de mayor a menor.

### 2. ¿Por qué pasan?
Ahora que ya sabíamos cual era la solicitud que pasaba a los servidores web, es decir, que no estaba siendo respondida desde el cache de Varnish, era momento de buscar la causa.

Para esta tarea nos apoyamos en la herramienta ```varnishlog```, que permite desplegar en tiempo real el registro de acciones de Varnish. Lo que nos interesaba en esta etapa era saber como se comporta el cache para la solicitud en cuestion.

Vamos a suponer que la solicitud que generaba el problema era www.noticias.com/pub/porque-sigues-usando-windows y que Varnish estaba configura para guarda una [cabecera con los HITS del cache](https://www.varnish-cache.org/trac/wiki/VCLExampleHitMissHeader):

```bash
# queremos saber cuantos hits tiene la solicitud
varnishlog -c -m RxURL:"pub/porque-sigues-usando-windows" | grep "X-Cache"
1430 TxHeader      c X-Cache: Varnish HIT #775
  943 TxHeader     c X-Cache: Varnish HIT #2 # <- Extraño
 1229 TxHeader     c X-Cache: Varnish HIT #1 # <- Extraño
  523 TxHeader     c X-Cache: Varnish HIT #776
 1551 TxHeader     c X-Cache: Varnish HIT #777
 1520 TxHeader     c X-Cache: Varnish HIT #1 # <- Extraño
 ...
 142 TxHeader     c X-Cache: Varnish HIT #783
 621 TxHeader     c X-Cache: Varnish HIT #784
 234 TxHeader     c X-Cache: Varnish HIT #785
 825 TxHeader     c X-Cache: Varnish HIT #786
```

Como se ve en el resultado el contador de Hits se comporta de forma extraña. Pareciera ser que para la solicitud existe más de un registro del cache, esto es lo único que explicaría que el contador de Hits fuera siempre incremental, así que seguimos nuestra investigación bajo esta hipótesis.


### 3. ¿Por qué habría más de un registro en cache para la misma solicitud?
Este era el momento de detenerse y volver a estudiar como es que Varnish guarda un registro en Cache. 

Después de una breve lectura de la documentación de Varnish, la que nos refrescala memoria, recordamos que por cada solicitud Varnish crea un [Hash](https://www.varnish-cache.org/docs/trunk/users-guide/vcl-hashing.html), es decir, un identificador único para acceder a los elementos en cache. También recordamos que existe la funcion ```vcl_hash``` que nos permite modificar el Hash creado por Varnish con nuestra propia información.

Con estos nuevos datos era momento de analizar el archivo de configuración de Varnish, el cual lucía más o menos así:

```c
if(!(req.url ~ "\?.*override=" ||
     req.url ~ "\.(png|gif|jpg|css|js|swf)$" ||
     req.url == "/favicon.ico" ||
     req.url ~ "\?.*service=") &&
    (req.http.User-Agent ~ "iPhone" ||
     req.http.User-Agent ~ "IEMobile" ||
     req.http.User-Agent ~ "PalmOS" ||
     req.http.User-Agent ~ "WindowsCE" ||
     req.http.User-Agent ~ "Windows CE" ||
     req.http.User-Agent ~ "Opera Mini" ||
     req.http.User-Agent ~ "Opera Mobi" ||
     req.http.User-Agent ~ "BlackBerry" ||
     req.http.User-Agent ~ "RIM" ||
     req.http.User-Agent ~ "Maemo" ||
     req.http.User-Agent ~ "OPWV" ||
     req.http.User-Agent ~ "portalmmm" ||
     req.http.User-Agent ~ "MIDP" ||
     req.http.User-Agent ~ "SymbianOS" ||
     req.http.User-Agent ~ "SonyEricsson" ||
     req.http.User-Agent ~ "Nokia" ||
     req.http.User-Agent ~ "Samsung" ||
     req.http.User-Agent ~ "Android")){
    hash_data(req.http.User-Agent);
}
```

La idea de esta configuración es generar un sitio web especial para los dispositivos móviles, pero a costa de que se va a generar un cache individual por cada texto de [User-Agent](http://es.wikipedia.org/wiki/Agente_de_usuario) existente, lo cual crece de forma exponencial.

Sólo piensa en todos los tipos de teléfonos que usan Android y las diferentes versiones de Android en el mercado. Yo lo quise comprobar por mi mismo y ver cuantos tipos diferentes de User-Agent podía obtener en 5 segundos:

```bash
# Solo tomamos el User-Agent
timeout -sHUP 5s varnishlog -c -m RxURL:"/"|grep User-Agent|grep -v Vary > /tmp/user-agent

# imprimimos desde la 5 columna, eliminamos los repetidos y contamos
awk '{print substr($0, index($0,$5))}' /tmp/user-agent |sort -u|wc -l
128
```

**Wow!!**, 128 distintos User-Agent en 5 segundos, es decir, **128 diferentes registros en el cache para la misma solicitud**. Parece que nos estamos acercando.

Al identifcar esta situación conversamos con los administradores de la aplicación que genera el contenido y nos comentaron dos cosas interesantes:

1. Desde que comenzamos a trabajar juntos el tráfico móvil aumento de 20% a 50%. Esto explicaría el porque al principio todo parecía funcionar correctamente.

2. El sistema CMS que usan genera el contenido de forma dinámica de acuerdo al User-Agent, pero ellos tienen el mismo sitio móvil para todos los dispositivos.


### 4. Eureka
Lo tenemos!!! Con toda esta información podíamos explicar claramente porque el sitio fallaba cuando se publicaba una noticia de alto impacto:

1. Las visitas eran en su mayoría desde dispositivos móviles, debido en gran parte a que se compartían por Twitter o Facebook.

2. Como el Hash se modificaba con el User-Agent, menos del 10% de las solicitudes tocaba el cache y por lo tanto saturaban los servidores web.

3. Al saturarse los servidores web, el sitio comenzaba a funcionar de forma incorrecta.


### 5. La solución
La solución es sencilla y bastante documenta por todo Internet. Básicamente consiste en sanitizar el campo User-Agent, es decir, si se usa un dispositivo Android dejamos el campo User-Agent como: ```User-Agent: android-movil```.

Varnish tiene un módulo comercial para detectar dispositivos móviles llamado [Varnish's Mobile Detection Module](https://www.varnish-software.com/product/mobile-device-detection), el cual es altamente eficiente y está soportado por la empresa Varnish Software.

Para nuestro caso nos fuimos por una solución completamente gratis y libre: [https://github.com/varnish/varnish-devicedetect/](https://github.com/varnish/varnish-devicedetect/).

### 6. Resultado
El resultado fue impresionante: bajamos de 200 solicitudes que pasaban a la granja de servidores, a sólo 10. Y en las pasadas elecciones fuimos capaces de responder sin ningún problema durante toda la jornada a un promedio de 8 mil usuarios concurrentes, con peaks de 16 mil.



#### Publicidad
¿Quieres mejorar 100 veces el desempeño de tu sitio sin tener que contar con un presupuesto 100 veces mayor? [Llámanos](http://www.itlinux.cl/contacto/).