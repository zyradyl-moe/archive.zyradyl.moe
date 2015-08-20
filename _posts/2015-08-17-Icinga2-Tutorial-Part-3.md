---
layout: post
title: "Icinga2 Tutorial: Part 3 - Network Monitoring for the Masses."
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
* [Icinga2 Tutorial: Part 0][1]
* [Icinga2 Tutorial: Part 1][2]
* [Icinga2 Tutorial: Part 2][3]

For this part of the tutorial, we are actually going to be rewriting the host
file for localhost. Unfortunately, I thought the EdgeOS would be capable of
running an Icinga2 client, but it isn’t available through the apt-repositories,
and after the ntopng incident of July and August, I am not stressing that
processor any more than it needs to be to run my network. (It is stressed
enough as it is, but that is a post for another day.)

Since I have done most of the explaining in the files, these tutorials will
start including links to the configuration files, and any external resources
I have used.

You can find the configuration file for the agent checks here.

[1]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-0 %} "Icinga2 Tutorial Part 0"
[2]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-1 %} "Icinga2 Tutorial Part 1"
[3]: {% post_url 2015-08-16-Icinga2-Tutorial-Part-2 %} "Icinga2 Tutorial Part 2"
