---
layout: post
title: "Creación de sitios web estáticos con nanoc"
date: 2013-07-29 17:42
comments: true
categories: [linux, shell, consola, howto, tips, ruby]
keywords: linux, shell, consola, howto, tips
description: Tutorial de nanoc para administradores de sistemas y seguidores de la cultura DevOps.
author: "Patricio Bruna"
---
Hace un tiempo atrás cada vez que necesitábamos un sitio web usábamos una de dos alternativas: Sitio HTML Plano o Wordpress.

{% img /images/nanoc_screen.png %}

Wordpress parece ser la selección por defecto en estos tiempos, pero viene con su carga asociada, sobre todo cuando quieres escribir tu contenido en Vi y publicar a lo mero macho desde la consola.

Lo otro que nos queda es trabajar con archivos html planos, pero la carga que significa su mantención es casi igual que la de administrar Wordpress. 

Como ninguna de las opciones satisface, nos pusimos a buscar alguna herramienta que permitiera mantener fácilmente sitios estáticos y cuyo uso fuera sencillo, en particular que cumpliera con:

* **Soporte de Layouts/Templates**, un diseño principal que se repita en todas las páginas.

* **Poder trabajar con _partials_**, en Rails un partial es una porción de una página web que se inserta a demanda en otra página.

* **Trabajo desde la consola**, la mayor parte del tiempo estamos trabajando en la consola y cambiarse a un página web o alguna otra aplicación rompe el flujo, así que necesitamos algo que nos deje crear contenido y publicar desde la consola.

Fue así que llegamos a [nanoc](http://www.nanoc.ws), un generador de sitios estáticos basado en Ruby y que permite crear desde sitios poderosos para empresas hasta blogs personales.

####Instalando nanoc
Como mencionamos antes, nanoc es una aplicación escrita en Ruby por lo que su instalación se realiza de forma sencilla con RubyGems:

```bash
% gem install nanoc
```

Ahora que está instalado te recomendamos que sigas el [tutorial oficial](http://nanoc.ws/docs/tutorial/), si tienes alguna duda déjanos un comentario y te ayudaremos a resolverla.



