---
layout: post
title: "Configurando interfaz de red con Puppet"
date: 2013-12-10 16:08
comments: true
categories: puppet, devops
author: Miguel Coa.
---
Es muy común en Linux que de forma constante tenemos que estar reconfigurando nuestras interfaces de red y ajustando valores como : autoneg, mtu, mascara de red, etc. Configuraciones que si bien son simples de aplicar pueden implicar una inversión en tiempo elevada dependiendo de la cantidad de servidores que tengamos que reconfigurar. Una solución para esto, es tener nuestra infraestructura administrada con [Puppet](http://puppetlabs.com/) esto nos permitirá de forma simple aplicar los cambios en un archivo para los <i>"N"</i> servidores que necesitemos actualizar. 
Por ejemplo y para este caso vamos a trabajar con un módulo de configuración de red que nos permite administrar nuestro servidor de forma rápida y eficaz.  

1- Instalamos el módulo
```ruby
puppet module install razorsedge/network
```
2- Donde tengamos declarado nuestro nodo intanciamos la configuración de red, por ejemplo:

```ruby
node 'base-node' {
        class { 'network::global':
                gateway => '10.0.0.1',
        }
}

node 'node1.example.com' inherits base-node {
        network::if::static { 'eth1':
                ensure       => 'up',
                ipaddress    => '10.0.0.134',
                netmask      => '255.255.255.0',
                macaddress   => '00:0C:29:47:B2:59',
                mtu          => '9000',
        }
}
```
Como se aprecia en la declaración del nodo <i>base-node</i> definimos la clase network::global configurando el gateway, luego en la declaración del nodo1 heredamos las configuraciones del nodo base-node y finalmente definimos la configuración de red para el nodo1. Una vez aplicada la configuración  en el servidor "node1" con el comando mágico (puppet aget --test) veremos nuestra configuración de la siguiente manera.

```ruby
[root@node1 ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth1
###
### File managed by Puppet
###
DEVICE=eth1
BOOTPROTO=none
HWADDR=00:0C:29:47:B2:59
ONBOOT=yes
HOTPLUG=yes
TYPE=Ethernet
IPADDR=10.0.0.134
NETMASK=255.255.255.0
MTU=9000
PEERDNS=no
NM_CONTROLLED=no

[root@node1 ~]# cat /etc/sysconfig/network
###
### File managed by Puppet
###
NETWORKING=yes
NETWORKING_IPV6=no
HOSTNAME=node1.example.com
GATEWAY=10.0.0.1

```
Las configuraciones que podemos realizar con Puppet y el módulo que instalamos son variadas (bonding, dhcp, etc) para lo cual puedes ver la documentación con mayor detalle [aquí](https://forge.puppetlabs.com/razorsedge/network). 


