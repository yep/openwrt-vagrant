# Creating OpenWRT images with Vagrant

Use this repository to create your own OpenWRT image by running Debian in a virtual machine (VM).

The VM is configured automatically with Vagrant (https://vagrantup.com).


## Getting Started

[Download and install Vagrant](https://www.vagrantup.com/downloads.html) for your operating system.

[Download and install VirtualBox](https://www.virtualbox.org/wiki/Downloads) for your operating system.

Open the `Vagrantfile` in a text editor. Update the following VM parameters to match the number of CPU cores and RAM you have. Do not assign all RAM to the VM, your computer may become unresponsive otherwise.

 - `vb.memory = "10240"` (10 GB)
 - `vb.cpus = 6`

Open a terminal and go to the directory containing the `Vagrantfile`, then type:

    vagrant up

This downloads a Debian VM and the OpenWRT git repository. When finished, the next steps are shown:

```
    default: openwrt setup complete. now type:
    default:     vagrant ssh
    default:     cd openwrt.git
    default:     make menuconfig
    default:     make -j6 V=99
```


## Building an Existing Configuration (autobuild)

The above commands instruct you to configure OpenWRT from scratch using `menuconfig`. If you have a previous OpenWRT config file, use the following method to build it directly.

 - Change the name of the config file to `openwrt.config.autobuild`
 - Place it next to the `Vagrantfile` and
 - Run `vagrant up`

In the logs, look for `using configuration file openwrt.config.autobuild` to be sure the config file is used.


## Install Option 1: Copying to an SD card

Some platforms, like Raspberry Pi, require to copy the OpenWRT image to an SD card.

On OS X, this can be done with the following commands:

- Show available hard disks before adding the SD card:
  `diskutil list`
- Attach empty micro SD card to OSX (4 GB or bigger)
- Show available hard disks after adding the SD card:
  `diskutil list`
- In this example, the SD card showed up as `/dev/disk4`. Change disk number in the following commands to match your setup.
  `IMPORTANT: don't mix up disk numbers, you could delete all your data!`
- Unmount the SD card:
  `diskutil unmount /dev/disk4s1`
- Copy the openwrt image to the SD card:
  `sudo dd if=openwrt-*.img of=/dev/disk4 bs=1m`
- Press ctrl-t to get the status. If it says `156+0 records out` this means that 156 MB have been copied as the block size `bs` is one megabyte `1m`.
- Unmount the sd card:
  `diskutil unmount /dev/disk4s1`


## Install Option 2: TFTP

Some platforms require to flash the OpenWRT image to the router using TFTP.

On OS X, this can be done like this:

- Install `tftp` using [homebrew](https://brew.sh) or [macports](https://www.macports.org)
- Disconnect from all WiFi and Ethernet networks
- Set a static IP to your Ethernet device in systems settings. Look up the exact IP depending on your router model in the OpenWRT wiki. This example will use `192.168.3.2` with a netmask of `255.255.255.0`
- Change to the directory containing your OpenWRT image
- Start `tftp` with the IP of the router, in this example `192.168.3.1`:

      tftp 192.168.3.1
      > binary
      > trace
      > put openwrt-*.trx


## Updating OpenWRT

You only have to do this if you intend to change the OpenWRT version.

Look up latest OpenWRT version on [github.com/openwrt/openwrt/tags](https://github.com/openwrt/openwrt/tags) or [git.openwrt.org/openwrt/openwrt.git](https://git.openwrt.org/openwrt/openwrt.git)

Current (as of 2019) is `v18.06.4`

In the `Vagrantfile` of this repository, update the variable called `GIT_TAG`:

    export GIT_TAG=v18.06.4

The `Vagrantfile` uses a Debian VM from the [bento](https://github.com/chef/bento) project as the disc size of the the default Debian and Ubuntu images, provided by Vagrant, is too small.

Latest version of the Debian image can be found at [app.vagrantup.com/bento](https://app.vagrantup.com/bento).

Current (as of 2019) is `bento/debian-10`


## License

    openwrt-vagrant
    Copyright (C) 2019 Jahn Bertsch

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details.

    You should have received a copy of the GNU General Public License
    along with this program.  If not, see <http://www.gnu.org/licenses/>.

