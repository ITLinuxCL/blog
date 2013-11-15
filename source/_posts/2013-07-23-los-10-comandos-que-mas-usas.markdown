---
layout: post
title: "Los 10 comandos que más usas"
date: 2013-07-23 17:55
comments: true
categories: [linux, shell, consola, howto, tips]
keywords: linux, shell, consola, howto, tips
description: Los 10 comandos que más usas
author: "IT Linux"
---
Todos los que administramos sistemas basados en Unix ejecutamos frecuentemente los mismos comandos y las mismas combinaciones de estos. Con la siguiente orden podemos obtener una lista de los 10 comandos más usados:

```bash
history | awk '{a[$2]++}END{for(i in a){print a[i] " " i}}' | sort -rn | head
```

El script anterior básicamente analiza tu historial (**history**) de comandos, hace una suma de las repeticiones del comando impreso en la segunda columna (**awk**), ordena de forma descendente el resultado (**sort**), y finalmente imprimiendo en pantalla los 10 primeros lugares (**head**).

En mi caso el resultado es similar a:

```bash
1705 git
1420 ack
1016 vi
501 ls
490 commit
310 cd
211 cat
202 g
191 rm
181 c
```

Ahora la gracia de tener esta lista de los 10 primeros es que puedes mejorar tu productividad utilizando **alias** de comandos. No sería mejor escribir ```l``` en vez de ```l```:

```bash
alias a="ack"
alias g="git"
alias v="vi"
alias l="ls -al"
alias c="git commit -m"
```

Si bien cada uno ahorra un par de segundos, a la semana esos segundos te dejan más tiempo libre para hacer cosas más interesantes que escribir comandos.

