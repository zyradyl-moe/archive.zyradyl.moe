---
layout: post
title: "Icinga2 Tutorial: Part 3 - Agent-Based Checks"
date: 2015-08-17 00:00:00 -05:00
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
* [Icinga2 Tutorial: Part 2 - Agent-Less Checks][3]
* [Icinga2 Tutorial: Part 4 - Extending Checks to SNMP][4]

For this part of the tutorial, we are actually going to be rewriting the host
file for localhost. Unfortunately, I thought the EdgeRouter would be
capable of running an [Icinga2][5] client, but it isn’t available through the
apt repositories, and after the ntopng incident of July and August, I am
not stressing that processor any more than it needs to be to run my network.
(It is stressed enough as it is, but that is a post for another day.)

Since I have done most of the explaining in the files, these tutorials will
start including links to the configuration files, and any external resources
I have used.

[You can find the configuration file for the agent checks here.][6]

**Import Notes**
It is worth noting that this article was intended to be a quick write up to
get something online. In time, I may come back to rewrite things, but at the
very least I hope the comments in the pastebin file are helpful.
*~Nat*

[1]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-0 %} "Icinga2 Tutorial Part 0"
[2]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-1 %} "Icinga2 Tutorial Part 1"
[3]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-2 %} "Icinga2 Tutorial Part 2"
[4]:  {% post_url 2015-09-07-Icinga2-Tutorial-Part-4 %} "Icinga2 Tutorial Part 4"
[5]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc "Icinga2 Official Documentation"
[6]: http://pastebin.com/KEfuNcEt "Pastebin: Captor Configuration"
