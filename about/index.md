---
layout: page
title: About
---

> **“And folks, let’s be honest. Sturgeon was an optimist. Way more than**
> **90% of code is crap.”** ~ *Al Viro*

## Character Statistics ##
**Name:**       Natalie  
**Level:**      29  
**Gender:**     Female  
**Class:**      Dual Class - Healer/Technomage  
**Occupation:** Mental Health Specialized CPhT/Pharm Student  

## Combat Statistics ##
**STR:** 08  
**DEX:** 15  
**CON:** 10  
**INT:** 18  
**WIS:** 06  
**CHA:** 06  
**SAN:** 08  

## Blurb ##
Hi, my name is Natalie and I’m a 29 year old Pharmacy student based out of
Missouri. When I’m not reading up on the latest and greatest pharmaceuticals
I like to take things apart. Anything. A car, a computer, dumping compiled
code into object code to see how it works. Running network scans on my ISP
to try and figure out why PMTUD on IPv6 is broken and I have to manually set
an MTU of 1280 on my router… If you can think of something, I’ve probably
done it.

Right now I am especially interested in ~~embedded systems and networking, which
is why it makes up so much of my content current~~ large sets of data and
efficient synchronization, however, this may change at a moments notice. I like
embedded systems because they still require an extreme amount of efficiency. The
previous sentence is still true, but I've moved on to working with large amounts
of data. Data transactions need to be precise and unfaltering, or you are
immediately penalized. That's where I tend to spend most of my time now.

As for the style of computer user I am, I believe it can be summed up by a
small list of view points:

 * My favourite processor is [SPARC][1]. This is followed by [MIPS][2],
   [AVR32][3], [ARM][4], and last and certainly least, [x86 and x64][5]. The
   last x86 processor I actually liked was when [Cyrix][6]
   [reverse engineered][7] [Intel’s Pentium][8] via [clean room engineering][9]
   and produced a faster processor, which Intel
   [then stole and made into the Pentium Pro.][10]
 * I think [microkernels][11] are the golden standard of what should be
   implemented in an operating system design.
 * [Modular kernels][12] are the worst thing to ever happen to security. (I
   will be nice and not go into the fragmentation penalty.)
 * [Dynamic linking][13] is
   [one of the worst things to ever happen to programming][14].
   ([Library Symbol Versioning Hell][15])
 * [SystemD][16] is the worst thing to ever happen to [Linux][17], and that’s
   [saying something][18]. (GNU is Not Usable)
      * While my viewpoint on this has not changed, it seems that I am in a
        rapidly diminishing minority. As such, my current linux systems use
        SystemD because that is what they're shipped "stable" with. I have
        recently had to start looking into writing unit files.
      * Refer to the final bullet point below for my views on widely used
        software.
 * In a past life, I was a developer for a source based linux distribution
   where I spent literally all of my time attempting to make linux packages
   build for [Clang/LLVM][19] and yelling at upstream for using GNU specific
   extensions.
 * [DragonFlyBSD][20] is the most exciting operating system of today.
 * ~~[NetBSD][21] should be the standard of Embedded System Development.~~
    * Recently, an [article was written][41] that stated the BSDs may be dying.
      While I still greatly prefer the BSD architecture, the fact is that fewer
      and fewer people are deploying it, and as the user-base dwindles, the
      amount of support will dwindle as well.
    * With this in mind, while NetBSD __SHOULD BE__ the standard of Embedded
      system development, I am ready to concede that it might not be. Instead,
      you should look for a Realtime Operating System, or consider Open/FreeBSD.
    * This might be one of the most painful updates made yet to this page. I
      really do love NetBSD.
 * [GPL][22] is ~~just as bad, if not~~ worse, than standard copyright.
    * Time has only further hardened my view on copyleft licensing. Copyfree is
      the only license I will ever use.
 * [GPLv3][23] is the [single][24] [most][25] [terrifying][26] [thing][27]
   to ever happen to GNU, and that is [REALLY saying something][28].
 * “There are two ways of constructing a software design: One way is to make it
   so simple that there are obviously no deficiencies and the other way is to
   make it so complicated that there are no obvious deficiencies.” ~ C.A.R.
   Hoare.
      - I believe [Object Oriented Programming][29] is the latter. (OOPs..)
 * It is highly likely that the days of good code are over, because when
   given the choice between easy and terrible ([Java][30], [Javascript][32])
   versus hard but stable ([Haskell][33],[C][34],[Go][35]) ~~the entire~~ most
   of the [computer science][36] world has embraced the first.
    * I previously listed [Python][42] as a member of the first set of
      languages. With my recent dabbling in AI, I have been impressed with the
      performace python is capable of obtaining. Additionally, Cython solves one
      of my primary complaints which was the inability to compile the language.
      With that said, I still do not believe Python should be used to handle
      important tasks. Even the backup system I am currently favoring
      ([BorgBackup][43]) implements the most serious portion of its codebase in
      C. Things like Portage still baffle me, even though I worked on them. At
      that low of a level, you should be shipping compiled binaries, not
      interpreted languages.
    * [Ruby][44] was also a former member of the first group of languages. With
      this section under way, let me start by saying _Ruby should absolutely
      not be used to write "programs"_. Ruby on Rails is a horrifying concept
      to me, but web developers have always been scary. All that being said,
      Ruby can be an absolutely wonderful scripting language. If you need
      something stronger than shell and don't want to write a full application,
      Ruby fits nicely into that niche as long as the script _does not need to
      be performant._ It is still horrifyingly slow.
    * Also, fuck the idea of writing full programs in Javascript.
 * I don’t believe that just because you have a 1TB Hard Drive that you can
   justify or allow a 16+ Gigabyte operating system.
 * I don’t believe just because you have CPU cycles you should waste them
   on abstraction compared to low level clean code.
 * I don’t believe that any program other than those handling data sets of
   over 512MB should EVER use 1GB or more of RAM.
 * [SQL Databases][37] are so horrible that the designer will get into heaven
   simply because the devil doesn’t have time to deal with that shit.
    * I've recently begun to quite like SQLite3. They're still fucking horrific
      to work with, but SQLite3 makes things nice by having a single file.
    * While we're on the topic of databases, KV Stores are really neat.
 * [Git][38] is the only sane version control software.
    * I'm starting to like [Mercurial][45] though.

 **Last, but certainly not least:**

 * Just because everyone uses a software, a software is easy, you are mandated
   to use a software, or the software/protocol is old **does not mean
   you should live in a delusional world about how bad it is.** ([*I’m looking
   at you,*][40] [*SSH*][39].)


   > **“Zyradyl is a 72 year old male Bell Labs Employee trapped in the**
   > **wrong body.”** - *Asaph*

[1]:  https://en.wikipedia.org/wiki/SPARC "Wikipedia: SPARC"
[2]:  https://en.wikipedia.org/wiki/MIPS_instruction_set "Wikipedia: MIPS"
[3]:  https://en.wikipedia.org/wiki/AVR32 "Wikipedia: AVR32"
[4]:  https://en.wikipedia.org/wiki/ARM_architecture "Wikipedia: ARM"
[5]:  https://en.wikipedia.org/wiki/X86 "Wikipedia x86"
[6]:  https://en.wikipedia.org/wiki/Cyrix "Wikipedia: Cyrix"
[7]:  https://en.wikipedia.org/wiki/Cyrix_6x86 "Wikipedia: Cyrix 6x86"
[8]:  https://en.wikipedia.org/wiki/Pentium "Wikipedia: Pentium"
[9]:  https://en.wikipedia.org/wiki/Chinese_wall "Wikipedia: Clean Room"
[10]: https://en.wikipedia.org/wiki/Cyrix#Legal_troubles "Wikipedia: Cyrix Litigation"
[11]: https://en.wikipedia.org/wiki/Microkernel "Wikipedia: Microkernel"
[12]: https://en.wikipedia.org/wiki/Kernel_(operating_system)#Hybrid_.28or_Modular.29_kernels "Wikipedia: Modular Kernels"
[13]: https://en.wikipedia.org/wiki/Dynamic_linker "Wikipedia: Dynamic Linker"
[14]: https://groups.google.com/forum/#!original/comp.os.plan9/pI20HoBExSg/VNRaQaQ_fO8J "Rob Pike Dynamic Libraries"
[15]: https://groups.google.com/d/msg/comp.os.plan9/x3s1Ibaj_l8/KQ8mrC8jxEIJ "Version Hell"
[16]: https://en.wikipedia.org/wiki/Systemd "Wikipedia: SystemD"
[17]: https://en.wikipedia.org/wiki/Linux "Wikipedia: Linux"
[18]: https://en.wikipedia.org/wiki/GNU "Wikipedia: GNU"
[19]: https://en.wikipedia.org/wiki/LLVM "Wikipedia: LLVM"
[20]: https://en.wikipedia.org/wiki/DragonFly_BSD "Wikipedia: DragonFlyBSD"
[21]: https://en.wikipedia.org/wiki/NetBSD "Wikipedia: NetBSD"
[22]: https://en.wikipedia.org/wiki/GNU_General_Public_License "Wikipedia: GPL"
[23]: https://en.wikipedia.org/wiki/GNU_General_Public_License#Version_3 "Wikipedia: GPL3"
[24]: https://www.youtube.com/watch?v=PaKIZ7gJlRU "Torvald's on GPL3"
[25]: https://lwn.net/Articles/200422/ "Kernel Developers on GPL3"
[26]: https://www.datamation.com/open-source/7-reasons-why-free-software-is-losing-influence_2.html "FSF Losing Influence"
[27]: https://osdelivers.blackducksoftware.com/2013/01/25/whos-afraid-of-gpl3/ "Who is afraid of GPL3"
[28]: https://www.gnu.org/software/automake/ "Automake Hell"
[29]: https://en.wikipedia.org/wiki/Object-oriented_programming "OOPs"
[30]: https://en.wikipedia.org/wiki/Java "Wikipedia: Java"
[31]: https://en.wikipedia.org/wiki/Python_(programming_language) "Wikipedia: Python"
[32]: https://www.javascript.com/ "Wikipedia: Ruby"
[33]: https://www.haskell.org/ "Haskell Website"
[34]: https://en.wikipedia.org/wiki/C_(programming_language) "Wikipedia: C"
[35]: https://en.wikipedia.org/wiki/Go_(programming_language) "Wikipedia: Go"
[36]: https://en.wikipedia.org/wiki/Computer_science "Wikipedia: Computer Science"
[37]: https://en.wikipedia.org/wiki/SQL "Wikipedia: SQL"
[38]: https://en.wikipedia.org/wiki/Git_(software) "Wikipedia: Git"
[39]: https://en.wikipedia.org/wiki/Secure_Shell "Wikipedia: Secure Shell"
[40]: https://interviews.slashdot.org/story/04/10/18/1153211/Rob-Pike-Responds "Cat-V: On SSH"
[41]: https://www.csoonline.com/article/3250653/open-source-tools/is-the-bsd-os-dying-some-security-researchers-think-so.html "Is BSD Dying?"
[42]: https://www.python.org/ "Python Homepage"
[43]: https://borgbackup.readthedocs.io/en/stable/index.html "BorgBackup Homepage"
[44]: https://en.wikipedia.org/wiki/Ruby_(programming_language) "Wikipedia: Ruby"
[45]: https://en.wikipedia.org/wiki/Mercurial "Wikipedia: Mercurial"
