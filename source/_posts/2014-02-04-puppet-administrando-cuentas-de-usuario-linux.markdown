---
layout: post
title: "Puppet: Administrando cuentas de usuario (Linux)"
date: 2014-02-04 12:04
comments: true
categories: puppet, devops
author: Miguel Coa M.
---
Una de las primeras cosas que realiza a la hora de trabajar con Puppet para la administración de sistema Linux es la gestión de cuentas de usuario. Como Puppet nos permite tomar el control para hacer y deshacer en nuestros nodos, es de real eficacia gestionar nuestras cuentas de usuario de esta forma. A continuación, vamos a ver como podemos crear cuentas de usuarios en los nodos que definamos. 
iEl módule que nos permitirá hacer la magia es bastante simple, lo llamativo es que la clase trabaja en base a variables lo que nos pemitirá reutilizar el código y con este módulo puede ser instaciado para la creación de "N" usuarios. De lo contrario (sin variables) tendríamos que crear una clase, por cada usuario lo que no tiene sentido. 

Bueno, manos a la obra y vamos a crear nuestro módulo:

1. Lo primero que tenemos que realizar es crear el path del módulo y el init.pp que será llamado cuando sea instanciado el módulo dentro de nuestro(s) nodo(s).

```ruby
[root@master modules]$ mkdir users
[root@master modules]$ mkdir users/{files,manifests,templates}
[root@master modules]$ touch users/manifests/init.pp
```

2. El contenido del init.pp tiene que ser el siguiente 

```ruby
root@master manifests]$ cat init.pp
define users::add_user ( $user, $uid) {

    user { $user:
            uid        => $uid,
            managehome => true,
    }

    group { $user:
            gid     => $uid,
            require => User[$user]
    }

    file { "/home/${user}/":
        ensure  => directory,
        owner   => $user,
        group   => $user,
        mode    => 750,
        require => [ User[$user], Group[$user] ]
    }

    file { "/home/${user}/.ssh":
            ensure  => directory,
            owner   => $user,
            group   => $user,
            mode    => 700,
            require => File["/home/${user}/"]
    }

    file { "/home/${user}/.ssh/id_rsa.pub":
                ensure  => present,
                source  => "puppet:///modules/users/${user}/id_rsa.pub",
                owner   => $user,
                group   => $user,
                mode    => 0600,
                require => File["/home/${user}"]
        }
}
```
###Algunas cositas que aclarar del módulo 

<!-- more -->

Como se mencionó al inicio del post el módulo trabajará con variables las cuales en Puppet para declararlas como tal, tienen que empezar con el simbolo "$". Para este caso será utilizado en las variables $user y $uid, valores que son pasados como parámetro cuando se crea un usuario. Dependiendo de las necesidades que se pueda incurrir al momento de crear el usuario, podemos definir como varible los valores de: grupo, clave ssh, etc y luego ser pasados como parámetro. En este caso, nosotros creamos un directorio adicional bajo el path "files" del módulo que almacenará las claves privadas de cada usuario que luego serán replicadas al momento de que el corra el agente en el nodo.

3. Creamos el path donde alojaremos la ssh key que será copiada
```ruby
[root@master modules]$ touch users/files/mcoa/
[root@master modules]$ ls users/files/mcoa/id_rsa.pub
users/files/mcoa/id_rsa.pub
```
4. Instanciamos dentro de nuestra declaración del nodo el módulo
```ruby
node 'node1.example.com'  {
        users::add_user { 'mcoa': user => "mcoa", uid  => '9000'}
}
```
Como se aprecia en la declaración, se instanció la clase <i>users::add_user</i> y se le pasó como parámetro los valores user (mcoa) y el uid (9000). 

5. Corremos el agente en el nodo para que se conecté al puppet server y luego aplique los cambios realizados
```ruby
[root@node1 ~]$ puppet agent --test --server=puppet.example.com --verbose
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/root_home.rb
Info: Loading facts in /var/lib/puppet/lib/facter/puppet_vardir.rb
Info: Loading facts in /var/lib/puppet/lib/facter/pe_version.rb
Info: Loading facts in /var/lib/puppet/lib/facter/hosts_managed.rb
Info: Loading facts in /var/lib/puppet/lib/facter/facter_dot_d.rb
Info: Caching catalog for node1.example.com
Info: Applying configuration version '1391525931'
Notice: /Stage[main]//Node[node1.example.com]/Users::Add_user[mcoa]/User[mcoa]/ensure: created
Notice: /File[/home/mcoa/]/mode: mode changed '0700' to '0750'
Notice: /File[/home/mcoa/.ssh]/ensure: created
Notice: /File[/home/mcoa/.ssh/id_rsa.pub]/ensure: defined content as '{md5}71870f4be78f298442c2b6929ab3a8cb'
Notice: Finished catalog run in 0.62 seconds
```
Como vemos al conctar el nodo al puppet server y ver el log del manifiesto aplicado vemos quepuppet executó la instancia al nuevo módulo y lo aplicó. Donde creó un nuevo usuario (línea 10), configuró los permisos (línea 11), creó el directorio ssh (línea 12) y finalmente copia el el id_rsa.pub (línea 13). 

6. Chequeamos los datos del usuario
```ruby
[root@node1 ~]$ id mcoa
uid=9000(mcoa) gid=9000(mcoa) groups=9000(mcoa)
[root@node1 ~]$ grep mcoa /etc/passwd
mcoa:x:9000:9000::/home/mcoa:/bin/bash
```


