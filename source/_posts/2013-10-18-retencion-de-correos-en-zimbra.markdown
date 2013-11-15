---
layout: post
title: "Retención De Correos en Zimbra"
date: 2013-10-18 16:55
comments: true
categories: [linux, tips, zimbra, correo]
description: Como borrar de forma automática correos antiguos en Zimbra
author: Daniel Eugenin
---
Hace algún tiempo, un cliente nos solicitó una tarea poco común, pero que nos ha llevado a pensar en alguna solución práctica que pueda ser aplicada por aquellos que requieran de algo parecido.

La problemática era con respecto a la retención de correos, esto quiere decir, por ejemplo:

{% blockquote %}
Necesito tener sólo los correos desde cierta fecha en adelante, ¿cómo borro los antiguos?
{% endblockquote %}

Para esto, en Zimbra Web existe una solución bastante práctica y que se refiere a la retención de correos, para una carpeta en específico. Y su configuración es muy simple:

1) Presione el botón derecho del mouse sobre la carpeta que necesita eliminar correos antiguos y seleccione Editar Propiedades 
{% img center https://itlinux.zendesk.com/hc/en-us/article_attachments/200110716/Captura_de_pantalla_2013-10-07_a_la_s__17.49.38.png %}

2) Diríjase a la pestaña de Retención
{% img center https://itlinux.zendesk.com/hc/en-us/article_attachments/200180763/Captura_de_pantalla_2013-10-07_a_la_s__17.49.51.png %}

3) Habilite la segunda opción y defina un tiempo en días/meses/años, por ejemplo:
{% img center https://itlinux.zendesk.com/hc/en-us/article_attachments/200110736/Captura_de_pantalla_2013-10-07_a_la_s__17.50.18.png %}


Esto anterior significa que se mantendrán todos los correos de aquella carpeta sólo de hace un año atrás a la fecha. Es decir, diariamente se irán borrando aquellos correos que tengan más de un año de antigüedad.

Como nota adicional también se debe mencionar que la primera vez que active esta opción en alguna de sus carpetas, los resultados se reflejarán al otro día, no es de manera inmediata.