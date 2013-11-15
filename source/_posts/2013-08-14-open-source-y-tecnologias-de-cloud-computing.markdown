---
layout: post
title: "Cloud Computing: Alternativas Open Source"
date: 2013-08-14 22:20
comments: true
categories: [linux, cloud computing, kvm, openstack]
keywords: linux, cloud computing, kvm, openstack
description: Breve resumen de las tecnologías más comunes en Cloud Computing y algunos ejemplos de productos Open Source disponibles para crear nubes.
author: "Patricio Bruna"
---
Aun cuando no tenga un significado concreto, Cloud Computing debe ser el término más usado en los últimos años de nuestra industria. De archivos compartidos en Internet hasta plataformas virtuales encuentran refugio en la Nube.

{% img /images/cloud_services.png %}

En términos técnicos Cloud Computing no es nada nuevo, la tecnología usada ha existido en una u otra forma desde hace tiempo, lo nuevo es el cambio en el modelo comercial utilizado por los proveedores. Lo que antes era un commodity: como comprar un servidor o un software, ahora se transformó en un servicio que se arrienda.

El cambio comercial empujó a su vez un cambio en la metodología usada para administrar las plataformas de TI. Ahora se deben levantar plataformas completas en cosa de minutos y los usuarios esperan inmediatez en cada uno de los servicios ofrecidos.

Este inmediatez también es la responsable de un nuevo movimiento llamado **DevOps**, el cual revisaremos otro momento. En este artículo exploraremos los tres conceptos más conocidos en Cloud Computing: SaaS, Paas e IaaS y daremos ejemplos de tecnologías Open Source con las que podemos configurar cada uno de ellos.

<!-- more -->

##SaaS: Software as a Service
Este modelo de negocio sonó fuerte en los año 90, pero con otro nombre **ASP**: _Application Service Provider_. Ahora bajo el nombre SaaS, su modelo es similar: el software corre en los servidores de la empresa que lo desarrolla y los usuarios acceden de forma remota a través de Internet. Está enfocado tanto a negocios como consumidores finales.

Hoy la mayoría de estos SaaS se utilizan mediante un navegador web y los ejemplos más conocidos deben ser los servicios de Correo como [ZBox](http://www.zbox.cl) y servicios de datos como [Dropbox](http://www.dropbox.com).

####Alternativas Open Source
**[OwnCloud](http://www.owncloud.com/)** es una aplicación escrita mayormente en PHP y que permite contar con una alternativa a Dropbox funcionando en nuestros servidores. Al igual que Dropbox cuenta con clientes para estaciones Windows, Mac y Linux, y para dispositivos móviles iOS y Android.


##PaaS: Platform as a Service
Este servicio está enfocado principalmente a empresas (o personas) que desarrollan software o servicios web. Los proveedores de PaaS ofrecen a sus clientes la plataforma para que sus aplicaciones se ejecuten y quitan la complejidad de la instalación, configuración, administración y escalabilidad de la plataforma.

Si tomamos como ejemplo una aplicación PHP, en el mundo tradicional deberíamos primero instalar un servidor con el sistema operativo Linux, luego configurar un servidor web, los módulos de PHP y la base de datos. Luego debemos aplicar las políticas de seguridad. Y finalmente publicamos el sitio a Internet. Si a nuestra aplicación le va bien y la demanda aumenta, deberíamos comprar nuevos servidores, realizar las mismas tareas y configurar un sistema de balanceo de carga.

En cambio si utilizamos un proveedor PaaS, como **[Heroku](http://www.heroku.com/)**, para publicar la aplicación sólo tenemos que _subirla_ y Heroku se encarga de todo el resto. Si necesitas más poder puedes aumentar inmediatamente la cantidad de "servidores" con el comando ```heroku ps:scale```.

Otra ventaja de los proveedores PaaS es que son **políglotas**, es decir, soportan casi todos los lenguajes de programación que existen y además pueden integrar fácilmente aplicaciones con cientos de otros [servicios en la nube](https://addons.heroku.com/) como: [bases de datos NoSQL](https://mongolab.com/), [Gateways de correo](http://sendgrid.com/), y [Monitoreo](http://www.newrelic.com/).

####Alternativas Open Source
El modelo que propone PaaS resulta interesante para corporaciones que operan con software desarrollado a medida y que utilizan _frameworks_ de nivel empresarial como J2EE, ya que ven reducidos los tiempos de habilitación de aplicaciones y también su mantención.

Tanto VMware como Red Hat tienen proyectos Open Source que apuntan en esta dirección: [Cloud Foundry](http://www.cloudfoundry.com) y [Open Shift](https://www.openshift.com/) respectivamente. Ambos proyectos cuentan además con una versión comercial que incluye el soporte de la marca respectiva.

Por el lado de la comunidad debemos mencionar [Flyn.io](https://flynn.io/), proyecto en desarrollo y financiado a través de Internet que ofrecerá una plataforma PaaS basada en la herramienta [Docker](https://www.docker.io/).

Todos estos proyectos tienen en común que utilizan [Linux Containers](http://lxc.sourceforge.net/) para entregar entornos de ejecución independientes a cada aplicación y que también emulan la manera de trabajar de Heroku.


##IaaS: Infrastruture as a Service
Por último llegamos al modelo de servicio Cloud de más bajo nivel y que consiste en el arriendo de servidores virtuales y espacio de almacenamiento.

Con este modelo se puede configurar una plataforma TI que está disponible inmediatamente, no hay que esperar por la llegada de equipos y la habilitación del data center, y que es elástica: crece y decrece según se necesite.

Los jugadores más importantes en este campo son Amazon con su servicio [EC2](http://aws.amazon.com/es/ec2/) y [RackSpace](http://www.rackspace.com/cloud/servers/). Ambos disponen de varios niveles que permiten el arriendo de recursos tarifados por hora.

También debemos mencionar que el almacenamiento de datos como objetos cada día gana más usuarios debido a los servicios que permite desarrollar. Dropbox es un importante usuario de [Amazon S3](http://aws.amazon.com/es/s3/)


####Alternativas Open Source
Actualmente existe un solo jugador de importancia en este campo en el mundo Open Source: [OpenStack](http://www.openstack.org/)

En términos simples, OpenStack permite replicar el mismo servicio ofrecido por Amazon y RackSpace, pero utilizando equipos propios. Su creación nace de un proyecto conjunto de RackSpace y la NASA, cuyo objetivo era poder contar con una plataforma elástica que ofreciera los mismos niveles de servicio que Amazon, pero que se instalara al interior de la organización.

OpenStack es el proyecto de Cloud Computing más importante a nivel mundial, y es usado y promocionado por las empresas más importantes de tecnología, como: Cisco, HP, IBM, Dell y Red Hat entre otras. La nueva plataforma Cloud de RackSpace opera sobre OpenStack y Red Hat ofrece una versión que cuenta con el soporte de la marca.


Bueno, eso es todo, y si llegaste hasta aquí muchas gracias por tu tiempo. Recuerda que las correcciones e ideas son bienvenidas en los comentarios.