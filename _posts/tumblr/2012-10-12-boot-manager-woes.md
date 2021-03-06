---
layout: post
title: Boot Manager Woes
date: '2012-10-12T02:00:00-07:00'
tags: []
tumblr_url: http://mgerow.tumblr.com/post/33421049553/boot-manager-woes
---
So, over the past few weeks I’ve been messing around with my boot setup trying to get an installation of Windows 7, Ubuntu 12.04, and Ubuntu 10.10 on my machine.  I was hoping to have Windows 7 take up most of my 1 TB hard drive, have Ubuntu 10.10 take up around 40 GB from the terabyte drive, and have Ubuntu 12.04 on my 64 GB SSD.  Should be simple enough, right?  Heh

As it turns out, Ubuntu 10.10 gets very confused when it sees anything UEFI related.  I’m still not quite sure what UEFI provides to me other than making the boot process incredibly confusing.  10.10 was so confused that it just decided to write over EFI boot partition like it was nothing important (hint, it was kinda important).  So I opted to try to force everything to use a regular BIOS style boot after 10.10 rendered my Windows 7 install unbootable.

So, I install Windows 7 like normal, and everything seems to be working.  Now I decide to install Ubuntu 12.04 on my SSD.  I get in there and notice that Windows 7, for a reason I can’t even begin to fathom, thought it should put its 100 MB boot manager on the SSD.  I try shutting the machine down and unplugging the SSD.  Sure enough, the BIOS sees no bootable drives when I start up.  I realize at this point that I probably should have just tried to move the boot manager over to the HDD, but I wanted to start working on something in Ubuntu as quickly as possible, so I just had Ubuntu try to use the rest of the SSD.  I kinda figured it would install grub and I’d just be able to do all of my boot managing out of that instead of Microsoft’s boot loader.  This is where it gets kinda insane.

So the 12.04 installation finishes, I reboot, I select the SSD as the boot drive and I boot into… Windows?  “Sure, ” I think to myself, “I guess Ubuntu just thought I would put an entry for it in the boot manager myself”.  I reboot the machine and, out of curiosity, try to boot from the Hard Drive, thinking it would just claim to not be bootable.  But it is, and it goes into my Ubuntu installation. So, let’s say Windows is on drive A and Ubuntu is on drive B.  Windows thought it would be appropriate to put its boot loader on drive B while Ubuntu thought it would be good to put its own on drive A. Wut.

I’m not sure how either decides this, but I’d almost rather just reinstall the whole thing and just switch OSes by using the BIOS.  It’s so much easier than all these silly boot managers.  Of course, I could just partition the drives myself and learn how to set up grub myself.  Maybe I will do that.
