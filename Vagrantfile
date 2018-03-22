# -*- mode: ruby -*-
# vi: set ft=ruby :

# Get the available physical CPU cores on host system, inspired by
# https://coderwall.com/p/ghbzhw
#
HW_CPUS = RbConfig::CONFIG['host_os'] =~ /darwin/ ?
    %x( sysctl -n hw.physicalcpu_max ) :
    %x( nproc )

# The guest system only uses half of physical CPU cores of host system.
VBOX_CPU = (HW_CPUS.to_i).to_s

# VBOX Name
VBOX_NAME = File.basename(Dir.getwd)

# Make sure `vagrant-shell-commander` plugin is installed,
# in order to execute some bash commands after the
# virtual machine is booted.
#
unless Vagrant.has_plugin?("vagrant-shell-commander")
  raise "vagrant-shell-commander is not installed!!"
end

# Hostname for guest system
VBOX_HOSTNAME = "#{VBOX_NAME}"

# Public port mappings.
#
NETWORK_PORT_MAPPINGS = [18000, 18001, 18002, 18003]

Vagrant.configure(2) do |config|

  # Use docker as base-image
  #
  config.vm.box = "geerlingguy/ubuntu1604"
  config.vm.hostname = VBOX_HOSTNAME

  # BASH script to be executed after share-folder is mounted.
  # config.sh.after_share_folders = "sudo /vagrant/init $(uname -s)"

  # Mount public ports..
  #
  NETWORK_PORT_MAPPINGS.each do |port|
    config.vm.network :forwarded_port, guest: port, host: port
  end

  config.vm.provider "virtualbox" do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    vb.customize [
      "modifyvm", :id,
      "--cpus", 1,
      "--memory", "1024",
      "--paravirtprovider", "kvm", # for linux guest
      "--usb", "on",
      "--usbehci", "on"
    ]

    # FTDI TTL232R-3V3
    #
    vb.customize ["usbfilter", "add", "1",
        "--target", :id,
        "--name", "TTL232R-3V3",
        "--vendorid", "0403",
        "--productid", "6001",
        "--manufacturer", "FTDI"
    ]
  end
end
