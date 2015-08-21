---
layout: post
title: Icinga2 Tutorial Part 4 - Expanding Checks to SNMP
---

SNMP is a very interesting protocol in that I consider it to be both an
agentless and an agent based check. Obviously, there is nothing that you really
need to install on most devices, which would suggest agentless, but you have
to enable the service, which implies an agent. Icinga2′s documentation
considers it to be agent based.

Install the plugins we will need using “apt-get install nagios-snmp-plugins”.

The first thing you need to do is open up your primary Icinga2 configuration
and add in the include to enable these plugins. This file is located at
/etc/icinga2/icinga2.conf. While you are in here, if you are anything like me,
you will notice that there are a few things in here that you don’t need. Don’t
worry, I plan to include a guide on these configuration files, as well as users
and groups, and establishing a notification method, soon. Find the line that
says “include <itl>” and add “include <manubulon>”. You do not have to reload
the configuration, but I did anyways just to make sure there were not any
errors. You can do this with “service icinga2 reload”

We are going to start with Djehuti. While Djehuti is able to use version
three, which is what I plan on doing, and thus makes configuration a bit more
difficult, Wepwawet will actually require some custom fetch commands to be
written, and I would like to save that for another article. Djehuti also has
some custom commands, but nothing as in depth as wepwawet (or so I think.)





External Resources
Icinga2 Check Plugins - SNMP Manubulon Plugins
