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
noting that I have switched over to [Debian Jessie][5], for no other reason than
to cause myself more [frustration and suffering][6]. Anyways, let's get started.

[SNMP][6] is considered an [Agent-Based Check][7], and is actually quite
flexible. You can even go as far as to code in custom return options, to check
things you normally wouldn't be able to check over snmp, for example,
[apt status][8], and other such things.

It is worth noting that due to using a very small [LAN][9], I will not be
fiddling around with [SNMPv3][10], I will be going with straight [SNMPv1][11],
just with a modified [community][12] string. We will get started with my core
router, [Djehuti][13]. It is outside the scope of this tutorial to discuss
[how to enable SNMP on your device][14], but if you use a [Ubiquiti][15] device,
hey that might come soon.

Starting from this post forward, I will be embedding code here instead of
referring to an external link, as embedding will encourage me to be a bit more
complete in my explanations. So, with all of that said, let's get started.

[1]
[2]
[3]
[4]
[5]
[6]
[7]
[8]
[9]
[10]
[11]
[12]
[13]
[14]
[15]
