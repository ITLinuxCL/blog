---
layout: post
title: "VPN Facil con SSH y Proxy Socks"
date: 2015-04-04 11:24
comments: true
categories: tips,linux,ssh,vpn
author: "IT Patricio Bruna"
keywords: tips,linux,ssh,vpn
description: Fácil guía para usar Proxy Socks con SSH como si fuera VPN
---
Todos hemos necesitado conectarnos a una red remota y trabjar en los equipos internos, para esos casos generalmente usamos algún tipo de VPN, y por algún tipo me refiero a una jungla de diferentes productos, algunos de ellos sin soporte para Linux.

Pero para fortuna de aquellos que usamos Linux, todo lo que necesitamos para simular una VPN es un equipo remoto al que podamos conectarnos por `SSH` y estamos. El siguiente comando habilita un túnel que puedes usar como `Endpoint` para un [Proxy Socks](http://es.wikipedia.org/wiki/SOCKS):

```bash
$ ssh -D localhost:1080 user@remote_host
```

Usando este túnel puedes re-enviar toda la comunicación a través de `remote_host`, como si fuera un _Gateway_. En tu equipo lo único que debes hacer es configurar el navegador web para que lo use, o también puedes hacerlo de forma global con un par de reglas de `IPTABLES` que vamos a dejar como ejercicio al lector.

Si usas otro Unix como Mac OS, sólo debes ir a las preferencias de red y habilitar Proxy Socks con la IP `127.0.0.1` y puerto `1080`.



