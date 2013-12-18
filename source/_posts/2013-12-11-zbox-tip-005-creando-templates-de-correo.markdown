---
layout: post
title: "zbox-tip-005-creando-templates-de-correo"
date: 2013-12-11 23:22
categories: [zbox, tips, zimbra]
comments: true
description: Creando Templates de Correo en Zimbra
author: Daniel Eugenin
---

Hay usuarios que constantemente envían correos de similares características, especialmente usuarios de Ventas o de Soporte.
Y Zimbra viene para ayudarnos con una formidable herramienta que permite a todos los usuarios enviar el mismo correo utilizando templates.



__Configurando carpeta de Templates__

1) El primer paso es crear una carpeta para almacenar los Templates. Esto nos permitirá después incluso compartir estos templates!


{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200336747/Captura_de_pantalla_2013-12-11_a_la_s__19.10.19.png %}

Escriba el nombre de la carpeta, si gusta le coloca un color:
{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200263718/Captura_de_pantalla_2013-12-11_a_la_s__19.10.46.png %}

<!-- more -->

Ahora cree un nuevo correo y seleccione la pestaña Templates para configurar la carpeta donde se almacenarán los correos:
{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200263708/Captura_de_pantalla_2013-12-11_a_la_s__19.13.04.png %}

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200336757/Captura_de_pantalla_2013-12-11_a_la_s__19.13.12.png %}

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200349123/Captura_de_pantalla_2013-12-11_a_la_s__19.13.22.png %}

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200263728/Captura_de_pantalla_2013-12-11_a_la_s__19.13.32.png %}

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200263738/Captura_de_pantalla_2013-12-11_a_la_s__19.13.40.png %}

Con esto anterior ya está en condiciones de empezar a crear sus propios templates!



__Creando el Template__

1. Cree un nuevo mensaje

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200349193/Captura_de_pantalla_2013-12-11_a_la_s__19.19.42.png %}

a) Ingrese el subject del template
b) Ingrese el texto que forma parte del mensaje
c) Ingrese los campos que varían en el mensaje, utilice el formato ${campo}, como por ejemplo ${nombre} o ${apellido} o ${fecha}.
d) Si ya tiene una firma en su correo, no es necesario incluirla en el template nuevamente

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200263808/Captura_de_pantalla_2013-12-11_a_la_s__19.32.46.png %}


2. Si el correo siempre será enviado a las mismas personas, puede ingresar la información de los campos TO, CC o BCC.

3. Ingrese su correo en el campo BCC.

4. Envíe este correo a usted mismo. Nota: si ingresó algunos correos adicionales en el número 2, ellos también recibirán este correo. Ojo!.

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200336777/Captura_de_pantalla_2013-12-11_a_la_s__19.35.16.png %}



5. Una vez recibido el correo, arrástrelo a su nueva carpeta de Templates que ha creado.

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200349203/Captura_de_pantalla_2013-12-11_a_la_s__19.35.48.png %}


Y Listo!, ahora tendrá ya un template listo para ser utilizado!!!


__Usando Templates__

Ahora, finalmente, para utilizar estos templates que hemos creado, debe realizar lo siguiente:

1. Escriba un nuevo correo


2. Desde la pestaña Templates, seleccione el template a utilizar. Las opciones que tiene son:

a) Insertar sólo el body del template. Esta opción ingresará sólo el texto del mensaje; el subject y destinatarios deberá ingresarlos por su cuenta.
b) Insertar body y subject del template.  Esta opción incluirá el subject que ha sido ingresado cuando creó el template.
c) Insertar body, subject y participantes del template. Esta opción incluirá las direcciones de correo de los destinatarios que ingresó cuando creó el template.

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200261216/Captura_de_pantalla_2013-12-11_a_la_s__19.44.18.png %}

3. Ahora aparecerá la ventana solicitando los datos que configuró previamente en el template:

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200261206/Captura_de_pantalla_2013-12-11_a_la_s__19.44.47.png %}

4. Ahora observará el correo completo y con todos los campos llenados.
Ingrese información adicional, por ejemplo en las opciones TO, CC o BCC y envíe el correo.

{%img https://itlinux.zendesk.com/hc/en-us/article_attachments/200349213/Captura_de_pantalla_2013-12-11_a_la_s__19.45.00.png %}


__Editando Templates__

Después que ha creado un template, lamentablemente este no puede ser editado, sin embargo existe una solución:

1. Cree un nuevo template
2. Copie y pegue el texto del correo desde el template anterior
3. Haga los cambios que necesite en el nuevo template
4. Envíe el template a sí mismo y arrástrelo a la carpeta de templates


__Borrando Templates__

Simplemente, para borrar un template debe eliminar el correo que tiene en su carpeta de Templates.


__Compartiendo Templates__

Al principio mencionamos que usted podría compartir sus propios templates con otros usuarios, para los cual debe hacer lo siguiente:

1. Click derecho sobre la carpeta Templates y seleccione "Editar Propiedades"
2. Seleccione "Compartir"
3. Ingrese el correo de la persona a la cual va a compartir su carpeta de templates.
