---
layout: post
title: 'Publicar Tweets usando Cron'
date: 2016-03-07 19:52
comments: true
categories: [linux, tips, tweeter]
keywords: linux, tips, tweeter
description: Automatizar la publicacion en Twitter usando cron y Ruby
author: "Patricio Bruna"
---
Como algunos ya sabrán estamos [buscando gente que se integre a nuestro equipo](https://www.getonbrd.cl/jobs/ing-soporte-linux-it-linux), y encontrar buenos candidatos
es casi tan difícil para la empresa, como para el que busca un buen trabajo, así que
tienes que tratar de llegar a la mayor cantidad de personas: Las Redes Sociales.

Entonces como buen `DevOps` decides que tienes que automatizar este proceso, nos vas a gastar
tu tiempo `posteando`, y para ello vamos a ocupar [Twitter CLI](https://github.com/sferik/t), una herramienta
de consola que permite postear en Twitter.

Su instalación, configuración y demás está muy bien [explicada en el Readme](https://github.com/sferik/t), así que no lo vamos a repetir aquí. Una vez instalada sólo debes configurar `crontab` con las publicaciones que quieras, en el caso nuestro lo configuramos así:

```bash
06 17 * * * /usr/local/bin/t update "En @ITLinux buscamos Ingeniero de Soporte Linux para nuestro equipo http://bit.ly/21hlwgy"
06 21 * * * /usr/local/bin/t update "Si amas crear soluciones con OpenSource, unete al equipo de @ITLinux http://bit.ly/21hlwgy"
06 8 * * * /usr/local/bin/t update "En @ITLinux buscamos Ingeniero de Soporte http://bit.ly/21hlwgy"
06 11 * * * /usr/local/bin/t update "Ven y pon a pruebas tus conocimientos #DevOps, en @ITLinux tenemos un puesto para ti http://bit.ly/21hlwgy"
30 14 * * * /usr/local/bin/t update "Quieres trabajar en una empresa donde importa la creatividad mas que los productos? Ven y unete a nuestro equipo http://bit.ly/21hlwgy"
```

Y eso es. Espero que a más de uno le sirva.
