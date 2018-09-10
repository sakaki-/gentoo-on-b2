# gentoo-on-b2

Bootable live-USB of Gentoo Linux for the Excito B2 miniserver, with Linux 4.9.72 LTS, profile 17.0

## Description

<img src="https://raw.githubusercontent.com/sakaki-/resources/master/excito/b2/Excito_b2.jpg" alt="Excito B2, aka Bubba|TWO" width="250px" align="right"/>

This project contains a bootable, live-USB image for the Excito B2 (aka Bubba|TWO) miniserver. You can use it as a rescue disk, to play with Gentoo Linux, or as the starting point to install Gentoo Linux on your B2's main hard drive. You can even use it on a diskless B2. No soldering, compilation, or [U-Boot](http://www.denx.de/wiki/U-Boot/WebHome) flashing is required! You can run it without harming your B2's existing software; however, any changes you make while running the system *will* be saved to the USB (i.e., there is persistence). Also, as with its [sister B3 project](https://github.com/sakaki-/gentoo-on-b3), a number of useful software packages (web server, mail server etc.) are included precompiled with the image (in their 'freshly emerged' configuration state), for convenience.

The kernel used in the image is **4.9.72** from gentoo-b2-sources (i.e., the [kernel.org](https://www.kernel.org) LTS sources, with Gentoo and [B2-specific](https://github.com/sakaki-/gentoo-b2-kernel-patches/) patches applied). The `.config` used for the kernel may be found [here](https://github.com/sakaki-/gentoo-on-b2/blob/master/configs/b2_live_usb_config) in the git archive.

As of version 1.5.0:
* a profile 17.0 [Gentoo binhost](https://wiki.gentoo.org/wiki/Binary_package_guide) (automatically updated weekly) has been provided (at https://isshoni.org/b2pie), to allow your B2 to perform fast updates via binary packages where possible, only falling back to local source-based compilation where necessary (using this facility is optional; it is activated by default in the live-USB, but instructions for turning it off are provided [below](#binhost_unsubscribe)). The binhost additionally provides an rsync mirror for the main `gentoo` repo (with `gemato` authentication), used to keep the B2's ebuild tree in lockstep; and
* a custom Gentoo profile, `gentoo-b2:default/linux/powerpc/ppc32/17.0/b2`, is provided, which supplies many of the default build settings, USE flags etc., required for Gentoo on the B2, keeping them also in lockstep with the binhost. You can view this profile (provided via the [gentoo-b2](https://github.com/sakaki-/gentoo-b2-overlay) overlay) [here](https://github.com/sakaki-/gentoo-b2-overlay/tree/master/profiles/targets/b2).

The images may be downloaded from the links below (or via `wget`, per the following instructions). (Incidentally, the image is now 'universal', and should work, without modification, whether your B2 has an internal hard drive fitted or not.) I have also provided a small `blinktest` image that you can use to very quickly establish if your USB key will work correctly with U-Boot; see [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Quickly-Testing-your-USB-Key-for-Compatibility).

Variant | Version | Image | Digital Signature
:--- | ---: | ---: | ---:
B2 with or without Internal Drive | 1.5.0 | [genb2img.xz](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.5.0/genb2img.xz) | [genb2img.xz.asc](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.5.0/genb2img.xz.asc)
USB Key Compatibility Tester | 1.2.0 | [blinktestimg.xz](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.2.0/blinktestimg.xz) | [blinktestimg.xz.asc](https://github.com/sakaki-/gentoo-on-b2/releases/download/1.2.0/blinktestimg.xz.asc)

The older images are still available (together with a short changelog) [here](https://github.com/sakaki-/gentoo-on-b2/releases).

> Please read the instructions below before proceeding. Also please note that these images are provided 'as is' and without warranty.

## Prerequisites

To try this out, you will need:
* A USB key of at least 8GB capacity (the *compressed* (.xz) image is 352MiB, the *uncompressed* image is 14,813,184 (512 byte) sectors = 7,584,350,208 bytes). I have tested it successfully with a [SanDisk Cruzer Blade 8GB USB 2.0 key](http://www.amazon.co.uk/gp/product/B002U28LZC), a [Lexar 16 GB USB 3.0 key](http://www.amazon.co.uk/gp/product/B008XIYKE8), and with a [Memwah single-slot micro-SD to USB adaptor](http://www.amazon.co.uk/gp/product/B004WFT762) (plus [Samsung 32GB micro-SD card](http://www.amazon.co.uk/gp/product/B00J29BR3Y)); unfortunately, a number of other brands do not work with U-Boot's drivers. You may find this [related list of known-good USB keys](http://forum.doozan.com/read.php?2,1915,page=1) useful. If in doubt, try using my `blinktest` image first to quickly check your USB key for U-Boot compatibility; see [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Quickly-Testing-your-USB-Key-for-Compatibility).
* An Excito B2 (obviously!). As of version 1.1.0, the *same* image will work both for the case where you have an internal hard drive in your B2 (the normal situation), *and* for the case where you are running a diskless B2 chassis.
* A PC to decompress the appropriate image and write it to the USB key (of course, you can also use your B2 for this, assuming it is currently running the standard Excito / Debian system). This is most easily done on a Linux machine of some sort, but tools are also available for Windows (see [here](http://tukaani.org/xz/) and [here](http://sourceforge.net/projects/win32diskimager/), for example). In the instructions below I'm going to assume you're using Linux.

> Incidentally, I also have an [Arch Linux](https://www.archlinux.org/) live USB for the B3, available [here](https://github.com/sakaki-/archlinux-on-b3), a RedSleeve Linux live USB for the B3, available [here](https://github.com/sakaki-/redsleeve-on-b3), and a Gentoo Linux live USB for the B3, available [here](https://github.com/sakaki-/gentoo-on-b3).

## Downloading and Writing the Image

On your Linux box, issue:
```
# wget -c https://github.com/sakaki-/gentoo-on-b2/releases/download/1.5.0/genb2img.xz
# wget -c https://github.com/sakaki-/gentoo-on-b2/releases/download/1.5.0/genb2img.xz.asc
```
to fetch the compressed disk image file (352MiB) and its signature.

Next, if you like, verify the image using `gpg` (this step is optional):
```
# gpg --keyserver pool.sks-keyservers.net --recv-key DDE76CEA
# gpg --verify genb2img.xz.asc genb2img.xz
```

Assuming that reports 'Good signature', you can proceed.

Next, insert (into your Linux box) the USB key on which you want to install the image, and determine its device path (this will be something like `/dev/sdb`, `/dev/sdc` etc.; the actual path will depend on your system, you can use the `lsblk` tool to help you). Unmount any existing partitions of the USB key that may have automounted (using `umount`). Then issue:

> **Warning** - this will *destroy* all existing data on the target drive, so please double-check that you have the path correct!

```
# xzcat genb2img.xz > /dev/sdX && sync
```

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

Begin with your B2 powered off and the power cable removed. Insert the USB key into either of the USB slots on the back of the B2, and make sure the other USB slot is unoccupied. Connect the B2 to your local network using the **wan** Ethernet port. Then, *while holding down the button on the back of the B2*, apply power (insert the power cable). After five seconds or so, release the button. If all is well, the B2 should boot the kernel off of the USB key (rather than the internal drive), and then proceed to mount the root partition (also from the USB key) and start Gentoo. This will all take about 3 minutes or so:
* The LED on the front of the B2 should first blink, then turn on solid as Gentoo comes up (approximately two minutes after applying power, depending on the speed of your USB key); then
* a minute or so after that, you should be able to `ssh` in (see below for details).

> NB, if, after five minutes or so from applying power, the LED is *still* blinking, then the image has failed to boot. If this happens, and if you believe your USB key [should be compatible](http://forum.doozan.com/read.php?2,1915,page=1) with U-Boot, it is worth powering the B2 off and trying once or twice more (as U-Boot does fail to initialize correctly sometimes).
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
and you're in! Obviously, substitute the correct network address for your B2 in the command above (if you changed it in `/install/net`, earlier). You may receive a different fingerprint type, depending on what your `ssh` client supports. Also, please note that as of version 1.1.0, the `ssh` host keys are generated on first boot (for security), and so the fingerprint you get will be different from that shown above.

If you have previously connected to a *different* machine with the *same* IP address as your B2 via `ssh` from the client PC, you may need to delete its host fingerprint (from `~/.ssh/known_hosts` on the PC) before `ssh` will allow you to connect.

## Using Gentoo

The supplied image contains a fully-configured Gentoo system (*not* simply a [minimal install](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media#Minimal_installation_CD) or [stage 3](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Media#What_are_stages_then.3F)), with a complete Portage tree already downloaded, so you can immediately perform `emerge` operations (Gentoo's equivalent of `apt-get`) etc. Be aware that, as shipped, it uses UK locale settings and timezone; however, these are easily changed if desired. See the [Gentoo Handbook](https://wiki.gentoo.org/wiki/Handbook:AMD64/Installation/Base#Timezone) for details.

The full set of packages in the image may be viewed [here](https://github.com/sakaki-/gentoo-on-b2/blob/master/reference/installed-packages) (note that the version numbers shown in this list are Gentoo ebuilds, but they generally map 1-to-1 onto upstream package versions).

The image is based on a `ppc` stage 3 release and minimal install system from Gentoo, but *all* binaries (libraries and executables) have been rebuilt to target the B2's processor (an [MPC8313E](http://cache.freescale.com/files/32bit/doc/data_sheet/MPC8313EEC.pdf?pspll=1) SoC, with an [e300c3](http://cache.freescale.com/files/32bit/doc/ref_manual/e300coreRM.pdf) CPU) specifically. Accordingly, the `CHOST` on the image has been set to `powerpc-e300c3-linux-gnu` (see also the later discussion about using `distcc`). All packages have been brought up-to-date as of 3 September 2018.

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

To power off cleanly (rather than rebooting), you have two options. First (as of version 1.1.0), you can simply press the B2's rear button for around 5 seconds, then release it (just as you would on a regular Excito system). The front LED will start blinking, then turn off once it is safe to physically remove the power cable.

Second, if you'd rather use the command line, you can issue: 
```
b2 ~ # poweroff-b2
```
which will have the same effect (and follow the same power-down LED sequence).

Have fun! ^-^

## Miscellaneous Points

* The B2's hardware (busses, interrupts etc.) is described by the file `8313E21.dtb` which lives in the 'stock' B2's `/boot` directory (and which is included - in decompiled form - in the [git archive](https://github.com/sakaki-/gentoo-on-b2/blob/master/reference/8313E21.dts) too, for reference).
* The live USB works because the B2's firmware boot loader will automatically try to run a file called `/install/8313E21.itb` (or `install/install.itb`, in the case of rev 1 B2s) from the first partition of the USB drive when the system is powered up with the rear button depressed. In the provided image, a flattened image tree (FIT, see [here](http://elinux.org/images/f/f4/Elc2013_Fernandes.pdf) and [here](http://www.denx.de/wiki/pub/U-Boot/Documentation/multi_image_booting_scenarios.pdf)) has been placed in that location; this encapsulates both the `8313E21.dtb` file just mentioned, _and_ a bootable kernel, with an internal command line set to `root=PARTUUID=BBF82D16-03 rootfstype=ext4 rootdelay=5 console=ttyS0,115200n8` (the alternative `install.itb` file has a `bubba.dtb` device tree blob instead of `8313E21.dtb`). To avoid having to flash new environment variables for the B2's U-Boot to accomodate the larger 4.9.16 kernel (via [`fw_setenv`](http://forum.mybubba.org/viewtopic.php?f=7&t=2715&p=17607&hilit=printenv#p17607)), I have provided an assembly-language relocation shim which is prepended to the kernel; see [the manpage to `buildkernel-b2`](https://github.com/sakaki-/gentoo-on-b2/raw/master/reference/buildkernel-b2.pdf) for further details.
* Unlike its [sister B3 project](https://github.com/sakaki-/gentoo-on-b3), the B2 does require a patched kernel to boot (because of custom settings for its USB driver, primarily). The patches used for the kernel in the image are available [here](https://github.com/sakaki-/gentoo-b2-kernel-patches/), and the patched 4.9.72 LTS kernel is best installed via its ebuild [here](https://github.com/sakaki-/gentoo-b2-overlay/tree/master/sys-kernel/gentoo-b2-sources) (part of my custom `gentoo-b2` [overlay](https://github.com/sakaki-/gentoo-b2-overlay), to which the image is subscribed). The image is additionally subscribed to my `sakaki-tools-lite` [overlay](https://github.com/sakaki-/sakaki-tools-lite) and `sakaki-tools` [overlay](https://github.com/sakaki-/sakaki-tools).
* Because the B2 has a small amount of RAM (256MB), the USB image includes a 1GiB swap partition; this is needed if you want to build large packages (such as `gcc`) locally.
* If you have a USB key larger than the minimum 8GB, after writing the image you can easily extend the size of the second partition (using `fdisk` and `resize2fs`), so you have more space to work in. See [these instructions](http://geekpeek.net/resize-filesystem-fdisk-resize2fs/), for example.
* <a name="binhost_unsubscribe">A cron-scripted weekly autobuild (`genup`) binhost has been set up at https://isshoni.org/b2pie, and the image is configured to use this by default (see `/etc/portage/make.conf`: `FEATURES="${FEATURES} getbinpkg"` and `PORTAGE_BINHOST="https://isshoni.org/b2pie"`; simply comment out these lines if you do not wish to use this facility). Furthermore, to ensure that the Portage ebuild tree is kept in sync with the binhost, an `rsync` mirror has been provided at `rsync://isshoni.org/gentoo-portage-b2`. This mirror is slaved off the binhost's tree (itself based upon a `webrsync-gpg` authenticated snapshot) and updated at the same time the new binary packages are uploaded each week. The image is configured to use this mirror for its main Portage tree (see `/etc/portage/repos.conf/gentoo.conf`), and `portage` configured with the `rsync-verify` USE flag, to ensure the tree has not been tampered with in transit.

   If you would rather use a standard Gentoo `rysnc` source instead, simply edit `/etc/portage/repos.conf/gentoo.conf` and set e.g. `sync-uri = rsync://rsync.us.gentoo.org/gentoo-portage`. Be aware that if you *do* do this, *and* use the `isshoni.org` binhost for updates, you may still end up building quite a lot from source on your own machine, should e.g. a new version of a large package like `gcc` be posted to the "gentoo" tree in-between the weekly binhost updates (using the custom `rsync` source prevents this kind of thing from happening).

* Also, since, by default, Gentoo will *not* use a binary package for update unless the USE flags selected when building it match those on your machine, a custom profile, `gentoo-b2:default/linux/powerpc/ppc32/17.0/b2`, has been provided, which helps keep these (and some other critical build settings) in sync. You can view this profile (provided via the [gentoo-b2](https://github.com/sakaki-/gentoo-b2-overlay) overlay) [here](https://github.com/sakaki-/gentoo-b2-overlay/tree/master/profiles/targets/b2). In addition to USE flags, the custom profile also provides package masks, certain `make.conf` settings, package keywording etc. The profile is sync'd by the binhost as part of the weekly build cycle, and will be picked up on the B2 during a `genup` run, so any USE flag modifications, new masks etc. required to build an updated version of a package can be distributed to your machine without manual intervention; again, simplifying the update process. Note that any settings you make in `/etc/portage/...` override those in the profile, so you always have control.

  Nevertheless, if you *don't* want to use this facility, simply issue `eselect profile set default/linux/powerpc/ppc32/17.0` to revert back to the vanilla profile. You'll need to merge back some changes into your `/etc/portage/make.conf`, `/etc/portage/package.use` etc. if you do so however, in order to continue to build packages on your B2 under Gentoo. Refer to the various files [in the custom profile](https://github.com/sakaki-/gentoo-b2-overlay/tree/master/profiles/targets/b2) to see which edits must be made.

* As of release 1.5.0, a weekly cron job (`/etc/cron.weekly/genup`) has been installed, to automate the process of keeping your B2 up-to-date (this can be deleted if you prefer).

## <a name="hdd_install">Installing Gentoo on your B2's Internal Drive (Optional)

If you like Gentoo, and want to set it up permanently on the B2's internal hard drive, you can do so easily (it takes less than 15 minutes). The full process is described below. (Note, this is strictly optional, you can simply run Gentoo from the USB key, if you are just experimenting, or using it as a rescue system.)

> **Warning** - the below process will wipe all existing software and data from your internal drive, so be sure to back that up first, before proceeding. It will set up:
* /dev/sda1 as a 64MiB boot partition, and format it `ext3`;
* /dev/sda2 as a 1GiB swap partition;
* /dev/sda3 as a root partition using the rest of the drive, and format it `ext4`.

> Note also that the script [`/root/install_on_sda.sh`](https://github.com/sakaki-/gentoo-on-b2/blob/master/reference/install_on_sda.sh) will install using a DOS partition table (max 2TiB); if you'd rather use GPT, then use [`/root/install_on_sda_gpt.sh`](https://github.com/sakaki-/gentoo-on-b2/blob/master/reference/install_on_sda_gpt.sh) instead; however, do bear in mind that the 'stock' B2 U-boot (version 1.3.4-00056-gf6f51b1-dirty on my device) does **not** support booting from GPT drives.

OK, first, boot into the image and then connect to your B2 via `ssh`, as described above. Then, (as of version 1.1.0) you can simply run the supplied script to install onto your hard drive:
```
b2 ~ # /root/install_on_sda.sh
Install Gentoo -> /dev/sda (B2's internal HDD)

WARNING - will delete anything currently on HDD
(including any existing Excito Debian system)
Please make sure you have adequate backups before proceeding

Type (upper case) INSTALL and press Enter to continue
Any other entry quits without installing: <type INSTALL and press Enter, to proceed>
Installing: check '/var/log/gentoo_install.log' in case of errors
Step 1 of 5: creating partition table on /dev/sda...
Step 2 of 5: formatting partitions on /dev/sda...
Step 3 of 5: mounting boot and root partitions from /dev/sda...
Step 4 of 5: copying system and bootfiles (please be patient)...
Step 5 of 5: syncing filesystems and unmounting...
All done! You can reboot into your new system now.
```

That's it! You can now try rebooting your new system (it will have the same initial network settings as the USB version, since we've just copied them over). Issue:
```
b2 ~ # reboot
```
And let the system shut down and come back up. **Don't** press the B2's back-panel button this time. The system should boot directly off the hard drive. You can now remove the USB key, if you like, as it's no longer needed.
> Note - on some HDDs and SSDs, it can actually take *longer* to boot from the internal drive than from the USB key, because of U-Boot's SATA drivers. Allow an extra 120 seconds or so over the usual boot time for the front LED to stop blinking, before deciding that you have a problem. Once Linux is loaded, it uses its own drivers of course, so this issue only occurs on boot.

Wait for the front light to turn solid on, then after a further 20 seconds or so from your PC on the same subnet issue:
```
> ssh root@192.168.1.122
Password: <type gentoob2 and press Enter>
b2 ~ # 
```
Of course, use whatever IP address you assigned earlier, rather than `192.168.1.122` in the above. Also, if you changed root's password in the USB image, use that new password rather than `gentoob2` in the above.

Once logged in, feel free to configure your system as you like! Of course, if you're intending to use the B2 as an externally visible server, you should take the usual precautions, such as changing `root`'s password, configuring the firewall, possibly [changing the `ssh` host keys](https://missingm.co/2013/07/identical-droplets-in-the-digitalocean-regenerate-your-ubuntu-ssh-host-keys-now/#how-to-generate-new-host-keys-on-an-existing-server), etc.

### Recompiling the Kernel (Optional)

If you'd like to compile a kernel on your new system, you can do so easily (even if still running from the USB - it has sufficient free space).

Suppose you wish to build 4.9.16 (the same version as supplied in the image), using our [specially patched](https://github.com/sakaki-/gentoo-b2-kernel-patches/) version of the kernel sources. Then you would issue:
```
b2 ~ # emerge =gentoo-b2-sources-4.9.16
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

You can update your system at any time (whether you are running Gentoo from USB or the B2's internal drive). As there are quite a few steps involved to do this correctly on Gentoo, I have provided a convenience script, [`genup`](https://github.com/sakaki-/genup) to do this as part of the image. So, to update your system, simply issue:
```
b2 ~ # genup
   (this will take some time to complete)
```
This is loosely equivalent to `apt-get update && apt-get upgrade` on Debian. See the [manpage](https://github.com/sakaki-/gentoo-on-b2/raw/master/reference/genup.pdf) for full details of the process followed, and the options available for the command.

Note that because Gentoo is a source-based distribution, and the B2 is not a particularly fast machine, updating may take a number of hours, or even days, if many packages have changed. However, `genup` will automatically take advantage of distributed compiling, using `distcc`, if you have that set up (see the next section for details).

When the update has completed, if promped to do so by `genup`, then issue:
```
b2 ~ # dispatch-conf
```
to deal with any config file clashes that may have been introduced by the upgrade process.

> Note that the **kernel** build process for Gentoo is separate (see the previous section for details).

For more information about Gentoo's package management, see [my notes here](https://wiki.gentoo.org/wiki/Sakaki's_EFI_Install_Guide/Installing_the_Gentoo_Stage_3_Files#Gentoo.2C_Portage.2C_Ebuilds_and_emerge_.28Background_Reading.29).

> You may also find it useful to keep an eye on the 'Development' forum at [excito.com](http://forum.excito.com/index.php), as I occasionally post information about this live-USB there.

Note that as of v1.4.0, the live-USB will use binary packages from the binhost https://isshoni.org/b2 where these are available (and USE-flag compatible), only falling back to local compilation where necessary.

> Although I make no guarantees about availability, this binhost is updated automatically on a weekly basis by a B2 running a `cron`-scripted [`genup`](https://github.com/sakaki-/genup) job, so should remain reasonably current. If you don't want to use this, comment out the `FEATURES="${FEATURES} getbinpkg"` line in `/etc/portage/make.conf`. Use the provide binary packages at you own risk.

## Have your Gentoo PC Do the Heavy Lifting!

One thing you will definitely notice about the B2 - its processor is not very fast by modern standards! While this is fine when running the device in day-to-day mode (as a mailserver, for example), it does pose a bit of an issue with a source-based distribution like Gentoo, where you must compile packages to upgrade them. Everything works, but an upgrade of a significant package, like `apache`, can take many hours, which soon gets tiresome.

However, there is a solution to this, and it is not as scary as it sounds - leverage the power of your PC (assuming it too is running Gentoo Linux) as a cross-compilation host!

For example, you can cross-compile kernels for your B2 on your PC very quickly (around 5-15 minutes from scratch), by using Gentoo's [`crossdev`](http://gentoo-en.vfose.ru/wiki/Crossdev) tool. See my full instructions [here](https://github.com/sakaki-/gentoo-on-b2/wiki/Set-Up-Your-Gentoo-PC-for-Cross-Compilation-with-crossdev) and [here](https://github.com/sakaki-/gentoo-on-b2/wiki/Build-a-B2-Kernel-on-your-crossdev-PC) on this project's [wiki](https://github.com/sakaki-/gentoo-on-b2/wiki).

Should you setup `crossdev` on your PC in this manner, you can then take things a step further, by leveraging your PC as a [`distcc`](https://wiki.gentoo.org/wiki/Distcc) server (instructions [here](https://github.com/sakaki-/gentoo-on-b2/wiki/Set-Up-Your-crossdev-PC-for-Distributed-Compilation-with-distcc) on the wiki). Then, with just some simple configuration changes on your B2 (see [these notes](https://github.com/sakaki-/gentoo-on-b2/wiki/Set-Up-Your-B2-as-a-distcc-Client)), you can distribute C/C++ compilation (and header preprocessing) to your remote machine, which makes system updates a *lot* quicker (and the provided tools [`genup`](https://github.com/sakaki-/genup) and [`buildkernel-b2`](https://github.com/sakaki-/buildkernel-b2) will automatically take advantage of this distributed compilation ability, if available).

## Feedback Welcome!

If you have any problems, questions or comments regarding this project, feel free to drop me a line! (sakaki@deciban.com)
