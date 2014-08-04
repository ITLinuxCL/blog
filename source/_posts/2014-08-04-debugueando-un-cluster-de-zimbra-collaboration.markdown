---
layout: post
title: "Debugueando un Cluster de Zimbra Collaboration"
date: 2014-08-04 17:15
comments: true
author: "Patricio Bruna"
categories: zimbra,cluster,sysadmin
keywords: zimbra,cluster,debug
description: Un cuento de como se realiza la resolución de problemas a través de la historia de un cluster porfiado de Zimbra Collaboration.
---
A continuación los dejo con un cuento de como en IT Linux realizamos el arte llamado **Resolución de Problemas**, o _Troubleshooting_ para los snobs, usando como ejemplo la historia de un cluster porfiado de Zimbra Collaboration.

{%img center /images/debug-condition-interface.jpg 400px %}

Algunos datos importantes de esta historia:

* El software de cluster usado en Red Hat Cluster Suite
* El sistema operativo RHEL 6
* La versión 7 de Zimbra
* Sistema en producción hace 4 años
* Errores recién se presentaron este año y de forma muy aliatoria
* Este trabajo realizó la única re-configuración que el cluster ha tenido en su historia

Ahora los dejo con la historia.

<!-- more -->
Después de leer todo el código de ```/opt/zimbra-cluster/bin/zmcluctl```, pude llegar a cual creo que es el problema, pero antes de decirlo, quiero contarles que aprendí de este script.

Cluster Suite ejecuta el siguiente comando cada cierto tiempo para saber el estado del cluster:

```bash
OCF_RESKEY_service_name=zimbra.gobiernosantiago.cl /opt/zimbra-cluster/bin/zmcluctl status
```

El parámetro ```status``` está definido en la línea 368 del archivo, y lo más importante que hace, bueno lo más importante para descubrir el problema, es que registra en ```/var/log/messages``` el siguiente texto:

```bash
status - No Zimbra service is running
```

####¿Cómo se obtiene texto?
Se obtiene al llamar la función ```getCurrentService()```. Esta función es:

```perl
sub getCurrentService() {
    my $fh = lockStatusFile();
    my $svc = getCurrentServiceFromFH($fh);
    unlockStatusFile($fh);
    return $svc;
}
```

La primera línea ```my $fh = lockStatusFile();``` sólo devuelve el archivo a usar (```/opt/zimbra-cluster/status/status.dat```).

En la segunda línea llamamos a la función ```getCurrentServiceFromFH()``` y está es la que nos interesa. Lo que hace esa función, si todo está bien, es devolver el nombre del servicio en cluster, que según la configuración de cluster suite es ```zimbra.example.com``` . Pero si algo está mal, que para este caso es que el archivo ```status.dat``` tenga alguna inconsistencia, esta función ejecuta:

```bash
su - zimbra -c `zmlocalconfig -m nokey zimbra_server_hostname`
```

Eso trae el nombre del host de Zimbra, que para cuando escribía esto era ```mail.example.com``` y lo guarda en la variable de nombre ```$actual```. Luego el script lee el contenido del archivo ```status.dat```, el cual tenía el texto ```zimbra.example.com```, y lo guarda en la variable de nombre ```$claimed```.

A continuación en la línea 161 se crea la variable ```$newval``` con texto vacío (```my $newval = '';```) Esta variable será de gran importancia.

Ahora se pone interesante la cosa, en la línea 162 el script verifica que estas dos variables sean iguales:

```perl
if ($claimed eq $actual){
.....
}
```

Si son iguales todo bien y la variable ```$newval``` toma el valor de la variable ```$claimed```, recuerden que el valor de ```$claimed``` sale del archivo ```status.dat``` (zimbra.example.com). Pero si no son iguales, el cual era nuestro caso, se borra el contenido del archivo ```status.dat``` y la variable $claimed queda vacía (```''```).

```perl
truncate($fh, 0); // Línea 171)
```

Finalmente la función ```getCurrentServiceFromFH()``` retorna el valor de $newval:

```perl
return $newval;
```

Ahora, si recuerdan la función ```getCurrentServiceFromFH()``` fue llamada por la función ```getCurrentService()```. Está última retorna el valor que entrega ```getCurrentServiceFromFH()```. Que en nuestro caso el valor era ```''```, texto vacío. Si siguen con buena memoria, la función ```getCurrentService()``` se llamaba cuando queríamos saber el estado del cluster (```status```) en la línea 371.

En la línea 374 el script revisa si el valor entregado por la función ```getCurrentService()``` es un texto vacio (```if ($curr eq '')```), y si lo es, hace dos cosas:

1. Escribe el texto "status - No Zimbra service is running.” en ```/var/log/messages``` <- Si el mismo del principio
2. Termina la ejecución del script entregando un error <- ```exit(1);``` // línea 380.

Así que ahí lo tienen, los problemas de caída del cluster pasaban porque el nombre del servicio en cluster suite era diferente al nombre del host configurado en Zimbra. Lo corregí dejando el nombre del servicio en cluster igual que el nombre host de Zimbra: mail.example.com. También tuve que cambiar el nombre del punto de montaje de zimbra.example.com a mail.example.com

Esto pasaba de forma aleatoria porque la inconsistencia del archivo status.dat sólo tiene que ver con cambios en los tiempos de lectura.