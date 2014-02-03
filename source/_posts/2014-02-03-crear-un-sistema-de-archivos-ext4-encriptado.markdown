---
layout: post
title: "Crear un sistema de archivos EXT4 encriptado"
date: 2014-02-03 17:19
comments: true
keywords: linux, admin, tips, ext4
categories: [linux, admin, tips, ext4, centos]
description: Como crear un sistema de archivos EXT4 encriptado en CentOS o RHEL 6 con LUKs
author: Patricio Bruna
---
{%img right /images/Lock-Wooden-USB-Pen-Drive.jpg %}
[LUKS (Linux Unified Key Setup)](http://code.google.com/p/cryptsetup/) es el estándar en Linux para encriptar discos duros y particiones. En comparación con otras soluciones, LUKS almacena toda su información en la cabecera de la partición, lo que permite contar con datos transportables, lo cual resulta ideal para discos externos.

Ahora te dejamos un manual de como crear un sistema de archivos encriptado con LUKS, el cual viene instalado por defecto en CentOS 6, que usamos en esta guía.

También haremos cuenta de que el disco externo quedó en **/dev/sdc** y que sólo tiene la partición **/dev/sdc1**.

## Encriptando la partición
```bash
[root@pbruna]$ cryptsetup -c aes-cbc-essiv:sha256 -y -s 256 luksFormat /dev/sdc1

WARNING!
========
This will overwrite data on /dev/sdb1 irrevocably.

Are you sure? (Type uppercase yes): YES
Enter LUKS passphrase:
Verify passphrase:
[root@pbruna]$
```

## Activando la partición encriptada
Para poder crear el sistema de archivos, primero debes _activar_ la partción que acabamos de encriptar.
Este paso también se realiza cuando llevas el disco a otro equipo y accedes a la información.

```bash
[root@pbruna]$ cryptsetup luksOpen /dev/sdc1 disco-seguro
Enter passphrase for /dev/sdc1:

[root@pbruna]$ ls /dev/mapper/disco-seguro # <- Este es el dispositivo con el que tienes que operar
```

## Creando el sistema de archivos

```bash
[root@pbruna]$ mkfs.ext4 -L DISCO-SEGURO /dev/mapper/disco-seguro
mke2fs 1.41.12 (17-May-2010)
Etiqueta del sistema de ficheros=DISCO-SEGURO
Tipo de SO: Linux
Tamaño del bloque=4096 (bitácora=2)
Tamaño del fragmento=4096 (bitácora=2)
Stride=0 blocks, Stripe width=0 blocks
61054976 nodos-i, 244189488 bloques
12209474 bloques (5.00%) reservados para el superusuario
Primer bloque de datos=0
Número máximo de bloques del sistema de ficheros=4294967296
7453 bloque de grupos
32768 bloques por grupo, 32768 fragmentos por grupo
8192 nodos-i por grupo
Respaldo del superbloque guardado en los bloques:
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
	4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968,
	102400000, 214990848

Escribiendo las tablas de nodos-i: hecho
Creating journal (32768 blocks): hecho
Escribiendo superbloques y la información contable del sistema de ficheros: hecho

Este sistema de ficheros se revisará automáticamente cada 21 montajes o
180 días, lo que suceda primero.  Utilice tune2fs -c o -i para cambiarlo.

[root@pbruna]$ cryptsetup remove disco-seguro # <- desactivamos la particion
```

## Montando el sistema de archivos
```bash
[root@pbruna]$ cryptsetup luksOpen /dev/sdc1 disco-seguro
Enter passphrase for /dev/sdc1:

[root@pbruna]$ mount /dev/mapper/disco-seguro /mnt
[root@pbruna]$
```

## Desmontando el sistema de archivos
```bash
[root@pbruna]$ umount /mnt
[root@pbruna]$ cryptsetup remove disco-seguro
```

