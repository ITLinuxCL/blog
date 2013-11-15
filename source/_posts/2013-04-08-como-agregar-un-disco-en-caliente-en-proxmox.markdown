---
layout: post
title: "Como agregar un disco en caliente en Proxmox"
date: 2013-04-08 14:17
comments: true
categories: [linux,devops,proxmox,virtualization,kvm]
keywords: linux,devops,proxmox,virtualization,kvm,virtualizacion
description: Como agregar un disco en caliente en Proxmox
author: "IT Linux"
---

Ultimamente hemos estado usando cada vez más la solución de virtualización [Proxmox VE](http://pve.proxmox.com/wiki/Main_Page). De hecho toda nuestra plataforma, ya va en 10 máquinas virtuales, ahora corre en un cluster administrado por Proxmox.

Uno de las razones de porque usamos Proxmox se debe a que el sistema base sigue siendo Linux y Proxmox sobre éste nos da una consola web para administrar, pero sin quitar el poder de hacer las cosas a mano, a lo mero macho. También, y similar a Zimbra, Proxmox entrega una herramienta de consola para administrar la plataforma: [**qm**](http://pve.proxmox.com/wiki/Qm_manual). Para los que usan Zimbra, qm es como zmprov.

Listo con la introducción, ahora manos a la masa: Cómo agregar un disco en caliente a una máquina virtual. Una aclaración, esto sólo funciona con Sistemas Operativos Linux que estén usando los drivers **virtio-***.

Lo primero que debes hacer es agregar un nuevo disco a la máquina virtual, con ésta corriendo obviamente. Esto lo haces desde la consola web de Proxmox, y vamos a suponer que el nuevo disco se llama vm-101-disk-3.qcow2 y quedó en /var/vz/images/101/vm-101-disk-3.qcow2.

El siguiente paso es conectar el nuevo disco a la máquina virtual. Esto lo hacemos desde la consola con el comando qm:

```bash
$ qm monitor 101
pci_add auto storage file=/var/vz/images/101/vm-101-disk-3.qcow2,if=virtio
```

Y eso es todo. Ahora tu disco está disponible para ser _formateado_ en el sistema operativo.
También puedes revisar todos los dispositivos asociados a la máquina virtual desde la consola:

```
$ qm monitor 101
info block
virtio1: removable=0 file=/dev/pve/vm-101-disk-2 ro=0 drv=raw encrypted=0
virtio2: removable=0 file=/dev/pve/vm-101-disk-1 ro=0 drv=raw encrypted=0
```