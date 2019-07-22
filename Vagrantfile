# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure(2) do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  #
  # Use Debian from bento because it contains a 40 GB hard disc instead of
  # the 10 GB hard disc in the default Debian images.
  # Supported Debian versions can be found at https://app.vagrantup.com/bento
  config.vm.box = "bento/debian-10"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder ".", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    # vb.gui = true

    # Customize the amount of memory on the VM (in MB):
    vb.memory = "10240"

    # Customzie the amount of cpu cores visible to the VM:
    vb.cpus = 6
  end


  # Enable provisioning with a shell script.
  config.vm.provision "shell", inline: <<-SHELL
    # based on http://wiki.openwrt.org/doc/howto/buildroot.exigence
    # latest tag can be found at https://git.openwrt.org/openwrt/openwrt.git
    export DEBIAN_FRONTEND=noninteractive
    export GIT_TAG=v18.06.4
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt-get install -y git-core build-essential libssl-dev libncurses5-dev unzip subversion mercurial gettext gawk wget
    sudo -u vagrant git clone --branch master --single-branch git://git.openwrt.org/openwrt/openwrt.git openwrt.git
    cd openwrt.git
    sudo -u vagrant git fetch --all --tags --prune
    sudo -u vagrant git checkout tags/${GIT_TAG} -b ${GIT_TAG}
    sudo -u vagrant ./scripts/feeds update -a
    sudo -u vagrant ./scripts/feeds install -a
    sudo -u vagrant make defconfig
    sudo -u vagrant make prereq
    if [ -f "/vagrant_data/openwrt.config.autobuild" ]; then
      # if there is a file called openwrt.config.autobuild, automatically build it
      sudo -u vagrant cp /vagrant_data/openwrt.config.autobuild .config
      echo "using configuration file openwrt.config.autobuild"
      sudo -u vagrant make -j$(nproc) || exit
      echo -e "\\nbuild successful!"
      mkdir -p /vagrant_data/build
      cp bin/*/openwrt-*.{img,bin} /vagrant_data/build
      cp .config /vagrant_data/build/config
      echo -e "copied the following files to 'build' directory:"
      ls /vagrant_data/build
      exit
    elif [ -f "/vagrant_data/openwrt.config" ]; then
      # if there is a file called openwrt.config, use it as configuration file
      sudo -u vagrant cp /vagrant_data/openwrt.config .config
      echo -e "using configuration file openwrt.config"
    fi
    echo -e "\\n\\nopenwrt setup complete. now type:\\n\\n    vagrant ssh\\n    cd openwrt.git\\n    make menuconfig\\n    make -j$(nproc) V=99\\n\\nafter compilation has finished, copy the result to the host machine using a command similar to:\\n\\n    cp -v bin/*/*/*/*.{bin,img,bin.gz,img.gz} /vagrant_data\\n    cp .config /vagrant_data/config"
  SHELL
end
