---
layout: post
title: NixOS Part 1 - Introduction & Basic Setup
date: 2019-08-04 01:00:00
license: cc0
published: true
future: true
categories:
- Software
tags:
- Nix
---

Once again, various life stressors have resulted in me attempting to retreat
into my little shell, and whenever that happens I always seem to find something
new to work on. In this particular instance, the stress seemed to perfectly
coincide with my discovery of a new Linux distribution, [NixOS][1]. I ended up
becoming very fond of the distribution almost immediately, and after two weeks
of living on it and learning a bit more about it, I recently redid the MacBook
utilizing Nix as the sole operating system on it.

The next series of blog posts, assuming I manage to stay consistent, will be
about my journey using NixOS, why I have become so fond of it so quickly, and
other interesting things I come up with along the way.

## Why NixOS? ##
The most predominant form of package management in linux distributions tends to
be [imperative][2] package management. Even in distributions like Gen/Funtoo,
the make file is in general a static entity composed of variables that resolve
how imperative commands, such as `emerge system` are executed. Essentially, we
spend our time telling the system _how_ we want it to accomplish _what_ we
actually want, and by following the list of imperative commands we
(theoretically) end up with what we wanted.

The problem is, this doesn't always work. To quote GlaDOS, "This former Linux
Developer would like to remind you that [Dependency Hell][3] is a real place
where you **WILL** be sent at the first sign of poor imperative order control."
Essentially, because we are so focused on _how_ something will be accomplished,
if anything along that chain breaks we end up in a genuinely unfortunate
situation.

This is not an old problem, and many distributions have attempted to resolve
this. Back in 2004/2005 when I was starting to get interested in Linux I spent
some time talking to a friend about what I was using to tinker. While I cannot
remember the specific distributions, I do know that [RPM][4] was the primary
mode of package management on them. He essentially scoffed at me, and said while
he was glad I was using Linux, he wished me luck when I inevitably discovered
"RPM Hell," perhaps one of the earliest forms of dependency hell.

When I inquired as to what he was using, he told me about [Gentoo][5]. This was
a very very long time ago, where Stage 1/Stage 2/Stage 3 installations were a
thing. The benefit, I was told, is that because literally every part of the
package is imperatively defined ahead of time, you mitigate the chance of
dependency hell, because you're modifying the entire operating system as a
single unit.

This began my extremely long and at times complicated history with the
Gen/Funtoo community during my past life. I still hold an incredible soft spot
for their ecosystem, but there are situations that can occur even within the
portage subsystem, and the method of doing things imperatively often means that
the problem isn't discovered until the middle of rebuilding your system, for
example with an `emerge world`. Not to mention there _is_ a penalty, and it was
even worse during the P3/P4 days, for building everything completely from
source.

Various hybrid Binary/Source systems were developed over time, one that I
remember offhand is [Sabayon][6] which started as a layer over Gentoo if I
remember correctly, that would install unmodified packages from binary but
anything modified would be built. However, this technique just solves the speed
issue, it does nothing to resolve the imperative issues.

## How does Nix Help? ##
So Nix works by utilizing a [declarative][7] form of package management.
Essentially, instead of focusing on telling Nix _how_ we want our system, we
tell it _what_ system we want. We declare the Operating System we desire as a
function, Nix evaluates that function, and then _it_ decides _how_ it will form
that system. This is combined with transactional, atomic, procedures. Every
"Generation" of Nix is a stand alone operating system. Installing/Un-installing
packages never leaves any cruft behind because the packages are just dynamically
linked upon boot. Removing the package from our declaration removes the request
to symbolically link it to the system, which means it never existed.

Nix, in particular, is a [functional][8] package manager, an even more strict
form of declarative package management. Instead of rehashing the explanations,
I'll let the wonderful [nix webpage][9] explain the difference. NixOS is simply
an entire operating system built on top of Nix.

Beyond the benefits discussed on the official Nix website, there are other
benefits to putting the entire operating system under functional control.

As an example, let us take the boot process. Since the system we are going to
get to below utilizes encrypted swap and an encrypted root, you would expect
that we need to handle a custom initrd, custom fstab, etc etc. You would be
right! But instead of handling multiple files to get this done, we can actually
do everything right in our `configuration.nix`.

**Swap:**
```
swapDevices = [
  {
    device = "/dev/disk/by-uuid/f6533f92-baf2-4804-afda-880a7b5975ac";
    encrypted = {
      enable  = true;
      keyFile = "/mnt-root/root/swap.key"; # Believe it or not, this is correct.
      label   = "nixos-swap";
      blkDev  = "/dev/disk/by-uuid/6babbdb8-26ec-43ee-b7ab-76b43015acd3";
    };
  }
];
```
**Root FS:**
```
boot = {
  initrd = {
    luks = {
      devices = {
        decrypted-disk-name = {
          device = "/dev/disk/by-uuid/0765a1fc-6045-45af-978e-db49609bc0e3";
          keyFile = "/root.key";
        };
      };
    };
  };
};
```

Additionally, while some distributions have opted to have several tools for
building your `grub.cfg`, those still rely on modifying external files, for
example under `/etc/default` or other directories. Instead, we add it right into
our `configuration.nix` as well. We are just declaring what we want Nix to do,
whatever else it decides it needs is up to it.

**Grub Configuration:**
```
boot = {
  grub = {
    device                = "nodev"; 	              # This isn't for BIOS.
    efiInstallAsRemovable = true; 		              # Try to use Standard EFI.
    efiSupport            = true;  		              # This IS for EFI.
    enable                = true;                   # Grub is needed for our weird shit
    enableCryptodisk      = true;                   # Add LUKS support
    extraInitrd           = "/boot/initrd.keys.gz"; # LUKS Key
    zfsSupport            = true;             		# Add ZFS support
  };
};
```

Some people may note right away that we're building our key into the initrd and
may worry about security issues, but we will get to that as well!

Needless to say, literally everything that is handled in configuration files
throughout a normal linux distribution, is instead located in a centralized
file. We can break that file up and import others as well, similar to any other
programming language. Essentially, NixOS reduces the entire operating system to
a series of Nix-Language source files, and we let Nix handle all the rest!

## About Our System ##
So, for this initial article I will be discussing what I wanted out of my new
daily driver operating system, and how I went about implementing it. You can
look at my entire Nix-Configuration through the GitHub repository
[of the same name][10], but I won't be referring to any specific files yet
because the repository is going to change layouts several times throughout this
series of articles, as I attempt to convert to a more functional form of
creating my system.

So, without further ado, what are our requirements?
  - Encrypted Root Partition
  - ZFS Root Partition
  - Encrypted Boot(!) Partition, to protect our kernels and initrd
  - Encrypted Swap
  - Hibernation Support _with_ encrypted swap(!)

There are actually more requirements, but these form the basis for this article.
Before we get into it, it is worth noting that I used several different sources
to compile all the steps needed to accomplish everything.

First, for Nix on ZFS, the [NixOS Wiki Page][11] of the same name was
instrumental in solving the basic requirements of our work. We skip over ZFS
Native encryption, because while it is not leaky in relevant ways, it is still
_slightly_ leaky. From `man zfs`:

```
zfs will not encrypt metadata related to the pool structure, including dataset
names, dataset hierarchy, file size, file holes, and dedup tables.
```

Next, we need to ensure that we can encrypt the boot directory. This
[blog post][12] was instrumental in getting things to work. Had I not found this
post, its possible that I would have forgone boot partition encryption, and I'm
very glad I was able to get it done.

Last, but not least, encrypted swap with hibernation was enabled by following
the first answer to [this stack exchange question][13] with some gentle
modifications made.

Additionally, it is worth noting that the `configuration.nix` file was simply
copied from my previous trial system, and tweaks made to it. The primary reason
the repository is out of date is that while trying to get the system up and
running, I ignored most standards of aesthetics, so I'd like to get it cleaned
up proper before releasing it.

Let's get started!

## Part 1 - Live Environment Pre-Work ##
While installing the trial system, I simply used the minimal disk environment,
without a GUI, and used my phone to access documentation. However, with the
number of things I wanted to try this time, I figured it would be best to have a
graphical environment to refer to the three sources mentioned above. This poses
a unique problem on a Mac, as the proprietary NVIDIA driver is the only driver
at the time of writing that will get an X-Session up and running.

Adding additional complexity is the fact that while preparing for this process,
I disassembled my MacBook and somewhere along the way caused some sort of issue
with the IO Board, which means I was limited to only one USB 3.0 port on the
left side of the computer.

To start, I used macOS to install the new macOS beta. This was important because
the only way to update MacBook firmware is through macOS. By installing a beta
release, I was trying to get out ahead of any firmware updates to be released in
the next six months. Fingers crossed, this is all that will be required, and we
won't need to figure out how to get macOS back because of a new firmware
exploit.

Once the beta was installed, and everything was good to go, I downloaded a quick
live-image of ElementaryOS (I knew the Nix live image would be a problem, and
wanted to wait till the system was ready for installation to deal with it), and
used Etcher to write it to a USB disk.

Rebooting into ElementaryOS, I ran a series of commands on the main SSD.
Starting with `blkdiscard`, I initiated a manual [TRIM][14] on the disk to mark
every sector as free of data. Next, I used an [ATA Secure Erase][15] command,
per the [Arch Wiki Memory Cell][16] article to reset the drive to factory
default write speed. For good measure, I ran the `--security-erase-enhanced`
form of the wipe. Finally, I ran another `blkdiscard` on the drive, just to be
_really sure_ that everything was gone.

Next, we rebooted into ElementaryOS again, this time telling GRUB I wanted the
whole live system to be stored in RAM. When this was done, I downloaded the
NixOS Graphical Install CD, and burned the ISO to the USB drive.

Rebooting, I was presented with the boot menu, and I made sure to load NixOS
entirely into RAM as well. The NixOS live CD does not contain any non-free
firmware or software, which means the Mac Broadcom WIFI chipset will not be
detected. I already needed to deal with one small issue with the live system,
and it would be easier to simply unplug the USB drive and use a USB->Ethernet
dongle to connect for the majority of the installation.

So, now we are inside the NixOS Live System, at a command prompt, and we have an
internet connection. Attempting to boot into a GUI, as expected, results in a
failure to find a valid display device. This isn't as much of an issue as it
could be on other systems, we simply need to edit `/etc/nixos/configuration.nix`
on the live-system to include `services.xserver.videoDrivers = [ "nvidia" ];`
and then run `nixos-rebuild switch`. Once this completes, we run the given
command to start the X-Session, and viola, it works. Checking all of our
networking areas, we see that we have a proper internet connection, and we can
move on to the more fun things.

## Part 2 - Disk Configuration ##
This part is fairly straight forward. We use `gdisk` to set up three partitions.
The first partition is for our EFI Boot Partition, the second will be for our
Swap partition, made large enough to handle hibernation plus a little extra, and
the third is our new root partition, where ZFS will live.

```
$ gdisk /dev/sda
GPT fdisk (gdisk) version 1.0.4

Partition table scan:
  MBR: not present
  BSD: not present
  APM: not present
  GPT: not present

Creating new GPT entries in memory.

Command (? for help): o
This option deletes all partitions and creates a new protective MBR.
Proceed? (Y/N): Y

Command (? for help): n
Partition number (1-128, default 1): 1
First sector (34-2097118, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-2097118, default = 2097118) or {+-}size{KMGTP}: +200M
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): ef00
Changed type of partition to 'EFI System'

Command (? for help): n
Partition number (1-128, default 2): 2
First sector (34-2097118, default = 2048) or {+-}size{KMGTP}:
Last sector (2048-2097118, default = 2097118) or {+-}size{KMGTP}: +20G
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): 8200
Changed type of partition to 'Linux swap'

Command (? for help): n
Partition number (2-128, default 3): 3
First sector (34-2097118, default = 411648) or {+-}size{KMGTP}:
Last sector (411648-2097118, default = 2097118) or {+-}size{KMGTP}:
Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300):
Changed type of partition to 'Linux filesystem'

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to /dev/sda.
The operation has completed successfully.
```

Now we can take a look at what we have:

```
$ gdisk /dev/sda
GPT fdisk (gdisk) version 1.0.4

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.

Command (? for help): p
Disk /dev/sda: 977105060 sectors, 465.9 GiB
Model: APPLE SSD SM0512
Sector size (logical/physical): 512/4096 bytes
Disk identifier (GUID): C35223A0-E004-474E-8B79-230B64658AB0
Partition table holds up to 128 entries
Main partition table begins at sector 2 and ends at sector 33
First usable sector is 34, last usable sector is 977105026
Partitions will be aligned on 2048-sector boundaries
Total free space is 2014 sectors (1007.0 KiB)

Number  Start (sector)    End (sector)  Size       Code  Name
   1            2048          411647   200.0 MiB   EF00  EFI System
   2          411648        42354687   20.0 GiB    8200  Linux swap
   3        42354688       977105026   445.7 GiB   8300  Linux filesystem

Command (? for help): q
```

So, everything is now set up on disk, it is time to build our filesystems.

## Part 3 - Filesystems ##
With our disk structures on place, let's talk about our filesystems. There will
be three "main" ones, but it gets a bit more complex than that. First, let's
start by setting up our new EFI System Partition

```
$ mkfs.vfat /dev/sda1
```

That solves that issue, next we need to set up our two encrypted partitions.
Despite the fact that we are going to use keyfiles, we should still establish a
typed passphrase in the event we need to tweak the partitions from outside the
operating system built on it. After setting up the encryption, we open each
encrypted container and assign it a friendly name to work with.

```
$ cryptsetup luksFormat /dev/sda2
Enter passphrase:
Verify passphrase:
Command successful.
$ cryptsetup luksFormat /dev/sda3
Enter passphrase:
Verify passphrase:
Command successful.
$ cryptsetup luksOpen /dev/sda2 nixos-swap
Enter passphrase:
Command successful.
$ cryptsetup luksOpen /dev/sda3 nixos-root
Enter passphrase:
Command successful.
```

Okay, so now we have our containers. Next step is to form the basic filesystems
inside of each one. Swap on our swap device, ZFS on our ZFS device. It is
important that we use the `/dev/disk/by-id/` entry with the UUID specification
_and_ the friendly name of the device. This makes identifying things easier when
we work with them, and helps ZFS to understand what exactly is going on.

```
$ mkswap /dev/disk/by-id/dm-uuid-CRYPT-LUKS1-deadbeef-nixos-swap
Setting up swapspace version 1, size = 20971520 KB
```

Before we get to the ZFS setup, I'd like to explain the options I am using.
While the explanations are available on the wiki page, they are restated here
for brevity.

 * `-O compression=lz4` - Disk space on an SSD is more valuable than CPU time. Using LZ4 will not impact the user experience to any discernible degree.
 * `-O normalization=formD` - Our whole filesystem will be in Unicode with this. While not really required, it could let you do some interesting things, and in general I like to use Unicode wherever possible.
 * `-O xattr=sa` - Boost performance with certain file attributes, this could become useful if I ever attempt system hardening (I likely will at some point)
 * `-O acltype=posixacl` - Required for `systemd-journald`
 * `-O mountpoint=none` - This turns off ZFS' automount machinery. In certain instances, ZFS' and NixOS' boot time automounting machinery could trigger a race condition and prevent the system from booting. This allows us you bypass that potential completely.
 * `-o ashift=12` - Force 4K sectors. It is very likely ZFS would have done this anyways, but instead of risking the chance that it would read the hardware incorrectly, I just manually declare it.

 So, with that out of the way, here is what our nice bulky `zpool` creation
 command looks like:

```
$ zpool create -O compression=lz4 -O normalization=formD -O xattr=sa -O acltype=posixacl -O mountpoint=none -o ashift=12 zroot /dev/disk/by-id/dm-uuid-CRYPT-LUKS1-deadbeef-nixos-root
```

With our zpool initialized, next we need to form the filesystems under it. We
will do three things. First, we will create a separate dataset for our `/home`
directory, so that user data is kept somewhere separate from the root partition.
Next, we will define a `root` dataset, and within that, a `nixos` dataset. This
means, should we ever want to, we could run multiple distributions off of the
same ZPool by nesting them under the `root` dataset, and pointing their `/home`
at our home dataset. Is it likely we will ever do this? No, but it would be nice
to have if we ever decided to try it!

Once again, we set our mount points to legacy to ensure that the automount
machinery has absolutely nothing to go on, preventing it from firing during
boot.

```
$ zfs create -o mountpoint=none zroot/root
$ zfs create -o mountpoint=legacy zroot/root/nixos
$ zfs create -o mountpoint=legacy zroot/home
```

Where does this leave us? As of right now, we have our EFI System Partition,
freshly created with nothing on it. We have our Swap Partition, nested within a
LUKS encrypted volume, and we have our three ZFS datasets, within our `zroot`
zpool, within a LUKS encrypted volume.

Not yet done, we need to mount everything to the proper locations, and then do
some additional work to make sure everything will boot as we want it to. To
begin with, we will set up the easy ZFS mount points. Next, we need to mount our
EFI System Partition to `/mnt/efi`. We do this because it will allow GRUB to
write to the EFI partition, and have that point to our actual, _encrypted_, boot
directory, which we also create here. Lastly, for some additional work we will
do here in a moment, we manually create the `/root` directory.

```
$ mount -t zfs zroot/root/nixos /mnt
$ mkdir /mnt/home
$ mount -t zfs zroot/home /mnt/home
$ mkdir /mnt/efi
$ mount /dev/sda1 /mnt/efi
$ mkdir /mnt/boot
$ mkdir /mnt/root
```

Now, we mentioned above that we would like to have the system automatically
unlock. This is secure, because before we can even access GRUB directly, we will
have to type in our decryption passphrase for our nixos-root partition.
Essentially, everything we are about to do will be encrypted based on that
master passphrase anyways, so there's no real chance of a leak occurring.

To do this, we will create two binary keyfiles. `swap.key` will be the binary
key for the Swap partition, and `root.key` the binary key for the root
partition. We will use LUKS to assign those keys to their respective LUKS
encrypted volumes, allowing the volumes to be decrypted both with a binary
keyfile and a passphrase. The `root.key` file will then be packaged into a CPIO
archive, and GRUB will append this to the initrd image made by NixOS.

During boot, we will type in our master password to unlock GRUB, select our boot
entry, and then GRUB will hand over control to the initrd after appending our
CPIO archive. During boot, the initrd will unlock `/dev/sda3` using `root.key`
and then hand over control to `systemd`. SystemD will continue the boot, and
then load `swap.key` to unlock the swap partition. Since the swap partition is
not encrypted randomly each time, this process can be repeated, thus enabling
hibernation to function properly.

The end result of all of this is that a single master passphrase only needs to
be entered once to allow the system to boot properly. Without this method, we
would have to re-enter the `nixos-root` passphrase twice, and the `nixos-swap`
passphrase once. I am not sure, but this also might break our hibernation
capabilities.

Let's get started. First we will create our binary keyfiles from `/dev/urandom`,
then assign them to the volumes, then create the CPIO archive and stash it where
it needs to be.

```
$ dd count=4096 bs=1 if=/dev/urandom of=/mnt/root/root.key
$ dd count=4096 bs=1 if=/dev/urandom of=/mnt/root/swap.key
$ cryptsetup luksAddKey /dev/sda2 /mnt/root/swap.key
Enter passphrase:
Command successful.
$ cryptsetup luksAddKey /dev/sda3 /mnt/root/root.key
Enter passphrase:
Command successful.
$ cd /mnt/root
$ echo ./root.key | cpio -o -H newc -R +0:+0 --reproducible | gzip -9 > /mnt/boot/initrd.keys.gz
```

With that last string of commands, we are all set. To recap what was
accomplished in this section:

  * We created the FAT filesystem for our EFI System Partition
  * LUKS formatted /dev/sda2 and /dev/sda3 with a passphrase
  * Opened /dev/sda2 and /dev/sda3 and assigned them to nixos-swap and nixos-root, respectively
  * Created a swap FS on nixos-swap
  * Created a zpool on nixos-root
  * Created 3 datasets on nixos-root
  * Mounted everything correctly
  * Generated 2 binary keyfiles
  * Assigned each binary keyfile to its respective partition
  * Generated a CPIO archive for our initrd.

It's time to move on to installing NixOS and configuring it to make use of our
work.

## End of Part 1 ##
While I had intended for us to have a system up and running by the end of part
one, this post is close to breaking 600 lines, and to be completely honest, this
is the most I have written in quite a while. Part 2 will cover getting the
system up and running, as well as a little preview of what our
`configuration.nix` file will look like. Expect that installment to be quite a
bit smaller than this one. Finally, part 3 will deal with the initial
declarative configuration of our `/home` directory.

The end goal is that all user data will end up kept in a `~/.library` directory,
similar to the `nix-store`, and upon login, a symbolic-link farm will be built
according to our declarative home-management system. In this way, everything,
from `/etc/nixos` to various dotfiles will be kept in an easy to understand
layout, and just linked to their less-easy to understand directories by the
derivation created by home-management.

But, for another time.

[1]:  https://nixos.org/ "NixOS Homepage"
[2]:  https://en.wikipedia.org/wiki/Imperative_programming "Imperative Programming"
[3]:  https://en.wikipedia.org/wiki/Dependency_hell "It is a real place."
[4]:  https://en.wikipedia.org/wiki/RPM_Package_Manager
[5]:  https://en.wikipedia.org/wiki/Gentoo_Linux
[6]:  https://en.wikipedia.org/wiki/Sabayon_Linux
[7]:  https://en.wikipedia.org/wiki/Declarative_programming
[8]:  https://en.wikipedia.org/wiki/Functional_programming
[9]:  https://nixos.org/nix/about.html
[10]: https://github.com/zyradyl/nix-configuration
[11]: https://nixos.wiki/wiki/NixOS_on_ZFS
[12]: https://elvishjerricco.github.io/2018/12/06/encrypted-boot-on-zfs-with-nixos.html
[13]: https://unix.stackexchange.com/questions/529047/is-there-a-way-to-have-hibernate-and-encrypted-swap-on-nixos
[14]: https://en.wikipedia.org/wiki/Trim_(computing)
[15]: https://ata.wiki.kernel.org/index.php/ATA_Secure_Erase
[16]: https://wiki.archlinux.org/index.php/Solid_state_drive/Memory_cell_clearing
