# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  ### choose which system "box" to use
  # Debian Wheezy
  config.vm.box = "ffuenf/debian-7.4.0-amd64"

  # Ubuntu 12.04 Precise Pangolin LTS 64bit
  #config.vm.box = "hashicorp/precise64"

  # Ubuntu 12.04 Precise Pangolin LTS 32bit
  #config.vm.box = "hashicorp/precise32"

  ### network
  # forward local port 8081 to port 8081 of the guest VM
  config.vm.network :forwarded_port, guest: 8081, host: 8081
  config.vm.hostname = "mediatum"

  ### provisioning
  config.vm.provision :ansible do |ansible|
        ansible.playbook = "site.yml"
        ansible.groups = {
          "mediatum" => ["default"],
          "db" => ["default"]
        }
        ansible.verbose = "vvvv"
    end
end
