---
layout: post
title: "Monitoreos en Nagios vía SNMP"
date: 2014-10-15 11:44
comments: true
categories: nagios,monitoreo,sysadmin
author: Miguel Coa.
---
{% img right /assets/images/logo_nagios.png %}
Al utilizar Nagios para el monitoreo de nuestros servidores, los plugins que vienen por defecto trabajan en base al chequeo de un recurso mediante umbrales de valores para alertar. 

Por ejemplo, si vamos a monitorear la memoria ram, no nos interesa el valor máximo que esta tiene, lo que si nos interesa es que nos alerte cuando estemos ocupando cerca del 90% ó 100% de la misma (umbrales). Pero hay casos en que igual necesitamos los valores de la memoria ram Ej: ram libre, ram usada, ram total. Para el caso podemos decirle a Nagios que nos muestre la información de un recurso mediante protocolo <code>SNMP</code> que puede ser trabajado con el plugins <code>check_snmp</code>. Para realizar esto, tenemos que realizar lo siguiente:

####Configurar SNMP en el servidor a monitorear.

<li>Instalamos los paquetes SNMP en el hosts </code>
```ruby
[root@node1]$ yum install net-snmp -y
[root@node1]$ vim /etc/snmp/snmpd.conf
```
<li>Configuramos la comunidad en nuestro <code>snmpd.conf</code></li>
```ruby
rocommunity public 10.10.0.0/24
```
La ip corresponde a la red de los equipos, podemos ser mas restrictivos y colocar solo la dirección del servidor Nagios.
<li>Finalmente subimos el servicio</li>
```ruby
[root@node1]$ service snmpd start && chkconfig snmpd on
```

####Configurando Nagios Server
En este punto nos aseguramos de tener todo operativo respecto al servidor Nagios, los chequeos serán realizados desde el servidor Nagios hacia los Nagios client, con el comando <code>check_snmp</code>.
<li>Definiendo nuevos comandos</li>
Los comandos a utilizar no vienen por defecto en la plantilla <code>commands.cfg</code> por lo cual vamos a proceder a crearlos, adicionando los siguientes valores: <i>memoria ram total</i>, <i>memoria ram libre</i>, <i>memoria ram usada</i>
```ruby
define command{
        command_name    snmp_RamSize
        command_line    $USER1$/check_snmp -o .1.3.6.1.4.1.2021.4.5.0 -H $HOSTADDRESS$ $ARG1$
}

define command{
        command_name    snmp_RamFree
        command_line    $USER1$/check_snmp -o .1.3.6.1.4.1.2021.4.11.0 -H $HOSTADDRESS$ $ARG1$
}

define command{
        command_name    snmp_RamUsed
        command_line    $USER1$/check_snmp -o .1.3.6.1.4.1.2021.4.6.0 -H $HOSTADDRESS$ $ARG1$
}
```
De lo anterior, la númeración <code>.1.3.6.1.4.1.2021.4.6.0</code> representa el <code>OIDs</code> canal identificador de lo que vamos a monitorear. Dependiendo de lo que se desea monitorear es el <code>OIDs</code> respectivo, la lista de estos identificadores es larga y la puedes ver [acá](http://www.debianadmin.com/linux-snmp-oids-for-cpumemory-and-disk-statistics.html). Por ejemplo para la memoria ram/swap tenemos los siguientes registros.
```ruby
Total Swap Size: .1.3.6.1.4.1.2021.4.3.0
Available Swap Space: .1.3.6.1.4.1.2021.4.4.0
Total RAM in machine: .1.3.6.1.4.1.2021.4.5.0
Total RAM used: .1.3.6.1.4.1.2021.4.6.0
Total RAM Free: .1.3.6.1.4.1.2021.4.11.0
Total RAM Shared: .1.3.6.1.4.1.2021.4.13.0
Total RAM Buffered: .1.3.6.1.4.1.2021.4.14.0
Total Cached Memory: .1.3.6.1.4.1.2021.4.15.0
```
<li> Ahora, vamos a configurar nuestros chequeos dentro del archivo de cada hosts</li>
```ruby
define host {
	contact_groups                 vagrant-vm
	host_name                      node1.example.com
	hostgroups                     vagrant-vm
	alias                          node1
	address                        10.10.0.102
	use                            linux-server
}

define service{
	contact_groups                 vagrant-vm
        use                     generic-service
        host_name               node1.example.com
        service_description     RAM Size
        check_command           snmp_RamSize!-C public
}
 
define service{
	contact_groups                 vagrant-vm
        use                     generic-service
        host_name               node1.example.com
        service_description     RAM Free
        check_command           snmp_RamFree!-C public
}

define service{
        contact_groups                 vagrant-vm
        use                     generic-service
        host_name               node1.example.com
        service_description     RAM Used
        check_command           snmp_RamUsed!-C public
}
```
Acá, por cada chequeo que realizamos utilizamos nuestro comando respectivo (que monitorea vía snmp) y la comunidad del nuestro nodo. 

<li>Finalmente reiniciamos nuestro servidor nagios</li>
```ruby
[root@nagios servers]$ /etc/init.d/nagios restart
```
Nos conectamos al webadmin del servidor Nagios para ver el estado de los servicios

{% img /assets/images/nagios-snmp.png %}


Referencias:

* [Configurando el servicio SNMP en CentOS y RHEL](http://www.unixmantra.com/2014/07/install-snmp-service-on-rhel-or-centos.html)
* [Como se integra Nagios con SNMP](http://www.thenoccave.com/2012/10/29/nagios-snmp-checks/)
* [Monitoreando con Nagios y SNMP](http://wiki.monitoring-fr.org/nagios/mise-en-place-complete-nagios-sur-rhel-5.4/supervision-nagios-snmp)


