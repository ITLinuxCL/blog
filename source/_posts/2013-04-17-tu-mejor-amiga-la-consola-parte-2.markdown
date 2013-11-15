---
layout: post
title: "Tu mejor amiga: La Consola - Parte 2"
date: 2013-04-17 12:44
comments: true
categories: [linux, devops, sysadmin, shell, bash]
keywords: consola, shell, linux, bash, devops
description: "Tu mejor amiga: La Consola - Parte 2"
author: "pbruna"
---

##Hablemos de BASH
BASH significa Bourne Again Shell. Fue liberada en 1989 como una re-encarnación de Bourne Shell, que en entonces era el shell por defecto en los sistemas Unix. En los sistemas Linux BASH se invoca con los ejecutables bin/sh y /bin/bash.

###El Prompt
Lo primero que aparece cuando obtienes una shell es el prompt de BASH. Podríamos pasar este tutorial completo revisando sus configuraciones y como personalizarlo, pero no lo haremos, y solo vamos a ver algunas cosas básicas.

```bash
[pbruna@lisa ~]$
```

Así es como se ve el prompt. La primera palabra, pbruna, es mi nombre de usuario, seguido de @ y el nombre de mi computador, y finalmente el directorio en el que me encuentro. El caracter "~" representa el directorio home del usuario actual, /home/pbruna en mi caso. La línea termina con el signo $. Cualquier cosa que escriba a continuación será interpretado por la shell como un comando e intentará ejecutarlo.

Lo que acabamos de ver es un simple ejemplo de un prompt básico y ahora veremos como agregarle más información.

<!--more-->

BASH tiene un conjunto de variables especiales: PS1, PS2, PS3 y PS4, las cuales controlan los contenidos que aparecen en el promot en las diferentes etapas de ejecución de un programa. En este tutorial solo veremos que contiene la variable PS1. Ouedes revisar su contenido con el siguiente comando:

```bash
[pbruna@lisa ~]$ echo $PS1
[\u@\h \W]\$
```

Lo que ves en el ejemplo es mi prompt, seguido del comando ```echo $PS1``` y en la otra línea aparece el resultado deñ comando. En BASH, uno pone el signo peso ($) antes de la variable de la cual queremos obtener su valor. El comando ```echo``` solo escribe cualquier cosa que reciba como parametro. Si el parámetro es una variable, escribe su valor en la pantalla.

El resultado del comando anterior representa el formato del prompt. ```\u``` representa el nombre de usuario. El valor ```\h``` es el nombre de host del equipo, y ```\W``` es la ruta base del directorio donde se encuentra el usuario actualmente.

Existen varios caracteres especiales que al ser precedidos por ```\``` toman un significado especial, es decir al usar ```\``` se escapa el carácter que sigue. En la página man de BASH podrás encontrar una lista completa de carácteres que puedes usar para personalizar tu prompt BASH.



###Trabjando con directorios y archivos
Lo que todos como mínimo deben saber hacer el la línea de comandos es navegar el sistema de archivos, crear, borrar, copiar, mover archivos y directorios, y ejecutar comandos. Esto puede ser familiar para varios de ustedes, así que démosle un vistazo rápido:

```bash
[pbruna@lisa ~]$ mkdir ~/tmp/
[pbruna@lisa ~]$ cd ~/tmp/BashTutorial/
[pbruna@lisa ~/tmp/BashTutorial]$ mkdir ./AnotherDir
[pbruna@lisa ~/tmp/BashTutorial]$ mkdir ./SecondDir
[pbruna@lisa ~/tmp/BashTutorial]$ touch ./SecondDir/aFile
[pbruna@lisa ~/tmp/BashTutorial]$ touch ./SecondDir/AnotherFile
[pbruna@lisa ~/tmp/BashTutorial]$ cd ./SecondDir/
[pbruna@lisa ~/tmp/BashTutorial/SecondDir]$ pushd ~/tmp/BashTutorial
~/tmp/BashTutorial ~/tmp/BashTutorial/SecondDir
csaba@csaba-pc ~/tmp/BashTutorial $ ls -al
total 16
drwxr-xr-x 4 pbruna pbruna 4096 Feb 19 21:09 .
drwx------ 7 pbruna pbruna 4096 Feb 19 21:09 ..
drwxr-xr-x 2 pbruna pbruna 4096 Feb 19 21:09 AnotherDir
drwxr-xr-x 2 pbruna pbruna 4096 Feb 19 21:09 SecondDir
[pbruna@lisa ~/tmp/BashTutorial]$ popd
~/tmp/BashTutorial/SecondDir
[pbruna@lisa ~/tmp/BashTutorial/SecondDir]$ ls -al
total 8
drwxr-xr-x 2 pbruna pbruna 4096 Feb 19 21:09 .
drwxr-xr-x 4 pbruna pbruna 4096 Feb 19 21:09 ..
-rw-r--r-- 1 pbruna pbruna    0 Feb 19 21:09 aFile
-rw-r--r-- 1 pbruna pbruna    0 Feb 19 21:09 AnotherFile
[pbruna@lisa ~/tmp/BashTutorial/SecondDir]$
```

Una explicación línea por línea:

* Creamos un directorio llamado BashTutorial bajo /home/pbruna/tmp.
* Nos movemos al directorio creado anteriormente.
* Creamos un directorio llamado "AnotherDir".
* Creamos un directorio llamado "SecondDir".
* Creamos dos archivos vacíos dentro de ```SecondDir``` usando el comando ```touch```
* Nos cambiamos al directorio SecondDir.
* Usamos el comando ```pushd``` para movernos al directorio ~/tmp/BashTutorial y dejar el directorio en que estábamos en memoria.
* Listamos todos los archivos de ~/tmp/BashTutorial
* Volvemos al directorio anterior usando el comando ```popd```, el cual obtiene (y elimina) el directorio de memoria.
* Nuevamente listamos el contenido del directorio y vemos los dos archivos creados anteriormente.

Eso es por ahora y nos vemos en el próximo capítulo.