---
layout: post
title: "Ejemplos de uso del comando find"
date: 2014-02-03 16:16
comments: true
categories: 
author: Miguel Coa
---
A la hora de nombrar comando útiles en linux uno de los primero que se vendrá a la mente es el comando <i>find</i> el cual nos permite buscar archivos/directorios bajo una serie de criterios que nosotros le argumentemos, esto con la finalidad de que la busqueda sea lo mas exacta posible. Como las funcionalidades operacionales del comando son muchísimas nos remitiremos a un uso bástante simple en este post.

. Encontrar todos los archivos que terminen en .php

```ruby
Miguel@MacBook:$ find . -type f -name *php
./dir1/app.php
./dir1/calm.php
./dir1/test1.php
./dir2/foo.php
```
. Encontrar todos los archivos que no terminen con .php

```ruby
Miguel@MacBook:$ find . -type f ! -name *php
./dir3/caz.html
```
Esta busqueda también se puede realizar con el comando

```ruby
Miguel@MacBook:$ find . -type f -not -name *php
./dir3/caz.html
```
<!-- more -->

. Es posible generar busquedas utilizando criterios multiples, en este caso vamos a buscar todos los arhivos con nombre "ca*" pero que no terminen con .php

```ruby
Miguel@MacBook:$ find . -type f -name "ca*" ! -name *php
./dir3/caz.html
```
también, podemos utilizar el operador "AND/OR" cuando utilizamos el comando find y con ello solo los archivos/directorios que cumplen con los criterios serán listados. 

```ruby
Miguel@MacBook:$ find . -name '*.php' -o -name '*.txt' -o -name '*.html'
./dir1/app.php
./dir1/calm.php
./dir1/sun.txt
./dir1/test1.php
./dir2/foo.php
./dir3/caz.html
./dir3/caz.php
```

. Busqueda en multiples rutas de forma conjunta

```ruby
Miguel@MacBook:$ find dir1/ dir2/ -name *php
dir1//app.php
dir1//calm.php
dir1//test1.php
dir2//foo.php
```

. Encontrando archivos ocultos

```ruby
Miguel@MacBook:$ find . -name ".*"
./dir3/.hid.php
```

. Encontrando archivos con algún criterio de permisos. En este caso el criterio de permiso será 775

```ruby
Miguel@MacBook:$ find . -type f -perm 0775
./dir1/kol.txt
./dir3/.hid.php
./dir3/hup.txt
```
Y si comprobamos los permisos, veremos que cumplen el criterio
```ruby
Miguel@MacBook:$ ll ./dir1/kol.txt
-rwxrwxr-x  1 Miguel  staff     0B Feb  3 16:52 ./dir1/kol.txt
Miguel@MacBook:$ ll ./dir3/.hid.php
-rwxrwxr-x  1 Miguel  staff     0B Feb  3 16:49 ./dir3/.hid.php
Miguel@MacBook:$ ll ./dir3/hup.txt
-rwxrwxr-x  1 Miguel  staff     0B Feb  3 16:51 ./dir3/hup.txt
```

. Buscar por un usuario en particular
```ruby
Miguel@MacBook:$ find dir1/ -user Miguel
dir1/
dir1//app.php
dir1//calm.php
dir1//kol.txt
dir1//sun.txt
dir1//test1.php
dir1//tool
```
Además del nombre podemos utilizar algún criterio adicional como el nombre, permiso, tipo, etc
```ruby
Miguel@MacBook:$ find dir1/ -user Miguel -type f -name *txt
dir1//kol.txt
dir1//sun.txt
```

. Buscar por un grupo propietario en particular

```ruby
Miguel@MacBook:$ find /var/ftp -group admin -type d
/var/ftp/site
```

. Encontrar todos los archivos modificados en la última hora

```ruby
[root@mail-old ~]$ find /var/log/ -mmin -60
/var/log/clamav/clamd.log
/var/log/secure
/var/log/wtmp
/var/log/lastlog
/var/log/maillog
/var/log/cron
/var/log/sa/sa03
```

. Encontrar todos los archivos de hasta 50 MG de tamaño
```ruby
[root@mail-old ~]$ find /var/tmp/ -size 50M
/tmp/nohup
```

. Realizar una búsqueda y ejecutar un comando sobre ella
```ruby
Miguel@MacBook:$ find . -type f -name *txt -exec ls -l {} \;
-rwxrwxr-x  1 Miguel  staff  0 Feb  3 16:52 ./dir1/kol.txt
-rw-r--r--  1 Miguel  staff  21 Feb  3 17:21 ./dir1/sun.txt
-rwxrwxr-x  1 Miguel  staff  0 Feb  3 16:51 ./dir3/hup.txt 
```

Como fue posible ver el comando <i>find</i> nos pemirte realizar busquedas de forma bastante simple e incluir una serie de criterios que nos ayudan a que esta sea lo más eficaz posible. Todas estas salidas las podemos complementar con otros comandos (sort, awk, cut, etc). 

