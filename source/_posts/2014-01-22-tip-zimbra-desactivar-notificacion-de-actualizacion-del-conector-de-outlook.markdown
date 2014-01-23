---
layout: post
title: "Tip Zimbra: Desactivar notificación de actualización del conector de Outlook"
date: 2014-01-22 23:26
comments: true
keywords: zimbra, outlook
categories: [zimbra, tips]
description: Como desactivar la notificación de actualización del conector Zimbra para Outlook
author: Patricio Bruna
---
Este es un tip cortito y sirve para desactivar la notificación que le puede aparecer y confundir a los usuarios del Conector de Zimbra para Outlook cuando hay una actualización disponible.

{%img center /images/zco_update.png %}

Esto se desactiva vaciando el archivo /opt/zimbra/jetty/webapps/zimbra/downloads/index.html.in. Recomendamos primero dejar un respaldo del archivo. Luego se debe re-iniciar el servicio mailbox.

