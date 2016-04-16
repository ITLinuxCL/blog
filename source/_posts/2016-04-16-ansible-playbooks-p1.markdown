---
layout: post
title: "Ansible Playbooks: Tutorial p1"
date: 2016-04-16 13:32
comments: true
categories: linux, ansible, devops, automatation
author: Patricio Bruna
---
{%img center /images/ansible_playbooks.png %}
Como [revisamos en un artículo anterior, Ansible](http://blog.itlinux.cl/blog/2016/04/09/automatizacion-y-el-desafio-de-la-nube/) se ha convertido en nuestra herramienta favorita. El **Poder** que entrega la **Automatización** es buena adicción, que lleva a contar con la seguridad de que tu plataforma siempre funcionará como corresponde.

En esta primera parte de dos artículos aprenderemos más sobre que son los **Playbooks** y su importancia al momento de automatizar el control de nuestras plataformas. Al final del segundo artículo ya tendremos los conocimientos necesarios para como ejemplo: Instalar un Servidor LAMP de forma autónoma.

Primero revisemos algunos términos importantes de Ansible:

* **Máquina de Administración**, es la máquina donde tenemos instalado Ansible, puede ser nuestro laptop, y desde donde se ejecutaran las tareas. Recuerda que con ansible **no necesitas** servidores o agentes.

* **Inventory**, es el archivo donde registramos los servidores sobre los cuales ejecutaremos las tareas,

* **Playbook**, un archivo donde listamos las tareas que de deben ejecutar, es como una receta de concina. Se escribe en formato [YAML](https://es.wikipedia.org/wiki/YAML)

* **Task**, un bloque dentro del `Playbook` que define una acción específica a realizar, pj: `instalar un paquete`.

* **Module**, son como `Plugins` que permiten realizar tareas de forma más fácil. Muchos vienen, como `yum` para instalar software, y también nosotros podemos crear los nuestros.

* **Role**, una forma de ordenar los diferentes `Playbooks`.

* **Play**, se refiere a la ejecución de un `Playbook`.

* **Facts**, variables dentro de Ansible que contienen información sobre los servidores. Ej: Sistema Operativo, Cantidad de Ram, Direcciones IP, etc.

* **Handlers**, pequeño código que se usa cuando algo cambia. Por ej: si actualizas el archivo de configuración de Apache, un `Handler` re-iniciará el servicio `httpd`.

### Tasks
Una `Task` (Tarea) define un sólo proceso que debe ser ejecutado durante el procedimiento. Generalmente se basa en el uso de algún módulo o la ejecución de un comando de `shell` (que en realidad es un módulo creado para ejecutar comandos). Este es un ejemplo de como se define una `Task`:

```yaml
- name: Instalar nmap
  yum: name=nmap state=latest
```

La parte `name` es opcional, pero nosotros recomendamos su uso para describir que es lo que hará la tarea. `yum` es un módulo que viene incluído en Ansible y se encarga de todo lo que tiene que ver con la gestión de paquetes para distribuciones basadas den [Red Hat](http://www.redhat.com). Esta `Task` le indica a Ansihle que el paquete `nmap` debe estar instalado en su última version, lo cual hará que `yum` lo instale si no está instalado, y que lo actualice si existe una versión más nueva.

### Playbook
Los `Playbooks` son el punto de inicio al trabajar con Ansible. Ellos contienen información de en que máquinas se debe ejecutar el provisionamiento, como también las directivas y pasos que se deben realizar y el orden de su ejecución. A continuación un ejemplo de `Playbook` que primero crea un directorio y luego descarga un archivo en el:

```yaml
---
- hosts: all
  tasks:
     - name: Crea directorio descargas
       file: path=/tmp/descargas state=directory mode=0755

     - name: Descarja Logo
       get_url: url=http://blog.itlinux.cl/images/logo_mini.png dest=/tmp/descargas/logo_mini.png
```

Como indicamos anteriormente, los `Playbooks` están escritos usando el formato `YAML`, el cual requiere que la **identación sea perfecta** para poder procesar el documento, por lo cual recomendamos que trabajes con un editor de texto que entienda el formato `YAML`, como puede ser [ATOM](https://atom.io).

Algunas cosas importantes del ejemplo:

* `hotst: all`, indica este `Playbook` se ejecutará en todos los hosts definidos en el `Inventory`
* `tasks`, es el conjunto de `Tareas` a ejecutar.


### Ejecutando el Playbook
Una vez que tengas listo tu `Playbook` es momento de hacerlo funcionar, esto se realiza con el comando `ansible-playbook`, por ejemplo:

```
$ ansible-playbook -i hosts download_itlinux_logo.yml
```


### Palabras al cierre
Esperamos que este artículo vaya aumentando tus ganas de **Automatizar** cuanto puedas en tu vida y veas lo simple que es hacerlo con Ansible.

Por ahora recomendamos que mientras esperan la segunda parte, jueguen con algunos [ejemplos de Playbooks](https://github.com/ansible/ansible-examples).












