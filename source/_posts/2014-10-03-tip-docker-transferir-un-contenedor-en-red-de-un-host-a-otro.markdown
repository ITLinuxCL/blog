---
layout: post
title: "Tip Docker: Transferir un contenedor con SSH"
date: 2014-10-03 17:25
comments: true
categories: [tips,linux,docker,devops]
keywords: tips,linux,docker,devops
description: Como copiar un contenedor docker con SSH
Author: Patricio Bruna
---
{% img right /images/docker_logo_dark.png 150 %}
Asi como van las cosas, parece que vamos a cambiar de nombre a ```IT Docker``` :).

Por ahora les dejamos un tip de como copiar un contenedor de un host a otro utilizando ssh y con el estado en tiempo real de la transmisión. El comando que se sigue se ejecutó en OS X utilizando dos máquinas virtuales [Vagrant](http://blog.itlinux.cl/blog/2014/08/01/encontrar-las-mejores-herramientas-para-devops/): 

```bash
docker save itlinux/carter-logstash | pv | ssh servidor 'DOCKER_HOST=tcp://192.168.59.103:2375 docker load'
```

Vamos por parte:

* ```docker save itlinux/carter-logstash```, guarda la imagen, en este caso la "tira" a la salida estándar: la consola,
* ```| pv```, recibe los datos de la imagen en tiempo real y los muestra por pantalla,
* ```|  ssh servidor 'DOCKER_HOST=tcp://192.168.59.103:2375 docker load'```, envío por SSH la imagen que está siendo exportada por el primer paso al equipo ```servidor``` donde ejecuto ```docker load``` para que la importe.

En mi caso esto resulta en lo siguiente:

{% img /images/docker_exporting_importing.png %}

