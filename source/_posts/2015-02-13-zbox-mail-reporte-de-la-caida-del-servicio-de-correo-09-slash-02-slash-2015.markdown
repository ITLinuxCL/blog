---
layout: post
title: "ZBox Mail: Reporte de la caída del servicio de correo 09/02/2015"
date: 2015-02-13 10:15
comments: true
categories: 
categories: [zbox]
keywords: zbox
description: Reporte de la caída del servicio de correo del día 09 de Febrero de 2015
author: Equipo ZBoxApp
---
A continuación se explican los eventos que resultaron en la caída de los servicios de correo el día Martes pasado, y que duró un poco más de 45 minutos. Esta caída afectó al 70% de los usuarios de ZBox Mail.

ZBox Mail es una plataforma distribuida, es decir, existen varios servidores que cumplen funciones específicas, no uno sólo que realiza todo el trabajo. Cada servidor pertenece a uno de estos roles:

* **Mailbox**, servidor donde residen los datos de las casillas de correo. El repartir las casillas en más de un servidor se mejora la velocidad de acceso y en caso de falla, no se ven afectados todos los usuarios.

* **MTA**, servidor encargado de recibir y despachar los correos hacia y desde Internet. También realiza el trabajo de filtrar los correos para permitir que sólo quienes estén autorizados, puedan enviar correo usando nuestra plataforma.

* **Balanceador de Carga / Proxy**, es el servidor que responde cuando los usuarios se conectan para revisar sus correos. Este servidor los redirigi al Mailbox correspondiente.

* **Base de Datos de Usuarios y Configuraciones (LDAP)**, aquí reside la información sobre las cuentas de correos y sus contraseñas.

##### 1. Lunes 09 22:00 - Incorporación de segundo servidor LDAP
Con la intención de poder balancear las configuraciones de las cuentas de correo, incorporamos otro servidor LDAP que funcionaría como par del ya existente (Master/Master). La instalación se completó sin mayores problemas, al menos por ese día.

##### 2. Martes 10 10:30 - Incorporación de nuevo servidor Mailbox
Este es un trabajo que se realiza siempre sin mayores contratiempos, y consiste en habilitar un nuevo servidor para ir alojando los nuevos usuarios de ZBox Mail o re-balanceando de los existentes en caso de que utilicen más recursos. También se finalizó sin problemas.

##### 3. Martes 10 12:30 - Activación de nuevo Mailbox
Al ingresar el nuevo servidor Mailbox a la plataforma, se vació el caché de los servidores de Balanceo de Carga. El Balanceador de carga mantiene en cache RAM la ubicación de las casillas en los diferentes Mailbox, esto hace que las respuestas sean mucho más rápida al no tener que ir a preguntar cada vez a cada servidor Mailbox.

##### 4. Martes 10 12:40 - Problema de validación de usuarios
Al vaciarse el caché se hizo manifiesto un problema con el trabajo realizado en el paso 1, el día Lunes. La incorporación del nuevo servidor LDAP produjo que se perdiera la información de usuario y contraseña del 70% de los usuarios. El motivo de esto aun lo estamos revisando con Zimbra, ZBox Mail está basado en Zimbra Collaboration Suite, y contamos con su apoyo para resolver problemas de cierta gravedad o que correspondan a bugs en el software.

Vale aclarar que los datos de las casillas, es decir los correos, citas, contactos, etc, no fueron afectados por este problema. Sólo la información usuario:contraseña.

##### 5. Martes 10 13:30 - Recuperación de Respaldo
Luego de intentar por más de 30 minutos y no dar con una solución al problema de la sincronización entre los servidores LDAP, se decide apagar el nuevo servidor LDAP y recuperar la información pérdida desde nuestros respaldos. El respaldo correspondía a la noche anterior, por eso quienes cambiaron contraseña o crearon nuevos usuarios en la mañana del Martes pueden haber encontrado problemas durante los días que siguieron.

##### 6. Martes 10 13:45 - Servicio Re-establecido
En este momento el 90% de los usuarios ya cuentan con acceso al correo.

##### 7. Martes 10 14:00 - Habilitación de Replica LDAP
Se tomó la desición de habilitar inmediatamente un nuevo servidor LDAP que sólo funcionara como una copia en línea del existente. Esto hasta que tengamos resuelto con Zimbra el motivo de porque falló la implementación en modalidad Activo / Activo.

Este respaldo en línea permitirá recuperar los datos en un plazo menor. Mucho menos de casi la hora que nos tomó este Martes.

##### 8. Jueves 12 18:00 - Falló de servidor virtualización
Uno de nuestros servidores de virtualización falló, fallando con él algunos servidores Mailbox. Esto afectó a las cuentas de correo que viven en esos Mailboxes. La plataforma de Alta Disponibilidad actuó por si sóla y recuperó los servidores en otro servidor de Virtualización en aproximadamente 5 minutos.


### ¿Qué estamos haciendo para mejorar?
Sabemos que es técnicamente imposible contar con una plataforma que esté operativa el 100% del tiempo, pero cada día trabajamos para acercarnos lo más posible. En este mes estamos agregando más servidores y un nuevo sitio para contar con más medidas de contingencia.

Pero lo anterior no vale de mucho si el en caso de fallo nos demoramos mucho en resolver los problemas. Por lo que también debemos mejorar nuestros procedimientos para solucionar los problemas en menos tiempo y dar información más clara a nuestros usuarios.

Para lograr estos objetivos realizaremos las siguientes acciones durante la próximas semanas:

##### 1. Sitio web de estado de la Plataforma
Habilitaremos un nuevo sitio, externo a nosotros, en el cual podrán revisar transparentemente el estado de la plataforma y métricas de la misma.

##### 2. Información inmediata durante fallos
En el mismo sitio anterior se publicará información de forma constante cuando ocurra un problema y también tendrá habilitado una cuenta en Twitter para que puedan seguir en línea el proceso de recuperación y el estado de la plataforma.

##### 3. Nueva dirección de correo de Soporte
Sabemos que la dirección de correo actual no es muy amigable, así que habilitaremos una nueva más simple, como ayuda@zboxapp.com

##### 4. Agenda de trabajos pública
Gracias a ustedes estamos creciendo, y eso nos obliga a realizar trabajos en la plataforma casi todas las semanas. Por lo tanto los mantendremos informados cada vez que se planifique una operación en la plataforma.

Desde ya le pedimos las disculpas por los contratiempos que estos cortes han probocado, también disculpas por el atraso de este informe, disculpas a nuestros clientes y también al equipo de ZBox. Tomó más tiempo de lo presupuestado el poder examinar correctamente la plataforma y estar seguro de la raíz del problema.

Todos los puntos anteriores estarán en funcionamiento el próximo Miércoles 18 de Febrero y los informaremos por este mismo medio.

Equipo ZBox

[www.zboxapp.com](http://www.zboxapp.com)
