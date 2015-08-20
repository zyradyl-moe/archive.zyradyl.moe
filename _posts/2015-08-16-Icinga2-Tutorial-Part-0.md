---
layout: post
title: "Icinga2 Tutorial: Part 0 - Network Monitoring for the Masses"
date: 2015-08-16 01:00:00 -05:00
license: cc0
categories:
- Icinga2
- Networking
- Tutorials
tags:
- Icinga2
- IcingaWeb2
---
* [Icinga2 Tutorial: Part 1 - Installation and Configuration][1]
* [Icinga2 Tutorial: Part 2 - Agent-Less Checks][2]
* [Icinga2 Tutorial: Part 3 - Agent-Based Checks][3]

## Introduction ##
This will be a bit more informal than most of my posts as this is more of a
hobby project than anything with really standardized applications. I’ve been
exploring the network protocols available to me on my EdgeRouter now for the
past year, and last night I sat down and taught myself [SNMP][4]. After three
hours and poking around the [MIBs][5], I realized that I absolutely hate SNMP.
That being said, I very much like the idea of it. I have to use a [CLI][6] and
a [GUI][7] on the same device most of the time to watch all the stats I like to
watch, and I immensely dislike putting that kind of rendering work on my
EdgeRouter, because I can assure you, the poor thing is taxed enough.

So I started exploring multi-protocol management systems today. Clearly,
[Spiceworks][8] is one of the best programs you can get. However, it is
[Windows][9] only. Here on Linux, I really only have [NAGIOS][10], or so I
thought.

## Enter Icinga2 ##
[Icinga2][11] is a very interesting program to me in the same way that
[Maxthon][12] was a very interesting browser. Both Maxthon 2 and Icinga 1 were
built on top of existing applications (Internet Explorer and NAGIOS,
respectively) with the  intent of expanding their functionality. I know a good
many people who would insist that NAGIOS has all the functionality you could
ever need, and I would agree, except for one thing. If I want ease of
configuration, I have to pay [$2000USD][13] for my small home network. I don’t
have that kind of money, and if I did, you can bet your ass it would go to
getting me embedded development boards or external controllers for debugging
and programming. Anyways, then Maxthon 3 came out, and it stepped away from the
[Trident][14] engine, and went with [Google's WebKit][15]. Just like Maxthon,
Icinga2 has stepped away from NAGIOS and has become its own solution, but still
remains compatibility with NAGIOS monitoring plugins.

## Purpose of These Posts ##
At first this was just going to be something fun for me to do. However, I have
noticed a somewhat disturbing lack of end to end documentation for Icinga.
Yes, all the information is there in the [manuals][16], but it is still nice to
see how someone sets it up from end to end. This series of posts is going to
show how to do just that, starting with how to install Icinga, which will go up
in the next post.

## Some Things to Consider ##
This is my first time using Icinga2. You will be learning right along with me.
Which means some posts may spend a lot of time going back and working on things
that should have already been done. Besides that, I am not using an Icinga
dedicated machine, which means I am running a desktop release,
[Linux Mint Debian Edition 2][17] to be exact. I like 2. It is a good number.
Also the distribution is really nice, and DOESN’T have [systemd][18], but
that’s a post for another day.

Additionally, my deployment is rather small. All told I will be monitoring
about 10 devices at most across four or five protocols, a fairly standard home
network. I will keep adding updates the more I learn about it, or if I add more
services. However, you may need to do some extra research to tweak things for
your deployment.

I do plan to branch off into [Netflow][19] at some point. So you can expect
that in the future.

[1]:  {% post_url 2015-08-16-Icinga2-Tutorial-Part-1 %} "Icinga2 Tutorial Part 1"
[2]:  {% post_url 2015-08-16-Icinga2-Tutorial-Part-2 %} "Icinga2 Tutorial Part 2"
[3]:  {% post_url 2015-08-17-Icinga2-Tutorial-Part-3 %} "Icinga2 Tutorial Part 3"
[4]:  https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol "Wikipedia: SNMP"
[5]:  https://en.wikipedia.org/wiki/Management_information_base "Wikipedia: MIB"
[6]:  https://en.wikipedia.org/wiki/Command-line_interface "Wikipedia: CLI"
[7]:  https://en.wikipedia.org/wiki/Graphical_user_interface "Wikipedia: GUI"
[8]:  https://en.wikipedia.org/wiki/Spiceworks "Wikipedia: Spiceworks"
[9]:  https://en.wikipedia.org/wiki/Microsoft_Windows "Wikipedia: Windows"
[10]: https://en.wikipedia.org/wiki/Nagios "Wikipedia: Nagios"
[11]: https://en.wikipedia.org/wiki/Icinga#Icinga_2 "Wikipedia: Icinga2"
[12]: https://en.wikipedia.org/wiki/Maxthon "Wikipedia: Maxthon"
[13]: https://assets.nagios.com/handouts/nagiosxi/Nagios-XI-2014-Pricing-Documentation.pdf "Official NAGIOS Pricing Information"
[14]: https://en.wikipedia.org/wiki/Trident_(layout_engine) "Wikipedia: Trident Engine"
[15]: https://en.wikipedia.org/wiki/WebKit "Wikipedia: WebKit"
[16]: http://docs.icinga.org/icinga2/latest/doc/module/icinga2/toc "Icinga2 Offcial Documentation"
[17]: https://en.wikipedia.org/wiki/Linux_Mint#Linux_Mint_Debian_Edition "Wikipedia: Linux Mint Debian Edition"
[18]: https://en.wikipedia.org/wiki/Systemd "Wikipedia: SystemD"
[19]: https://en.wikipedia.org/wiki/NetFlow "Wikipedia: Netflow"
