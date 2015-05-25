---
layout: post
title: "Buscando mensajes de correo de Zimbra con Kibana"
date: 2015-05-25 09:45
comments: true
categories: [zimbra, logstash, kibana, devops]
keywords: zimbra, logstash, kibana, devops
description: Como utilizar Elastiseach, Logstash y Kibana para manejar logs de Zimbra y encontrar mensajes
author: "Patricio Bruna"
---
{%img right /images/screen_logstash_350x250.png %}
Como muchos de ustedes no saben, porque aun no publico el post correspondiente, en [IT Linux](http://www.itlinux.cl) y [ZBox](https://www.zboxapp.com) usamos el stack [ELK](https://www.elastic.co/videos/introduction-to-the-elk-stack) para indexar los logs producidos por las plataformas Zimbra que administramos.

`ELK` no es más que las siglas de [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) y [Kibana](https://github.com/elastic/kibana). Y es usado ampliamente para el análisis de logs. De hecho hace unos meses [Zimbra hizo un concurso sobre esto](https://community.zimbra.com/collaboration/f/1884/t/1138432).

Nosotros no participamos del concurso, pero si publicamos nuestra configuración para quien quiera usarla: [https://github.com/ITLinuxCL/zimbra_logstash](https://github.com/ITLinuxCL/zimbra_logstash).

## Tengo un monton de logs, ¿Cómo encuentro un mensaje?
El uso más comun de esta herramienta es para responder preguntas del tipo: _¿Puedes decirme qué pasó con el mensaje enviado por xx a yy?_. O como dice el cliente: ¿Qué diablos paso con el correo que aún no llega?.

Como veremos, existen dos tipos de consultas:

* Tenemos `Amavisd` funcionando
* No tenemos `Amavisd`

Empecemos con la primera que es la más rápida. Todas las búsquedas las haremos en Kibana.

### Buscando con Amavisd
Supongamos que nos piden un listado de todos los correos de las ultima hora  de `pbruna@example.com` para `deugenin@example.com`.

Para ello ejecutamos los siguientes pasos en Kibana.

#### 1. Buscar Message-ID en registros de Amavis
En el cuadro de búsqueda de Kibana ingresa lo siguiente:
```sql
tags:"amavis" AND tags:"result" AND from:"pbruna@example.com" AND to:"deugenin@example.com"
```

{% img center /images/discover_amavis.png %}

Del reultado obtenido sólo nos interesa el campo `messageid`

{% img center /images/amavis_messageid.png %}

Como se ve en la imagen en nuestro caso el `messageid` es `200345754.22435379.1432571094009.JavaMail.zimbra@itlinux.cl`

**¿Por qué solo nos interesan los campos `messageid`?**

Porque es lo único que identifica exclusivamente a un mensaje. El `Message-ID` es la huella digital del mensaje. Mientras que cosas como el `queue_id` pueden ser muchos y todos apuntar al mismo mensaje.

#### 2. Buscar todas las colas generadas para este mensaje

Ahora que tenemos el `messageid` debemos buscar todas las colas que se generaron. `Postfix` genera una cola por cada proceso que trabaja con el mensaje. Estos procesos pueden ser `Amavisd`, `DKIM`, y otro filtro que tengas.

En el cuadro de búsqueda de Kibana ingresa lo siguiente:
```sql
component:"cleanup" AND messageid:"200345754.22435379.1432571094009.JavaMail.zimbra@itlinux.cl"
```

{% img right /images/amavis_qids.png %}
La cantidad de resultados pueden variar según los filtros que se apliquen al mensaje. Para este ejemplo obtuvimos 4 resultados. Por cada resultado anota el valor del campo `qid`, que en nuestro caso fueron:

* `5261B2816D5`,
* `31D4F2816E1`,
* `2D79C2816DD` y
* `0CCC62816D5`

El encontrar 4 `queue_id` diferentes para el mismo mensaje quiere decir que el mensaje fue procesado por 4 diferentes programas, sabemos que el primero de ellos es `smtpd` que recibe el correo y el último debería ser `smtp` o `lmtp` que entrega el correo al destinatario.

#### 3. Obtener traza completa del mensaje

Por último tenemos que buscar todos los registros (`logs`) relacionados con los `qids` obtenidos en el paso anterior y así tendremos la traza completa del mensaje.

En el cuadro de búsqueda de Kibana ingresa lo siguiente:
```sql
qid:"5261B2816D5" OR qid:"31D4F2816E1" OR qid:"2D79C2816DD" OR qid:"0CCC62816D5"
```

Esto resultó en 21 registros, 21 líneas de un archivo de log. A continuación ofrecemos una explicación resumida de que nos cuenta cada uno de ellos:

```
 # 1. Se establece la conexión
2015-05-25T13:24:54.053455-03:00 mta-in-01 postfix/smtpd[27693]: 0CCC62816D5: client=mailbox-02.zboxapp.com[182.168.0.152]

# 2. Se asigna el Message-ID
2015-05-25T13:24:54.058674-03:00 mta-in-01 postfix/cleanup[31598]: 0CCC62816D5: message-id=<200345754.22435379.1432571094009.JavaMail.zimbra@itlinux.cl>

# 3. Se encola
2015-05-25T13:24:54.062257-03:00 mta-in-01 postfix/qmgr[1595]: 0CCC62816D5: from=<pbruna@example.com>, size=2750, nrcpt=1 (queue active)

# 4. Lo Entregamos a DKIM
2015-05-25T13:24:54.306708-03:00 mta-in-01 postfix/smtp[31504]: 0CCC62816D5: to=<deugenin@example.com>, relay=127.0.0.1[127.0.0.1]:10026, delay=0.26, delays=0.01/0/0.01/0.24, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10030): 250 2.0.0 Ok: queued as 31D4F2816E1)

# 5. Lo sacamos de la cola
2015-05-25T13:24:54.323934-03:00 mta-in-01 postfix/qmgr[1595]: 0CCC62816D5: removed

# 6. Nos saltamos algunos pasos para mostrar que ahora DKIM lo pasa a Amavisd
2015-05-25T13:24:54.342114-03:00 mta-in-01 postfix/smtp[31468]: 31D4F2816E1: to=<deugenin@example.com>, relay=127.0.0.1[127.0.0.1]:10025, delay=0.14, delays=0.12/0/0.01/0.01, dsn=2.0.0, status=sent (250 2.0.0 Ok: queued as 5261B2816D5)

# 7. Amavis lo recibe y le asigna queue_id
2015-05-25T13:24:54.186406-03:00 mta-in-01 postfix/amavisd/smtpd[28298]: 2D79C2816DD: client=localhost[127.0.0.1]

# 8. Amavis lo procesa
2015-05-25T13:24:54.305138-03:00 mta-in-01 amavis[31520]: (31520-03) Passed CLEAN {RelayedInternal,Archived}, ORIGINATING/MYNETS LOCAL [192.168.0.152]:33696 <pbruna@example.com> -> <deugenin@example.com>, quarantine: deugenin-20150309@itlinux.cl.archive, pbruna-20150307@itlinux.cl.archive, Queue-ID: 0CCC62816D5, Message-ID: <200345754.22435379.1432571094009.JavaMail.zimbra@itlinux.cl>, mail_id: bStz4_w6-5Pp, Hits: -, size: 2750, queued_as: 31D4F2816E1, 239 ms

# 9. Postfix recibe nuevamente el mensaje procesado por Amavisd
2015-05-25T13:24:54.342158-03:00 mta-in-01 postfix/qmgr[1595]: 5261B2816D5: from=<pbruna@example.com>, size=3910, nrcpt=1 (queue active)

# 10. Postfix entrega el mensaje en la casilla
2015-05-25T13:24:54.453188-03:00 mta-in-01 postfix/lmtp[28300]: 5261B2816D5: to=<deugenin@example.com>, relay=mailbox-02.zboxapp.com[182.168.0.172]:7025, delay=0.12, delays=0.01/0/0/0.1, dsn=2.1.5, status=sent (250 2.1.5 Delivery OK)

# 11. Se elimina el mensaje de la cola
2015-05-25T13:24:54.453439-03:00 mta-in-01 postfix/qmgr[1595]: 5261B2816D5: removed

```

*Wow!!!!* Ese si que fue un paso por la vida de un mensaje de correo :).
Para mantener el artículo lo más breve posible omitimos algunos pasos.


### Buscando sin Amavisd
Como te habrás dado cuenta, toda la magia se basa en el campo `messageid`, y la gracia de contar con `Amavisd` es que guarda en un sólo registro la información de los campos: `From`, `To` y `messageid`, como se ve resumido en el siguiente ejemplo:

```
2015-05-25T13:24:54.... <pbruna@example.com> -> <deugenin@example.com>, ....,
Message-ID: <200345754.22435379.1432571094009.JavaMail.zimbra@itlinux.cl>...
queued_as: 31D4F2816E1, 239 ms
```

Sin `Amavisd` la cosa se hace un poco más tediosa, ya que debes hacer un mapa de todos los `messageid`. El plan en estos casos es el siguiente, por brevedad solo veremos para el campo `From`:

#### 1. Busca los posibles qids
En el cuadro de búsqueda de Kibana ingresa lo siguiente:
```sql
component:"qmgr" AND from:"pbruna@example.com"
```

Anota todos los `qids` que resultaron.

#### 2. Busca los posibles Message-ID con los qids obtenidos
En el cuadro de búsqueda de Kibana ingresa lo siguiente:
```sql
component:"cleanup" AND (qid:"0CCC62816D5" OR qid:"31D4F2816E1"....)
```

De los resultados anota los valores de los campos `messageid`. Anótalo una sola vez, aunque se repitan.

#### 3. Obtener la traza
Por cada uno de los `messageid` resultantes, debes ejecutar los pasos 2 y 3 que vimos cuando tenemos `Amavisd`, es decir:

1. Buscar todas las colas generadas para este mensaje, y
2. Obtener traza completa del mensaje

## Palabras al cierre
Este artículo resultó un poco más largo de lo esperado y gracias si aún sigues leyendo. Recuerda nuestra configuración de Logstash esta disponible para ser usada por quien quiera en: [https://github.com/ITLinuxCL/zimbra_logstash](https://github.com/ITLinuxCL/zimbra_logstash).

La ayuda es siempre bienvenida, sobre todo si tiene que ver con documentación.

Saludos y hasta el próximo artículo, que será si o si sobre como instalamos `ELK` y lo que hemos aprendido al respecto.

Chao.
