---
layout: post
title: "Migración de base de conocimiento"
date: 2015-07-06 16:25
comments: true
categories: kb, sphinx-doc, migración
---

Decidimos actualizar el lugar donde almacenamos nuestra documentación. Por lo cual nos cambiaremos a [Read The Docs](https://readthedocs.org/) y además se hace uso de [Sphinx-doc](http://sphinx-doc.org/), el cual es reconocido en el mundo del desarrollo de software por lo ordenado y sencillez con el que se genera la documentación. Existen varios proyectos que hacen uso de este software para documentar el desarrollo y que realmente, es digno de seguir. 

#Pasos generales:

1. Crear cuenta en [Read The Docs](https://readthedocs.org/accounts/signup/)
2. Instalar sphinx-doc
3. Crear proyecto base
4. Limpiar proyecto
5. Crear página de ejemplo
6. Subir proyecto a GitHub
7. Importar repositorio a RTD
8. Repetir punto 6 y 7


##1. Crear cuenta en Read The Docs

Este paso es sencillo, simplemente te diriges a : [https://readthedocs.org/accounts/signup/
](https://readthedocs.org/accounts/signup). 

Llenas los datos y ya tienes la cuenta creada. Nada de otro mundo.

##2. Instalar sphinx-doc

La instalación de la herramienta es necesaria para generar el proyecto base. Pero aquí hay dos cosas que hay que entender.

1. Si utilizarás el servicio Read The Docs, no será necesaria la estructura y archivos extras que crea el generador.
2. Si alojarás la documentación en tu servidor local, es necesaria toda la estructura y archivos extras generados.

Nosotros queremos simplificar las cosas, elegimos el punto 1.

###¿Cómo se instala?

- Forma recomendada: `pip install sphinx`
- CentOS : `yum install python-sphinx-doc`
- Debian/Ubuntu: `apt-get install sphinx-doc`
- Mac OS X: `pip install sphinx`

##3. Crear proyecto base

Tenemos la herramienta instalada, nos queda generar el proyecto.

1. Crear directorio de trabajo: `mkdir -p mi_documentacion/docs`
2. Generar proyecto: `sphinx-quickstart`

Ya que el resultado de este comando es algo largo, omitiré las opciones que indiqué con *No*

```bash
> Root path for the documentation [.]: docs
> Project name: Prueba de documentacion
> Author name(s): Nicolas Fuenzalida
> Project version: 1
> Project release [1]:1
> Project language [en]: es
> ifconfig: conditional inclusion of content based on config values (y/n) [n]: y
> Create Makefile? (y/n) [y]: y
```

Recuerda, algunas opciones de construcción se omitieron porque indiqué que no se aplique.

El proyecto quedó similar a esto:

```bash
➜  docs git:(master) ✗ ls
Makefile   _build     _static    _templates conf.py    index.rst
```

##4. Limpiar proyecto
Ya que se subirá el proyecto a RTD, el servicio generará de forma automática a partir del repositorio GitHub los html.

```bash
rm -rf _build _static _templates
```
Estos directorio no nos serán útiles, ya que sólo crearemos archivos y asociaremos al index.rst.

##5. Crear página de ejemplo

Es tan sencillo como hacer: `vim saludo.rst`
e incrustar en el documento sintaxis de [Restructured Text (reST)](http://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html)
Un ejemplo: 

```rest
*****
ZBox
*****

`ZBox <www.zbox.cl>`_
```

Guardar el contenido y la primera página está creada.

##6. Subir proyecto a GitHub

Aquí es sencillo, revisar cambios, hacer commit y subirlos a tu repositorio:

- `git add .`
- `git commit -m "Agregada primer documento`
- `git push`

##7. Importar repositorio a RTD

Se mostrará los pasos mas generales para hacer esto, ya que luego de tus necesidades harás los cambios correspondientes.

![Impo]()