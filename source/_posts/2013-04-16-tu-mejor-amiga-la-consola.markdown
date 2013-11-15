---
layout: post
title: "Tu mejor amiga: La Consola - Parte 1"
date: 2013-04-16 14:32
comments: true
categories: [linux, devops, sysadmin]
keywords: zimbra, consola, shell, linux
description: "La mejor amiga del administrador: La Consola de Linux - Parte 1"
author: "pbruna"
---
La Consola puede ser tu mejor amiga o peor aliada. Sólo depende de como y para que la uses. Si eres uno de los que sufre de tan sólo pensar en que debes usarla, has llegado al lugar correcto.

_Disclaimer: Este artículo es la traducción de [The command line is your best friend](http://net.tutsplus.com/tutorials/tools-and-tips/the-command-line-is-your-best-friend/) y lo hemos divido en 7 partes._

<!--more-->

##Command Line Interface (CLI)
Sí, es esa pantalla con texto blanco (o verde) sobre un fondo negro, donde se ejecutan extraños comandos y aparecen letras como en la Matrix. Conozco algunos programadores que nunca usan la CLI y también conozco usuarios básicos que hacen todo en la línea de comando, ellos tienen aplicaciones de consola para navegar la web y el sistema de archivos, para editar texto e imágenes. Incluso algunos ven videos de Youtube y leen PDF sin usar una interfaz gráfica.

Al final del día depende de como cada uno trabaja mejor. Algunos prefieren un entorno gráfico, mientras que otros estamos más cómodos en modo texto.

###Definiciones
{% img right http://cdn.tutsplus.com/net.tutsplus.com/authors/jeremymcpeak/cmd-console-terminal-shell-schema.png 132 254 %}

Los recién llegados al mundo de Unix/Linux generalmente se confunden con las diferencias entre terminal, consola y _shell_.


Desde la perspectiva del usuario, la diferencia es mínima o ninguna entre ellas, pero en la realidad, el usuario utiliza la consola para conectarse a un terminal, y así poder acceder a la _shell_ en el computador.

En el pasado, estos tres elementos estaban separados en diferentes equipos. La consola no era mas que un monitor y un teclado, sin capacidad de cómputo alguna y se conectaba a un terminal a través de una interfaz serial.

Una terminal correspondía a un punto de acceso a un _mainframe_. Generalmente contaba con alguna capacidad de cómputo y podia comunicarse a través de una red, de algún tipo, con el mainframe. La terminal también daba derechos de administración en el sistema, razón por la cual habitualmente se encontraba en una sala bajo llave. Las consolas de los empleados conectadas a estas terminales les permitían trabajar sin tener derechos de administración sobre el mainframe.

{%img right http://www.old-computers.com/museum/photos/digital_VT100_Terminal.jpg %}
Las consolas y terminales se unieron eventualmente en un sólo dispositivo, siendo los más conocidos los terminales VT, emulados en la mayoría de las distribuciones Linux modernas.

La _shell_ es el programa que puede leer lo que ingresa el usuario y entregar un resultado en pantalla. Una shell puede ser de texto (como la CLI) o gráfica (como Gnome). En la computación actual una shell es mucho más que una simple interfaz entre el usuario y el sistema, es responsable de manejar procesos, ventanas, aplicaciones, comandos y otros aspectos del sistema.

{% blockquote %}
La CLI es una shell que entrega al usuario una interfaz basada en texto
{% endblockquote %}

La shell interpreta los comandos ingresados a través de la línea de comandos, para este efecto los scripts son un conjunto de comandos ingresados e interpretados secuencialmente por la shell. Las shell modernas tienen lenguajes de _scripting_ propios que permiten ejecutar operaciones complejas.

La mayoría de las distribuciones modernas de Linux, y sistemas basados en Unix como Mac OSX, usan una shell llamada **BASH**. A lo largo de estos artículos usaremos BASH para aprender como sacarle provecho a nuestra consola.

Puedes continuar leyendo [la segunda parte](/blog/2013/04/17/tu-mejor-amiga-la-consola-parte-2/)
