---
layout: post
title: "Zimbra Logs Optimization"
date: 2015-04-17 12:16
comments: true
categories: [zimbra, devops, tips]
keywords: zimbra, tips, optimization
description: How to configure Zimbra Loggin on a multi-server setup
author: "Patricio Bruna"
---
_This is a short but valuable post. Next week we will Open Source our [ELK Stack](https://www.elastic.co/downloads)_

By default when you do a [Multi-Server Installation](https://www.zimbra.com/docs/ne/8.6.0/multi_server_install) of Zimbra, one of the servers gets _elected_ as a central log server. This server must have installed the `Zimbra-Logger` package to work correctly.

Later on you have to configure the local `Syslog` software on every other server of the platform, you do this running the `/opt/zimbra/libexec/zmsyslogsetup` command. What this command do is:

** 1. Read the value of the `zimbraLogHostname` variable **

```
$ zmprov -l gacf zimbraLogHostname
zimbraLogHostname: server.example.com
```

** 2. Modify the local Syslog software **

If we take as an example [Rsyslog](https://github.com/rsyslog/rsyslog), the configuration is done on the `/etc/rsyslog.conf` file of your servers, which should look something like this:

```
local0.*                @server.example.com
local1.*                @server.example.com
auth.*                  @server.example.com
local0.*                -/var/log/zimbra.log
local1.*                -/var/log/zimbra-stats.log
auth.*                  -/var/log/zimbra.log
mail.*                @server.example.com
mail.*                -/var/log/zimbra.log
```

## What is wrong with this?
If you have several servers as we do at [ZBox](http://www.zboxapp.com), you are going to start wasting a lot network bandwidth with all this logs, and in reallity, you don't need to send all the logs to `server.example.com`.

The **only log** you need to send to `server.example.com` is `local1.*`, because `Zimbra Logger` use the information of the `/var/log/zimbra-stats.log` file to show the status of the server in the Web Admin Console.

So you can optimize your configuration to something like this:

```
local1.*                @server.example.com
local0.*                -/var/log/zimbra.log
auth.*                  -/var/log/zimbra.log
mail.*                -/var/log/zimbra.log
```

You can improve it a bit more an reduce it to this:

```
local1.*                @server.example.com
local0.*                -/var/log/zimbra.log
auth.*                  -/var/log/zimbra.log
```

Because Rsyslog by default store `mail.*` into the `maillog` file, so you really don't need `mail.* -/var/log/zimbra.log`.


That's all. Next post we are going to show how we use [Elasticsearch, Logstash and Kibana](https://www.elastic.co/downloads) and from where you can get and use our setup.