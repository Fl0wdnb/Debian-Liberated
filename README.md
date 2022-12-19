# How to Liberate Debian from SystemD using only Debian.
<br>
For those who hate SystemD but want to keep Debian, a Gentoo user has decided to help you out a bit. Debian has a guide on how to install SysVinit but it isn't complete, so this guide will contain the proper way. No it does NOT involve Devuan or another repository, this can be done straight from the way Debian is setup.

First off, you need the Debian live minimal image. This will come in handy to chroot on a preexisting installation but can also serve as installation media.

After acquiring the media, flash it to a USB, write to a CD, boot in VM, etc. However you plan to use the medium, just prepare it accordingly.

Boot the image and install normally up to your desktop. Now what you want to do is install a base system. Forget about GRUB or a desktop environment.

After you've installed just enough for a chroot (apt included) reboot into live mode on your debian live minimal image. You can use another live image if you prefer a GUI, but a terminal is still absolutely necessary.

Use "lsblk" to find the partition containing Debian as a root filesystem (generally Debian sets up as 2 or 3 partitions: boot \[UEFI/GPT only\] root, swap). Replace sdXy with the designation of the drive and Debian ROOT partition (generally the largest unless you have seperate root and home partitions). Mount proc, sys, dev, and run similarly to the Gentoo wiki.

* sudo su (Verification, but not necessary. You are chrooting, you should already be in root)
* mount /dev/sdXy /mnt
* mount --types proc /proc /mnt/proc
* mount --rbind /sys /mnt/sys
* mount --make-slave /mnt/sys
* mount --rbind /dev /mnt/dev
* mount --make-rslave /mnt/dev
* mount --bind /run /mnt/run (Pay attention here, as /run has --bind not --rbind)
* mount --make-slave /mnt/run (Tag should be --make-slave not --make-rslave)

Run "apt update" and "apt --download-only install \[pick one: openrc, sysvinit-core, or runit\] libpam-elogind elogind" IT IS IMPORTANT TO ONLY PICK ONE INIT SYSTEM!

Run "apt install /var/cache/apt/archives/\*.deb"

Run "apt install grub". After grub installs, run "grub-install /dev/sdX && grub-mkconfig -o /boot/grub/grub.cfg"

Assuming you haven't done so, now is a good time to install a Desktop or at least networking daemons. You'll also want to start the network daemon according to your init system's instructions.

Exit chroot and reboot into the Debian partition

Enjoy your new cleanly liberated Debian. The advantages over Devuan include more support and newer packages.
