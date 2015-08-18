---
layout: post
title: "Icinga2 Tutorial: Part 1 - Installation and Configuration."
date: 2015-08-16 02:00:00 -05:00
license: cc0
categories:
- Icinga2
- Network Monitoring
tags:
- Icinga2
- IcingaWeb2
- Network Monitoring
- Tutorials
---
* [Icinga2 Tutorial: Part 0][1]
* Icinga2 Tutorial: Part 2
* Icinga2 Tutorial: Part 3

# Introduction #
I wanted to get this out fairly quickly, because I just actually did this, and
while the default [Icinga2 tutorial][4] is pretty good, it is lacking in some
areas, and since everything is fresh in my mind I wanted to go ahead and draft
this up.

It is worth noting that in this post I will assume you are root. You can get to
root as a sustained environment by using “[sudo -s][5]” I do not condone this
for day to day usage, it is **bloody dangerous**.

So, let’s get started. *Sudo* to root.

    zyradyl@captor ~ $ sudo -s
    [sudo] password for zyradyl:
    captor zyradyl #

### Installing Icinga2 ###
This assumes you are *sudo'd*, and on a [Debian][6] based system. For other
systems, you can find [additional documentation here][49]. Note that I follow
that documentation very closely except for a few small notes, so it should be
easy to adjust this tutorial for your needs.

#### Adding Repositories ####

    captor zyradyl # wget -O - http://debmon.org/debmon/repo.key 2>/dev/null | apt-key add -
    captor zyradyl # echo ‘deb http://debmon.org/debmon debmon-jessie main’ >/etc/apt/sources.list.d/debmon.list
    captor zyradyl # apt-get update

The first command downloads ([wget][7]) the [cryptographic key][48] for the
[debmon][8] [repository][47] and hands it over to [apt][9]. Apt then
[installs that key][10], which allows you to verify that you are using packages
signed off by and validated by that repository. The second command is then
[echoing][50] the debmon main repository into your [apt-sources][11], so that
the software available there is added to your system’s list, which allows you to
[install][51] with just a command. The third command tells your system to
[update][12] its internal package list to incorporate the changes. After that we
can go ahead and install.

#### Installation ####
    # apt-get install icinga2 monitoring-plugins

This will install both [icinga2][13] and the [nagios][14]
[monitoring plugins][15] that are compatable. This gives you a very good base.

Start the program through the [service][16] command, and then set it to start
on boot with [update-rc.d][38].

    # service icinga2 start
    # update-rc.d icinga2 enable

Tada! You now have a [data aggregator][39]/network monitor setup! However, we
want a nice way to get information from our monitoring host, so now we need to
install the web frontend.

# Installing [IcingaWeb2][17] #
Let’s start with all the essentials.

#### Package Installation ####

    captor zyradyl # apt-get install postgresql icinga2-ido-pgsql apache2 php5-ldap php5-imagick php5-pgsql php5-intl icingaweb2 icingaweb2-module-doc icingaweb2-module-monitoring icingaweb2-module-setup

This is a big one, so if you are living on about a meg down, prepare to settle
in for a bit. In order, this will install the [PostgreSQL][18] [database][36],
the [Icinga2 database connector][19], the [apache2][20] [web server][37],
the [ldap][21] [php5][22] module, the [imagemagick][23] module for php5, the
postgresql php5 module, the intl php5 module, the IcingaWeb2 core,
the IcingaWeb2 documentation module, the
IcingaWeb2 monitoring module, and the IcingaWeb2 setup module. Note
that on debian systems, it will prompt you to allow it to setup the database. I
**strongly** recommend you say NO to that, and do it manually.

Once this completes, get everything running and added to the default
[runlevel][29].

    captor zyradyl # service postgresql start
    captor zyradyl # update-rc.d postgresql enable
    captor zyradyl # service apache2 start
    captor zyradyl # update-rc.d apache2 enable

#### Database Configuration ####
Now we need to make that database. The first command defines a new user named
"*icinga*" with a password of the same name. The second command then creates a
database named *icinga*, and grants ownership to the user *icinga*.

    captor zyradyl # cd /tmp
    captor tmp # sudo -u postgres psql -c “CREATE ROLE icinga WITH LOGIN PASSWORD ‘icinga’”;
    captor tmp # sudo -u postgres createdb -O icinga -E UTF8 icinga

Once this is done, edit your [pg_hba conf][30]. This file controls the way
that hosts can authenticate with your database. We want to make sure that
icinga can utilize standard password login, or [md5][40].

    captor tmp # vim /etc/postgresql/9.4/main/pg_hba.conf

    # icinga
    local    icinga    icinga                    md5
    host     icinga    icinga    127.0.0.1/32    md5
    host     icinga    icinga    ::1/128         md5

Save, and then restart the database to activate the changes. Once restarted,
we can import the [IDO SQL][31] [schema][41] into Postgres. The [export][43]
line pushes the password into the [environment][44], thus allowing us to avoid
having to type it in.

    captor tmp # service postgresql restart
    captor tmp # export PGPASSWORD=icinga
    captor tmp # psql -U icinga -d icinga < /usr/share/icinga2-ido-pgsql/schema/pgsql.sql

Now we need to go ahead and enable the [features][42] that icinga2 will use.

    captor tmp # icinga2 feature enable command
    captor tmp # icinga2 feature enable ido-pgsql
    captor tmp # icinga2 feature enable livestatus
    captor tmp # icinga2 feature enable statusdata
    captor tmp # service icinga2 restart

The final line restarts icinga2, which is the final step to enable these
modules. Now, we need to update the configuration file for ido-pgsql to connect
it to our database that we manually set up above.

    captor tmp # vim /etc/icinga2/features-enabled/ido-pgsql.conf

Fill in the needed information as appropriate. Now you need to update the
[date.timezone][33] variable in the php configuration, which can be found
below.

    captor tmp # vim /etc/php5/apache2/php.ini

As an example, mine is set to “*America/Detroit*”. Now, to allow the web server
to pass commands to icinga2, we need to modify the *www-data* user into the
proper group. On Debian, this is the nagios [group][45].

    captor tmp # usermod -a -G nagios www-data

Finally, we need to use [icingacli][34] to generate our authentication token
to run initial setup.

    captor tmp # icingacli setup token create
    captor tmp # icingacli setup token show

The second command will show you the token should you forget it. Now open a
browser and navigate to the [localhost web page][46]. and follow along.

Something to note is that you should never select skip verification until you
have checked all your log files in */var/log*, and you are certain everything
is correct, and the programming is just being buggy. Also keep in mind that
postgres uses **port 5432**, not the default port of 3306. Finally, you should
set a password for the postgres’ database [superuser][35], because you are
going to need to use the superuser's account credentials in the setup program
to create the database.

    captor tmp # sudo -u postgres psql postgres

    # \password postgres
    Enter Password:

This will change the password, and with that, we can end this part. Next up
will be configuring hosts both capable of using the icinga2 protocol, and
configuring icinga to speak to simple hosts.

[1]:  {% post_url 2015-08-16-Icinga2-Tutorial-Part-0 %} "Icinga2 Tutorial Part 0"
<!-- [2]:  % post_url 2015-08-16-Icinga2-Tutorial-Part-2 % "Icinga2 Tutorial Part 2" -->
<!-- [3]:  % post_url 2015-08-17-Icinga2-Tutorial-Part-3 % "Icinga2 Tutorial Part 3" -->
[4]:  http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/getting-started#setting-up-icinga2 "Icinga2 Setup Guide"
[5]:  http://linux.die.net/man/8/sudo "Manual Page: Sudo"
[6]:  https://en.wikipedia.org/wiki/Debian "Wikipedia: Debian"
[7]:  http://linux.die.net/man/1/wget "Manual Page: Wget"
[8]:  http://debmon.org/ "Debmon Official Site"
[9]:  http://linux.die.net/man/8/apt "Manual Page: Apt"
[10]: http://linux.die.net/man/3/ansible.apt_key "Manual Page: apt-key"
[11]: https://wiki.debian.org/SourcesList "Debian Wiki: Apt Sources"
[12]: http://askubuntu.com/questions/222348/whhttp://manpages.org/icinga2-enable-feature/8at-does-sudo-apt-get-update-do "Ask Ubuntu: Apt-get update"
[13]: https://www.icinga.org/icinga/icinga-2/ "Icinga2 Official Website"
[14]: https://en.wikipedia.org/wiki/Nagios "Wikipedia: Nagios"
[15]: https://www.monitoring-plugins.org/ "Monitoring Plugins Official Website"
[16]: http://linux.die.net/man/8/service "Manual Page: Service"
[17]: https://github.com/Icinga/icingaweb2 "Github: IcingaWeb2 Official"
[18]: https://en.wikipedia.org/wiki/PostgreSQL "Wikipedia: PostgreSQL"
[19]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/advanced-topics#db-ido "Icinga2 DB IDO Official Documentation"
[20]: https://en.wikipedia.org/wiki/Apache_HTTP_Server "Wikipedia: Apache2"
[21]: https://en.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol "Wikipedia: LDAP"
[22]: https://en.wikipedia.org/wiki/PHP "Wikipedia: PHP"
[23]: https://en.wikipedia.org/wiki/ImageMagick "Wikipedia: ImageMagick"
[29]: https://en.wikipedia.org/wiki/Runlevel "Wikipedia: Runlevels"
[30]: http://www.postgresql.org/docs/9.3/static/auth-pg-hba-conf.html "pg_hba.conf Official Documentation"
[31]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/getting-started#configuring-db-ido-postgresql "Icinga2 IDO SQL Official Docmentation"
[33]: http://php.net/manual/en/timezones.php "date.timezone official documentation"
[34]: https://github.com/Icinga/icingaweb2/blob/master/bin/icingacli "IcingaCLI Github Repository"
[35]: https://en.wikipedia.org/wiki/Superuser "Wikipedia: Superuser"
[36]: https://en.wikipedia.org/wiki/Database "Wikipedia: Database"
[37]: https://en.wikipedia.org/wiki/Web_server "Wikipedia: Web Server"
[38]: http://www.tin.org/bin/man.cgi?section=8&topic=update-rc.d "Manpage: update-rc.d"
[39]: https://en.wikipedia.org/wiki/Data_aggregation "Wikipedia: Data Aggregation"
[40]: https://en.wikipedia.org/wiki/MD5 "Wikipedia: MD5"
[41]: https://en.wikipedia.org/wiki/Database_schema "Wikipedia: Database Schema"
[42]: http://manpages.org/icinga2-enable-feature/8 "Manpage: Icinga2 Features"
[43]: http://linux.die.net/man/1/export "Manpage: Export"
[44]: https://en.wikipedia.org/wiki/Environment_variable "Wikipedia: Environmental Variables"
[45]: https://wiki.archlinux.org/index.php/Users_and_groups "Arch Linux Official Wiki: Users and Groups"
[46]: http://127.0.0.1/icingaweb2/setup "LocalHost IcingaWeb2 Setup Module"
[47]: https://en.wikipedia.org/wiki/Software_repository "Wikipedia: Software Repository"
[48]: https://en.wikipedia.org/wiki/Key_(cryptography) "Wikipedia: Cryptographic Keys"
[49]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/getting-started#setting-up-icinga2 "Icinga2 Installation Methods"
[50]: http://linux.die.net/man/1/echo "Manpage: Echo"
[51]: http://manpages.debian.org/cgi-bin/man.cgi?query=apt-get "Manpage: apt-get"
