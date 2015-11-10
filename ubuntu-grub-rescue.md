# Ubuntu and Grub Rescue

## Grub rescue error after deleting Ubuntu installation

After accidentally formatting the HD partition contaning my Ubuntu installation, grub2 would go to grub-rescue on startup, thereby blocking access to Windows. Using:

<pre>
ls
</pre>

in the grub rescue listed the hard disk partitions [(hd0) (hd0,msdos1) (hd0,msdos5) (hd0,msdos6)] none of which was readable by grub, because none of them was the Ubuntu boot.


## Resolution

### Creating a bootable disk

<strong>Note:</strong> Live bootable Ubuntu USB flash drive created using Unetbootin on Mac (OSX 10.11.1 El Capitan) was not able to boot up the PC.

After downloading the <strong>.iso</strong> image of Ubuntu 14.04 from Ubuntu website, I used:

<pre>
hdiutil convert -format UDRW -o Downloads/ubuntu-14.04-desktop-i386 Downloads/ubuntu-14.04-desktop-i386.iso
</pre>

to convert the ISO format. After inserting the USB flash drive, I used:

<pre>
diskutil list
</pre>

to determine the USB drive based on the drive size. In my case, it was /dev/disk2. I unmounted the disk:

<pre>
diskutil unmountDisk /dev/disk2
</pre>

and created a bootable disk using:

<pre>
sudo dd if=/Users/uname/Downloads/ubuntu-14.04-desktop-i386.dmg of=/dev/rdisk2 bs=1m
</pre>

The <strong>r</strong>disk helps format the disk quickly. After the load was complete, an alert box pops up, which I left undisturbed. I ejected the disk using:

<pre>
diskutil eject /dev/disk2
</pre>

and clicked the Ignore on the previous pop-up box. The USB bootable disk was ready to use. 

### Boot and Install Ubuntu

The PC was now booted from the USB and the partition which was accidentally formatted was used to install a fresh copy of Ubuntu. 

Restart brought back the grub rescue prompt. Using <strong>ls</strong> now gives an extra partition (the Ubuntu partition) which in my case was (hd0,msdos10).

I used:

<pre>
ls (hd0,msdos10)/boot
</pre>

in the grub rescue prompt to check if the grub folder was in that directory or not (which was). Then I used:

<pre>
set root=(hd0,msdos10)
set prefix=(hd0,msdos10)/boot/grub
insmod normal 
normal
</pre>

to bring up the grub OS selection screen. Everything boots up fine but the grub rescue prompt still comes back on restart.
