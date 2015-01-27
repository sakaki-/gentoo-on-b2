# gentoo-on-b2

Bootable live-USB of Gentoo Linux for the Excito B2 miniserver, with Linux 3.18.2

## Description

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/excito/b2/Excito_b2.jpg" alt="Excito B2, aka Bubba|TWO" width="250px" align="right"/>
This project contains a bootable, live-USB image for the Excito B2 (aka Bubba|TWO) miniserver. You can use it as a rescue disk, to play with Gentoo Linux, or as the starting point to install Gentoo Linux on your B2's main hard drive. You can even use it on a diskless B2. No soldering, compilation, or [U-Boot](http://www.denx.de/wiki/U-Boot/WebHome) flashing is required! You can run it without harming your B2's existing software; however, any changes you make while running the system *will* be saved to the USB (i.e., there is persistence). Also, as with its [sister B3 project](https://github.com/sakaki-/gentoo-on-b3), a number of useful software packages (web server, mail server etc.) are included precompiled with the image (in their 'freshly emerged' configuration state), for convenience.

The kernel used in the image is **3.18.2** from gentoo-b2-sources (i.e., the [kernel.org](https://www.kernel.org) sources, with Gentoo and [B2-specific](https://github.com/sakaki-/gentoo-b2-kernel-patches/) patches applied). The `.config` used for the kernel may be found [here](https://github.com/sakaki-/gentoo-on-b2/blob/master/configs/b2_live_usb_config) in the git archive.

The images may be downloaded from the links below (or via `wget`, per the following instructions). Most people will want to use the `genb2img.xz` variant - the diskless version should *only* be used if you have no drive installed in your B2; it will fail to boot on a standard system (and vice versa). I have also provided a small `blinktest` image that you can use to very quickly establish if your USB key will work correctly with U-Boot; see [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Quickly-Testing-your-USB-Key-for-Compatibility).

Variant | Image | Digital Signature
:--- | ---: | ---:
B2 with Internal Drive | [genb2img.xz](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2img.xz) | [genb2img.zx.asc](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2img.xz.asc)
Diskless B2 | [genb2disklessimg.xz](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2disklessimg.xz) | [genb2disklessimg.zx.asc](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2disklessimg.xz.asc)
USB Key Compatibility Tester | [blinktestimg.xz](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/blinktestimg.xz) | [blinktestimg.xz.asc](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/blinktestimg.xz.asc)

> Please read the instructions below before proceeding. Also please note that these images are provided 'as is' and without warranty.

## Prerequisites

To try this out, you will need:
* A USB key of at least 8GB capacity. I have tested it successfully with a [SanDisk Cruzer Blade 8GB USB 2.0 key](http://www.amazon.co.uk/gp/product/B002U28LZC), a [Lexar 16 GB USB 3.0 key](http://www.amazon.co.uk/gp/product/B008XIYKE8), and with a [Memwah single-slot micro-SD to USB adaptor](http://www.amazon.co.uk/gp/product/B004WFT762) (plus [Samsung 32GB micro-SD card](http://www.amazon.co.uk/gp/product/B00J29BR3Y)); unfortunately, a number of other brands do not work with U-Boot's drivers. You may find this [related list of known-good USB keys](http://forum.doozan.com/read.php?2,1915,page=1) useful. If in doubt, try using my `blinktest` image first to quickly check your USB key for U-Boot compatibility; see [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Quickly-Testing-your-USB-Key-for-Compatibility).
* An Excito B2 (obviously!). If it has an internal hard drive (i.e., it runs the standard Excito software), use the `genb2img.xz` image; if using a diskless chassis, use the `genb2disklessimg.xz` instead.
* A PC to decompress the appropriate image and write it to the USB key. This is most easily done on a Linux machine of some sort, but tools are also available for Windows (see [here](http://tukaani.org/xz/) and [here](http://sourceforge.net/projects/win32diskimager/), for example). In the instructions below I'm going to assume you're using Linux.

## Downloading and Writing the Image

On your Linux box, issue:
```
# wget -c https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2img.xz
# wget -c https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2img.xz.asc
```
to fetch the compressed disk image file (324MiB) and its signature.
> If you want the 'diskless' variant (because you have no internal hard drive in your B2), use:
```
# wget -c https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2disklessimg.xz
# wget -c https://github.com/sakaki-/gentoo-on-b2/releases/download/1.0.0/genb2disklessimg.xz.asc
```
instead.

Next, if you like, verify the image using `gpg` (this step is optional):
```
# gpg --keyserver pool.sks-keyservers.net --recv-key DDE76CEA
# gpg --verify genb2img.xz.asc genb2img.xz
```
> If you downloaded the 'diskless' image, use:
```
# gpg --keyserver pool.sks-keyservers.net --recv-key DDE76CEA
# gpg --verify genb2disklessimg.xz.asc genb2disklessimg.xz
```
instead.

Assuming that reports 'Good signature', you can proceed.

Next, insert (into your Linux box) the USB key on which you want to install the image, and determine its device path (this will be something like `/dev/sdb`, `/dev/sdc` etc.; the actual path will depend on your system, you can use the `lsblk` tool to help you). Unmount any existing partitions of the USB key that may have automounted (using `umount`). Then issue:

> **Warning** - this will *destroy* all existing data on the target drive, so please double-check that you have the path correct!

```
# xzcat genb2img.xz > /dev/sdX && sync
```
> If you downloaded the 'diskless' image, use:
```
# xzcat genb2disklessimg.xz > /dev/sdX && sync
```
instead.

Substitute the actual USB key device path, for example `/dev/sdc`, for `/dev/sdX` in the above command. Make sure to reference the device, **not** a partition within it (so e.g., `/dev/sdc` and not `/dev/sdc1`; `/dev/sdd` and not `/dev/sdd1` etc.)

The write may take some time, due to the decompression (it takes between 10 and 25 minutes on my machine, depending on the USB key used). It should exit cleanly when done - if you get a message saying 'No space left on device', then your USB key is too small for the image, and you should try again with a larger capacity one.

## Specifying Required Network Settings

The Gentoo system on the image will setup the `eth0` network interface on boot (this uses the **wan** Ethernet port on the B2). However, before networking is started, it will attempt to read two files from the first partition of the USB key, namely `/install/net` and `/install/resolv.conf`; if found, these will be used to *overwrite* the files `/etc/conf.d/net` and `/etc/resolv.conf` on the USB root (in the USB key's second partition). Therefore, you can edit these two files to specify settings appropriate for your network.

In the image, `/install/net` initially contains:
> 
```
# static setup for eth0 (wan Ethernet port)
# this will be automatically brought up on boot
# edit the below to match your system
config_eth0="192.168.1.122 netmask 255.255.255.0 brd 192.168.1.255"
routes_eth0="default via 192.168.1.254"
# dynamic setup for eth1 (lan Ethernet port)
# this is not automatically started on boot
config_eth1="dhcp"
```

and `/install/resolv.conf` is:
> 
```
# use Google public DNS, as a sensible fallback
# modify this to match your system
nameserver 8.8.8.8
```

That is, as shipped, the Gentoo system will attempt to bring up the eth0 (**wan**) Ethernet interface, with a fixed address of 192.168.1.122 (NB - this is different from the default [gentoo-on-b3](https://github.com/sakaki-/gentoo-on-b3) address), netmask 255.255.255.0, broadcast address 192.168.1.255 and gateway 192.168.1.254, using Google's DNS nameserver at 8.8.8.8. If these settings are not appropriate for your network, edit these files as required (note that you will have to specify a fixed address at this stage; later, when you are logged into the system, you can configure DHCP etc. if desired). The first USB partition is formatted `fat16` and so the edits can be made on any Mac or Windows box (any [modified line endings](https://danielmiessler.com/study/crlf/) will be fixed up automatically, when the files are copied across during boot); or, if using Linux:
```
# mkdir /tmp/mntusb
# mount -v /dev/sdX1 /tmp/mntusb
# nano -w /tmp/mntusb/install/net
  <make changes as needed, and save>
# nano -w /tmp/mntusb/install/resolv.conf
  <make changes as needed, and save>
# sync
# umount -v /tmp/mntusb
```
Obviously, substitute the appropriate path for `/dev/sdX1` in the above. If your USB key is currently on `/dev/sdc`, you'd use `/dev/sdc1`; if it is on `/dev/sdd`, you'd use `/dev/sdd1`, etc.

All done, you are now ready to try booting your B2!

## Booting!

Begin with your B2 powered off and the power cable removed. Insert the USB key into either of the USB slots on the back of the B2, and make sure the other USB slot is unoccupied. Connect the B2 to your local network using the **wan** Ethernet port. Then, *while holding down the button on the back of the B2*, apply power (insert the power cable). After five seconds or so, release the button. If all is well, the B2 should boot the kernel off of the USB key (rather than the internal drive), and then proceed to mount the root partition (also from the USB key) and start Gentoo. This will all take about 60 seconds or so:
* The LED on the front of the B2 should first blink, then turn on solid as Gentoo comes up (approximately 40 seconds after applying power, depending on the speed of your USB key); then
* 20 seconds or so after that, you should be able to `ssh` in (see below for details).

> NB, if, after three minutes or so from applying power, the LED is *still* blinking, then the image has failed to boot. If this happens, and if you believe your USB key [should be compatible](http://forum.doozan.com/read.php?2,1915,page=1) with U-Boot, it is worth powering the B2 off and trying once or twice more (as U-Boot does fail to initialize correctly sometimes).
> If you are *unsure* if your USB key is compatible, I recommend using my `blinktest` image first, as this will let you check very quickly, without the long delay to write the full image. See [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Quickly-Testing-your-USB-Key-for-Compatibility) for details.

## Connecting to the B2

Once booted, you can log into the B2 from any other machine on your subnet (the root password is **gentoob2**). Issue:
```
> ssh root@192.168.1.122
The authenticity of host '192.168.1.122 (192.168.1.122)' can't be established.
ED25519 key fingerprint is 18:d5:09:5e:56:62:1f:a1:c9:88:ba:90:2b:39:94:85.
Are you sure you want to continue connecting (yes/no)? <type yes and press Enter>
Warning: Permanently added '192.168.1.122' (ED25519) to the list of known hosts.
Password: <type gentoob2 and press Enter>
b2 ~ # 
```
and you're in! Obviously, substitute the correct network address for your B2 in the command above (if you changed it in `/install/net`, earlier). Also, note that you may receive a different fingerprint type, depending on what your `ssh` client supports. The `ssh` host key fingerprints on the image are as follows:
> 
```
1024 3d:d7:c4:40:4d:b8:26:f0:e0:06:bf:e6:40:97:b2:e0 (DSA)
 256 18:d5:09:5e:56:62:1f:a1:c9:88:ba:90:2b:39:94:85 (ED25519)
2048 c9:66:c6:49:ed:0f:32:5a:14:c9:ae:3e:ee:ae:90:7e (RSA1)
2048 fd:0d:52:d1:3a:3c:c9:f8:78:2b:03:8e:00:b6:80:41 (RSA)
```

If you have previously connected to a *different* machine with the *same* IP address as your B2 via `ssh` from the client PC, you may need to delete its host fingerprint (from `~/.ssh/known_hosts` on the PC) before `ssh` will allow you to connect.

## Using Gentoo

The supplied image contains a fully-configured Gentoo system (*not* simply a [minimal install](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media#Minimal_installation_CD) or [stage 3](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media#What_are_stages_then.3F)), with a complete Portage tree already downloaded, so you can immediately perform `emerge` operations (Gentoo's equivalent of `apt-get`) etc. Be aware that, as shipped, it uses UK locale settings and timezone; however, these are easily changed if desired. See the [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Base#Timezone) for details.

The full set of packages in the image may be viewed [here](https://github.com/sakaki-/gentoo-on-b2/blob/master/reference/installed-packages) (note that the version numbers shown in this list are Gentoo ebuilds, but they generally map 1-to-1 onto upstream package versions).

The image is based on a `ppc` stage 3 release and minimal install system from Gentoo, but *all* binaries (libraries and executables) have been rebuilt to target the B2's processor (an [MPC8313E](http://cache.freescale.com/files/32bit/doc/data_sheet/MPC8313EEC.pdf?pspll=1) SoC, with an [e300c3](http://cache.freescale.com/files/32bit/doc/ref_manual/e300coreRM.pdf) CPU) specifically. Accordingly, the `CHOST` on the image has been set to `powerpc-e300c3-linux-gnu` (see also the later discussion about using `distcc`). All packages have been brought up-to-date as of 25 January 2015.

The drivers for WiFi (if you have the hardware on your B2) *are* present, but configuration of WiFi in master mode (using hostapd) is beyond the scope of this short write up (see [here](http://nims11.wordpress.com/2012/04/27/hostapd-the-linux-way-to-create-virtual-wifi-access-point/) for some details). I have created a wireless network service (`net.wlp1s0`) on the image, but this has not been setup to run on boot. Similarly, the **lan** port (`eth1`) interface service exists on the image (`net.eth1`), but is also not setup to run on boot. Feel free to configure these as desired; see [this volume](https://wiki.gentoo.org/wiki/Handbook:AMD64#Gentoo_network_configuration) of the Gentoo Handbook for details.

> I do not own a WiFi-enabled B2, so I'm unsure whether the above WiFi device name (borrowed from the B3) is correct. If you have a WiFi B2, and run this live-USB image, please let me know if the `net.wlp1s0` name needs to be changed. Many thanks.

Once you have networking set up as you like it, you should issue:
```
b2 ~ # rc-update del copynetsetup default 
```
to prevent your settings being overwritten again next by the files in the first USB partition, next time you boot.

When you are done using your Gentoo system, you can simply issue:
```
b2 ~ # reboot
```
and your machine will cleanly restart back into your existing (Excito) system off the hard drive. At this point, you can remove the USB key if you like. You can then, at any later time, simply repeat the 'power up with USB key inserted and button pressed' process to come back into Gentoo - any changes you made will still be present on the USB key. This makes for an easy way to migrate across gradually to Gentoo if you like, without having to disrupt your normal Excito Debian setup (which you can always just reboot back into at any time).

Also, please note that pressing the rear button will _not_ shut down the B2 when running Gentoo (this is a point I still need to address), so if you want to power off cleanly, issue: 
```
b2 ~ # poweroff-b2
```
Wait until the front LED turns off, before physically removing power (the process takes about 30 seconds, in total).

Have fun! ^-^

## Miscellaneous Points

* The B2's hardware (busses, interrupts etc.) is described by the file `8313E21.dtb` which lives in the 'stock' B2's `/boot` directory (and which is included - in decompiled form - in the [git archive](https://github.com/sakaki-/gentoo-on-b2/blob/master/reference/8313E21.dts) too, for reference).
* The live USB works because the B2's firmware boot loader will automatically try to run a file called `/install/8313E21.itb` from the first partition of the USB drive when the system is powered up with the rear button depressed. In the provided image, a flattened image tree (FIT, see [here](http://elinux.org/images/f/f4/Elc2013_Fernandes.pdf) and [here](http://www.denx.de/wiki/pub/U-Boot/Documentation/multi_image_booting_scenarios.pdf)) has been placed in that location; this encapsulates both the `8313E21.dtb` file just mentioned, _and_ a bootable kernel, with an internal command line set to `root=/dev/sdb3 rootfstype=ext4 rootdelay=5 console=ttyS0,115200n8`. (The 'diskless' variant uses a command line of `root=/dev/sda3 rootfstype=ext4 rootdelay=5 console=ttyS0,115200n8`.) To avoid having to flash new environment variables for the B2's U-Boot to accomodate the larger 3.18.2 kernel (via [`fw_setenv`](http://forum.mybubba.org/viewtopic.php?f=7&t=2715&p=17607&hilit=printenv#p17607)), I have provided an assembly-language relocation shim which is prepended to the kernel; see [the manpage to `buildkernel-b2`](https://github.com/sakaki-/gentoo-on-b2/raw/master/reference/buildkernel-b2.pdf) for further details.
* Unlike its [sister B3 project](https://github.com/sakaki-/gentoo-on-b3), the B2 does require a patched kernel to boot (because of custom settings for its USB driver, primarily). The patches used for the kernel in the image are available [here](https://github.com/sakaki-/gentoo-b2-kernel-patches/), and the patched 3.18.2 kernel is best installed via its ebuild [here](https://github.com/sakaki-/gentoo-b2-overlay/tree/master/sys-kernel/buildkernel-b2) (part of my custom `gentoo-b2` [overlay](https://github.com/sakaki-/gentoo-b2-overlay), to which the image is subscribed). The image is additionally subscribed to my `sakaki-tools-lite` [overlay](https://github.com/sakaki-/sakaki-tools-lite).
* Because the B2 has a small amount of RAM (256MB), the USB image includes a 1GiB swap partition; this is needed if you want to build large packages (such as `gcc`) locally.
* If you have a USB key larger than the minimum 8GB, after writing the image you can easily extend the size of the second partition (using `fdisk` and `resize2fs`), so you have more space to work in. See [these instructions](http://geekpeek.net/resize-filesystem-fdisk-resize2fs/), for example.

## Installing Gentoo on your B2's Internal Drive (Optional)

If you like Gentoo, and want to set it up permanently on the B2's internal hard drive, you can do so easily (it takes less than 10 minutes). The full process is described below. (Note, this is strictly optional, you can simply run Gentoo from the USB key, if you are just experimenting, or using it as a rescue system.)

> **Warning** - the below process will wipe all existing software and data from your internal drive, so be sure to back that up first, before proceeding.

OK, first, boot into the image and then connect to your B2 via `ssh`, as described above. Then, configure the partition table on your hard drive, as described below (**warning** - this will delete all data and software on there, including your existing Excito system, so only proceed if you are sure). We'll make three partitions, for boot, swap and root (feel free to adopt a different scheme if you like; however, note that you will have to recompile your kernel unless targeting a `root` on `/dev/sda3`):
```
b2 ~ # fdisk /dev/sda
<press o and Enter (to create a new disk label)>
<press n and Enter (to create a new partition)>
<press Enter (to make a primary partition)>
<press Enter (to define partition 1)>
<press Enter (to accept the default start location)>
<type +64M and press Enter (to make a 64MiB sector, for boot)>
<type a and press Enter (to turn the boot flag on)>
<press n and Enter (to create a new partition)>
<press Enter (to make a primary partition)>
<press Enter (to define partition 2)>
<press Enter (to accept the default start location)>
<type +1G and press Enter (to make a 1GiB sector, for swap)>
<type t and press Enter (to change the sector type)>
<press Enter (to accept changing partition 2's type)>
<type 82 and press Enter (to set the type as swap)>
<type n and press Enter (to create a new partition)>
<press Enter (to make a primary partition)>
<press Enter (to define partition 3)>
<press Enter (to accept the default start location)>
<press Enter (to use all remaining space on the drive)>
<type p and press Enter (to review the partition table)>
<type w and press Enter (to write the table and exit)>
```

Next, format the partitions (NB, do **not** use `ext4` for the boot partition (`/dev/sda1`), as U-Boot will not be able to read it):
```
b2 ~ # mkfs.ext3 /dev/sda1
b2 ~ # mkswap /dev/sda2
b2 ~ # mkfs.ext4 /dev/sda3
```

Now, we need to copy the necessary system information. I have provided a second version of the kernel (in `root`'s home directory) that looks for its `root` partition on `/dev/sda3`, and has no `rootdelay` (but is otherwise identical to the one on the USB key you booted off), so you need to copy that across:
```
b2 ~ # mkdir /mnt/{sdaboot,sdaroot}
b2 ~ # mount /boot
b2 ~ # mount /dev/sda1 /mnt/sdaboot
b2 ~ # mount /dev/sda3 /mnt/sdaroot
b2 ~ # mkdir /mnt/sdaboot/boot
b2 ~ # cp /root/root-on-sda3-kernel/{uImage,8313E21.dtb,config,System.map} /mnt/sdaboot/boot/
```
Note that this kernel will be booted *without* the button pressed down, so it needs to live in the special path `/boot/uImage` on the first sector (which is where we just copied it to, above).

Next, we'll set up the `root` partition itself. The process below isn't quite what your mother would recommend ^-^, but it gets the job done (the first line may take some time to complete):
```
b2 ~ # cp -ax /bin /dev /etc /lib /root /sbin  /tmp /usr /var /mnt/sdaroot/
b2 ~ # mkdir /mnt/sdaroot/{boot,home,media,mnt,opt,proc,run,sys}
```

Since we simply copied over the `/etc/fstab` file, it will be wrong; a valid copy (for the internal drive) is present in `root`'s home directory on the USB image. Copy it over now:
```
b2 ~ # cp /root/fstab-on-b2 /mnt/sdaroot/etc/fstab
b2 ~ # sed -i 's/vfat/ext3/g' /mnt/sdaroot/etc/fstab
```
Finally, `sync` the filesystem, and unmount:
```
b2 ~ # sync
b2 ~ # umount -l /boot /mnt/{sdaboot,sdaroot}
b2 ~ # rmdir /mnt/{sdaboot,sdaroot}
```

That's it! You can now try rebooting your new system (it will have the same initial network settings as the USB version, since we've just copied them over). Issue:
```
b2 ~ # reboot
```
And let the system shut down and come back up. **Don't** press the B2's back-panel button this time. The system should boot directly off the hard drive. You can now remove the USB key, if you like, as it's no longer needed.
> Note - on some SDDs, it can actually take *longer* to boot from the internal drive than from the USB key, because of U-Boot's drivers. Allow an extra 60 seconds or so over the usual boot time for the front LED to stop blinking, before deciding that you have a problem.

Wait for the front light to turn solid on, then after a further 20 seconds or so from your PC on the same subnet issue:
```
> ssh root@192.168.1.122
Password: <type gentoob2 and press Enter>
b2 ~ # 
```
Of course, use whatever IP address you assigned earlier, rather than `192.168.1.122` in the above. Also, if you changed root's password in the USB image, use that new password rather than `gentoob2` in the above.

Once logged in, feel free to configure your system as you like! Of course, if you're intending to use the B2 as an externally visible server, you should change the `ssh` host keys, change `root`'s password, install a firewall etc.

### Recompiling the Kernel (Optional)

If you'd like to compile a kernel on your new system, you can do so easily (even if still running from the USB - it has sufficient free space).

Suppose you wish to build 3.18.2 (the same version as supplied in the image), using our [specially patched](https://github.com/sakaki-/gentoo-b2-kernel-patches/) version of the kernel sources. Then you would issue:
```
b2 ~ # emerge =gentoo-b2-sources-3.18.2
   (this will take some time to complete, depending on your network connection)
b2 ~ # eselect kernel list
   (this will show a numbered list of kernels)
b2 ~ # eselect kernel set 1
   (specify the appropriate numbered kernel from the list)
b2 ~ # cd /usr/src/linux
```
to prepare. Then, if still running off the USB, issue:
```
b2 linux # buildkernel-b2 --menuconfig --usb
```
or if running Gentoo from the hard drive:
```
b2 linux # buildkernel-b2 --menuconfig
```
This command will compile your kernel and modules, offering you the chance (at the beginning) to change its configuration (which it will initially base on that of the running kernel) via the standard `menuconfig` editor (if you don't want to do that, simply omit the `--menuconfig` flag). It uses my `buildkernel-b2` script, which is provided from the [gentoo-b2](https://github.com/sakaki-/gentoo-b2-overlay) overlay (with underlying source [here](https://github.com/sakaki-/buildkernel-b2), and manpage [here](https://github.com/sakaki-/gentoo-on-b2/raw/master/reference/buildkernel-b2.pdf)), provided as part of the image. When completed, the new bootable kernel (in either [uImage](http://elinux.org/images/f/f4/Elc2013_Fernandes.pdf) form, if running from the internal drive, or [FIT](http://elinux.org/images/f/f4/Elc2013_Fernandes.pdf) form, if running from the USB) and modules will be copied into the correct locations for you. Simply reboot, and you'll be using your new kernel!

### Keeping Your Gentoo System Up-To-Date

You can update your system at any time (whether you are running Gentoo from USB or the B2's internal drive). As there are quite a few steps involved to do this correctly on Gentoo, I have provided a convenience script, [`genup-lite`](https://github.com/sakaki-/genup-lite) to do this as part of the image. So, to update your system, simply issue:
```
b2 ~ # genup-lite
   (this will take some time to complete)
```
This is loosely equivalent to `apt-get update && apt-get upgrade` on Debian. See the [manpage](https://github.com/sakaki-/gentoo-on-b2/raw/master/reference/genup-lite.pdf) for full details of the process followed, and the options available for the command.

Note that because Gentoo is a source-based distribution, and the B2 is not a particularly fast machine, updating may take a number of hours, if many packages have changed. However, `genup-lite` will automatically take advantage of distributed compiling, using `distcc`, if you have that set up (see the next section for details).

When the update has completed, if promped to do so by `genup-lite`, then issue:
```
b2 ~ # dispatch-conf
```
to deal with any config file clashes that may have been introduced by the upgrade process.

> Note that the **kernel** build process for Gentoo is separate (see the previous section for details).

For more information about Gentoo's package management, see [my notes here](https://wiki.gentoo.org/wiki/Sakaki's_EFI_Install_Guide/Installing_the_Gentoo_Stage_3_Files#Gentoo.2C_Portage.2C_Ebuilds_and_emerge_.28Background_Reading.29).

## Have your Gentoo PC Do the Heavy Lifting!

One thing you will definitely notice about the B2 - its processor is not very fast by modern standards! While this is fine when running the device in day-to-day mode (as a mailserver, for example), it does pose a bit of an issue with a source-based distribution like Gentoo, where you must compile packages to upgrade them. Everything works, but an upgrade of a significant package, like `apache`, can take many hours, which soon gets tiresome.

However, there is a solution to this, and it is not as scary as it sounds - leverage the power of your PC (assuming it too is running Gentoo Linux) as a cross-compilation host!

For example, you can cross-compile kernels for your B2 on your PC very quickly (around 5-15 minutes from scratch), by using Gentoo's [`crossdev`](http://gentoo-en.vfose.ru/wiki/Crossdev) tool. See my full instructions [here](https://github.com/sakaki-/gentoo-on-b2/wiki/Set-Up-Your-Gentoo-PC-for-Cross-Compilation-with-crossdev) and [here](https://github.com/sakaki-/gentoo-on-b2/wiki/Build-a-B2-Kernel-on-your-crossdev-PC) on this project's [wiki](https://github.com/sakaki-/gentoo-on-b2/wiki).

Should you setup `crossdev` on your PC in this manner, you can then take things a step further, by leveraging your PC as a [`distcc`](https://wiki.gentoo.org/wiki/Distcc) server (instructions [here](https://github.com/sakaki-/gentoo-on-b2/wiki/Set-Up-Your-crossdev-PC-for-Distributed-Compilation-with-distcc) on the wiki). Then, with just some simple configuration changes on your B2 (see [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Set-Up-Your-B2-as-a-distcc-Client)), you can distribute C/C++ compilation (and header preprocessing) to your remote machine, which makes system updates a *lot* quicker (and the provided tools [`genup-lite`](https://github.com/sakaki-/genup-lite) and [`buildkernel-b2`](https://github.com/sakaki-/buildkernel-b2) will automatically take advantage of this distributed compilation ability, if available).

## Feedback Welcome!

If you have any problems, questions or comments regarding this project, feel free to drop me a line! (sakaki@deciban.com)
