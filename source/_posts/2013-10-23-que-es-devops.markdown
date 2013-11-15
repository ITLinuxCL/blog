---
layout: post
title: "¿Qué es DevOps?"
date: 2013-10-23 20:18
comments: true
keywords: devops, sysadmin, puppet, zabbix
categories: [devops, sysadmin, puppet, zabbix]
description: DevOps consiste en traer las prácticas del desarrollo ágil a la administración de sistemas
author: Patricio Bruna
---
DevOps es un término para un grupo de conceptos, que aunque no son nuevos, han experimentado un renacimiento y rápida propagación a través de la comunidad técnica. Como todo término de moda, ([Cloud Compunting?](http://blog.itlinux.cl/blog/2013/08/14/open-source-y-tecnologias-de-cloud-computing/)), las personas tienen impresiones erradas y a veces contradictorias de lo que significa. Este artículo intenta dar una definición base de lo que DevOps representa.

{% img center /images/devops.png  500 %}

## Definición de DevOps
DevOps es un término relativamente nuevo para describir lo que también ha sido llamado como "administración ágil de sistemas" y también el trabajo y colaboración en conjunto de los equipos de operaciones y de desarrollo.

Puedes pensar en DevOps como la **participación efectiva de los administradores de sistemas en el proceso de desarrollo de aplicaciones, utilizando las mismas técnicas ágiles que usan los desarrolladores**.

Para este propósito "DevOps" no diferencia entre las diferentes sub-disciplinas de la administración de sistemas. Bajo el alero de "Ops" se ubican ingenieros, administradores de sistema, personal de operaciones, entre varios otros.

DevOps significa un montón de cosas diferentes para diferentes personas porque el "desarrollo ágil" en si mismo cubre un terreno bastante amplio. Algunas personas dirán que DevOps es "la colaboración de los departamentos de desarrollo y operaciones", o que es "tratar tu código como infraestructura", o "usar automatización", o "usar kanban", o una variedad de términos similares que se relacionan de una u otra forma. 

La mejor forma de definir su significado en profundidad es comparándolo con la definición de desarrollo ágil, que según [Wikipedia](http://en.wikipedia.org/wiki/Agile_software_development) y el [manifiesto ágil](http://agilemanifesto.org/), se resumen en tres puntos:

* **Principios Ágiles** - son los valores principales del movimiento, como: colaboración, personas antes que procesos, software antes que documentación y la disposición al cambio antes que la sobre planificación. 

* **Métodos Ágiles** - procesos específicos para implementar los principios ágiles. Iteraciones, _Lean_, Programación [Extrema (XP)](http://es.wikipedia.org/wiki/Programaci%C3%B3n_extrema), [_Scrum_](http://es.wikipedia.org/wiki/Scrum). En resumen, todo lo contrario al "Desarrollo en Cascada".

* **Prácticas Ágiles** - técnicas usadas a menudo con el desarrollo ágil, pero que no son requisitos. Como puede ser el [Test Driven Development (TDD)](http://es.wikipedia.org/wiki/Desarrollo_guiado_por_pruebas), [Integración continua (CI)](http://es.wikipedia.org/wiki/Integraci%C3%B3n_continua), etc.


Al poner atención a lo que las personas hablan acerca de DevOps, podemos ver que se relacionan fuertemente con los puntos anteriores:

* **Principios DevOps** - Como debemos pensar diferentes sobre operaciones. Ejemplos incluyen la colaboración entre administradores y desarrolladores, "infraestructura como código", etc.

* **Métodos DevOps** - Procesos que se usan para realizar operaciones ágiles, como iteraciones, lean/kanban, reuniones de 5 minutos, etc.

* **Prácticas DevOps** - Técnicas y herramientas específicas usadas como parte de la implementación de los procesos, como herramientas de automatización ([Puppet](https://puppetlabs.com/), Cheft), _continuous deployment_, sistemas de monitoreo (Nagios, [Zabbix](https://www.zabbix.com/)), y cualquier aplicación que tengas en tu "caja de herramientas".

Si nos guiamos por esta conclusión, de que DevOps consiste en traer las prácticas del desarrollo ágil a la administración de sistema y el trabajo en conjunto entre desarrolladores y administradores de sistemas, vemos que DevOps no es una descripción de cargo o el uso de herramientas, sino un método de trabajo enfocado a los resultados.

DevOps es un término que está sonando fuerte, prueba de ello es la [fuerte demanda de _Ingenieros DevOps_](http://puppetlabs.com/blog/what-is-a-devops-engineer), pero no te dejes engañar, tal cosa no existe. Lo que si existe es tu experiencia y tu disposición a trabajar en equipo [usando múltiples sombreros](http://servicevirtualization.com/profiles/blogs/devops-means-large-organizations-thinking-more-like-small-ones) para sacar adelante los desafíos.



-----
_Este post es un extracto traducido del Blog [The Agile Admin](http://theagileadmin.com), puedes encontrar el post original en el siguiente enlace: [http://theagileadmin.com/what-is-devops/](http://theagileadmin.com/what-is-devops/)_
