---
layout: post
title: "Shellshock: Grave vulnerabilidad en Bash afecta a Linux y otros *nixes"
date: 2014-09-25 21:44
comments: true
categories: [linux,seguridad,bash]
keywords: linux,seguridad,bash
description: Bug en Bash crea un gran agujero de seguridad que afecta a todos los *nix
author: Patricio Bruna
---
{% img right /images/shellshock.jpg 325 %}
El 24 de Septiembre de 2014 se dio a conocer una importante vulnerabilidad en Bash. Esta vulnerabilidad, llamada "Shellshock" y con los identificadores [CVE-2014-6271](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-6271) y [CVE-2014-7169](http://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2014-7169), permite ejecución remota de código.

Lo que primero comenzó como una vulnerabilidad de ejecución local, se ha extendido y ahora existen formas de ejecutarla de forma remota. Recomendamos a todos que actualicen sus sistemas tan pronto sea posible.

Existe una forma fácil de comprobar si tus sistemas son vulnerables. En una consola escribe lo siguiente:

```bash
env x='() { :;}; echo vulnerable' bash -c "echo es una prueba"
```

Si tu sistema es vulnerable, la salida será:

```bash
vulnerable
 this is a test
```

A continuación dejamos un listado de enlaces donde podrás encontrar información específica a cada distribución:

* Ubuntu Linux: [http://www.ubuntu.com/usn/usn-2362-1/](http://www.ubuntu.com/usn/usn-2362-1/)
* Debian Linux: [https://www.debian.org/security/2014/dsa-3032](https://www.debian.org/security/2014/dsa-3032)
* RedHat Linux: [https://access.redhat.com/articles/1200223](https://access.redhat.com/articles/1200223)
* CentOS Linux: [http://centosnow.blogspot.com/2014/09/critical-bash-updates-for-centos-5.html](http://centosnow.blogspot.com/2014/09/critical-bash-updates-for-centos-5.html)
* SUSE Linux: [http://support.novell.com/security/cve/CVE-2014-6271.html](http://support.novell.com/security/cve/CVE-2014-6271.html)

Si quieres saber como opera este bug te recomendamos el siguiente artículo de [Ars Technica](http://arstechnica.com/security/2014/09/bug-in-bash-shell-creates-big-security-hole-on-anything-with-nix-in-it/)