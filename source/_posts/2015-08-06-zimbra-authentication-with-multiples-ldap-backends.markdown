---
layout: post
title: "Zimbra Authentication with Multiples LDAP Backends"
date: 2015-08-06 20:10
comments: true
categories: [zimbra, tips, migration]
author: Patricio Bruna
keywords: postfix,linux,howto
description: How to use OpenLDAP Meta Backend as a Proxy LDAP for Zimbra Authentication
---
So here we are again with another `Zimbra Migration Challenge`, this time the customer's
requirement was to enable External Authentication for a Domain, the catch: **it had to be done in group of users**. This way the IT Department could minimize the support calls when the user's were facing with de `Enter Password` dialog.

The External Authentication Directory is a Samba 4 (Active Directory) LDAP, that has been working for almost a year now, so it was time to consolidate the users and password.

The approach we took was to deploy a `LDAP Proxy` using [OpenLDAP Meta Backend](http://www.openldap.org/doc/admin24/backends.html) and [LDAP SASL Authentication](http://www.openldap.org/doc/admin24/sasl.html). The following diagram shows the setup.

{%img center /images/ldap-proxy-diagram.png  %}

As you can see in the diagram, the flow of the authentication is:

1. Zimbra was configured to authenticate against the `LDAP Proxy`.
2. The `LDAP Proxy` pass the authentication request to the local `saslauthd` daemon.
3. `saslauthd` ask the `LDAP Proxy` to do a `Pass-Trough authentication` to the corresponding `Backend`
4. The `Samba4` or the internal Zimbra LDAP validate the user.

So how did we do this? Glad you ask ;)

<!--more-->

### 1. Setup the Proxy LDAP
We used `CentOS 6.5` and the standard packages that you can install with `yum`:

```bash
$ yum install cyrus-sasl cyrus-sasl-ldap openldap-servers openldap-clients
```

 The first thing we did was to make `OpenLDAP` use the _old_ `/etc/openldap/slapd.conf` configuration file instead of the new `cn=config`:

```
 # Add this to /etc/sysconfig/ldap
 SLAPD_OPTIONS="-f /etc/openldap/slapd.conf"
```

### 2. Populate the Proxy LDAP
We need to populate the Proxy LDAP with the users information, for this we took a copy a of the Zimbra LDAP information and pasted here, but first you need to configure the LDAP Server.

_**Note:**_ The full version of the files are available at [https://gist.github.com/pbruna/7229c3e99dd4bf57b73c](https://gist.github.com/pbruna/7229c3e99dd4bf57b73c)

#### 2.1. Configure the LDAP DB

No magic here, just the standard setup.

```
# /etc/openldap/slapd.conf
database	bdb
suffix		"dc=proxy,dc=ldap"
rootdn		"cn=Manager,dc=proxy,dc=ldap"
rootpw		secret
```

#### 2.2. Configure the LDAP SASL Authentication

This is new, well for me at least. We are going to tell OpenLDAP to pass the authentication to the local `saslauthd` daemon.

```
 # /etc/openldap/slapd.conf
 sasl-host       localhost
 sasl-secprops   none
```

`OpenLDAP` talks with `saslauthd` using a mutex, so open `/usr/lib64/sasl2/slapd.conf` and add:

```
pwcheck_method: saslauthd
saslauthd_path: /var/run/saslauthd/mux
```

Finally add the ldap user to the saslauth group:

```bash
$ usermod -a -G saslauth ldap
```

#### 2.3. Clear Text Passwords

For all of this to work we are going to need that OpenLDAP stores the `userPassword` as without any **hashing**.

```
 # /etc/openldap/slapd.conf
 password-hash   {CLEARTEXT}
```

#### 2.4. Import users

Start the LDAP service with, grab the LDIF you exported from the Zimbra LDAP and load it:

```bash
$ service slapd start
$ ldapadd -x -D "cn=Manager,dc=proxy,dc=ldap" -wsecret -f zimbra_export.ldif -c
```

No, i didn't tell you how to export the `ldif` file from Zimbra, but you can find a step by step howto in our knowledge base: [Populating Proxy LDAP with Zimbra users](http://soporte.itlinux.cl/hc/es/articles/205723865)

### 3. Lets configure the Meta Backends
We are going to use the `Meta Backends` like an `IPTABLES` [NAT](https://en.wikipedia.org/wiki/Network_address_translation), like this:

* When we search for the ldap base `ou=zimbra,dc=local` the search will be `nated` to the `Zimbra LDAP Server`,
* When we search for the base `ou=samba4,dc=local` the search will be `nated` to the `Samba 4 LDAP Server`.

```bash
 $ ldapsearch  -x -D "cn=Manager,dc=local" -wsecret -b ou=zimbra,dc=local uid=itlinux
 # extended LDIF
 #
 # LDAPv3
 # base <ou=zimbra,dc=local> with scope subtree
 # filter: uid=itlinux
 # requesting: ALL
 #

 # itlinux, zimbra, local
 dn: uid=itlinux,ou=zimbra,dc=local
 uid: itlinux
 mail: itlinux@example.com
 ZIMBRAMAILDELIVERYADDRESS: itlinux@example.com
 ......

 $ ldapsearch  -x -D "cn=Manager,dc=local" -wsecret -b ou=samba4,dc=local cn=itlinux
 # extended LDIF
 #
 # LDAPv3
 # base <ou=samba4,dc=local> with scope subtree
 # filter: cn=itlinux
 # requesting: ALL
 #
 # itlinux, Users, samba4, local
 dn: cn=itlinux,cn=Users,ou=samba4,dc=local
 objectClass: top
 cn: itlinux
 SAMACCOUNTNAME: itlinux
 ....
```

#### 3.1. Add the Meta Backends to the LDAP config

```
 # /etc/openldap/slapd.conf
 # Meta Databases
 database        meta
 suffix          "dc=local"
 rootdn          "cn=Manager,dc=local"
 rootpw          secret

 # zimbra
 uri ldap://zimbra-server.example.com/ou=zimbra,dc=local

 lastmod       off
 suffixmassage   "ou=zimbra,dc=local" "ou=people,dc=example,dc=com"
 idassert-bind bindmethod=simple
   binddn="uid=zimbra,cn=admins,cn=zimbra"
   credentials="password"
   mode=none
   flags=non-prescriptive
 idassert-authzFrom "dn.exact:cn=Manager,dc=local"


 # Samba4
 uri  ldap://samba4-server.example.com/ou=samba4,dc=local

 lastmod       off
 suffixmassage "ou=samba4,dc=local" "ou=users,dc=example,dc=com"
 idassert-bind bindmethod=simple
  binddn="cn=manager,cn=users,dc=example,dc=com"
  credentials="password"
  mode=none
  flags=non-prescriptive
 idassert-authzFrom "dn.exact:cn=Manager,dc=local"
```

#### 3.2. Configure salsauthd
The key configurations of this are:

* `ldap_search_base: ou=%d,dc=local`, the `%d` is the text after the `@`. If we use itlinux@example.com, `%d = example.com`
* `ldap_filter: (|(uid=%U)(SAMACCOUNTNAME=%U))`, we search matching the `uid` or `SAMACCOUNTNAME` value with the username. `%U = itlinux`, if we keep with the last example.

```
# /etc/saslauthd.conf
ldap_servers: ldap://127.0.0.1
ldap_search_base: ou=%d,dc=local
ldap_timeout: 10
ldap_filter: (|(uid=%U)(SAMACCOUNTNAME=%U))
ldap_bind_dn: cn=Manager,dc=local
ldap_password: secret
ldap_deref: never
ldap_restart: yes
ldap_scope: sub
ldap_use_sasl: no
ldap_start_tls: no
ldap_version: 3
ldap_auth_method: bind
```

#### 3.3. Restart the services

```bash
$ service slapd start
$ service saslauthd start
```

#### 3.4. Magic and the CLEARTEXT Password
We are going to use the `userPassword` field to let the `LDAP Proxy` decide to which backend redirect the authentication, like this:

```yaml
dn: uid=itlinux,ou=people,dc=proxy,dc=ldap
uid: itlinux
userPassword: {SASL}itlinux@zimbra

dn: uid=pbruna,ou=people,dc=proxy,dc=ldap
uid: pbruna
userPassword: {SASL}pbruna@samba4
```

The flow of this is:

1. LDAP Proxy receives an authentication request,
2. LDAP Proxy check the `userPassword` field and notice that has to pass the authentication to `saslauthd`
3. `saslauthd` receives the authentication request with the param `pbruna@samba4 => [%U: pbruna, %d: samba4]`
4. `salsauthd` now knows that has to make a search in `ou=samba4,dc=local` with the filter `(|(uid=pbruna)(SAMACCOUNTNAME=pbruna))`
5. This search is going to hit the `Meta Backend` that points to the `Samba4 LDAP Server`.
6. The `Samba4 LDAP Server` authenticate the user.


### 4. Zimbra Configuration
This is by far the easiest part, just go and configure a Domain to use External LDAP authentication and point it to the `LDAP Proxy` server: [https://wiki.zimbra.com/wiki/LDAP_Authentication](https://wiki.zimbra.com/wiki/LDAP_Authentication)

**And that is all!!**

### 5. Considerations

#### 5.1 How to set the userPassword field

```
$ ldappasswd -x -D "cn=Manager,dc=proxy,dc=ldap" -wsecret \
  -s '{SASL}itlinux@zimbra' uid=itlinux,ou=people,dc=proxy,dc=ldap
```

Remember that the `userPassword` field is show in `base64` when you do a `ldapsearch`, so if you see this:

```yaml
userPassword:: e1NBU0x9aXRsaW51eEB6aW1icmE=
```

and need to be sure that its ok, just decoded with:

```bash
$ echo e1NBU0x9aXRsaW51eEB6aW1icmE= | base64 --decode
{SASL}itlinux@zimbra
```

#### 5.2 Thanks

* As always to the amazing people of [Zimbra](http://www.zimbra.com) for such a wonderful product, and for keeping it Open Source.
* To the people of [LDAP Tool Box project](http://ltb-project.org/wiki/start) from where we got all the information to do this. A must read [Pass-Trough authentication with SASL](http://ltb-project.org/wiki/documentation/general/sasl_delegation)
* To [Daniel Eugenin](https://www.twitter.com/deugenin) for all the help setting up OpenLDAP.

And to **You** for keep reading!!!!
