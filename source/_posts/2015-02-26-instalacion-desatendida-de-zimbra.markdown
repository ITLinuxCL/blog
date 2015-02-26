---
layout: post
title: "Instalación desatendida de Zimbra"
date: 2015-02-26 11:52
comments: true
categories: [linux,zimbra,devops]
keywords: linux,zimbra,devops
description: Zimbra nos permite realizar una instalación de forma desatendida y aquí te mostramos como se hace
author: Miguel Angel Coa
---
[Zimbra](http://www.zimbra.com) nos permite realizar una instalación de forma desatendida usando el archivo `config.xxxx`, archivo que se crea en cada instalación Zimbra y almacena la información de la configuración utilizada. Te recomiendo que guardes este archivo para que lo tengas como referencia. 

La ventaja que tiene el método que vamos a ver es que en nuestras futuras instalaciones solo deberemos ejecutar un comando similar a:

```bash
$ /opt/zimbra/libexec/zmsetup.pl + <NombreDelArchivo>
```

y se realizará el proceso automáticamente.

Pero bueno, ahora veamos esto en la práctica y como anda; en este escenario nosotros vamos a realziar una instalación del tipo **Multi-Servidor**, lo anterior implica que necesitaremos los siguientes roles zimbra:

* 1 Servidor Ldap Master
* 1 Servidor Mailbox
* 1 Servidor MTA/Proxy

_Nota: asumimos que cada servidor se encuentra ya con los paquetes instalados según su rol `install.sh -s`_

Lo anterior también aplica para el orden en que tenemos que realizar la instalación y que recomienda Zimbra. Por lo tanto, el primer paso será instalar nuestro servidor **Zimbra Ldap Master**.

Nuestro archivo de configuración para este rol fue reutilizado de una instalación anterior en donde se adicionaron los siguientes valores (líneas 2 a 9):

```bash
[root@zco-ldapmaster-1 zimbra-conf]$ cat zcsOpenLdapMasterConfig 
CREATEADMINPASS="zcs_password"
LDAPAMAVISPASS="MyPassword"
LDAPPOSTPASS="MyPassword"
LDAPROOTPASS="MyPassword"
LDAPADMINPASS="MyPassword"
LDAPREPPASS="MyPassword"
ldap_nginx_password="MyPassword"
ldap_bes_searcher_password="MyPassword"
AVDOMAIN="example.local"
AVUSER="admin@example.local"
CREATEADMIN="admin@example.local"
CREATEDOMAIN="example.local"
DOCREATEADMIN="no"
DOCREATEDOMAIN="yes"
EXPANDMENU="no"
HOSTNAME="zco-ldapmaster-1.example.local"
....
....
```

Las líneas agregadas corresponden a todas las password necesarias para la configuración de Zimbra, las pide cuando realizamos una instalación manual. Hay algunas passwords que no necesitamos configurarlas, ya que no son requeridas (`ldap_bes_searcher_password`) y el mismo proceso de configuración las agrega automáticamente. Lo valores anteriores son importante ya que serán utilizados en los procesos de instalación de los demás servidores y si no coinciden fallará el proceso. Por lo mismo, estos valores también tienen que estar en los otros archivos de configuración.

Bueno, configurados los valores anteriores es cosa de instalar el Zimbra Ldap Master, para lo cual ejecutamos el comando:

```bash
/opt/zimbra/libexec/zmsetup.pl  -c /root/zimbra-conf/zcsOpenLdapMasterConfig
```

En el log que escribe la instalación podemos ver en línea la información de proceso de instalación:

```bash
[root@zco-ldapmaster-1 ~]# tail -f /tmp/zmsetup02262015-104418.log
Wed Feb 25 11:45:48 2015 Operations logged to /tmp/zmsetup02252015-114548.log
Wed Feb 25 11:45:48 2015 Installing LDAP configuration database...
Wed Feb 25 11:45:49 2015 done.
Wed Feb 25 11:45:49 2015 *** Running as zimbra user: /opt/zimbra/libexec/zmldapschema 2>/dev/null
Looking for LDAP installation...succeeded
Installing core schema...
Installing cosine schema...
Installing inetOrgPerson schema...
Installing zimbra schema...
Installing amavis schema...
Installing dyngroup schema...
Installing OpenDKIM schema...
Installing PGP schema...
Wed Feb 25 11:45:50 2015 Determining installed web applications
Wed Feb 25 11:45:50 2015 Getting installed packages
Wed Feb 25 11:45:51 2015 checking isEnabled zimbra-core
Wed Feb 25 11:45:51 2015 zimbra-core not in enabled cache
Wed Feb 25 11:45:51 2015 enabled packages 
Wed Feb 25 11:45:51 2015 Newinstall enabling all installed packages
Wed Feb 25 11:45:51 2015 Enabling zimbra-core
Wed Feb 25 11:45:51 2015 Enabling zimbra-ldap
Wed Feb 25 11:45:52 2015 Setting defaults...
Wed Feb 25 11:45:52 2015 Setting local config zimbra_java_home to /opt/zimbra/java
Wed Feb 25 11:45:52 2015 *** Running as zimbra user: /opt/zimbra/bin/zmlocalconfig -f -e zimbra_java_home='/opt/zimbra/java' 2> /dev/null
```

Terminado todo el proceso anterior, podemos chequear que el proceso zimbra este operativo:

```bash
[zimbra@zco-ldapmaster-1 ~]$ zmcontrol status
Host zco-ldapmaster-1.example.com
	ldap                    Running
	stats                   Running
	zmconfigd               Running
[zimbra@zco-ldapmaster-1 ~]$
```

Y vemos nuestros valores correctamente configurados:

```bash
[zimbra@zco-ldapmaster-1 ~]$ zmlocalconfig -s |grep password
antispam_mysql_password = 
antispam_mysql_root_password = 
client_ssl_truststore_password = ${mailboxd_truststore_password}
ldap_amavis_password = MyPassword
ldap_bes_searcher_password = MyPassword
ldap_nginx_password = MyPassword
ldap_postfix_password = MyPassword
ldap_replication_password = MyPassword
ldap_root_password = MyPassword
mailboxd_keystore_base_password = zimbra
mailboxd_truststore_password = changeit
mysql_root_password = zimbra
zimbra_ldap_password = MyPassword
zimbra_mysql_password = zimbra
zimbra_vami_password = vmware
[zimbra@zco-ldapmaster-1 ~]$ 
```

Una vez, finalizado la instalación anterior, se instala el servidor Zimbra Mailbox

```bash
/opt/zimbra/libexec/zmsetup.pl  -c /root/zimbra-conf/zcsOpenMailboxConfig
```

Y luego el Zimbra MTA/Proxy

```bash
/opt/zimbra/libexec/zmsetup.pl  -c /root/zimbra-conf/zcsOpenMTAConfig
```

Como mencionamos antiormente: Los restantes archivos de configuración (Mailbox y MTA) tambien tienen que tener los valores de password, además de los respectivos valores de configuración para cada tipo de rol. A continuación te dejamos algunos ejemplos.

#### Archivo Zimbra Mailbox

```bash
[root@zco-ldapmaster-1 ~]# cat zimbra-conf/zcsOpenMailboxConfig 
CREATEADMINPASS="zcs_password"
LDAPAMAVISPASS="MyPassword"
LDAPPOSTPASS="MyPassword"
LDAPROOTPASS="MyPassword"
LDAPADMINPASS="MyPassword"
LDAPREPPASS="MyPassword"
ldap_nginx_password="MyPassword"
ldap_bes_searcher_password="MyPassword"
AVDOMAIN="zco-mailbox-1.example.local"
AVUSER="admin@zco-mailbox-1.example.local"
CREATEADMIN="admin@example.local"
CREATEDOMAIN="example.local"
DOCREATEADMIN="yes"
DOCREATEDOMAIN="no"
DOTRAINSA="yes"
ENABLEGALSYNCACCOUNTS=""
```

#### Archivo Zimbra MTA/Proxy

```bash
[root@zco-ldapmaster-1 ~]# cat zimbra-conf/zcsOpenMTAConfig 
CREATEADMINPASS="zcs_password"
LDAPAMAVISPASS="MyPassword"
LDAPPOSTPASS="MyPassword"
LDAPROOTPASS="MyPassword"  
LDAPADMINPASS="MyPassword"
LDAPREPPASS="MyPassword"
ldap_nginx_password="MyPassword"
ldap_bes_searcher_password="MyPassword"
AVDOMAIN="zco-mta-1.example.local"
AVUSER="admin@zco-mta-1.example.local"
CREATEADMIN="admin@example.local"
CREATEDOMAIN="example.local"
DOCREATEADMIN="no"
DOCREATEDOMAIN="no"
ENABLEGALSYNCACCOUNTS=""
```

Saludos