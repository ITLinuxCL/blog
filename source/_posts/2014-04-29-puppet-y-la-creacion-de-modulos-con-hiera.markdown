---
layout: post
title: "Puppet y la creación de módulos con Hiera"
date: 2014-05-08 10:02
comments: true
published: true
keywords: linux, admin, puppet, hiera, devops
categories: devops, puppet, hiera
author: Miguel Coa M.
---
Primero que todo ¿Qué es Hiera?

De las palabras de la documentación de Puppet:
{% blockquote %}
Hiera es una herramienta para consultar información del tipo clave/valor, desarrollado para mejorar Puppet y permitir guardar sin duplicación datos específicos de los nodos.
{% endblockquote %}

En palabras un poco más simple, Hiera; es un componente (así como facter) que nos permite generar un grado de abstracción y portabilidad mayor al momento de configurar nuestros módulos. Ya que en archivos en formatos "yaml, json, mysql y otros"  de forma jeráquica podemos almacenar información que luego será llamada por el módulo y "cargada" al manifiesto. 

### Un par de aclaraciones de Hiera
Hiera viene de forma nativa en las versiones de Puppet 3, si trabajas con versiones más antiguas tendras que instalar el paquete de Hiera o realizar un upgrade de [Puppet](http://docs.puppetlabs.com/hiera/1/installing.html). La información referente a la configuración de Hiera se carga y lee de forma jerárquica desde el archivo <code>/etc/puppet/hiera.yaml</code>  y el contenido que tiene el archivo es similar al siguiente:

```ruby
---
:backends: yaml
:yaml:
  :datadir: /var/lib/hiera
:hierarchy: common
:logger: console
```
Vamos por líneas

1. <code>:backends:</code> Define que tipo de backends (ficheros) leerá Hiera, pudiendo ser yaml, json, mysql, etc.
2. <code>:yaml:</code> Indica la ruta en donde almacenaremos nuestros backends.
3. <code>:hierarchy:</code> Indica cual será la jerárquia por la que buscará las variables para los nodos, las variables que toma son los valores de facter ::osfamily, ::fqdn, ::hostname, etc. El valor <code>common</code>, es el archivo común para todas las configuraciones de uso general.
4. <code>:logger:</code> Permite generar log y dubug al funcionamiento de hiera.

Una vez configurado el fichero hiera.yaml, tenemos que hacer un lik simbólico hacia <code>/etc</code>
```ruby
[root@master ~]# ln -s /etc/puppet/hiera.yaml /etc/
``` 
Por cada cambio que realizamos en el archivo hiera.yaml tenemos que reiniciar el servicio puppet para que tome los nuevos valores. Con lo anterior, ya estamos en condiciones de empezar a utilizar Hiera e integrarlo con Puppet. Para mayor detalle de las configuraciones que se pueden realizar en dicho archivo puedes echar un vistazo a la [documentación oficial](http://docs.puppetlabs.com/hiera/1/configuring.html).


### Empecemos con nuestro ejemplo
Vamos a instalar Apache en dos servidores uno que corre en Ubuntu y otro que corre en Centos, en donde toda la definición de variables para el proceso de instalación será declarada en Hiera.


<li>Configuración archivo Hiera <code>/etc/puppet/hiera.yaml</code> será el siguiente:</li>
```ruby
[root@master ~]$ cat /etc/hiera.yaml
---
:backends: 
  - yaml
:hierarchy:
  - %{osfamily}
  - common
:yaml:
  :datadir: /etc/puppet/hieradata/
```
Creamos el directorio donde almacenaremos nuestros archivos yaml
```ruby
[root@master ~]$ mkdir /etc/puppet/hieradata/
```
En <code>:hierarchy:</code> definimos que nuestra jerárquia por la que se va a buscar las variables, para el caso será <code>%{osfamily}</code>, osea a nivel de facter preguntará si el sistema operativo pertenece a la familia de RedHat (RedHat,Centos, Fedora) o Debian (Ubuntu, Debian) y a partir de eso actuará, donde dependiendo del <code>%{osfamily}</code> irá a leer los archivos <code>RedHat.yaml</code> y <code>Debian.yaml</code> (que crearemos más adelante) y finalmente irá a leer el archivo <code>common.yaml</code> donde están definidas las configuraciones globales (si es que las hay).


Por ejemplo, en la siguiente imagen es posible ver otra facts y su orden jerárquico
{% img http://docs.puppetlabs.com/hiera/latest/images/hierarchy1.png %}

Creamos los archivos Debian.yaml, RedHat.yaml y common.yaml.
```ruby
[root@master puppet]$ ls hieradata/
common.yaml  Debian.yaml  RedHat.yaml
```
<u>Nota</u> En el caso de trabajar con nodos de forma directa, tendríamos que cambiar nuestro fact en hierarchy a algo como <code>:hierarchy: %{::fqdn}</code> o  <code>:hierarchy: %{::clientcert}</code> y crear el fichero yaml <code>node.example.com.yaml</code>

El contenido de los archivos Debian.yaml y RedHat.yaml es el siguiente
```ruby
[root@master puppet]$ cat hieradata/RedHat.yaml 
---
apache_package_name: "httpd"
apache_name_service: "httpd"
apache_owner_name: "apache"
apache_name_group: "apache"
apache_dir_name: "/var/www/html/index.html"
[root@master puppet]$ cat hieradata/Debian.yaml 
---
apache_package_name: "apache2"
apache_name_service: "apache2"
apache_owner_name: "www-data"
apache_name_group: "www-data"
apache_dir_name: "/var/www/index.html"
[root@master puppet]# 
```
Como sabemos el nombre del paquete apache difiere entre Debian y RedHat, por lo tanto se declaran las respectivas variables (nombre, grupo, usuario y directorio).
Para comprobar el funcionamiento de las configuraciones realizadas podemos utilizar la herramienta de línea de comando de Hiera. En el siguente caso comprobaremos el valor de la variable <code>apache_package_name</code> que nos devolverá el valor definido para el fact que consultamos.

```ruby
[root@master puppet]$ hiera -c hiera.yaml apache_package_name osfamily=RedHat
httpd
[root@master puppet]$ hiera -c hiera.yaml apache_package_name osfamily=Debian
apache2
```
<!-- more -->
Si por el contrario, nos arroja el valor <code>nil</code>, tenemos que repazar las configuraciones del archivo de hiera o chequear que donde estamos ejecutando el comando vemos el archivo <code>hiera.yaml</code>


### Configuración módulo Apache

Lo primero es crear el direcorio para el módulo Apache 

```ruby
[root@master puppet]$ mkdir /etc/puppet/modules/apache
[root@master puppet]$ mkdir /etc/puppet/modules/apache/{manifest,files,templates}
```
Toda la configuración del la clase Apache, fue dividida en subclases (install, config, service)
```ruby
[root@master manifests]$ ls
config.pp  init.pp  install.pp  service.pp
```
<li>Configuración init.pp</li>
```ruby
[root@master manifests]$ cat init.pp 
class apache ( 
	$apache_package_name = hiera('apache_package_name'),
	$apache_name_service = hiera('apache_name_service'),
  	$machineos = $operatingsystem,
  	$machinenode = $hostname,
  	$machineip = $ipaddress,
){
	class {'::apache::install':} -> 
	class {'::apache::config':} -> 
	class {'::apache::service':} ->
	Class ['apache']
}
```
Como se ve, luego de iniciar la clase apache se instancian una serie de variables (líneas nº3 y nº4) las cuales toman el valor que nosotros definimos en hiera. Las restantes variables <code>$machinenode</code>, <code>$machineip</code> y <code>$machineos</code> toman los valores de facter del nodo que utiliza la clase, valor que es utilizado en el template (erb) que crearemos mas adelante, finalmente tenemos la dependencia de las subclases de la clase apache que tenemos creadas.

<li>Configuración clase config.pp</li>
```ruby
[root@master manifests]$ cat config.pp 
class apache::config ( 
        $apache_owner_name = hiera('apache_owner_name'), #Valor de la variable Hiera
        $apache_name_group = hiera('apache_name_group'), #Valor de la variable Hiera
        $apache_dir_name = hiera('apache_dir_name'),
){

  file {'index.html':
    	ensure => file,
    	mode => 0640,
    	owner =>  $apache_owner_name, 
    	group => $apache_name_group,
	path => $apache_dir_name,
	#path => "/var/www/html/index.html",
    	content => template('apache/index.html.erb'),
	require => Class["apache::install"],
    	notify => Class["apache::service"],
    } 
	#notify {"El grupo es: $apache_name_group":} #Para comprobar el valor de la variables
	#notify {"El path es: $apache_dir_name":}
}
```
Aquí traemos los valores de las variables hiera <code>$apache_owner_name</code>, <code>$apache_name_group</code> y <code>$apache_dir_name</code> y las volcamos en las variables "locales" que luego utilizamos en la creación del archivo <code>index.html</code>. Como adicional, aquí vemos el contenido del archivo index.html es provisto por un temlate erb. <u>Nota</u> Los valores que tomamos de hiera para el config.pp lo podemos perfectamente instanciar en el archivo init.pp y llamarlos así:<code>"${apache::variablename}"</code> para ser utilizados en la misma subclase.
<li>Configuración clase service.pp</li>
```ruby
[root@master manifests]$ cat service.pp 
class apache::service {	
	service { $apache_name_service:
		ensure => "running", 
		hasstatus => true,
		hasrestart => true,
		enable => true, 
		name => "${apache::apache_name_service}", #Aqui se instanció la variable en el init.pp y luego solo instanción en la subclase service.pp 
		require => Class ["apache::config"],
    }
	#notice{"The value is: ${apache_name_service}": },
}
```
Todas las configuraciones de las clases las podemos ir validando en la medida que se crean con el comando 
```ruby
[root@master manifests]$ puppet config validate init.pp
```
<li>El template que carga el contenido dinámico <code>index.html.erb</code>, que tendrá el index.html de los nodos que se conectan</li>
```ruby
[root@master manifests]$ cat ../templates/index.html.erb 
<% $machinenode = scope.lookupvar('apache::machinenode') %>
<% $machineip = scope.lookupvar('apache::machineip') %>
<% $machineos = scope.lookupvar('apache::machineos') %>
<html>
<body>
<h1>Apache se encuentra corriendo</h1>
<p>Nombre de la maquina: <%= $machinenode %></p>
<p>Ip de la maquina: <%= $machineip %></p>
<p>Sistema operativo: <%= $machineos %></p>
</body>
</html>
```
Aquí cargamos el valor de las variables <code>apache::machinenode</code>, <code>apache::machineip</code> y <code>apache::machineos</code> que definimos en el <code>init.pp</code>.
<li>Finalmente, en nuestro archivo node.pp definimos que los nodos utilizaran la clase apache

```ruby
node 'node2.example.com'  { #servidor Debian
        include apache
}node 'node1.example.com'  { #servidor Centos
        include apache
}
```
Con todo lo anterior, ya tenemos realizada la integración del Puppet y Hiera, ahora podemos ir al cliente y conectarlo al Puppet

<li>Conectamos el nodo Debian</i>
```ruby
root@node2:~$ puppet agent --test --server=master.example.com 
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/hosts_managed.rb
Info: Loading facts in /var/lib/puppet/lib/facter/puppet_vardir.rb
Info: Loading facts in /var/lib/puppet/lib/facter/root_home.rb
Info: Loading facts in /var/lib/puppet/lib/facter/pe_version.rb
Info: Loading facts in /var/lib/puppet/lib/facter/facter_dot_d.rb
Info: Caching catalog for node2.example.com
Info: Applying configuration version '1394205585'
Notice: /Stage[main]/Apache::Install/Package[undef]/ensure: ensure changed 'purged' to 'present'
Notice: /Stage[main]/Apache::Config/File[index.html]/ensure: defined content as '{md5}8aa97e548602570e4cad25e10a4686cd'
Info: /Stage[main]/Apache::Config/File[index.html]: Scheduling refresh of Class[Apache::Service]
Info: Class[Apache::Service]: Scheduling refresh of Service[undef]
Notice: /Stage[main]/Apache::Service/Service[undef]: Triggered 'refresh' from 1 events
Notice: Finished catalog run in 3.92 seconds
```
<li>Conectamos el nodo RedHat</i>
```ruby
[root@node1 ~]$ puppet agent --test --server=puppet.example.com --verbose 
Info: Retrieving plugin
Info: Loading facts in /var/lib/puppet/lib/facter/root_home.rb
Info: Loading facts in /var/lib/puppet/lib/facter/puppet_vardir.rb
Info: Loading facts in /var/lib/puppet/lib/facter/pe_version.rb
Info: Loading facts in /var/lib/puppet/lib/facter/hosts_managed.rb
Info: Loading facts in /var/lib/puppet/lib/facter/facter_dot_d.rb
Info: Caching catalog for node1.example.com
Info: Applying configuration version '1394205585'
Notice: /Stage[main]/Apache::Install/Package[undef]/ensure: created
Notice: /File[index.html]/ensure: defined content as '{md5}d10727efb6c240c7b17e1a44470af78b'
Info: /File[index.html]: Scheduling refresh of Class[Apache::Service]
Info: Class[Apache::Service]: Scheduling refresh of Service[undef]
Notice: /Stage[main]/Apache::Service/Service[undef]/ensure: ensure changed 'stopped' to 'running'
Info: /Stage[main]/Apache::Service/Service[undef]: Unscheduling refresh on Service[undef]
Notice: Finished catalog run in 12.69 seconds
```
Como podemos ver en el debug del proceso, en ambos casos nos infoma que se instaló el paquete <code>apache</code>, se creó el archivo <code>index.html</code> el cual tiene el contenido dinámico definido en nuestro <code>index.html.erb</code> y ahora si nos conectamos por navegador veremos que el servicio Apache se encuentra operativo y como <code>index.html</code> cargará los valores propios del servidor donde se instaló la clase Apache.

<li>Acceso servidor Debian</li>
```ruby
Miguel:~$ curl http://node2.example.com
<html>
<body>
<h1>Apache se encuentra corriendo</h1>
<p>Nombre de la maquina: node2</p>
<p>Ip de la maquina: 10.10.0.10</p>
<p>Sistema operativo: Ubuntu</p>
</body>
</html>
```
<li>Acceso servidor RedHat</li>
```ruby
Miguel:~$ curl http://node1.example.com
<html>
<body>
<h1>Apache se encuentra corriendo</h1>
<p>Nombre de la maquina: node1</p>
<p>Ip de la maquina: 10.10.0.11</p>
<p>Sistema operativo: CentOS</p>
</body>
</html>
```
Como vimos el uso de Hiera es bastante interesante ya que para el caso del ejemplo fue capaz y de forma automática hacer el proceso de instalación y configuración del servicio apache para distintos sistemas operativos. En el caso de no utilizar Hiera, la configuración del módulo se torna un poco más compleja ya que nosotros tendríamos que definir mediante estructuras condicionales <code>if</code>, <code>case</code>, etc. que paquetes, nombres, grupos, etc. son los secesarios para uno u otro S.O. 


<u>Referencias</u>


* [http://docs.puppetlabs.com/hiera/1/index.html](http://docs.puppetlabs.com/hiera/1/index.html)
* [http://docs.puppetlabs.com/hiera/1/complete_example.html](http://docs.puppetlabs.com/hiera/1/complete_example.html)
* [http://www.craigdunn.org/2011/10/puppet-configuration-variables-and-hiera/](http://www.craigdunn.org/2011/10/puppet-configuration-variables-and-hiera/)


