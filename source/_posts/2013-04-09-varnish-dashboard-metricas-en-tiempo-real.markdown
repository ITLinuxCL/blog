---
layout: post
title: "Varnish Dashboard: Métricas en tiempo real"
date: 2013-04-09 12:08
comments: true
categories: [varnish, devel, devops]
keywords: varnish,dashboard,devops
description: Varnish Dashboard - Metricas en tiempo real
author: "IT Linux"
---

Hace poco acabamos de implementar un proyecto de mejoramiento de la plataforma web de uno de los medios de comunicación más importantes del país. Uno de los puntos del proyecto era mejorar la configuración del sistema de cache configurado con Varnish Cache.

Para los que no conocen Varnish Cache, se trata de un producto que trabaja como un Proxy Reverso y almacena en cache las páginas más visitadas de nuestros sitios. En otro post hablaremos más sobre su configuración.

Este post es para contar que hemos desarrollado una herramienta que permite ver en tiempo real el desempeño de Varnish Cache y así poder entender inmediatamente como está respondiendo nuestra plataforma web.

![Screenshot](https://raw.github.com/pbruna/Varnish-Agent-Dashboard/master/img/screenshot.png)

Pueden descargar la aplicación y ver como se instala en el siguiente enlace: [https://github.com/pbruna/Varnish-Agent-Dashboard](https://github.com/pbruna/Varnish-Agent-Dashboard)



