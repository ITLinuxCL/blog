---
layout: post
title: "Linux Tip 1: Borrar palabra completa en consola"
date: 2013-10-15 14:44
comments: true
categories: [linux, tips, linux_tips]
description: Como borrar una palabra completa en la consola de Linux y otros Unix
author: Patricio Bruna
---
Nace una nueva serie de artículos rápidos donde compartimos un dato útil para administradores Linux/Unix. Esta serie tiene un motivo doble: Obligarme a escribir más seguido y ayudar a nuestros amigos administradores. Comencemos!!

El artículo de hoy tiene que ver con los atajos de teclado, y en particular uno que acabo de descubrir de forma accidental: `ALT+Delete`

Cuando estas trabajando en la consola con un comando extenso, `ALT+Delete` permite borrar de forma inmediata palabra por palabra, por ejemplo:

```bash
$ ls -lt /tmp | wc -n | grep "te|yu"

# Presionamos la combinación con el cursor al final
$ ls -lt /tmp | wc -n | grep "te

# Presionamos 2 veces la combinación con el cursor al final
$ ls -lt /tmp | wc -n | 

```

Como con la mayoría de los atajos Unix, es probable que esta combinación haya sido adoptaba por varios editores de texto. Yo la acabo de probar en [Sublime](http://www.sublimetext.com/) y funcionó enseguida.

**Ps**: Si tienes alguna duda que quieras resolver, déjala en los comentarios, y de paso me ayudas con los artículos del Blog.