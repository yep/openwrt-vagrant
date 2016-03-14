Creating OpenWRT images with Vagrant
====================================

This repository allows you to create your own OpenWRT images using Debian running in a Virtual Machine (VM).

The VM is configured automatically using Vagrant (https://vagrantup.com).

Getting Started
---------------

[Download and install Vagrant](https://www.vagrantup.com/downloads.html) for your operating system.

[Download and install VirtualBox](https://www.virtualbox.org/wiki/Downloads) for your operating system.

Open the `Vagrantfile` with a text editor. Update the following VM parameters depending on the number of CPU cores and RAM you have. Do not assign all RAM to the VM or your computer may become very unresponsive.

 - `vb.cpus = 4`
 - `vb.memory = "6144"` (6 GB)

Open a terminal and go to the directory containing the `Vagrantfile`, then type:

    vagrant up

This should download a Debian VM and download the OpenWRT git repository. When finished, the next steps are shown:

```
==> default: openwrt setup complete. now type:
==> default:   vagrant ssh
==> default:   cd openwrt.git
==> default:   make menuconfig
==> default:   make -j4 V=99
==> default: 
==> default: after compilation has finished, copy the result to the host machine:
==> default:   cp bin/*/*.{bin,img} /vagrant_data/build
==> default:   cp .config /vagrant_data/build/config
```

Building an existing configuration
----------------------------------

The above commands allow you to configure all OpenWRT options using menuconfig. If you already have an OpenWRT config file, you can use this method to build it directly, with no configuration necessary:

Name the config file to `openwrt.config.autobuild` and place it next to the `Vagrantfile`.

In the build logs, look for `using configuration file openwrt.config.autobuild` to be sure the config file is used.


Install Option 1: Copying to an SD card
---------------------------------------

Some platforms, like Raspberry Pi, require to copy the OpenWRT image to an SD card.

On OS X, this can be done with the following commands:

- Show available hard disks before adding the SD card:
  `diskutil list`
- Attach empty 4 GB micro SD card to OSX
- Show available hard disks after adding the SD card:
  `diskutil list`
- In this example, the SD card showed up as /dev/disk4. Change disk number in the following commands to match your setup.
  IMPORTANT: don't mix up disk numbers, you could delete all your data!
- Unmount the SD card:
  `diskutil unmount /dev/disk4s1`
- Copy the openwrt image to the SD card:
  `sudo dd if=openwrt-*.img of=/dev/disk4 bs=1m`
- Press ctrl-t to get the status. If it says `156+0 records out` this means that 156 MB have been copied.
- Unmount the sd card, again:
  `diskutil unmount /dev/disk4s1`


Install Option 2: TFTP
----------------------

Some platforms require to flash the OpenWRT to the router using TFTP.

On OS X, this can be done like this:

- Disconnect from all Wifi and Ethernet networks
- Set a static IP to your Ethernet device in systems settings. Look up the exact IP depending on your model in the OpenWRT wiki. This example will use `192.168.3.2` with a netmask of `255.255.255.0`
- Change to the directory containing your OpenWRT image
- Start tftp with the IP of the router, in this example `192.168.3.1`:

    tftp 192.168.3.1
    > binary
    > trace
    > put openwrt-*.trx


Updating OpenWRT
----------------

You only have to use this section if you want to change the OpenWRT version used.

Lool up latest OpenWRT version on http://wiki.openwrt.org/about/history

Current (as of september 2015):

    chaos calmer 15.05, svn r46767

The corresponding svn commit for the 15.05 release:

    https://dev.openwrt.org/changeset/46767

Loop up the above svn commit (r46767) for this release (15.05) in git:

    http://git.openwrt.org/?p=15.05/openwrt.git;a=summary

Search for the svn revsion `46767` to get the corresponding git hash: `483dac821788b457d349233e770329186a0aa860`

Look up this git hash on github:

    https://github.com/openwrt-mirror/openwrt/commit/483dac821788b457d349233e770329186a0aa860

