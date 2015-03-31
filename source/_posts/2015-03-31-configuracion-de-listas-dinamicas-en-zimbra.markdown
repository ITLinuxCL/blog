---
layout: post
title: "Configuración de listas dinámicas en Zimbra"
date: 2015-03-31 10:52
comments: true
categories: 
author: Miguel Coa M.
---
Una de las nuevas características en Zimbra 8.x es el trabajo con listas de distribución dinámicas, la gracia de esto es que podemos crear una lista  de distribución y que esta se valla "alimentando" de forma automática en la medida que nosotros creamos o eliminamos usuarios del servidor de correos, al contrario de las listas de distribución genéricas en donde nosotros tenemos que agregar o sacar un usuario manualmente para que esta se actualice.
#### <li> Creando nuestra lista dinámica </li>
Para poder trabajar con este tipo de lista, Zimbra incorpora un nuevo comando llamado `cddl` para trabajar con `zmprov` el cual nos permitirá crear la lista conectada al ldap mediante el valor que le asignemos al atributo `memberURL`  Ej.
```ruby
[zimbra@mail ~]$ zmprov cddl todos@example.local memberURL 'ldap:///??sub?(objectClass=zimbraAccount)' zimbraIsACLGroup FALSE
``` 
Lo anterior nos crea la lista `todos@example.local` que es del tipo dinámica y que se conectará al ldap local de Zimbra. Ahora, si nos vamos a nuestro panel web la podremos ver con todos los usuarios de Zimbra, cuando digo todos son *todos*, por que aparecerán incluso los usuarios de sistema *(virus, spam, gal, etc)*, como vemos en la imagen siguiente:

{% img center /images/listas_imagen1.png  %}

Para evitar lo anterior nos conectamos vía web al servidor, nos vamos al apartado de las listas de distribución, buscamos y editamos nuestra lista. En la opción *Propiedades* cambiamos la query del <code>memberURL</code> dejandola así:
```
memberURL ldap:///??sub?(objectClass=zimbraAccount)(zimbraAccountStatus=active)(!(zimbraIsSystemAccount=TRUE))
```
{% img center /images/listas_imagen2.png  %}

Y ahora veremos que aparecen solo cuentas de usuarios
{% img center /images/listas_imagen3.png %}
La query que utilizamos como conexión al Ldap la podemos modificar como nos sea mas conveniente para ir filtrando aún mas los miembros que componen cada lista. Para mas información y ejemplos podemos ver [la documentación de Zimbra](https://www.zimbra.com/docs/os/8.6.0/administration_guide/wwhelp/wwhimpl/common/html/wwhelp.htm#href=860_admin_os.Using_CLI_to_Manage_Dynamic_Distribution_Lists.html&single=true) . 
