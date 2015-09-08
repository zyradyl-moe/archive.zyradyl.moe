---
layout: post
title: "Icinga2 Tutorial: Part 2 - Agent-Less Checks"
date: 2015-08-16 03:00:00 -05:00
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
* [Icinga2 Tutorial: Part 0 - Network Monitoring for the Masses][1]
* [Icinga2 Tutorial: Part 1 - Installation and Configuration][2]
* [Icinga2 Tutorial: Part 3 - Agent-Based Checks][3]
* [Icinga2 Tutorial: Part 4 - Extending Checks to SNMP][4]

## Master Host Configuration ##
So in our last part we focused on getting your machine set up as the
[Icinga2 master controller][5]. Now we can focus on getting interoperability
setup. As always, this tutorial assumes you are sudo’d as root. You can do this
by running “sudo -s”. Then we need to set ourselves up as the master node on our
network.

    captor zyradyl # icinga2 node wizard
    Welcome to the Icinga 2 Setup Wizard!

    We’ll guide you through all required configuration details.

    Please specify if this is a satellite setup (‘n’ installs a master setup) [Y/n]: n
    Starting the Master setup routine…
    Please specifiy the common name (CN) [captor.zyradyl.org]:
    information/cli: Generating new CSR in ’/etc/icinga2/pki/captor.zyradyl.org.csr’.
    information/cli: Created backup file ’/etc/icinga2/pki/captor.zyradyl.org.key.orig’.
    information/cli: Created backup file ’/etc/icinga2/pki/captor.zyradyl.org.csr.orig’.
    information/base: Writing private key to ’/etc/icinga2/pki/captor.zyradyl.org.key’.
    information/base: Writing certificate signing request to ’/etc/icinga2/pki/captor.zyradyl.org.csr’.
    information/cli: Signing CSR with CA and writing certificate to ’/etc/icinga2/pki/captor.zyradyl.org.crt’.
    information/cli: Created backup file ’/etc/icinga2/pki/captor.zyradyl.org.crt.orig’.
    information/cli: Copying CA certificate to ’/etc/icinga2/pki/ca.crt’.
    information/cli: Created backup file ’/etc/icinga2/pki/ca.crt.orig’.
    information/cli: Dumping config items to file ’/etc/icinga2/zones.conf’.
    Please specify the API bind host/port (optional):
    Bind Host []:
    Bind Port []:
    information/cli: Enabling the APIlistener feature.
    information/cli: Updating constants.conf.
    information/cli: Updating constants file ’/etc/icinga2/constants.conf’.
    information/cli: Updating constants file ’/etc/icinga2/constants.conf’.
    Done.
    Now restart your Icinga 2 daemon to finish the installation!

    captor zyradyl # service icinga2 restart
    checking Icinga2 configuration.
    [...]
    captor zyradyl #

Note that in your case, you may see one or two warning messages, but if it
pertains to files already existing, don't worry about it, I also clipped all
the output that [Icinga2][6] spits out when it restarts in order to save space.
These posts are long enough as it is. After restarting, if you check the
website for your master node, you will see a whole bunch of new information.
This has also established the certificates the [icinga2 protocol][7] uses for
security.

## Host Monitoring ##
Now, while it is nice to have access to the Icinga protocol, in our case we
will be working with devices that do not make the option of installing Icinga
possible. Instead, we will be monitoring through four different
[protocols][10]: [SSH][11], [SNMP(v1 and v3)][12], [HTTP/S][13],
and [ICMP][14].

We are going to go simple in this route through the use of
[agent-less checks.][15] Agent-less checks do not rely on having a remote
program installed, and this is useful for [embedded devices][16] that may not
have enough [memory][17] to host another program.  This is also helpful if your
client only offers one or two services and it isn't worth taking the time to
install and configure a node setup. For example, you can check if SSH is
available on a remote host, or check if the HTTP server is alive, or even
simply see if the host is alive. We will start there.

Before we get into editing the actual files, Syntax highlighting can make life
a whole lot easier. Note, you should repeat this process with a non-privileged
user as that will give you a way to take a look at icinga2 configuration files
without having to be the root user.

    captor conf.d # mkdir -p ~/.vim/{syntax,ftdetect}
    captor conf.d # cd /usr/share/icinga
    icinga2/           icinga2-common/    icinga2-ido-pgsql/ icingaweb2/
    captor conf.d # cd /usr/share/icinga2-common/syntax/
    captor syntax # cp vim/syntax/icinga2.vim ~/.vim/syntax/
    captor syntax # cp vim/ftdetect/icinga2.vim ~/.vim/ftdetect/

Now test it by opening any file under the /etc/icinga2/conf.d/ directory. You
can find instructions to do this for [nano][18] [here][37]. Now, there are a lot of
ways to configure Icinga2. You can choose to use the pre-existing
[hosts.conf][19] and [services.conf][20], or, since the [conf.d][21] directory
is read on startup, you can use one file per host. I will be doing the latter,
as I think it keeps it a lot neater. The [Access point][22] is named
[Wepwawet][23], so let’s get started. (Note that you clearly do not need to
use my header method. You can also indent with tabs, I mean.. if you like that
kind of uncertainty in how the file will be displayed.)

    captor conf.d # vim /etc/icinga2/conf.d/wepwawet.conf

You can find the document, with syntax highlighting, [here][24].

So with this basic configuration, we get three checks:

 * An ICMP Echo (hostalive)
 * A HTTPS Up Check (http)
 * A SSH Up check (ssh)

For my HTTP check, the website uses HTTPS, and no HTTP site is available. So
by setting the SSL variable, we can ensure that just HTTPS is being checked.
This ensures we are getting an accurate reading. I also set warning time to
five minutes of down, and critical to 10 minutes of down. Now we need to
assure that our definition works.

    captor conf.d # /etc/init.d/icinga2 checkconfig
    checking Icinga2 configuration.
    captor conf.d # /etc/init.d/icinga2 reload
    checking Icinga2 configuration.
    Reloading icinga2 monitoring daemon: icinga2.
    captor conf.d #

Then log in to your web page, and you should see your new host and service
definition. If this works, then move on to the next section. If not, you
should consult the error messages printed out as well as the Icinga2 log that
can be found in [/var/log][25]. A useful method to check if the error is in
your configuration is to run the checks manually. Attempt to [ping][26] the
host, or [ssh][27] to the host, or access a web page. If you are unable to do
these things manually, the problem may very well be with your host.

The one final thing I would like to do is to demonstrate that you can actually
do a fairly nice amount of checks without needing to have a remote agent.
For this next example, we are going to setup a check that will verify if our
SSL certificate is still valid, or how long we have till it runs out.
Previously, this was done through an [external custom command][36] but Icinga2
now has a [TCP check plugin][28] that can do this natively.

Open your configuration file and make the edits. You can follow this file with
syntax colouring [as an example][38].

Once done, reload your configuration as demonstrated above. It is worth stating
that the reload parameter also calls checkconfig, however I prefer to run them
as two separate commands. It is also worth stating that you could combine the
SSL certificate check with the HTTPS check. I separate them because for me a
certificate expiring isn’t too big of a deal – it will only throw an error –
but the HTTPS server going down is a much bigger deal.

You can see the
[configuration of the monitoring setup for my core router here][29].

## External Resources: ##
* [Icinga2 Monitoring Remote Systems: Agentless Checks][30]
* [Icinga2 Monitoring Basics: Hosts and Services][31]
* [Icinga2 Plugin Check Commands: SSH][32]
* [Icinga2 Plugin Check Commands: SSL][33]
* [Icinga2 Monitoring Basics: Command Passing Variables][34]
* [Icinga2 Configuration Validation][35]
* [Icinga2 Check SSL Service and Certificate Expiration][36]

[1]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-0 %} "Icinga2 Tutorial Part 0"
[2]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-1 %} "Icinga2 Tutorial Part 1"
[3]: {% post_url 2015-08-17-Icinga2-Tutorial-Part-3 %} "Icinga2 Tutorial Part 3"
[4]:  {% post_url 2015-09-07-Icinga2-Tutorial-Part-4 %} "Icinga2 Tutorial Part 4"
[5]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/icinga2-client#icinga2-client-installation-master-setup "Icinga2 Official Documentation: Setting up a Master for Data Gathering"
[6]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc "Icinga2 Official Documentation"
[7]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/icinga2-client#icinga2-client-setup "Icinga2 Official Documentation: Icinga2 Client"
[10]: https://en.wikipedia.org/wiki/Communications_protocol "Wikipedia: Network Protocol"
[11]: https://en.wikipedia.org/wiki/Secure_Shell "Wikipedia: SSH"
[12]: https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol "Wikipedia: SNMP"
[13]: https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol "Wikipedia: HTTP"
[14]: https://en.wikipedia.org/wiki/Internet_Control_Message_Protocol "Wikipedia: ICMP"
[15]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/monitoring-remote-systems#agent-less-checks "Icinga2 Official Documentation: Agent-Less Checks"
[16]: https://en.wikipedia.org/wiki/Embedded_system "Wikipedia: Embedded System"
[17]: https://en.wikipedia.org/wiki/Random-access_memory "Wikipedia: RAM"
[18]: http://linux.die.net/man/1/nano "Manual Page: Nano"
[19]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/configuring-icinga2-first-steps#hosts-conf "Icinga2 Official Documentation: hosts.conf"
[20]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/configuring-icinga2-first-steps#services-conf "Icinga2 Official Documentation: services.conf"
[21]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/configuring-icinga2-first-steps#conf-d "Icinga2 Official Documentation: conf.d Directory"
[22]: https://en.wikipedia.org/wiki/Wireless_access_point "Wikipedia: Access Point"
[23]: https://en.wikipedia.org/wiki/Wepwawet "Wikipedia: Wepwawet"
[24]: http://pastebin.com/twTv3bem "Pastebin: Wepwawet v1 conf"
[25]: http://www.linfo.org/var.html "Page for info on var log"
[26]: http://linux.die.net/man/8/ping "Manual Page: Ping"
[27]: https://www.freebsd.org/cgi/man.cgi?query=ssh&sektion=1 "Manual Page: SSH"
[28]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/plugin-check-commands#plugin-check-command-tcp "Icinga2 Official Documentation: TCP Check Plugin"
[29]: http://pastebin.com/NnJCYuLM "Pastebin: Djehuti v1 Conf"
[30]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc#!/icinga2/latest/doc/module/icinga2/chapter/monitoring-remote-systems#agent-less-checks "Icinga2 Official Documentation: Agent-less Checks."
[31]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc#!/icinga2/latest/doc/module/icinga2/chapter/monitoring-basics#hosts-services "Icinga2 Official Documentation: Hosts and Services."
[32]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc#!/icinga2/latest/doc/module/icinga2/chapter/plugin-check-commands#plugin-check-command-ssh "Icinga2 Official Documentation: SSH Plugin."
[33]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc#!/icinga2/latest/doc/module/icinga2/chapter/plugin-check-commands#plugin-check-command-ssl "Icinga2 Official Documentation: SSL Plugin."
[34]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc#!/icinga2/latest/doc/module/icinga2/chapter/monitoring-basics#command-passing-parameters "Icinga2 Official Documentation: Passing Variables."
[35]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc#!/icinga2/latest/doc/module/icinga2/chapter/cli-commands#config-validation "Icinga2 Official Documentation: Configuration Validation."
[36]: http://namsep.blogspot.com/2014/07/icinga-2-https-and-ssl-key-expiry-check.html "Blog Entry on Custom Command to Check SSL Expiration"
[37]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/chapter/getting-started#configuration-syntax-highlighting-nano "Icinga2 Official Documentation: Nano Syntax Highlighting"
[38]: http://pastebin.com/H7TMnCpQ "Pastebin: Wepwawet v2"
