---
layout: post
title: "Usar SSH Agent Forwarding para conectarse a servidores remotos"
date: 2014-01-16 18:21
comments: true
keywords: ssh, forwarding, agent
categories: [linux, tips, ssh, devops]
description: Como usar SSH Agent Forwarding para reenviar tus llaves a servidores remotos.
author: Patricio Bruna
---

Asumamos que tenemos el siguiente escenario:

1. Desde nuestra oficina (por la IP de la oficina) podemos conectarnos a servidores de nuestros clientes solo utilizando nuestras llaves SSH.
2. En los servidores de la oficina tenemos creados nuestros usuario, pero no queremos dejar repartidas nuestras llaves SSH privadas.
3. Estamos en casa y tenemos que conectarnos donde un cliente, por lo que debemos saltar a la oficina y desde ahí al equipo del cliente, pero recuerden que sólo podemos validarnos con llave SSH.

Es en este caso que **SSH Agent** se luce.

### ¿Qué es SSH Agent?
Usando la [definición de Wikipedia](http://en.wikipedia.org/wiki/Ssh-agent): es un programa que se usa junto a OpenSSH para almacenar y comunicar de forma segura las llaves privadas.

La parte del _Forwarding_ quiere decir que podemos usar un servidor de paso, como el de la oficina, para que **reenvie** nuestras llaves a otros servidores.

### ¿Cómo lo usamos?
```bash
# Agregamos nuestras llaves a SSH Agent
pbruna@itlinux-casa ~ $ ssh-add
Identity added: /home/pbruna/.ssh/id_rsa (/home/pbruna/.ssh/id_rsa)
Identity added: /home/pbruna/.ssh/id_dsa (/home/pbruna/.ssh/id_dsa)
pbruna@itlinux-casa ~ $

# Nos conectamos al servidor de paso usando el agente (-A)
pbruna@itlinux-casa ~ $ ssh -A pbruna@staging.itlinux.cl
Last login: Thu Jan 16 18:18:56 2014 from 200.124.47.206
pbruna@staging.itlinux.cl:~ #


# Nos conectamos al cliente
pbruna@staging.itlinux.cl:~ # ssh itlinux@customer.example.com
Last login: Thu Jan 16 18:19:03 2014 from 126.67.32.114
itlinux@customer.example.com:~ #
```

Como ven, al conectarse al servidor del cliente **no se solicitó la contraseña** porque el Agente SSH reenvío la llave privada que tenemos en nuestro equipo y nos autentificamos usando las llaves.


### Más información
Te recomendamos los siguientes enlaces si quieres tener más información de como funciona **SSH Agent Forwarding** y recuerda que cualquier consulta nos puedes dejar un comentario:

* [en.wikipedia.org/wiki/Ssh-agent](http://en.wikipedia.org/wiki/Ssh-agent)
* [help.github.com/articles/using-ssh-agent-forwarding](https://help.github.com/articles/using-ssh-agent-forwarding)


