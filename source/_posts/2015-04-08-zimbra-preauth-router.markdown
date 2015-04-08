---
layout: post
title: "Zimbra Preauth Router: 0 downtime migrations"
date: 2015-04-08 11:15
comments: true
categories: [zimbra, zbox, ZPR]
keywords: zimbra, zbox, ZPR
description: Zero (0) downtime migration of Zimbra
author: "Patricio Bruna"
---
{% img right http://i.imgur.com/dJ5jnB0.gif %}
At [ZBox Mail](https://www.zboxapp.com) we are faced with migrations from production Zimbra servers to Our Cloud Platform. Some times the source servers are small enough that you can bring down the service for a couple of hours, migrate all the mailboxes and start again in the new home.

But _luckily_ for us we are migrating a lot of big platforms. For big we mean over 1,000 mailboxes and Terabytes of mail data. And `One Does Not Simply` migrate this in a couple of hours.

So we came up with this procedure to do a _0 downtime migration_:

1. Setup a split domain between the 2 platforms, customer and ours,
3. The company must announce that a migration is in place, and for as long as the migration take place, only the Webmail is allowed
3. Every day we migrate a group of N mailboxes.
4. Repeat 3 until finish
5. Celebrate

The exclusive use of the Webmail is paramount. This way we can use [Zimbra Preauth Router](https://github.com/pbruna/zimbra_preauth_router) an let it redirect the user to the old or new platform.

The way the Zimbra Preauth Router (`ZPR`) works is by replacing the standard Zimbra Login Page, and decide where the user should be redirected. The steps followed are:

1. The user authenticate against `ZPR`,
2. `ZPR` looks into a DB file for an entry for this user. Any user listed was migrated.
3. If it's a migrated user, ZPR redirects him to the new platform, if not, to the current platform.
4. Thanks to `Zimbra Preauth` the user enter directly to the Webmail, without the need of another validation.

{% img /images/ZPR.png %}

This way the users doesn't need to learn a new URL and the migration process is mostly transparent for them.

For `ZPR` to works you need to configure Preauth Keys for the Domains, as explained at the [Zimbra Wiki](https://wiki.zimbra.com/wiki/Preauth)

All the information on how to install and use ZPR can be found here: [https://github.com/pbruna/zimbra_preauth_router](https://github.com/pbruna/zimbra_preauth_router)

As always, all ideas are welcome.