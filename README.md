# NVMe/TCP live

A bootable live image configuration to serve a NVMe disk as an NVMe/TCP target.

The live image is built with [Debian Live](https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html), this repository holds a compatible configuration directory. The configuration is based on Debian sid amd64.

The image is configured to run headless, just customize to your needs, build, flash to a thumb drive and plug it into any computer. An IP address is obtained through DHCP.

## Features

* NVMe/TCP target configured to serve `/dev/nvme0n1`
* SSH server to allow root remote access (add your SSH key!)
* Enables wake on lan on all network devices
* Disables nvidia/nouveau kernel modules and turns off nvidia card with bbswitch (for laptops with optimus/prime)
* Disables Bluetooth kernel modules

## Requirements for building

You need a Debian trixie (13) Linux distribution to build the image, as well as the live-build package:

	apt install sudo live-build

## Setup and customization

Clone this repository

	git clone https://github.com/mh0rst/nvmetcp-live
	cd nvmetcp-live

Add your SSH key here for root access: `config/includes.chroot/root/.ssh/authorized_keys`

The default configuration is to serve the disk target on a thunderbolt networking connection with IP address 192.168.250.2, port 4420.

If you want to change either the interface the disk is served on or the IP configuration, you need to edit both `config/includes.chroot/bin/enablenvmet` and `config/includes.chroot/etc/network/interfaces` files. Changing the disk to be served (default is `/dev/nvme0n1`) requires editing `config/includes.chroot/bin/enablenvmet` only.

## Building the image

To build the ISO file, run

	sudo lb build

Flash the resulting `live-image-amd64.hybrid.iso` to a thumb drive or burn it to a CD. The size of the image should be around 400 MBytes.

If you customized some files after building, it may be necessary to clean the project

	sudo lb clean
	sudo lb clean --binary
	git restore .build/config

Sometimes it might even be necessary to remove the cache directory, it was required when I updated my debian from 12 to 13 to get the latest package versions and kernel inside the image:

	sudo rm -rf cache/

## iSCSI

This image previously was designed to serve an iSCSI target. If you cannot use NVMe (which you should if you can), take a look at the iscsi tag: https://github.com/mh0rst/nvmetcp-live/tree/iscsi