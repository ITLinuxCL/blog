---
layout: post
title: "Puppet creando usuarios desde Hiera"
date: 2014-05-28 12:24
comments: true
categories: devops, puppet, hiera
published: false
author: Miguel Coa M.
---
En una entrada anterior habíamos escrito sobre como [crear usuarios mediante Puppet](http://blog.itlinux.cl/blog/2014/02/04/puppet-administrando-cuentas-de-usuario-linux/). Bueno, ahora lo que veremos es este mismo proceso pero ahora la data será importada desde Hiera y aplicada a los nodos. 

####Definiendo nuesta arquitectura de búsqueda en Hiera

En nuestro archivo <code>hiera.yaml</code> nosotros creamos una nueva jerarquía de que se llama <code>users</code> esto con dos finalidades; 1. Tener mas limpio nuestro <code>common.yaml</code> ya que si son muchos usuarios se empezará a distorcionar el archivo  2. Manejar en un solo archivo las configuracones de cuentas de usuario. Por lo anterior nuestro <code>hiera.yaml</code> queda así:
```ruby
---
:backends:
  - yaml
:yaml:
  :datadir: /etc/puppet/hieradata
:hierarchy:
  - "osystem/%{::osfamily}"
  - "nodes/%{::fqdn}" 
  - users
  - common
```
Y creamos nuestro <code>users.yaml</code> 
```ruby
[root@master puppet]$ touch hieradata/users.yaml 
```
Con las configuraciones realziadas a nivel de Hiera, ahora tenemos que tener nuestra clase. Para este caso, la clase que utilizaremos es la misma que que utilizamos en el link que figura arriba salvo que ahora tiene unos pequeños cambios.

<li> Archivo <code>init.pp</code> </li>
```ruby
[root@master puppet]# cat modules/users/manifests/init.pp 
class users (
  $users = hiera('users')
){
  create_resources(users::add_user, $users)
}
```
Aquí como vemos tenemos el parámetro <code>create_resources</code> que nos permite tomar los valores que vienen dentro de un "hash" y adicionar esos valores dentro del manifiesto. Para el caso, nuestro hash viene en <code>hiera('users')</code>, lo pasamos a la variable <code>$users</code> y luego a la sub clase <code>users::add_user</code>

<li>Archivo <code>add_user.pp</code> </li>

```ruby
define users::add_user ( 
	$uid  	= '',
	$user 	= '',
	$realname = '',
	$shell = '',
	$groups = []
){

include 'users::add_group'

    user { $user:
            uid    => $uid,
            managehome  => true,
	    comment  => $realname,
	    shell    => $shell,
	    groups => $groups,
    }

    file { "/home/$user/":
        ensure  => directory,
        owner   => $user,
        mode    => 700,
        require => User[$user]
    }

    file { "/home/$user/.ssh":
            ensure  => directory,
            owner   => $user,
            mode    => 700,
            require => File["/home/$user/"]
    }

    file { "/home/$user/.ssh/authorized_keys":
                ensure  => present,
                source  => "puppet:///modules/users/$user.key",
                owner   => $user,
                mode    => 0600,
                require => File["/home/$user"]
        }

}
```

Como se ve al comienzo del manifiesto se tomam los valores del hash de hiera y luego son volcados en sus respectivas variables, en la línea nº9 tenemos un include <code>include 'users::add_group'</code> que llama a la clase <code>add_group</code>. 

<li>Archivo <code>add_group.pp</code> </li>

```ruby
[root@master manifests]$ cat add_group.pp 
class users::add_group {
	group {"itlinux":
                ensure => present,
        }
	 group {"admin":
                ensure => present,
        }
	 group {"conta":
                ensure => present,
        }
}
```
Lo que realiza este manifiesto  es la definición de los grupos que pertenecerán los usuarios (un usuario puede pertenecer a muchos grupos que tenemos que definirlos acá, de lo contrario Puppet arrojará que el grupo no existe)

<li>Archivo hiera <code>users.yaml</code> </li>
Acá se definen todos los valores con los que se creara el usuario
```ruby
[root@master hieradata]$ cat users.yaml 
---
classes: [ 'users' ]

users:
  mcoa:
     uid:  9001
     user: 'mcoa'
     realname: 'Miguel Coa'
     shell:  '/bin/bash'
     groups: [itlinux, admin, conta]
  linux:
     uid:  9002
     user: 'linux'
     realname: 'Linux Puppet'
     shell:  '/bin/bash'
     groups: [itlinux]
```
Podemos ocupar el comando <code>hiera</code> para chequear la información de la clase users que tenemos definida 

```ruby
[root@master puppet]$ hiera users ::fqdn=master.itlinux.cl
{"mcoa"=>
  {"uid"=>9001,
   "groups"=>["itlinux", "admin", "conta"],
   "shell"=>"/bin/bash",
   "user"=>"mcoa",
   "realname"=>"Miguel Coa"},
{"linux"=>
  {"uid"=>9002,
   "groups"=>["itlinux"],
   "user"=>"linux",
   "realname"=>"Linux Puppet",
   "shell"=>"/bin/zsh"},
```


Para que las clases sean aplicadas por Hiera a cada uno de los nodos estamos utilizando la función <code>hiera_include</code>

```ruby
[root@master manifests]$ cat nodes.pp 

node 'master.itlinux.cl' {
    hiera_include( 'classes')
}
```

Finalmente conectamos el agente puppet al servidor para cargar las configuraciones del manifiesto

```ruby
[root@master puppet]$ puppet agent --test 
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Caching catalog for master.itlinux.cl
Info: Applying configuration version '1401306968'
Notice: /Stage[main]/Users/Users::Add_user[mcoa]/User[mcoa]/ensure: created
Notice: /Stage[main]/Users/Users::Add_user[mcoa]/File[/home/mcoa/.ssh]/ensure: created
Notice: /Stage[main]/Users/Users::Add_user[mcoa]/File[/home/mcoa/.ssh/authorized_keys]/ensure: defined content as '{md5}d3d6f1310e9c2909c74568eb145e06d9'
Notice: Finished catalog run in 1.23 seconds
[root@master puppet]$
```
Comprobamos la configuración del usuario
```ruby
[root@master puppet]$ id mcoa
uid=9002(mcoa) gid=9002(mcoa) groups=9002(mcoa),9502(itlinux),9503(admin),10000(conta)
```
