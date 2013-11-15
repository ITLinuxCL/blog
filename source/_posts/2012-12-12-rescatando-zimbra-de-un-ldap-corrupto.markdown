---
layout: post
title: "Rescatando Zimbra de un LDAP corrupto"
date: 2012-12-12 12:48
comments: true
categories: zimbra
author: "IT Linux"
keywords: zimbra,ldap
description: Corregir ldap corrupto en Zimbra
---

Hoy fue un día de aquellos y comenzó temprano.

A las 7AM recibí un llamado de uno de nuestros clientes, avisando que ya llevaban un día sin correo y que el Gerente General había dado la orden de que no podía pasar ni un minuto más sin servicio. Digan lo que digan, el correo electrónico es la herramienta más importante en las empresas.

El único antecedente antes de revisar el servidor era que todos los servicios de Zimbra arrancaban excepto por LDAP, esto según la persona a cargo del servidor.

Una vez concedido el acceso remoto hice lo que siempre hago al conectarme: comprobar el espacio con df -h e inmediatamente saltó a la vista que la partición /opt estaba al 100%.

Con este resultado creí que había encontrado la causa del problema así que ejecuté **du -h –exclude=store –max-depth 1 /opt/zimbra** para ver cuales eran los directorios con más datos y  de donde podía recuperar espacio. Al terminar de correr obtuve los mismos responsables de siempre:

* /opt/zimbra/log
* /opt/zimbra/data/amavisd/quarantine

Una vez liberado el espacio, y pensando que había ganado, me dispuse a iniciar ldap:

```bash
zimbra@mail:~$ ldap start
Dec 12 07:12:49 mail slapd[3179]: @(#) $OpenLDAP: slapd 2.4.23 (Mar  4 2011 17:11:29) $#012#011root@zre-debian5-64.eng.vmware.com:/home/build/p4/HELIX-701/ThirdParty/openldap/openldap-2.4.23.5z/servers/slapd
Dec 12 07:12:49 mail slapd[3179]: slapd stopped.
Dec 12 07:12:49 mail slapd[3179]: connections_destroy: nothing to destroy.
```

pero como ven no funcionó y el log no aportó muchos datos. Así que no era tan fácil como parecía.

Mi primer desafío fue iniciar LDAP con más información (debug) para ver que sucedía, esto lo logré lanzando el servicio a mano:

```bash
# Importante es la opción "-d -1"
# -d: nivel de debug
# -1: nivel de log más alto -> http://www.openldap.org/doc/admin24/runningslapd.html
 
/opt/zimbra/libexec/zmslapd -l LOCAL0 -4 -u zimbra -h ldap://zimbra.server.com:389 ldapi:/// -F /opt/zimbra/data/ldap/config -d -1
```

```bash
@(#) $OpenLDAP: slapd 2.4.23 (Mar  4 2011 17:11:29) $
  root@zre-debian5-64.eng.vmware.com:/home/build/p4/HELIX-701/ThirdParty/openldap/openldap-2.4.23.5z/servers/slapd
ldap_pvt_gethostbyname_a: host=mail, r=0
daemon_init: ldap://zimbra.server.com:389
daemon_init: listen on ldap://zimbra.server.com:389
daemon_init: 1 listeners to open...
ldap_url_parse_ext(ldap://zimbra.server.com:389)
daemon: listener initialized ldap://zimbra.server.com:389
daemon_init: 1 listeners opened
ldap_create
slapd init: initiated server.
slap_sasl_init: initialized!
backend_startup_one: starting "cn=config"
ldif_read_file: read entry file: "/opt/zimbra/data/ldap/config/cn=config.ldif"
=> str2entry: "2lkdGg6
OTBweDtjdXJzb3I6aGFuZH0NCg0KLmNlbGRhYmFzaWNvIHt3aWR0aDo4NTsgZm9udC1mYW1pbHk6
IEFyaWFsLCBIZWx2ZXRpY2EsIHNhbnMtc2VyaWY7IHRleHQtYWxpZ246Y2VudGVyOyB9DQouY2Vs
ZGFjYXJ0YXt0ZXh0LWFsaWduOmxlZnQ7cGFkZGluZy1sZWZ0OjJweDtwYWRkaW5nLXJpZ2h0OjJw
eDtmb250LXNpemU6MTBweDt9DQouY2VsZGFjb250ZXh0byB7d2lkdGg6MTA1OyBmb250LWZhbWls
eTogQXJpYWwsIEhlbHZldGljYSwgc2Fucy1zZXJpZjsgdGV4dC1hbGlnbjpjZW50ZXI7fQ0KLmNl
bGRhZGVyZWNoYSB7dGV4dC1hbGlnbjpjZW50ZXI7fQ0KLmNlbGRhZWxlbWVudG8ge3dpZHRoOjEw
cHg7dGV4dC1hbGlnbjpjZW50ZXI7cGFkZGluZy1sZWZ0OjJweDtwYWRkaW5nLXJpZ2h0OjJweH0N
Ci5jZWxkYWV0aXF1ZXRhIHt3aWR0aDoxMHB4O3BhZGRpbmctcmlnaHQ6MnB4O2ZvbnQtc2l6ZTox
MHB4fQ0KLmNlbGRhZm9ybW5vbWJyZXtwYWRkaW5nLWxlZnQ6NHB4O3BhZGRpbmctcmlnaHQ6OHB4
O30NCi5jZWxkYWludGVyaW9yIHtmb250LWZhbWlseTogQXJpYWwsIEhlbHZldGljYSwgc2Fucy1z
ZXJpZn0NCi5jZWxkYWl6cXVpZXJkYSB7Zm9udC1mYW1pbHk6IEFyaWFsLCBIZWx2ZXRpY2EsIHNh
bnMtc2VyaWY7IGZvbnQtc2l6ZToxMHB4O30NCi5jZWxkYW1lbnV7Y3Vyc29yOmhhbmQ7d2lkdGg6
NzZweDtib3JkZXItbGVmdC13aWR0aDowcHg7Ym9yZGVyLXJpZ2h0OiAxcHggc29saWQgd2hpdGU7
dGV4dC1hbGlnbjpjZW50ZXI7YmFja2dyb3VuZDojMzM2Njk5O30NCi5jZWxkYW1lbnVmaW5"
ldif_parse_line: missing ':' after 2lkdGg6
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after OTBweDtjdXJzb3I6aGFuZH0NCg0KLmNlbGRhYmFzaWNvIHt3aWR0aDo4NTsgZm9udC1mYW1pbHk6
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after IEFyaWFsLCBIZWx2ZXRpY2EsIHNhbnMtc2VyaWY7IHRleHQtYWxpZ246Y2VudGVyOyB9DQouY2Vs
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after ZGFjYXJ0YXt0ZXh0LWFsaWduOmxlZnQ7cGFkZGluZy1sZWZ0OjJweDtwYWRkaW5nLXJpZ2h0OjJw
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after eDtmb250LXNpemU6MTBweDt9DQouY2VsZGFjb250ZXh0byB7d2lkdGg6MTA1OyBmb250LWZhbWls
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after eTogQXJpYWwsIEhlbHZldGljYSwgc2Fucy1zZXJpZjsgdGV4dC1hbGlnbjpjZW50ZXI7fQ0KLmNl
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after bGRhZGVyZWNoYSB7dGV4dC1hbGlnbjpjZW50ZXI7fQ0KLmNlbGRhZWxlbWVudG8ge3dpZHRoOjEw
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after cHg7dGV4dC1hbGlnbjpjZW50ZXI7cGFkZGluZy1sZWZ0OjJweDtwYWRkaW5nLXJpZ2h0OjJweH0N
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after Ci5jZWxkYWV0aXF1ZXRhIHt3aWR0aDoxMHB4O3BhZGRpbmctcmlnaHQ6MnB4O2ZvbnQtc2l6ZTox
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after MHB4fQ0KLmNlbGRhZm9ybW5vbWJyZXtwYWRkaW5nLWxlZnQ6NHB4O3BhZGRpbmctcmlnaHQ6OHB4
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after O30NCi5jZWxkYWludGVyaW9yIHtmb250LWZhbWlseTogQXJpYWwsIEhlbHZldGljYSwgc2Fucy1z
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after ZXJpZn0NCi5jZWxkYWl6cXVpZXJkYSB7Zm9udC1mYW1pbHk6IEFyaWFsLCBIZWx2ZXRpY2EsIHNh
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after bnMtc2VyaWY7IGZvbnQtc2l6ZToxMHB4O30NCi5jZWxkYW1lbnV7Y3Vyc29yOmhhbmQ7d2lkdGg6
<= str2entry NULL (parse_line)
ldif_parse_line: missing ':' after NzZweDtib3JkZXItbGVmdC13aWR0aDowcHg7Ym9yZGVyLXJpZ2h0OiAxcHggc29saWQgd2hpdGU7
<= str2entry NULL (parse_line)
<= str2entry ran past end of entry
send_ldap_result: conn=-1 op=0 p=0
send_ldap_result: err=80 matched="" text="internal error (cannot parse some entry file)"
slapd destroy: freeing system resources.
slapd stopped.
connections_destroy: nothing to destroy.
```

El dato importante está en la línea 14 y nos dice que el archivo **/opt/zimbra/data/ldap/config/cn=config.ldif** está con problemas. Al revisarlo y compararlo con otro servidor se notaba que ocurrió un problema a nivel de sistema de archivos que lo corrompió. En otro servidor este archivo tenía un contenido estándar, así que procedí a copiar y pegar, quedando:

```bash
cn=config  cn=config.ldif  cn=config.ldif.old  cn=config.ldif.org
zimbra@mail:~/data/ldap/config$ cat cn\=config.ldif
dn: cn=config
objectClass: olcGlobal
cn: config
olcConfigDir: /opt/zimbra/data/ldap/config
olcArgsFile: /opt/zimbra/openldap/var/run/slapd.args
olcAttributeOptions: lang-
olcAuthzPolicy: none
olcConcurrency: 0
olcConnMaxPending: 100
olcConnMaxPendingAuth: 1000
olcGentleHUP: FALSE
olcIdleTimeout: 0
olcIndexSubstrIfMaxLen: 4
olcIndexSubstrIfMinLen: 2
olcIndexSubstrAnyLen: 4
olcIndexSubstrAnyStep: 2
olcIndexIntLen: 4
olcLocalSSF: 128
olcPidFile: /opt/zimbra/openldap/var/run/slapd.pid
olcReadOnly: FALSE
olcSaslSecProps: noplain,noanonymous
olcSockbufMaxIncoming: 262143
olcSockbufMaxIncomingAuth: 16777215
olcTLSCertificateFile: /opt/zimbra/conf/slapd.crt
olcTLSCertificateKeyFile: /opt/zimbra/conf/slapd.key
olcTLSCRLCheck: none
olcTLSVerifyClient: never
structuralObjectClass: olcGlobal
entryUUID: 1525b684-333e-102d-86f5-d562901af228
creatorsName: cn=config
createTimestamp: 20081020215916Z
olcTLSCACertificatePath: /opt/zimbra/conf/ca
olcThreads: 8
olcLogLevel: 32768
olcToolThreads: 1
olcSecurity: ssf=0
olcWriteTimeout: 0
entryCSN: 20121212124142.737657Z#000000#000#000000
modifiersName: cn=config
modifyTimestamp: 20121212124142Z
```

Volví a lanzar el servicio LDAP a mano con el mismo comando anterior para ver si funcionaba y no levantó, ahora el culpable era el archivo **/opt/zimbra/data/ldap/config/cn=config/olcDatabase={2}hdb.ldif**. Nuevamente lo comparé con el otro servidor y también era un archivo con contenido estándar, así que a copiar y pegar.

Vamos por otro reinicio…. y ahora sí funcionó.

Si continuas leyendo, y lo agradezco, el dato importante de este post es que para analizar errores de LDAP en Zimbra debes tener siempre a mano el comando:

```bash
# Importante es la opción "-d -1"
# -d: nivel de debug
# -1: nivel de log más alto -> http://www.openldap.org/doc/admin24/runningslapd.html
 
/opt/zimbra/libexec/zmslapd -l LOCAL0 -4 -u zimbra -h ldap://zimbra.server.com:389 ldapi:/// -F /opt/zimbra/data/ldap/config -d -1
```
