---
layout: post
title: 'Introducción a Ansible'
date: 2016-04-09 14:41
comments: true
categories: linux, ansible, devops, automatation
author: Patricio Bruna
---
{%img center /images/ansible2014_logo_black_tagline.png %}
[Ansible](https://www.ansible.com) es una herramienta que permite **Automatizar**
todas las tediosas y repetitivas tareas con que nos encontramos los administradores
de sistema, como por ejemplo:

* Configurar el computador para el _nuevo_
* Instalar, configurar e ingresar los nuevos servidores al dominio
* Administrar los usuarios de la VPN, y borrar los que ya no existen
* Preparar las nuevas `38 VMs` para el proyecto **E-Commerce**, las que deben estar
creadas para mañana en `Amazon AWS` y `RackSpace`. Además de las de desarrollo en
la plataforma `Proxmox VE`

Ufff, como que nos fuimos lejos con esa última. Pero **sí**, Ansible permite automatizar
todo esto.

### Como funciona Ansible
Una de las gracias de Ansible frente a la competencia es que no tienes que instalar ni
`Agentes` ni `Servidores`. Sólo instalar `ansible` en tu máquina y estás listo para comenzar.

Ansible se conecta a los equipos que quieras configurar utilizando `SSH` y le envía las instrucciones
que quieres ejecutar y las configuraciones que se deben aplicar.

{%img center /images/ansible-workflow.png %}

Todo esto en **paralelo**, así que no importa si tienes `5` o `2 mil` equipos que configurar.

### Instalación de Ansible
La forma más fácil es utilizar el administrador de paquetes de tu sistema operativo.

```bash
 # RedHat y Clones
 $ yum install ansible

 # Debian y Clones
 $ apt-get install ansible

 # En Mac Os X con Homebrew
 $ brew install ansible
```

### Trabajando con Ansible

#### El Inventario
Ansible trabaja con un **Inventario** de tu plataforma o servidores. Este `Inventario` es un
archivo en el cual agrupas y listas los servidores según más te acomode:

```
 # Archivo hosts.cfg de inventario
 [dbs]
 db1.example.com
 db2.example.com
 db3.example.com
```

El archivo `hosts.cfg` contiene un grupo de servidores: `dbs` el cual tiene 3 servidores.
Este es un `Inventario` sencillo, el de tu empresa puede ser mucho más complejo y también es
posible [hacer que se creen sólos](http://docs.ansible.com/ansible/intro_dynamic_inventory.html).

#### Ejecutando comandos sencillos
El primer comando que vamos a usar, nos va a permitir comprobar que tenemos acceso a los clientes:

```bash
$ ansible -i hosts.cfg -m ping all
```

Otro ejemplo, para revisar la memoria disponibles en todos los servidores:

```bash
$ ansible -i hosts.cfg all -m shell -a 'free -m' -U root
```

Como vez en los dos ejemplos indicamos cual es el archivo **Inventario** que vamos a usar: `-i hosts.cfg`.
Y en el último indicamos cual es el usuario con el que nos conectamos: `-U root`. Recuerda que
la conexión se realiza con `SSH`.

Otro ejemplo que puede ser útil: **Aplicar la actualización de Hora** a todos los servidores, vamos
a suponer que son del tipo Red Hat:

```bash
$ ansible -i hosts.cfg all -m yum -a "name=tzdata state=latest"
```

Del comando es importante notar lo siguiente:

* `-i hosts.cfg all`, indica que se debe usar el inventario `hosts.cfg` y que
se ejecutará en todos los servidores: `all`

* `-m yum`, indica que módulo de ansible usaremos. Anteriormente ejecutamos comandos
usando el módulo `shell`, ahora que queremos instalar un paquete, usamo `yum`.

* `-a "name=tzdata state=latest"`, son los parámetros que recibe el módulo. En este caso
el nombre del paquete: `name=tzdata`, y el estado que debe tener: `state=latest`, en
este caso que esté instalada la última versión.

En la [documentación de Ansible](http://docs.ansible.com/ansible/modules_by_category.html) puedes encontrar el listado completo de módules disponibles, y como se usan.

### Donde vamos ahora
En un próximo artículo hablaremos sobre los [Playbooks](http://docs.ansible.com/ansible/playbooks.html) que son las recetas de Ansible
para mantener toda tu plataforma en Orden.
