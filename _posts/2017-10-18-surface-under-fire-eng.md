---
layout: single
title: A Tale of Reformatting My Surface Pro 3
date: 2017-10-18 09:07:30.000000000 +09:00
header:
  teaser: "/assets/images/surface_broken.jpg"
  image: "/assets/images/surface_broken.jpg"
type: post
categories:
- IT
tags: [Surface Pro3, format]
---

I had to reinstall the OS on my Microsoft Surface Pro 3 (hereafter "the Surface"). Conveniently, the Surface offers a variety of reinstall and reset options. But I don't trust that kind of thing. It's because of a painful memory from the Windows 95 days, when I rounded up 30 floppy disks, made a backup, formatted, and ended up losing everything.

![floppy disk](/assets/images/floppy-disk.jpg)
Memories of bad sectors...

I'm quick to make decisions about this sort of thing. The moment I hit it with a clean using [DiskPart], the Surface returned, however briefly, to a world of peace. Then I decided to reinstall Windows 10 the way I always do. With the Surface, a clean install of Windows is a bit trickier than on other devices. Laid out in the order of operations, here's what it looks like:

1. Format the Windows installation USB as FAT32.
2. Download the Windows installation files to put on the USB from the [official page](https://support.microsoft.com/ko-kr/surfacerecoveryimage).
3. Copy the downloaded image onto the USB as-is.
4. Change the UEFI settings as follows.
 - Set the boot order to USB > SSD.
 - Disable secure boot control.
5. Insert the USB into the Surface.
6. During boot, don't be alarmed if the screen turns red along with the Surface logo (this is because of the secure boot control setting change).
7. If it doesn't work, refer to the [official Surface recovery or reset documentation].

I said it was a bit tricky above, but compared to Windows 95 or other old operating systems, it's nothing. Everything should have gone smoothly, but for some reason this particular error kept happening to me.

![windows-boot-manage-install-error](/assets/images/windows-install-error.jpg)

In text, the message reads like this.
```
Windows failed to start.  A recent hardware or software change might be the cause.  To fix the problem :
1. Insert your windows installation disk and restart your computer.. 
2. 3. blah blah blah..

File:  \EFI\Microsoft\Boot\BCD
Status:  0xc000000f
Info: The boot configuration data for your pc is missing or contains errors. 
```

Once I ran into the problem above, no matter what I did, I couldn't move on to the next step. Googling turned up nothing. At least not on the Korean-language web. So I searched in English. My search term was `surface Pro3 0xc000000f boot bcd`. A [Q&A post](https://answers.microsoft.com/en-us/windows/forum/windows_10-update/reset-surface-pro-3/f587ee8f-6a68-45fb-ac9e-6cc89c9936a1) came up right away, and to summarize, it said the following:

> ## Take out the MicroSD card!

I'll wrap up here, hoping no Korean wastes their life needlessly the way I did. [_ K], you saved my life!

[DiskPart]: <https://technet.microsoft.com/ko-kr/library/cc766465%28v=ws.10%29.aspx?f=255&MSPPError=-2147217396>
[official Surface recovery or reset documentation]: <https://support.microsoft.com/ko-kr/help/4023533/surface-restore-or-reset-surface>
[_ K]: <https://answers.microsoft.com/en-us/profile/bb629687-c2c5-4168-a6e0-263237695c37>
