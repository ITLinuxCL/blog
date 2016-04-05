---
layout: post
title: 'Nuevamente se cambia la hora en Chile'
date: 2016-04-05 14:41
comments: true
categories: linux, tips, admin, cambio de hora
author: Patricio Bruna
---
{%img center /images/cambio_hora_2016.jpg %}
`SysAdmins` de Chile preparen sus agendas y avisen en casa que nuevamente nos tocará estar en alerta por el cambio al horario de _Invierno_.

Está vez el cambio se realizará el día **Domingo 15 de Mayo a las 00:00**, [según la información oficial de nuestro Gobierno](http://www.horaoficial.cl/cambio_hora.html).  Ojo que en el sitio dice `14 de Mayo, 24:00 horas`, pero esa fecha no existe. El reloj avanza de `00:00 a 23:59`.

En el momento indicado los relojes deben retroceder una hora, lo cual nos dejará con la fecha `14 de Mayo, 23:00`.

La mayoría de los fabricantes de software ya han sacado actualizaciones y recomendamos a todos aplicarlas lo antes posible. Estas actualizaciones no producen daño alguno:

* [Documentación de Red Hat](https://access.redhat.com/articles/1187353)
* [Java de Oracle](http://www.oracle.com/technetwork/java/javase/tzdata-versions-138805.html)

Es importante hacer un alcance sobre las aplicaciones Java o J2EE, Java maneja una DB de zonas horarias independiente del sistema operativo, así que, **sí o sí** hay que actualizar la `JVM`.

### ¿Cómo actualizo el Sistema Operativo?

#### Para Red Hat y derivados

```bash
$ yum install tzdata tzdata-java -y
```

#### Para Debian y derivados

```bash
$ apt-get install tzdata tzdata-java -y
```

#### Para Microsoft
La verdad es que no sabemos :)

Feliz `15|14 de Mayo` para Todos!!!
