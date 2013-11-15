---
layout: post
title: "Horario de Verano Chile 2013"
date: 2013-08-26 12:09
comments: true
categories: linux, tips, admin, cambio de hora
description: Como revisar la fecha en que cambiará la hora en Linux y ajustarla de ser necesario
author: Patricio Bruna
keywords: linux, tips, admin, cambio de hora
---
Se acerca nuevamente una fecha que debería estar en la agenda de cada SysAdmin de Chile: Inicio horario de Verano, el cual se aplica el Domingo 8 de Septiembre a las 00:00, hora que no existirá pues debes adelantar el reloj una hora, por lo que en realidad serán las 01:00.

{% img http://www.horaoficial.cl/img/reloj_verano.gif %}

Si tus sistemas están actualizados, el cambio debería aplicarse de forma automática. De todas formas te puedes asegurar con el comando ```zdump```:

```bash
$> zdump -v America/Santiago |grep 2013
America/Santiago  Sun Apr 28 02:59:59 2013 UTC = Sat Apr 27 23:59:59 2013 CLST isdst=1 gmtoff=-10800
America/Santiago  Sun Apr 28 03:00:00 2013 UTC = Sat Apr 27 23:00:00 2013 CLT isdst=0 gmtoff=-14400
America/Santiago  Sun Sep  8 03:59:59 2013 UTC = Sat Sep  7 23:59:59 2013 CLT isdst=0 gmtoff=-14400
America/Santiago  Sun Sep  8 04:00:00 2013 UTC = Sun Sep  8 01:00:00 2013 CLST isdst=1 gmtoff=-10800
```

Del resultado anterior nos interesan la tercera y cuarta línea, que indican cuando termina el horario de invierno y comienza el horario de verano respectivamente.

Como puedes ver en el ejemplo el horario de invierno termina a las 23:59:59 del día Sábado 7 de Septiembre y de ahí pasa a las 01:00:00 del Domingo 8 de Septiembre. Lo cual esta correcto según la [información oficial](http://www.horaoficial.cl/cambio_hora.html) del nuestro Gobierno.

Si ejecutaste el comando y tuviste un resultado diferente significa que debes actualizar tu sistema operativo, en específico el paquete llamado **tzdata**, y también recomendamos el **tzdata-java** para que tus aplicaciones Java funcionen no fallen.

```bash
# Actualización en Fedora/RedHat/CentOS
$> yum install tzdata tzdata-java

# Actualización en Ubuntu/Debian
$> apt-get install tzdata tzdata-java
```

Si por algún motivo no puedes aplicar actualizaciones de esta forma, puedes descargar un parche que tenemos disponible en nuestra base de conocimientos en: [https://itlinux.zendesk.com/entries/21102008-Actualizaci%C3%B3n-de-Cambio-de-Hora-2013](https://itlinux.zendesk.com/entries/21102008-Actualizaci%C3%B3n-de-Cambio-de-Hora-2013)

Saludos y feliz madrugada para el 8 de Septiembre.
