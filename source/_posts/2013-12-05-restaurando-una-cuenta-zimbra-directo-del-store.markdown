---
layout: post
title: "Restaurando una cuenta Zimbra directo del store"
date: 2013-12-05 12:08
comments: true
categories: [tips, zimbra]
description: Restaurando una cuenta de Zimbra directo desde el store
author: Daniel Eugenin
---

El otro día tuve un gran desafío con un cliente que le sucedió una gran tragedia.

Un poco de información adicional para tener el contexto del asunto:
* Servidor Zimbra 8 OpenSource
* Guarda respaldo de todo el /opt/zimbra vía rsync en un disco externo (QNAP)

Por motivos que no vienen al caso detallar aquí, a este cliente se le perdieron algunos correos de algunas cuentas de usuario y cuando se dió cuenta, tenía un vacío de algunas semanas en sus correos. Por ejemplo, tenía correos hasta hace 2 semanas atrás y luego se saltaba a los correos que ya tenía el día de hoy.

Afligido este cliente me llamó, y empezamos a ver qué tipo de soluciones podríamos tener, entre estas se me ocurrió;
- Instalemos un servidor extra, y recuperamos la información del QNAP por rsync. Con esto levantemos un servidor extra y podemos sacar un backup tipo tgz de cada cuenta, para luego importarlo en el servidor original sin pisar nada. A lo que el cliente me respondió: no tengo posibilidad de poner otro servidor, ni tampoco tengo espacio suficiente como para restaurar la data respaldada.

OK, ante esto también se me ocurrió guardar un backup tipo tgz de cada una de las cuentas, restaurar todo el /opt/zimbra desde el QNAP rsync (que era de la noche anterior), levantar el servicio Zimbra y luego importar cada una de esas cuentas del tgz. Antes esto yo mismo no quise recomendarlo pues es un riesgo sumamente alto el bajar todo, recuperar un rsync que no sabía su status de copia, pisar toda la data actual de Zimbra, y luego vaciar los backups tgz.

Y finalmente, buscando un poco de información por Zimbra, dí con la siguiente solución (con procedimiento y todo):


1) Me cambio al usuario zimbra:
```bash
   su - zimbra
```

2) Voy a un directorio de backups para trabajar tranquilamente:
```bash
   cd /opt/zimbra/backup/
```

3) Por seguridad previa, hago un Backup de la cuenta:
```bash
   zmmailbox -z -m cuenta@dominio.cl getRestURL "//?fmt=tgz" > cuenta@dominio.cl.tgz
```

4) Obtengo el ID de la cuenta:
```bash
   $ zmprov gmi cuenta@dominio.cl
   mailboxId: 785
   quotaUsed: 542813215
```

Por lo tanto, el store es: /opt/zimbra/store/0/785/msg/{0,1,2,3,4...N}

5) Me copio del QNAP el store del usuario a la carpeta de backup
```bash
   mkdir /opt/zimbra/backup/785
   rsync -avxHS admin@X.X.X.X:/share/zimbrabackup/zimbra/store/0/785/ /opt/zimbra/backup/785
```

6) Le seteo en la cabecera la fecha real de cada uno de los correos: (se demora un poco)
```bash
   for a in `ls -1 /opt/zimbra/backup/785/msg/`; do /usr/local/bin/zimdates /opt/zimbra/backup/785/msg/$a; done
```

* Nota: el script zimdates se debe crear adicionalmente, ver al final.


7) Creo una carpeta de recovery en el usuario:
```bash
   zmmailbox -z -m cuenta@dominio.cl cf /Recovery
```

8) Importo todos los correos del respaldo:
```bash
   for a in `ls -1 /opt/zimbra/backup/785/msg/`; do echo "Importando directorio $a"; zmmailbox -z -m cuenta@dominio.cl addMessage /Recovery /opt/zimbra/backup/785/msg/$a; done
```


Y voilá!
Este usuario ya tiene TODOS sus correos hasta el último backup rsync de la noche anterior. Todo bajo un directorio Recovery creado en su casilla.

Luego con esto ya restaurado, el usuario puede hacer una búsqueda de correos entre las fechas que le faltaban correos y moverlos a su bandeja de entrada por ejemplo.


Acerca del script zimdates:
Este es un script adicional, el cual sirve para modificar todos los archivos de una carpeta en particular y agregarles en la cabecera la fecha real del correo utilizando el valor X-Zimbra-Received.

Si esto no se realiza, al momento de hacer la restauración de los correos, todos éstos quedarán con la fecha y hora en que se hizo la restauración.

Script (como root):
```
   vi /usr/local/bin/zimdates
```

```
#!/bin/bash
#
# zimdates
# Chris Gitzlaff 2005-11-16
#
# This script inserts an X-Zimbra-Received header into each message
# immediately after the Date header.
#

SCRIPTDIR=`pwd`
TMPFILE="$SCRIPTDIR/zimdates.tmp"

show_usage() {
   echo "Usage: zimdates DIRECTORY"
   echo "Inserts the X-Zimbra-Received header into a directory of messages"
   echo
   echo "Example: zimdates ./mail/"
}

# check for correct usage: 1 argument (directory)
if [ $# -eq 1 ]; then
   MSGDIR=$1
   if [ ! -d $MSGDIR ]; then
      show_usage
      exit 1
   fi
else
   show_usage
   exit 1
fi

# if the temporary file exists, delete it
if [ -f $TMPFILE ]; then
   rm -f $TMPFILE
fi

cd $MSGDIR
for file in *
do
   grep "^Date\:\ " $file > $TMPFILE
   # use the first Date occurrence
   datestring=`sed -n '1p' $TMPFILE`
   # remove the 'Date: ' prefix
   datestring=${datestring#*\ }

   sed -n '1,/^Date\:\ /p' $file > $TMPFILE
   echo "X-Zimbra-Received: $datestring" >> $TMPFILE
   sed '1,/^Date\:\ /d' $file >> $TMPFILE

   mv $TMPFILE $file
done
```


Darle permisos de usuario zimbra:
```
   chown zimbra.zimbra /usr/local/script/zimdates
   chmod 755 /usr/local/script/zimdates
```

Y listo para usar!


Referencias:
http://www.zimbra.com/forums/installation/12617-recover-data-store-folders.html
http://www.zimbra.com/forums/administrators/729-solved-using-zmlmtpinject.html
http://wiki.zimbra.com/index.php?title=Account_mailbox_database_structure



