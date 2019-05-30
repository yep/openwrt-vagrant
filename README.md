Creating OpenWRT images with Vagrant
====================================

This repository allows you to create your own OpenWRT images using Debian running in a Virtual Machine (VM).

The VM is configured automatically using Vagrant (https://vagrantup.com).

Getting Started
---------------

[Download and install Vagrant](https://www.vagrantup.com/downloads.html) for your operating system.

[Download and install VirtualBox](https://www.virtualbox.org/wiki/Downloads) for your operating system.

Open the `Vagrantfile` with a text editor. Update the following VM parameters depending on the number of CPU cores and RAM you have. Do not assign all RAM to the VM or your computer may become unresponsive.

 - `vb.memory = "10240"` (10 GB)
 - `vb.cpus = 6`

Open a terminal and go to the directory containing the `Vagrantfile`, then type:

    vagrant up

This downloads a Debian VM and the OpenWRT git repository. When finished, the next steps are shown:

```
==> default: openwrt setup complete. now type:
==> default:   vagrant ssh
==> default:   cd openwrt.git
==> default:   make menuconfig
==> default:   make -j6 V=99
==> default:
==> default: after compilation has finished, copy the result to the host machine:
==> default:   cp bin/*/*.{bin,img} /vagrant_data/build
==> default:   cp .config /vagrant_data/build/config
```

Building an existing configuration
----------------------------------

The above commands instruct you to configure OpenWRT using `menuconfig`. If you have a previous OpenWRT config file, use the following method to build it directly.

Name the config file `openwrt.config.autobuild`, place it next to the `Vagrantfile` and run `vagrant up`.

In the logs, look for `using configuration file openwrt.config.autobuild` to be sure the config file is used.


Install Option 1: Copying to an SD card
---------------------------------------

Some platforms, like Raspberry Pi, require to copy the OpenWRT image to an SD card.

On OS X, this can be done with the following commands:

- Show available hard disks before adding the SD card:
  `diskutil list`
- Attach empty micro SD card to OSX (4 GB or bigger)
- Show available hard disks after adding the SD card:
  `diskutil list`
- In this example, the SD card showed up as /dev/disk4. Change disk number in the following commands to match your setup.
  IMPORTANT: don't mix up disk numbers, you could delete all your data!
- Unmount the SD card:
  `diskutil unmount /dev/disk4s1`
- Copy the openwrt image to the SD card:
  `sudo dd if=openwrt-*.img of=/dev/disk4 bs=1m`
- Press ctrl-t to get the status. If it says `156+0 records out` this means that 156 MB have been copied.
- Unmount the sd card:
  `diskutil unmount /dev/disk4s1`


Install Option 2: TFTP
----------------------

Some platforms require to flash the OpenWRT image to the router using TFTP.

On OS X, this can be done like this:

- Disconnect from all WiFi and Ethernet networks
- Set a static IP to your Ethernet device in systems settings. Look up the exact IP depending on your router model in the OpenWRT wiki. This example will use `192.168.3.2` with a netmask of `255.255.255.0`
- Change to the directory containing your OpenWRT image
- Start `tftp` with the IP of the router, in this example `192.168.3.1`:

    tftp 192.168.3.1
    > binary
    > trace
    > put openwrt-*.trx


Updating OpenWRT
----------------

You only have to do this if you want intend to change the OpenWRT version.

Look up latest OpenWRT version on https://github.com/openwrt/openwrt/tags

Current (as of 2019) is `v18.06.2`

In the `Vagrantfile` of this repository, update the variable called `GIT_TAG`:

    export GIT_TAG=v18.06.2
