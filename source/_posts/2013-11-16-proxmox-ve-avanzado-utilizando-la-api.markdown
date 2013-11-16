---
layout: post
title: "Proxmox VE Avanzado: Utilizando la API"
date: 2013-11-16 18:16
comments: true
categories: [devops, devel, proxmox]
description: Además de la consola web, Proxmox viene con una podera API REST para sacarle el máximo provecho
author: Patricio Bruna
---
[Proxmox VE](http://pve.proxmox.com/wiki/Main_Page) se ha transformado en nuestra solución favorita para crear fácilmente entornos de virtualización sencillos de administrar y con alto potencial. También lo [usamos en nuestra plataforma](http://blog.itlinux.cl/blog/2013/04/08/como-agregar-un-disco-en-caliente-en-proxmox/).

{%img center /images/rest-api.png %}

Una de las primeras características que destaca de Proxmox es su Consola Web, desde la cual puedes realizar todas las tareas de administración, pero cuando ya necesitas más poder o [automatizar tareas](http://blog.itlinux.cl/blog/2013/11/14/para-que-sirve-puppet/) empieza a quedar chica.

Es aquí cuando descubres otra fortaleza de Proxmox y es que su Consola Web se comunica con una [capa de servicios REST](http://pve.proxmox.com/wiki/Proxmox_VE_API), la [API](http://pve.proxmox.com/pve2-api-doc/), con la cual tú también te puedes comunicar para realizar tareas más complejas.

Esta es la API que vas a usar cuando construyas tus scripts o programas, [existen librerías para los lenguajes](http://pve.proxmox.com/wiki/Proxmox_VE_API#Clients) más comunes, y Proxmox también viene con el comando ```pvesh``` que permite familiarizarse con la API. 

A continuación vamos a ver algunos ejemplos, para los cuales debes considerar que el nodo de Proxmox donde se ejecutan los comandos se llama _virtual1_

```bash
# Obtener un listado de todas las máquinas virtuales
pvesh get /nodes/virtual1/qemu

# Arrancar la VM con id 100
pvesh create /nodes/virtual1/qemu/100/status/start

# Obtener toda la configuración de la VM con id 100
pvesh get /nodes/virtual1/qemu/100/config

# Borrar la máquina virtual con id 100
pvesh delete /nodes/virtual1/qemu/100

# Obtener la información de los _Storages_
pvesh get /nodes/virtual1/storage

```

Como ves la API de Proxmox permite realizar todas las tareas que encuentras en la Consola Web, pero de forma programática por lo que puedes usarla desde tus scripts o en herramientas de automatización como [Puppet](http://blog.itlinux.cl/blog/2013/11/14/para-que-sirve-puppet/).

Antes de despedirme los dejo con un ejemplo de la vida real, y es que hace poco tuve que preparar un cluster de Proxmox con dos nodos que estaban funcionando de forma independiente y necesitaba que el nodo "B" tuviera creada las máquinas virtuales con configuraciones idénticas al nodo "A".

Las máquinas en el nodo "B" fueron creadas de forma manual a través de la Consola Web por un compañero de trabajo, cerca de 20 máquinas. Como yo soy flojo, una virtud en los DevOps, no iba a revisarlas una a una, así que me hice un _one-liner_ que comprobara la configuración por mi:

```bash
pvesh get /nodes/virtual1/qemu|grep vmid|awk '{print $3}' \
|perl -ne 'chomp; $result1 = `pvesh get /nodes/virtual1/qemu/$_/config|md5sum`; $result2 = `ssh 10.0.0.2 "pvesh get /nodes/virtual1/qemu/$_/config|md5sum"`; if($result1 != $result2){print "VM $_ es Diferente\n";}'
```

Saludos y buen fin de semana.