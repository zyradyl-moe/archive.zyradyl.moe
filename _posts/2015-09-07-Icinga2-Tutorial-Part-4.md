---
layout: post
title: Icinga2 Tutorial Part 4 - Expanding Checks to SNMP
date: 2015-09-07 16:18:00 -05:00
license: cc0
categories:
- Icinga2
- Networking
- Tutorials
tags:
- Icinga2
- IcingaWeb2
---
* [Icinga2 Tutorial: Part 0 - Network Monitoring for the Masses][1]
* [Icinga2 Tutorial: Part 1 - Installation and Configuration][2]
* [Icinga2 Tutorial: Part 2 - Agent-Less Checks][3]
* [Icinga2 Tutorial: Part 3 - Agent-Based Checks][4]

## Introduction ##
Well I have finally persuaded myself to continue writing these posts by
completely deleting all the configuration I had already set up. It is worth
noting that I have switched over to Debian Jessie, for no other reason than
to cause myself more [frustration and suffering][5]. Anyways, let's get started.

SNMP is considered an [Agent-Based Check][6], and is actually quite
flexible. You can even go as far as to code in custom return options, to check
things you normally wouldn't be able to check over snmp, for example,
[apt status][7], and other such things.

It is worth noting that due to using a very small LAN, I will not be
fiddling around with SNMPv3, I will be going with straight SNMPv1,
just with a modified community string. We will get started with my core
router, Djehuti. It is outside the scope of this tutorial to discuss
how to enable SNMP on your device, but if you use a Ubiquiti device,
hey that might come soon.

Starting from this post forward, I will be embedding code here instead of
referring to an external link, as embedding will encourage me to be a bit more
complete in my explanations. So, with all of that said, let's get started.

## Initial Setup ##
To monitor SNMP we will be using the [Manubulon SNMP Plugins][8]. So we first
need to install them.

    zyradyl@captor:~$ sudo apt-get install nagios-snmp-plugins

Now we need to open up the main Icinga2 Configuration file and add in the
proper include to allow us to use these plugins. You may notice while poking
around this file that there are many things you either don't need or would like
to change. I do plan to come back to this file at a later time, but feel free to
edit this file before that happens. Once you have made the proper changes,
restart Icinga2 so the new settings take effect.

    zyradyl@captor:~$ sudo vim /etc/icinga2/icinga2.conf

    include <manubulon>

    zyradyl@captor:~$ sudo service icinga2 restart

With that, we can move on to creating configuration files!

## Djehuti ##
We will be starting with my core router, which is running SNMPv1. The first
thing we will want to do is to add some essential variables to our host
directive so that we don't have to redefine them with every service.

    //
    // Host Declaration Block
    //
    object Host "djehuti.zyradyl.org" {
        // Define the host IPv4 Address
        address             = "10.0.0.1"
        // Define a basic functionality test
        // Hostalive does a basic ICMP ECHO to the target
        // specified in the address directive.
        check_command       = "hostalive"
        // Define SNMP Variables
        vars.snmp_address   = "10.0.0.1"
        vars.snmp_community = "zyradyl"
        // These are not strictly needed. I add them
        // so I know at a glance what version of snmp
        // I am using.
        vars.snmp_v2        = "false"
        vars.snmp_v3        = "false"
    }

The new additions are any of the _var.snmp*_ commands located under the
*check_command* line. With our host variables set up, we can now move on to
defining a service. The first service defined in the
[Icinga2 Manubulon Documentation][9] is the *snmp-load* check. Seems like a
good starting place to me!

    //    
    // Service Declaration Block
    // Service:     snmp_load
    // Description: Uses SNMP commands to check the load averages
    //              on the device.
    //
    object Service "snmp-load" {
        host_name      = "djehuti.zyradyl.org"
        // Set the Load Average warning threshold.
        vars.snmp_warn = "5.00, 3.00, 2.00"
        // Set the Load Average critical threshold.
        vars.snmp_crit = "6.00, 5.00, 3.00"
        check_command  = "snmp-load"
    }



[1]
[2]
[3]
[4]
[5]
[6]
[7]
