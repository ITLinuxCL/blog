---
layout: post
title: "Exportar imagen de oVirt/RHEV a qcow2 y VMDK para VMware"
date: 2013-04-16 14:42
comments: true
categories: kvm,rhev,virtual
author: "Patricio Bruna"
keywords: kvm,rhev,virtual
description: Como exportar una disco virtual desde oVirt o RHEV a qcow2 u otro formato como de VMware
---
{% img right /images/Disk-clone.jpg %}
Acabamos de migrar de [oVirt](http://www.ovirt.org) a [Proxmox](https://pve.proxmox.com/wiki/Main_Page), la razón de esto es tema para otro artículo, pero aquí vamos a dar la receta de como exportar los discos de las VMs de oVirt a un archivo [qcow2](http://www.linux-kvm.org/page/Qcow2) que puede ser transportado. El dicos VM origen estaba basado en una imagen LVM.

*Ovirt es el proyecto Open Source en que se basa el desarrollo de [Red Hat Enterprise Virtualization](http://www.redhat.com/rhev3/).*

### Preparar la VM

Lo primero que se debe hacer son las siguientes tareas:

1. **Borrar la configuración de hardware de las tarjetas de red**, De esta forma mantienes el mismo nombre de la tarjeta que ya tienes. Lo único que debes hacer es vaciar el archivo ```/etc/udev/rules.d/70-persistent-net.rules```.

2. **Llenar el disco con ceros**, Es tip viene de [Richard WM Jones](http://rwmj.wordpress.com/2010/10/19/tip-making-a-disk-image-sparse/) y permite obtener una imagen una imagen qcow2 del tipo *thin*, mucho más ligera. En cada punto de montaje del sistema operativo de tu vm, debes ejecutar:

```
dd if=/dev/zero of=zerofile bs=1M
rm -rf zerofile
```

### Listo para la conversión

####1. Descubre la asociación de la VM con el LV
En Ovirt cada disco de tu VM está asociado con un volumen LVM, así que lo primero es saber cual LV se debe exportar. Esto se logra con la VM aun corriendo y usando SSH te conectas al Hypervisor donde se está ejecutando la VM y corres el siguiente comando:

```
[root@ovirt]$ virsh --readonly -c qemu:///system domblklist pbrunalab
**vda** /rhev/data-center/70ca7a78-b570-11e1-bcfe-2e18820c3a67/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/images/7e4c3ed3-73e6-44d8-8d21-efbb7818af86/e0c77de3-7589-43bc-bfdd-db11e11dab93
```

Del comando debes notar que ```pbrunalab``` es el nombre de la máquina virtual en cuestión. El resultado que nos entrega el comando es que el disco ```vda``` está asociado a un tipo de dispositivo. Ahora es momento de  obtener el dispositivo: 

```
[root@ovirt]$ ls -l /rhev/data-center/70ca7a78-b570-11e1-bcfe-2e18820c3a67/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/images/7e4c3ed3-73e6-44d8-8d21-efbb7818af86/e0c77de3-7589-43bc-bfdd-db11e11dab93

lrwxrwxrwx. 1 vdsm kvm 78 Feb 25 23:11 /rhev/data-center/70ca7a78-b570-11e1-bcfe-2e18820c3a67/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/images/7e4c3ed3-73e6-44d8-8d21-efbb7818af86/e0c77de3-7589-43bc-bfdd-db11e11dab93 -> /dev/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/e0c77de3-7589-43bc-bfdd-db11e11dab93
```

Si te fijas con cuidado ya sabrás que el Volumen Lógico que buscamos es ```/dev/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/e0c77de3-7589-43bc-bfdd-db11e11dab93```. Ahora puedes apagar la VM.

####2. Exportar a qcow2
Con la máquina apagada y conociendo el LV es tiempo de hacer la exportación. Este punto asume que tenemos montado en /mnt/export el destino donde queremos dejar la nueva imagen, lo que puede ser un recurso NFS o un disco USB externo.

##### 1. Activamos el volumen
```
[root@ovirt]$ lvchange -ay /dev/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/e0c77de3-7589-43bc-bfdd-db11e11dab93
```

##### 2. Exportamos a qcow2 (para KVM)
```
[root@ovirt]$ qemu-img convert -p -O qcow2 /dev/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/e0c77de3-7589-43bc-bfdd-db11e11dab93 /mnt/export/vm_disk-vda.qcow2
```

##### 3. Exportamos a vmdk (para VMware)
```
[root@ovirt]$ qemu-img convert -p -O vmdk /dev/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/e0c77de3-7589-43bc-bfdd-db11e11dab93 /mnt/export/vm_disk-vda.vmdk
```

##### 4. Desactivamos el volumen
```
[root@ovirt]$ lvchange -an /dev/e1537ba0-7d0f-4e4a-88b8-7f5698b2ceae/e0c77de3-7589-43bc-bfdd-db11e11dab93
```

Y eso es todo, esperamos te sirva.