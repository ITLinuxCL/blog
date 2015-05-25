---
layout: post
title: "Buscando mensajes de correo con Kibana"
date: 2015-05-25 09:45
comments: true
categories: [zimbra, logstash, kibana, devops]
keywords: zimbra, logstash, kibana, devops
description: Como utilizar Elastiseach, Logstash y Kibana para manejar logs de Zimbra y encontrar mensajes
author: "Patricio Bruna"
---
Como muchos de ustedes no saben, porque aun no publico el post correspondiente, en [IT Linux](http://www.itlinux.cl) y [ZBox](https://www.zboxapp.com) usamos el stack [ELK](https://www.elastic.co/videos/introduction-to-the-elk-stack) para indexar los logs producidos por las plataformas Zimbra que administramos.

´ELK´ no es más que las siglas de [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) y [Kibana](https://github.com/elastic/kibana). Y es usado ampliamente para el análisis de logs. De hecho hace unos meses Zimbra hizo un concurso sobre esto: https://community.zimbra.com/collaboration/f/1884/t/1138432

Nosotros no participamos del concurso, pero si publicamos nuestra configuración para quien desee usarla: [https://github.com/ITLinuxCL/zimbra_logstash](https://github.com/ITLinuxCL/zimbra_logstash).

## Tengo un monton de logs, ¿Cómo encuentro un mensaje?
El uso más comun de esta herramienta es para responder preguntas del tipo: _¿Puedes decirme que pasó con el mensaje enviado por xx a yy?_. Es decir, ¿que diablos paso con el correo que aun no llega?.

Como veremos, existen tres tipos de consultas:

* Cuando conocemos el From y To,
* Cuando conocemos solo el From,
* Cuando conocemos solo el To.

Empecemos con la primera que es la más rápida. Todas las búsquedas las haremos en Kibana.

### Buscando con From y To
Supongamos que nos piden un listado de todos los correos de las ultima hora  de ´pbruna@itlinux.cl´ para ´deugenin@itlinux.cl´.

Para ello ejecutamos los siguientes pasos en Kibana.

#### 1. Encontrar Message-ID en registros de Amavis
En el cuadro de búsqueda de Kibana ingresa lo siguiente:

´´´sql
component:"smtpd/amavis" AND from:"pbruna@itlinux.cl" AND to:"deugenin@itlinux.cl"
´´´

Solo nos interesan los campos ´messageid´ de estos resultados. Así que anótalos en algun lado.

**¿Por qué solo nos interesan los campos ´messageid´?**

Porque es lo único que identifica exclusivamente a un mensaje. El ´Message-ID´ es la huella digital del mensaje. Mientras que cosas como el ´queue_id´ pueden ser muchos y todos apuntar al mismo mensaje.

