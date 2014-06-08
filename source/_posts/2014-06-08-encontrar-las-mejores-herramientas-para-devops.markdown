---
layout: post
title: "Encontrar las mejores herramientas para DevOps"
date: 2014-06-08 11:43
comments: true
categories: devops
author: "Patricio Bruna"
keywords: devops,herramientas
description: Como en cada profesión, la selección de las herramientas correctas puede hacer la diferencia entre un trabajo feliz o miserable.
---
Para alcanzar la velocidad y agilidad ofrecida por DevOps - [¿Qué es DevOps?](http://blog.itlinux.cl/blog/2013/10/23/que-es-devops/) - necesitas elegir las herramientas correctas con las cuales podrás automatizar todas las tareas de desarrollo, producción y operación. De acuerdo al [estudio realizado el 2013 por PuppetLabs](http://puppetlabs.com/blog/2013-state-of-devops-benchmark-your-organization), más del 80% de las organizaciones de alto desempeño que usan tecnología en aspectos claves de su negocio confían en herramientas de automatización para el manejo de su plataforma y pasos a producción de sus sistemas. Según esto, es probable que ya tengas muchas de las herramientas usadas en ambientes DevOps, ¿pero son las herramientas correctas para tu empresa?


{%img center /images/devops-tools.jpg %}

###Toma un acercamiento estratégico
No existe una única forma de seleccionar las herramientas correctas para tus necesidades. Los requerimientos de tu industria, departamento de TI, sistemas legados y flujos de trabajos son únicos a tu empresa. Y también es la manera para elegir las herramientas que usarán para maximizar los beneficios de DevOps.

La clave es crear una estrategia centrada en lo que es importante para tu empresa. Si tu objetivo es la colaboración, las herramientas correctas pueden alentar a tu equipo a hacer las cosas correctamente. En un breve tiempo se darán cuenta de que el esfuerzo puesto al principio aprendiendo a utilizar nuevas herramientas, se verá recompenzado a no tener que lidiar con trabajo tedioso como puede ser la habilitación de ambientes de prueba y la revisión de código o configuraciones de servidores.


### Identificar los cuello de botella
El primer paso es identificar los cuello de botella de tus flujo de trabajo que impiden que tu empresa pueda contar con una plataforma más estable y usar la tecnología más fácilmente para apoyar a al negocio.

Hay dos caminos para identificar estos cuellos de botella. Primero: Pregunta! Pregunta a tu equipo en que parte las cosas se ponen lentas o problemáticas. Pídeles que generen un ranking según la severidad de estos cuellos de botella, y que identifiquen los que afectan a sistemas críticos.

Pero esto no es todo. También deberás analizar los logs de tus sistemas. Aprender que tareas y servicios son usados más y tienen un peor desempeño, que sistemas son los más inestables, y suma y sigue. Esto te dará evidencia dura de que está funcionando bien en tu plataforma y empresa, donde esta peor, y que es lo que está fallando.

Combina estos dos métodos para categorizar y comenzar la búsqueda de las herramientas que necesitan.

###Enfócate en categorías claves
Las empresas más exitosas en implementar DevOps automatizan sólo utilizando herramientas en una pocas categorías claves, como por ejemplo:

**Administración de configuraciones**. Cuando los aficionados de DevOps hablan de cosas como "automatización de infraestructura", "infrastructura como código" e "infrastructura programable", ellos se refieren a la administración de configuraciones. Esto es la mantención y el seguimiento de forma centralizada de cambios en la configuración de los sistemas y softwares, lo que permite realizar operaciones rápidamente y de forma segura sobre la plataforma. Las herramientas más conocidas en este ámbito son [Ansible](http://www.ansible.com), [CFEngine](https://cfengine.com), [Chef](http://www.getchef.com), [Puppet](http://www.puppetlabs.com) y [SaltStack](http://www.saltstack.com). La real pregunta es que necesitan ustedes de un sistema de configuración? Por ejemplo [usando Puppet](http://blog.itlinux.cl/blog/categories/puppet/) puedes tener de forma inmediata un inventario de tu plataforma, mientras que está está trabajando como debe.

**Paso a Producción**. Las herramientas para hacer _deploys_ permiten que el paso a producción de sistemas, lo que es el corazón de _continuous delivery_, uno de los principales objetivos de DevOps. [Capistrano](http://capistranorb.com), una librería de deploy, es la herramienta más popular en esta categoría. Otras herramientas importantes para automatizar los pasos a producción son [Ansible](http://www.ansible.com/home), [Fabric](http://www.fabfile.org), y [Jenkins](http://jenkins-ci.org). Nuevamente, lo importante es encontrar una herramienta que haga el seguimiento del historial de los sistemas y que esta información sea significativa para tu equipo.

**Monitoreo**. En DevOps se requieren dos tipos de monitoreo. El primero tiene que ver con la disponibilidad de los servicios, y aquí [Nagios](http://www.nagios.org) es la herramienta más conocida, aunque [Sensu][http://sensuapp.org] está ganando terreno rápidamente. El otro tipo de monitoreo es sobre el desempeño de aplicaciones y dispositivos (CPU, Memoria, Disco, etc.), en este campo la recolección de estadísticas está gobernada por herramientas como [statsd](https://github.com/etsy/statsd/) y [collectd](http://www.collectd.org), mientras que para la visualización recomendamos sin lugar a dudas [Grafana](http://grafana.org).

**Análisis de Logs**. Si hay un espacio importante en nuestra actividad que ha sido dejado de lado por años, este es el análisis de los logs de nuestros sistemas y aplicaciones. Es en los logs donde encontramos la **verdad** de la salud de nuestra plataforma. Para la recoleción y transformación de logs en datos analisables la herramienta que se impone es [Logstash](http://www.elasticsearch.org/overview/logstash/) la cual viene hoy con una consola gráfica de análisis excelente como es [Kibana](http://www.elasticsearch.org/overview/kibana/).

**Pruebas de configuraciones**. Otro ámbito importante y que no es muy usual ver que se aplique es la **automatización de pruebas** de los servicios que dan nuestras plataformas TI. Por ejemplo revisar que desde Internet sólo estén habilitados ciertos puertos, o que ninguno de los servidores permita el acceso del usuario root utilizando ssh. Hoy gracias a [Serverspec](http://serverspec.org) es ridículamente sencillo escribir estos test y así estar seguro de que nuestras configuraciones están correctas.

Contar con el conjunto correcto de herramientas DevOps automatizará los trabajos de TI, entregará visibilidad en tiempo real del desempeño de sistemas y dispositivos, y te dará la seguridad de contar con una única fuente de información verídica. Más importantes que las capacidades individuales de cada herramienta, es como en conjunto pueden te ayudan en lograr los objetivos de tu empresa.

