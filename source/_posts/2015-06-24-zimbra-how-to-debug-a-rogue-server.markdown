---
layout: post
title: "Zimbra: Debugging un servidor mailbox con mal desempeño"
date: 2015-06-24 22:26
comments: true
categories: [zimbra, debug, tips]
keywords: zimbra, debug, optimization
description: How to debug a mailbox server with bad performance
author: "Patricio Bruna"
---
Hace unos días comenzamos a recibir alertas de alta carga de uno de los servidores `Mailbox` de [ZBox](https://www.zboxapp.com), lo cual resultaba extraño ya que todos comparten la misma configuración, toda nuestra plataforma está definida con [Ansible](http://www.ansible.com).

Como buenos sysadmins que somos tenemos monitoreado cada uno de los componentes de la plataforma ZBox, así que lo primero que hicimos fue mirar que decían las gráficas. Lo que más resaltaba era que el proceso de MySQL de este Mailbox estaba siempre al 100% de CPU, mientras que todos los demás estaban muy por debajo. A mirar que pasaba con MySQL.

Gracias al detalle que nos permite [Hyperic](http://www.hyperic.com) identificamos que la cantidad de consultas `SELECT` por minuto eran 100 veces más que en el resto de los mailboxes. Algo extraño pasa aquí.

{%img center /images/selects-counter-mbxs.png %}

**Qué son estas consultas?**

Ya sabíamos que algo extraño pasaba con las consultas `SELECT`, pero lo que no sabíamos era que consultas eran. Para poder ver esto hay que habilitar el log de todas las consultas en MySQL. Entonces como usuario `zimbra` ejecutamos:

```
[zimbra@mailbox-03 ~]$ mysql
MariaDB [(none)]> SET GLOBAL general_log = 'ON';
MariaDB [(none)]> exit
```

Lo anterior habilita el registro de todas las consultas en el archivo `/opt/zimbra/log/mysql-mailboxd.log`. Hay que tener cuidado que este archivo crece mucho, muy rápido. Luego de un par de minutos desactive el registro con:

```
[zimbra@mailbox-03 ~]$ mysql
MariaDB [(none)]> SET GLOBAL general_log = 'OFF';
MariaDB [(none)]> exit
```

Nuetra intuición nos decía que algún usuario estaba provocando este comportamiento del servidor, así que lo que nos interesaba saber era que usuario. Para ello sacamos la información de las casillas con el siguiente comando:

```
$ grep SELECT /opt/zimbra/log/mysql-mailboxd.log|perl -ne 'chomp; @a=split(/\s+/,$_);print "$a[-7]$a[-6]$a[-5]\n"' > /tmp/mbxs
```

El contenido del archivo `/tmp/mbxs` son varias líneas del tipo:

```
...
mailbox_id=922
mailbox_id=922
mailbox_id=909
mailbox_id=922
mailbox_id=909
mailbox_id=909
mailbox_id=922
mailbox_id=922
...
```

Inmediatemente vimos un patrón, algo raro hay con las casillas `922` y `909`. Contemos para ver si hay algo verdaderamente extraño:

```
$ cat /tmp/mbxs | cut -d= -f2 | sort | uniq --count
      1 500
      5 520
      1 536
      1 859
  65234 909
  32430 922
```

Definitivamente algo raro hay con esas casillas. Pero ¿a qué usuario corresponden?. Para saber esto ejecutamos el siguiente comando:

```
[zimbra@mailbox-03 ~]$  mysql -e 'SELECT * FROM zimbra.mailbox WHERE id=909 OR id=922\G'
*************************** 1. row ***************************
                  id: 909
            group_id: 9
          account_id: dc0b1ca1-2764-4494-a654-56a1f3d6c5c7
     index_volume_id: 2
  item_id_checkpoint: 338657
       contact_count: 4562
     size_checkpoint: 43794615187
   change_checkpoint: 561405
       tracking_sync: 122119
       tracking_imap: 0
      last_backup_at: 1434698066
             comment: usuario_problema_1@example.com
    last_soap_access: 1434976906
        new_messages: 364
  idx_deferred_count: 0
     highest_indexed: NULL
             version: 2.7
       last_purge_at: 1435196438
itemcache_checkpoint: 0
*************************** 2. row ***************************
                  id: 922
            group_id: 22
          account_id: 191bd27e-b87e-4cb3-828c-31da1a01b642
     index_volume_id: 2
  item_id_checkpoint: 313381
       contact_count: 5510
     size_checkpoint: 43947359451
   change_checkpoint: 859858
       tracking_sync: 198
       tracking_imap: 0
      last_backup_at: 1434704383
             comment: usuario_problema_2@example.com
    last_soap_access: 1435192996
        new_messages: 26
  idx_deferred_count: 0
     highest_indexed: NULL
             version: 2.7
       last_purge_at: 1435197163
itemcache_checkpoint: 0

[zimbra@mailbox-03 ~]$
```

Como puedes ver en el resultado, `comment` indica cual es el usuario. Una vez ideniticados, los bloqueamos y el servidor volvió a su comportamiento normal, lo que nos permitió examinar con más calma cual es la causa del problema.

{%img center /images/mbx-new-select-chart.png %}

Después de unos minutos nos dimos cuentas que ambos usuarios, con más de `40GB` de correos, habían configurados sus iPads para descargar todo el correo. Si, así mismo.

