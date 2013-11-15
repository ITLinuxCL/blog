---
layout: post
title: "Como matar un proceso en Linux"
date: 2013-04-22 22:44
comments: true
categories: [linux, procesos, kill, sysadmin, shell, bash]
keywords: linux, procesos, kill, sysadmin, shell, bash
description: "Como matar un proceso en Linux"
author: "pbruna"
---
Si bien Linux es muy estable, hay veces en que un proceso quiere hacer de las suyas y queda colgado y no responde cuando queremos detenerlo. Para estos casos puedes usar algunos de estos comandos y obligar a que se detenga:

##kill
Este comando se usa para enviar _señales_ a los procesos. Los procesos reciben esta señal y realizan la acción correspondiente. Por defecto la señal que se envía es la de terminar el proceso correctamente, algo así como seleccionar la opción "Salir". El comando toma como parámetro el número que identifica al proceso (PID). Para obtener este número puedes usar el comando ```pidof```:

```bash
[pbruna@lisa ~]$ pidof firefox
38922
```

Una vez que tenemos el PID del proceso, podemos detenerlo, por ejemplo:

```bash
[pbruna@lisa ~]$ kill 38992
```

##killall
Este comando es una _wrapper_ del comando ```kill``` que hace más cómodo enviar señales a los procesos ya que no es necesario que sepas el PID. Sólo debes pasar el nombre del proceso que quieres detener. Debes tener en cuenta que la señal será enviada a todos los procesos que tengan este nombre. Por ejemplo si queremos detener todos los procesos java, ejecutamos:

```bash
[pbruna@lisa ~]$ killall java
```
##pkill
En el caso de que sufras de flojera crónica, este comando permite matar un proceso sin tener que ingresar el nombre completo. Piensa en ```pkill``` como la unión entre  ```grep``` y ```killall```. Al ejecutarlo, la señal se enviará a todos los procesos cuyos nombres coincidan con el texto que pasas como parámetro. Por ejemplo si quieres matar el proceso del navegador Firefox:

```bash
[pbruna@lisa ~]$ pkill fire
```
o

```bash
[pbruna@lisa ~]$ pkill fox
```

##Señales
Existen varias señales que puedes enviar a los procesos de Linux, puedes obtener un listado completa de estas leyendo el manual de la página signal: ```man signal```. Algunas de las señales más usadas son:

* SIGHUP (1): Termina un proceso de forma controlada
* SIGKILL (9): Termina un proceso si esperar que realice las acciones de salida. Como un APH.


