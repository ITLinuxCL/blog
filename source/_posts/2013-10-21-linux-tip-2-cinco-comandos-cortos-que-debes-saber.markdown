---
layout: post
title: "Linux Tip 2: 5 comandos cortos que debes saber"
date: 2013-10-21 17:33
comments: true
categories: [linux, tips, linux_tips]
description: 5 Comandos de consolas cortos pero bastante útiles para todos los Sysadmins
author: Patricio Bruna
---
Para los administradores de Linux/Unix poder dominar la consola y sus comandos pasa de ser una habilidad a un requisito, pero si eres nuevo no debes preocuparte, como todo en la vida mientras más se práctica una habilidad mejor se domina ésta.

Hoy comenzamos la práctica con estos 5 comandos fáciles de usar.

## 1. Bajar un archivo de la Web y mirar el progreso

Si sabes la URL de un archivo que quieres bajar, usa el comando ``curl`` con la opción **-O**:

```bash
curl -O http://files2.zimbra.com/downloads/8.0.5_GA/zcs-8.0.5_GA_5839.RHEL6_64.20130910123908.tgz
```

Asegúrate de usar la URL completa y de que usas la 'O' mayúscula.

## 2. Listar un directorio por fecha de modificación
Esto es algo que realizo siempre con los navegadores de archivos: Ordenar los archivos por fecha de modificación. De esta forma puedo encontrar fácilmente los últimos en que he trabajado.

En el caso de la consola nos interesa que estos queden al final, no al principio:

```bash
ls -thor
```

Este comando además de ser muy útil, es muy fácil de recordar: sólo llama al Dios del Trueno.

## 3. Matar procesos usando comodines
¿Alguna vez has necesitado matar un montón de procesos con un sólo comando? El comando ``kill`` estándar no soporta comodines, pero el comando ``pkill`` si lo hace.

Por ejemplo, si queremos matar todos los procesos "TengounNombreMuyLargoparaSerunProcesotanMolesto" de una vez, puedes usar lo siguiente:

```bash
pkill Tengoun*
```

Recuerda que los comodines no perdonan, y que ``pkill`` "matará" sin pedir confirmación.

## 4. Ejecuta el último comando como root
¿No odias cuando has ejecutado un comando extenso y te das cuenta que requiere permisos de administrador? Este truco permite ejecutar el comando con los permisos necesarios sin tener que escribirlo nuevamente:

```bash
sudo !!
```

## 5. Obtén el último comando ejecutado, sin ejecutarlo
¿No recuerdas la sintaxis que usaste la última vez que ejecutaste un comando? Con este tip puedes recuperarla sin tener que ejecutar el comando.

Por ejemplo, para obtener el último comando que usó el prefijo "sudo":

```bash
!sudo:p
```

El resultado será el último comando ejecutado, pero no lo ejecutará. Para buscar otros comandos sólo reemplaza la palabra _sudo_


Como siempre, esperamos tus comentarios e ideas.
