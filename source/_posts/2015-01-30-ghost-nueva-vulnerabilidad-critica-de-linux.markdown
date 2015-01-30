---
layout: post
title: "GHOST: nueva vulnerabilidad crítica de Linux"
date: 2015-01-30 13:35
comments: true
categories: [linux,seguridad,kernel]
keywords: linux,seguridad,kernel
description: Este agujero de seguridad, que impacta versiones antiguas y actuales de Linux, debe ser corregido cuanto antes
author: Patricio Bruna, Nicolás Fuenzalida
---
Investigadores de la empresa de seguridad [Qualys](https://www.qualys.com/) han descubierto un severo agujero de seguridad en la librería GNU C de Linux (glibc). Esta vulnerabilidad, llamada [GHOST (CVE-2015-0235)](http://www.openwall.com/lists/oss-security/2015/01/27/9), permitiría a hackers tomar control remoto de cualquier sistema sin siquiera conocer usuarios o contraseñas.

Qualys alertó inmediatamente a los distribuidores de Linux acerca del agujero de seguridad, y la mayoría ya ha publicado parches para corregir el problema. **Al final de este artículo entregamos un listado con todos los enlaces de descarga**.

Este agujero existe en cualquier sistema Linux que fue construido con ```glibc-2.2```, la cual fue liberada en Noviembre 10 del año 2000. Qualys también identifico que el bug ya fue resuelto con un parche que se aplicó entre las versiones ```glibc-2.17``` y ```glibc-2.18```, liberadas el 21 de Mayo de 2013.

De todas formas, esta corrección no fue clasificada como un problema de seguridad, y como resultado, muchas distribuciones de nivel empresarial no la aplicaron. Algunos sistemas Linux que están vulnerables incluyen: Debian 7 (Wheezy), RHEL 5, 6, and 7, CentOS 6 and 7 and Ubuntu 12.04. Además de Red Hat, Debian ya reparó su distribución principal y Ubuntu a parchado el bug para 12.04 and the older 10.04.

La vulnerabilidad se gatilla al explotar la función ```gethostbyname``` de glibc. Esta función se usa en todo tipo de interacción con redes en los sistemas Linux, ya sea utilizando el archivo ```/etc/hosts``` o resolviendo nombres a través de DNS.

Para utilizar la vulnerabilidad, el atacante sólo tiene que producir un _buffer overflow_ enviando un hostname inválido a alguna aplicación que realice resolución DNS. La vulnerabilidad permitirá al atacante ejecutar cualquier tipo de código o comandos con los mismos permisos del usuario que ejecuta la aplicación afectada. En resumen, una vez que el atacante utilizó la falla GHOST pueden tomar control del sistema.

## Como saber si nuestros sistemas son vulnerables
En sistemas [Red Hat](https://www.redhat.com) o CentOS puedes utilizar la siguiente herramienta para comprobar si el sistema es vulnerable a este agujero de seguridad:

```bash
$ wget https://gist.githubusercontent.com/pbruna/16c27df9ccb333f08f10/raw/30bcfd1da9cc250bf8c9bbf91eb4645780c77129/GHOST-test.sh
$ chmod +x GHOST-test.sh
$ ./GHOST-test.sh
```

Si su sistema es vulnerable debería obtener un resultado similar al siguiente:

```
This system is vulnerable to CVE-2015-0235 <https://access.redhat.com/security/cve/CVE-2015-0235>

Please refer to 'https://access.redhat.com/articles/1332213' for more information
```

## Corrección del problema

Si tienes algún sistema vulnerable, recomendamos que en cuanto antes apliques las correciones a tus sistemas. Debes tener en cuenta que una vez aplicada la corrección **es necesario re-iniciar** el servidor para que tome efecto.

Para aplicar las actualizaciones se recomienda utilizar el administrador de paquetes de cada distribución, por ejemplo:

```bash
 # Para Red Hat o CentOS
 $ yum install glibc -y
 
 # Para Ubuntu o Debian
 $ apt-get install libc6 libgcc1 -y
 
 # En ambos casos es necesario re-iniciar
 $ reboot
```
A continuación dejamos el listado de los parches liberados por las diferentes distribuciones:

#### Ubuntu Lucid Lynx  10.04 LTS x64
http://launchpadlibrarian.net/102638240/libc6_2.15-0ubuntu10_amd64.deb
http://launchpadlibrarian.net/195506965/libc-bin_2.15-0ubuntu10.10_amd64.deb
http://launchpadlibrarian.net/102638240/libc6_2.15-0ubuntu10_amd64.deb
http://launchpadlibrarian.net/102057143/libgcc1_4.6.3-1ubuntu5_amd64.deb
http://launchpadlibrarian.net/187931405/tzdata_2014i-0ubuntu0.12.04_all.deb

#### Ubuntu Lucid Lynx 10.04 LTS x32
http://launchpadlibrarian.net/195520980/libc6_2.11.1-0ubuntu7.20_i386.deb
http://launchpadlibrarian.net/195520980/libc6_2.11.1-0ubuntu7.20_i386.deb
http://launchpadlibrarian.net/43553544/debconf_1.5.28ubuntu4_all.deb
http://launchpadlibrarian.net/40588211/findutils_4.4.2-1ubuntu1_i386.deb
http://launchpadlibrarian.net/195520986/libc-bin_2.11.1-0ubuntu7.20_i386.deb
http://launchpadlibrarian.net/96023674/libgcc1_4.4.3-4ubuntu5.1_i386.deb
http://launchpadlibrarian.net/187931399/tzdata_2014i-0ubuntu0.10.04_all.deb

#### Ubuntu Precise Pangolin 12.04 LTS x64
http://launchpadlibrarian.net/195506798/libc6_2.11.1-0ubuntu7.20_amd64.deb
http://launchpadlibrarian.net/43553544/debconf_1.5.28ubuntu4_all.deb
http://launchpadlibrarian.net/40588199/findutils_4.4.2-1ubuntu1_amd64.deb
http://launchpadlibrarian.net/195506802/libc-bin_2.11.1-0ubuntu7.20_amd64.deb
http://launchpadlibrarian.net/96032925/libgcc1_4.4.3-4ubuntu5.1_amd64.deb
http://launchpadlibrarian.net/187931399/tzdata_2014i-0ubuntu0.10.04_all.deb

#### Ubuntu Precise Pangolin 12.04 LTS x32
http://launchpadlibrarian.net/129759665/libc6_2.15-0ubuntu10.4_i386.deb
http://launchpadlibrarian.net/195509227/libc-bin_2.15-0ubuntu10.10_i386.deb
http://launchpadlibrarian.net/102057932/libgcc1_4.6.3-1ubuntu5_i386.deb
http://launchpadlibrarian.net/187931405/tzdata_2014i-0ubuntu0.12.04_all.deb

#### Red Hat 5 x86
https://rhn.redhat.com/errata/RHSA-2015-0090.html

#### Red Hat 6
https://rhn.redhat.com/errata/RHSA-2015-0092.html#Red%20Hat%20Enterprise%20Linux%20Server%20%28v.%206%29

#### Red Hat 7
https://rhn.redhat.com/errata/RHSA-2015-0092.html#Red%20Hat%20Enterprise%20Linux%20Server%20%28v.%207%29

#### CentOS 5 i386
http://mirror.centos.org/centos/5/updates/i386/RPMS/glibc-2.5-123.el5_11.1.i386.rpm
http://mirror.centos.org/centos/5/os/i386/CentOS/libgcc-4.1.2-55.el5.i386.rpm
http://mirror.centos.org/centos/5/updates/i386/RPMS/glibc-common-2.5-123.el5_11.1.i386.rpm
http://mirror.centos.org/centos/5/updates/i386/RPMS/tzdata-2014g-1.el5.i386.rpm

#### CentOS 5 x64
http://mirror.centos.org/centos/5/updates/x86_64/RPMS/glibc-common-2.5-123.el5_11.1.x86_64.rpm
http://mirror.centos.org/centos/5/updates/x86_64/RPMS/tzdata-2014g-1.el5.x86_64.rpm
http://mirror.centos.org/centos/5/os/x86_64/CentOS/glibc-common-2.5-123.x86_64.rpm
http://mirror.centos.org/centos/5/os/x86_64/CentOS/libgcc-4.1.2-55.el5.x86_64.rpm

#### CentOS 6 i386
http://mirror.centos.org/centos/6/updates/i386/Packages/glibc-2.12-1.149.el6_6.4.i686.rpm
http://mirror.centos.org/centos/6/os/i386/Packages/libgcc-4.4.7-11.el6.i686.rpm
http://mirror.centos.org/centos/6/updates/i386/Packages/nss-softokn-freebl-3.14.3-18.el6_6.i686.rpm
http://mirror.centos.org/centos/6/updates/i386/Packages/tzdata-2014h-1.el6.noarch.rpm
http://mirror.centos.org/centos/6/updates/i386/Packages/glibc-common-2.12-1.149.el6_6.4.i686.rpm

#### CentOS 6 x64
http://mirror.centos.org/centos/6/updates/i386/Packages/tzdata-2014h-1.el6.noarch.rpm
http://mirror.centos.org/centos/6/updates/x86_64/Packages/glibc-common-2.12-1.149.el6_6.4.x86_64.rpm
http://mirror.centos.org/centos/6/os/x86_64/Packages/libgcc-4.4.7-11.el6.x86_64.rpm
http://mirror.centos.org/centos/6/updates/x86_64/Packages/glibc-2.12-1.149.el6_6.4.x86_64.rpm


#### CentOS 7 x64
http://mirror.centos.org/centos/7/updates/x86_64/Packages/glibc-2.17-55.el7_0.1.x86_64.rpm
http://mirror.centos.org/centos/7/updates/x86_64/Packages/glibc-common-2.17-55.el7_0.1.x86_64.rpm
http://mirror.centos.org/centos/7/updates/x86_64/Packages/tzdata-2014e-1.el7_0.noarch.rpm
http://mirror.centos.org/centos/7/updates/x86_64/Packages/libgcc-4.8.2-16.2.el7_0.x86_64.rpm