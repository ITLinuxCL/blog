---
layout: post
title: "Virtualizando con contenedores y Proxmox VE"
date: 2013-12-19 12:27
comments: true
categories: proxmox virtualizacion
published: true
author: Miguel Coa
---

Una de las gracias de tener un ambiente de virtualización bajo Proxmox VE, es que nos permite la creación de máquinas virtuales bajo lo que se denomina <i>contenedores</i>. ¿Qué carajo es esto? ....básicamente es la creación de una máquina virtual en un ambiente 'chroot' directamente en el sistema de arhichos del servidor base, lo que nos da una serie de ventajas:

1. Administración de recursos de forma directa: Ejemplo, podemos incrementar la ram, swap, cpu, disco, etc. Y los cambios son aplicados al instante en el servidor virtual.
2. El uso del mismo sistema de ficheros para todas las máquinas virtuales no 'sobrecarga' al sistema base.
3. La creación y borrado de máquinas virtuales es casi instantaneo.
4. La velocidad de las aplicaciones que tenemos corriendo en el contenedor es casi la misma si se tratara del servidor base.
5. Acceso directo al kernel del servidor base.

Pero no todo es positivo, una de los contra es que solo pemite trabajar bajo esta modalidad solo ambientes que corran en Linux y no Windows (para variar Windows).

Pues bien, metamos las manos al teclado y veamos toda la teoría en la praxis.

1. Para empezar a trabajar con Contenedores, primero tenemos que tener nuestro template de instalación. Esto lo podemos conseguir de dos formas:

    * Lo bajamos directamente desde los template que hay en [OpenVZ](http://download.openvz.org/template/precreated/)
    * Podemos crear una máquina virtual y luego la convertimos en template
2. En este caso, tomamos la opción "a" y bajaremos el template. Nos dirigimos hacia nuestro "Storage local" -> Template -> Y descargamos "Centos 6 standard"

{% img /assets/images/downloadTemplate.png %}

3. Descargado el template, podemos crear el contenedor: Create CT -> Completamos los datos que tendrá el contenedor.

<!-- more -->

4. Y listo contenedor creado (de verdad se demora tan poco, que ni para pegarse una dormida alcanza). Ahora, podemos acceder al nuestra máquina virtual creada directamente accediendo por ssh. Ahora, si nos conectamos por SSH al servidor proxmox veremos el "chroot" del contenedor

```ruby
root@proxmox2:~# df -h
Filesystem               Size  Used Avail Use% Mounted on
udev                      10M     0   10M   0% /dev
tmpfs                    382M  484K  381M   1% /run
/dev/mapper/pve-root      95G  1.1G   89G   2% /
tmpfs                    5.0M     0  5.0M   0% /run/lock
tmpfs                    763M   22M  742M   3% /run/shm
/dev/mapper/pve-data     344G   45G  300G  13% /var/lib/vz
/dev/sda1                495M   35M  435M   8% /boot
/dev/fuse                 30M   16K   30M   1% /etc/pve
/var/lib/vz/private/102   15G  590M   15G   4% /var/lib/vz/root/102
none                     512M  4.0K  512M   1% /var/lib/vz/root/102/dev
none                     512M     0  512M   0% /var/lib/vz/root/102/dev/shm
```

Como se mencionó arriba, una de las gracias es la de administrar recursos de la máquina virtual, de forma directa (sin reinicios). Por ejemplo, nuestro contenedor fue creado con 1GB de swap, 1GB de ram y 10GB de disco.

```ruby
[root@Centos ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/simfs             10G  590M  9.5G   6% /
none                  512M  4.0K  512M   1% /dev
none                  512M     0  512M   0% /dev/shm
[root@Centos ~]# free -m
             total       used       free     shared    buffers     cached
Mem:          1024         40        983          0          0         11
-/+ buffers/cache:         29        994
Swap:         1024          0       1024
```

Y ahora modificaremos los recursos pasando de 10GB hacia 15GB en disco, de 1GB de ram hacia 2GB y bajando la swap a 512MB.

Para ello: Seleccionas el contenedor -> Resources y ajustamos los valores. Y en nuestra máquina virtual veremos que los cambios son aplicado de forma inmediata

```ruby
[root@Centos ~]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/simfs             15G  590M   15G   4% /
none                  512M  4.0K  512M   1% /dev
none                  512M     0  512M   0% /dev/shm
[root@Centos ~]# free -m
             total       used       free     shared    buffers     cached
Mem:          2048         40       2007          0          0         11
-/+ buffers/cache:         29       2018
Swap:          512          0        512
[root@Centos ~]#
```

Y eso, si quieres ver más puedes chequear la wiki de [Proxmox](http://pve.proxmox.com/wiki/Main_Page)

