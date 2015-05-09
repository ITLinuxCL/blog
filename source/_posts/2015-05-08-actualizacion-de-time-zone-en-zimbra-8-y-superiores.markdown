---
layout: post
title: "Actualizacion de Time Zone en Zimbra 8 y superiores"
date: 2015-05-08 22:15
comments: true
categories: [zimbra, timezone]
keywords: zimbra, timezone, java
description: Como actualizar la zona horaria de servidores Zimbra Mailbox
author: "Patricio Bruna"
---
{% img right images/daylight-savings.jpg %}
Para quienes vivimos en Chile y trabajamos en computación se ha convertido en un ritual anual tener que actualizar nuestros sistemas porque la fecha de cambio de horario de Verano a Invierno o vice versa no es siempre la misma. Aunque este año se tomo la _brillante_, y si por brillante quiero decir **estúpida**, desición de dejar fijo el horario de Verano.

Pero bueno este post no es sobre mis opiniones, por más acertadas que sean, si no sobre como corregir la base de datos de Zonas Horarias que viene integrada en la máquina virtual de Java que usa Zimbra.

Como ya sabrán la zona horaria de Linux se mantiene actualizada con la simple actualización del paquete `tzdata`, pero Java al igual que otros programas trabaja con su propia `DB` de Zonas Horarias. Nosotros ya contamos en nuestra Base de Coonocimientos [con un artículo de como corregir la zona horaria](http://soporte.itlinux.cl/hc/es/articles/200120508-Actualizaci%C3%B3n-por-cambios-en-Zona-Horaria), pero sólo aplica para versiones inferiores a `Zimbra 8`.

El síntoma más claro de que la Zona Horaria de Zimbra es incorrecta es que al mirar en línea el archivo `/opt/zimbra/log/mailbox.log` verás que existe una diferencia con la hora del Sistema Operativo.

#### Qué cambió?
Las nuevas versiones de Zimbra vienen con [OpenJDK](http://openjdk.java.net/) versión 8, y esta nueva versión cambia la forma en que se manejan las zonas horarias. En las versiones 7 de OpenJDK, toda la información de zonas horarias estaba en el directorio `jre/lib/zi/`, que para el caso de Zimbra sería:

```bash
 # Zimbra version 7
 
[root@zimbra ~]# ls /opt/zimbra/java/jre/lib/zi/
Africa   Antarctica  Atlantic   CET      EET  EST5EDT  Europe  HST     MET  MST7MDT  PST8PDT  WET
America  Asia        Australia  CST6CDT  EST  Etc      GMT     Indian  MST  Pacific  SystemV  ZoneInfoMappings
[root@zimbra ~]#

```

En las nuevas versiones de OpenJDK todo este directorio fue reemplazado por un sólo archivo llamado `tzdb.dat`:

```bash
 # Zimbra version 8.5
 
[root@zimbra8.5 ~]# ls /opt/zimbra/java/jre/lib/tzdb.dat
/opt/zimbra/java/jre/lib/tzdb.dat
[root@zimbra8.5 ~]#

```

#### Como lo actualizó si no existe una actualización de Zimbra?
Si Zimbra no ha liberado una actualización, o simplemente no quieres aplicar una actualización completa a tu plataforma de más de 10 servidores como tenemos en [ZboxApp](https://www.zboxapp.com), puedes seguir el siguiente procedimiento (probado en CentOS 6 y 7):

**1. Instala la última versión de OpenJDK**

Que al momento de escribir este artículo era `1.8.0.45`

```bash
$ yum install java-1.8.0-openjdk.x86_64
$ rpm -qa java-1.8.0-openjdk
java-1.8.0-openjdk-1.8.0.45-28.b13.el6_6.x86_64
$
```

**2. Reemplaza el archivo `tzdb.dat` de Zimbra por el de OpenJDK**

```bash
 # Primero un respaldo por buenas costumbres
$ cp /opt/zimbra/java/jre/lib/tzdb.dat /opt/zimbra/java/jre/lib/tzdb.dat.`date +%Y%m%d` 

 # Actualizamos el archivo
$ cp /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.45-28.b13.el6_6.x86_64/jre/lib/tzdb.dat /opt/zimbra/java/jre/lib

 # Reiniciamos el mailboxd para que tome los cambios
$ zmmailboxdctl restart
```

Y eso es todo, ahora Zimbra se encuentra con la hora que corresponde.