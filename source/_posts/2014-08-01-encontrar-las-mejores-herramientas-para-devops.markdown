---
layout: post
title: "Utilizando Vagrant para hacer pruebas de Provisioning"
date: 2014-08-01 10:47
comments: true
published: true
keywords: linux, admin, puppet, hiera, devops
categories: devops, puppet, hiera
author: Miguel Coa M.
---
Una buena práctica a la hora de trabajar con Vagrant es que nos permite hacer lo que se demomina <i>provisioning</i> hacia nuestras máquinas virtuales una vez que las vallamos creando, configurar nuestros ambientes de esta forma nos da  2 ventajas importantes:
<ol>
<li>Automatizar las configuraciones e instalación de software que tendrán cada uno de estos ambientes virtuales</li>
<li>Evitar repetir las mismas tareas una y otra vez por cada ambiente virtual</li>
</ol>

Por supuesto que si trabajar con provisioning no nos llama la atención nos podemos conectar hacia la máquina virtual mediante <code>vagrant ssh</code>e instalar y configurar todo a mano. Pero hay que tener en claro que una de las gracias de estas herramientas y metodologías de trabajo es crear un ambiente de virtualziación en el menor tiempo posible, destruirlo (si fuera necesario) y volver a crearlo con solo un par de minutos.
Vagrant nos permite trabajar con multiples opciones de <i>provisioning</i>, desde un simple script shell o herramientas mas avanzadas para la administración de systemas: Ansible, Chef, <i>Puppet</i>, dependiendo de nuestra abilidades y experiencias tomaremos el camino hacia una u otra opción. 

Para el caso del presente post vamos a realizar nuestro provisioning con Puppet. Bajo la combinación de estas dos herramientas tenemos dos opciones de trabajo
<li>Mediante Puppet Apply</li> Nuestro manifiesto es declarado localmente en nuestro "ambiente" de virtualización en Vagrant y la máquina virtual leerá ese manifiesto para realizar lo declarado en el (no tenemos que tener un servidor Puppet Master).
<li>Mediante Agent</li> Nos conectamos hacia un servidor Puppet para cargar nuestro manifiesto.

####Manos a la obra y veamos como funciona el Provisioning de Vagrant + Puppet####

Creamos nuestro path de trabajo
```ruby
Miguel:VagrantVM$ mkdir Apache #Directorio donde alojaremos nuestra máquina virtual
Miguel:Apache$ mkdir manifests #Directorio donde alojaremos nuestros manifiestos
Miguel:Apache$ touch manifests/site.pp #Manifiesto para nuestra máquina virtual
```
La máquina virtual que vamos a crear tiene que será un Centos 6 que viene  el el agente Puppet pre-instalado en el. Para eso agregamos el siguiente <code>box</code> de Vagrant que utilizaremos como fuente de instalación base.
```ruby
Miguel:Apache$ vagrant box add  Centos6Puppet http://developer.nrel.gov/downloads/vagrant-boxes/CentOS-6.4-x86_64-v20131103.box
==> box: Successfully added box 'Centos6Puppet' (v0) for 'virtualbox'!
```
Luego inicializamos nuesta nueva máquina virtual
```ruby
Miguel:Apache$ vagrant init Centos6Puppet
A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` ....... for more information on using Vagrant.
```
Ahora ya tenemos nuestro archivo de configuración <code>Vagrantfile</code> creado por defecto por Vagrant para nuestra nueva máquina virtual. Ahora, lo vamos a editar y agregar el apartado para configurar nuestro provisioning.
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
>
# VM setup 
   config.vm.define :www do |config| 
   	config.vm.box = "Centos6Puppet"
   	config.vm.hostname  = "www.example.com"
	config.vm.network :public_network,  bridge: 'en1: Wi-Fi (AirPort)', ip: "192.168.1.37"
# VBox setup
      config.vm.provider "virtualbox" do |vboxconfig|
        vboxconfig.name = "www.example.com"
      end
# Puppet Provisioner setup
    config.vm.provision "puppet" do |puppetconfig|
        puppetconfig.manifests_path = "manifests"
        puppetconfig.manifest_file = "base.pp"
    end
 end

end

```
Entre las líneas nº 18 y  nº 23 tenemos toda nuestra declaración de <i>Puppet provisioner</i>, todas las opciones a configurar con Puppet son variadas y las puedes ver en detalle [acá](https://docs.vagrantup.com/v2/provisioning/puppet_apply.html), para nuestro caso solo utilizamos <code>manifests_path</code> donde se define la ruta que contendrá nuestros archivos de maniesto y <code>manifest_file</code> que es donde le pasamos el nombre de nuestro manifiesto.
Nuestro manifiesto es sencillo y esta creado solo con la finalidad de entender el funcionamiento del provisioner, nuestro manifiesto puppet lo que hace es; 
<ol>
<li> Instalar el paquete Apache </li>
<li> Subir el servicio</li>
<li> Crear un index.html (que sacará desde nuestro equipo local)</li>
<li> Crear una regla en el firewall para permitir el acceso al puerto 80 </li>
</ol>
El contenido de nuestro manifiesto <code>base.pp</code> es el siguiente
```ruby
package {"httpd":
	ensure => present,
	notify => Service["httpd"],	
}

service {"httpd":
	ensure    => running,
	enable    => true,
	hasstatus => true,
    	require   => Package["httpd"],
}

file {"apache-index":
        ensure  => present,
        require => Package['httpd'],
   	path    => '/var/www/html/index.html',
        source  => "/vagrant/index.html",
	notify  => Service["httpd"],
}

exec {"firewall-rule-80":
        path    =>  "/bin/:/sbin/:/usr/bin/:/usr/sbin/", 
	command => "iptables -I INPUT -p tcp --dport 80 -j ACCEPT",
}
```

Creamos nuestro index.html, esto tiene estar a la misma altura de nuestro Vagrantfile
```ruby
Miguel:Apache$ tree
.
├── Vagrantfile
├── index.html
└── manifests
    └── base.pp

1 directory, 3 files
```
Con todo lo anterior estamos listos para arrancar nuestra máquina virtual configurada con Puppet provisioner.
```ruby
Miguel:Apache$ vagrant up www --provision 
Bringing machine 'www' up with 'virtualbox' provider...
==> www: Clearing any previously set forwarded ports...
==> www: Clearing any previously set network interfaces...
==> www: Preparing network interfaces based on configuration...
    www: Adapter 1: nat
    www: Adapter 2: bridged
==> www: Forwarding ports...
    www: 22 => 2222 (adapter 1)
==> www: Booting VM...
==> www: Waiting for machine to boot. This may take a few minutes...
    www: SSH address: 127.0.0.1:2222
    www: SSH username: vagrant
    www: SSH auth method: private key
    www: Warning: Connection timeout. Retrying...
    www: Warning: Remote connection disconnect. Retrying...
==> www: Machine booted and ready!
==> www: Checking for guest additions in VM...
==> www: Setting hostname...
==> www: Configuring and enabling network interfaces...
==> www: Mounting shared folders...
    www: /vagrant => /Users/Miguel/VagrantVM/Apache
    www: /tmp/vagrant-puppet-2/manifests => /Users/Miguel/VagrantVM/Apache/manifests
==> www: Running provisioner: puppet...
==> www: Running Puppet with base.pp...
==> www: Notice: Compiled catalog for www.example.com in environment production in 0.56 seconds
==> www: Notice: /Stage[main]//Exec[firewall-rule-80]/returns: executed successfully
==> www: Notice: /Stage[main]//Package[httpd]/ensure: created
==> www: Notice: /Stage[main]//File[apache-index]/ensure: defined content as '{md5}0f25e63c5ca1dbb91163ad16c3ee4bde'
==> www: Notice: /Stage[main]//Service[httpd]/ensure: ensure changed 'stopped' to 'running'
==> www: Notice: Finished catalog run in 12.06 seconds
```
Desde las líneas nº24 hasta 31 Vagrant ejecutó todo nuestro provisioner, el cual realizó todo lo declarado en nuestro manifiesto <code>base.pp</code>. Desde ahora no necesitarás realizar siempre las mismas tareas ya que nuestro manifiesto puede ser reutilizado en diversas máquinas virtuales que tengamos creadas o vallamos creando en un futuro.
