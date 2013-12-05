---
layout: post
title: "ZBOX Tip 004: Configurando Firmas de correo con Imagenes"
date: 2013-11-19 15:18
comments: true
categories: [zbox, tips, zimbra]
description: Configurar el webmail de Zimbra para insertar Imagenes en el pie de firma
author: Daniel Eugenin
---

Muchos usuarios utilizan alguna imagen en su pie de firma cuando envían sus correos.

Cómo se configura esto en Zimbra? Aquí vamos...

* Ingrese al webmail con su login y password

* Diríjase a la sección Preferencias

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200210448/Captura_de_pantalla_2013-11-19_a_la_s__14.05.49.png %}

<!-- more -->

* En el menú de la izquierda seleccione Firmas

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200288163/Captura_de_pantalla_2013-11-19_a_la_s__14.06.02.png %}

* A continuación seleccione que el Formato de la firma es HTML

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200278207/Captura_de_pantalla_2013-11-19_a_la_s__14.06.35.png %}


* Escriba el texto de su firma, y presione el botón Insertar imagen

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200210468/Captura_de_pantalla_2013-11-19_a_la_s__14.06.53.png %}

* Se le abrirá una ventana donde podrá seleccionar la imagen desde sus archivos. Seleccione su imagen y presione Aceptar.

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200278217/Captura_de_pantalla_2013-11-19_a_la_s__14.07.18.png %}

* A continuación seleccione que para todos los mensajes nuevos y los que responda, utilice su firma:

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200210458/Captura_de_pantalla_2013-11-19_a_la_s__14.07.36.png %}

* Guarde los cambios

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200278237/Captura_de_pantalla_2013-11-19_a_la_s__14.07.46.png %}


* Adicionalmente, chequee en sus preferencias que los correos que usted envía son en formato HTML, pues de lo contrario la firma nunca aparecerá. Preferencias -> Correo -> Redactar Mensajes -> "Como HTML". Y guarde los cambios.

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200278227/Captura_de_pantalla_2013-11-19_a_la_s__14.08.12.png %}



* Y ahora: envía tu correo con firma!!!!

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200288223/Captura_de_pantalla_2013-11-19_a_la_s__14.10.15.png %}




Nota de ayuda:
Qué puedo hacer si me sale el siguiente mensaje cada vez que envío mis correos?

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200278297/Captura_de_pantalla_2013-11-19_a_la_s__14.10.29.png %}

Bueno, en primer lugar presiona "Cancelar", y a continuación, para que no vuelva a aparecer debemos realizar lo siguiente:

Antes, debemos explicar que este mensaje aparece porque la imagen que se ha insertado en la firma, queda físicamente guardada en el Maletín de su cuenta de Zimbra.

Este maletín es -obviamente- privado, y es por eso que aparece la ventana de autentificación, puesto que está enviando un correo al exterior con una imagen que pertenece a su maletín privado.

Entonces, lo que debemos hacer es "publicar" o dejar esa imagen del maletín en forma pública.

* Para lo anterior, debemos realizar lo siguiente:

1) Diríjase al Maletín:

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200288253/Captura_de_pantalla_2013-11-19_a_la_s__14.37.42.png %}

2) En el menú de la izquierda, presione el botón derecho del mouse sobre el Maletín, y seleccione: compartir carpeta

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200288263/Captura_de_pantalla_2013-11-19_a_la_s__14.37.59.png %}

3) Seleccione acceso Público y luego Aceptar:

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200210548/Captura_de_pantalla_2013-11-19_a_la_s__14.38.08.png %}


Con esto ya no volverá a aparecer esa ventana de autentificación!!!

...
...
...

Sí, lo sé! antes de que me digas que nooooo, que es un problema de seguridad, pues estamos compartiendo -o dejando de público acceso- todo el contenido de nuestro Maletín. 

Aquí tenemos la solución, que es un poco más complicada, pero al fin y al cabo, a tí que te preocupa la seguridad, y ya tienes mayor experiencia, debes realizar lo siguiente:

* Crea una nueva carpeta en el Maletín
{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200288443/Captura_de_pantalla_2013-11-19_a_la_s__14.11.02.png %}

* Arrastra la imagen de la firma a esa nueva carpeta

* Ahora comparte de acceso público sólo esa nueva carpeta (obviamente ya eliminaste el permiso de la carpeta Maletín principal)
{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200278367/Captura_de_pantalla_2013-11-19_a_la_s__14.11.17.png %}

* Vuelve a las preferencias de la firma

* Selecciona la imagen y presiona el botón Insertar/Editar imagen

* Modifica la URL de la imagen y coloca el nombre de la nueva carpeta que creaste, luego Actualizar
{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200210628/Captura_de_pantalla_2013-11-19_a_la_s__14.12.37.png %}

* Guarda los cambios y ahora sí que sí sólo estarás compartiendo públicamente una carpeta con la imagen de tu firma.
