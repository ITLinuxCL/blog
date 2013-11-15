---
layout: post
title: "Controlar velocidad de envío en Postfix"
date: 2013-04-08 12:12
comments: true
categories: [postfix, linux]
author: IT Linux
keywords: postfix,linux,howto
description: Controlar velocidad de envio en Postfix
---

Algunos servidores están configurados para rechazar correos cuando la taza de envío supera el límite establecido. Por ejemplo, si enviamos 100 correos por minuto a GMail, nuestros correos serán rechazados con un mensaje similar a:

```bash
Jan 22 03:49:16 zimbra-proxy postfix/smtp[29092]: 7F42E2A1D5D: to=<xxx@yyyy.com>,
relay=smtp.yyyy.com[300.72.236.190]:25, delay=42527, delays=42527/0.02/0.03/0.02, dsn=4.0.0,
status=deferred (host smtp.yyyy.xom[300.72.236.190] said: 450 too many connections from
your IP (rate controlled) (in reply to RCPT TO command))
```
 
Como se puede ver en el log, nuestros correos están siendo rechazados porque hemos realizado más conexiones que las que están permitidas en un tiempo determinado. Los correos rechazados quedarán encolados en nuestros servidores y se intentará su reenvio según nuestra configuración.


En algunos casos podemos optar por esperar a que el envío se realice después, pero si necesitamos que estos se realicen lo más pronto posible y sabemos que el dominio de destino tiene una politica de restricción podemos configurar Postfix para que envíe los correos a una taza menor.


Si quieres saber como controlar la velocidad de envío, visita el artículo en nuestra base de conocimientos: [https://itlinux.zendesk.com/entries/22989368-controlar-velocidad-de-envio-en-postfix](https://itlinux.zendesk.com/entries/22989368-controlar-velocidad-de-envio-en-postfix)