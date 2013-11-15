---
layout: post
title: "Monitorear cambios en un directorio"
date: 2013-04-08 12:26
comments: true
categories: [linux,devops,sysadmin]
keywords: linux,devops,sysadmin
description: Monitorear cambios en un directorio
author: "IT Linux"
---

Hay veces en que se hace necesario monitorear directorios y realizar acciones cuando ocurra un evento como la creación o eliminación de un archivo.

Para esto podemos utilizar la herramienta  incrond, que es un demonio que monitorea los cambios del sistema de archivos utilizando el sistema inotify. inotify  está incluído en todos los kernel modernos y permite suscribirse a eventos que ocurren en el sistema de archivo.

En nuestra base de conocimientos hemos agregado un manual de como utilizar incrond: [https://itlinux.zendesk.com/entries/22791381-como-monitorear-cambios-en-un-directorio](https://itlinux.zendesk.com/entries/22791381-como-monitorear-cambios-en-un-directorio)


Como muestra el manual esto facilita bastante algunas tareas de monitoreo y la gracia que tiene es que ocurre inmediatamente cuando sucede el evento, al contrario de utilizar un cron normal que se ejecuta cada cierto tiempo.
