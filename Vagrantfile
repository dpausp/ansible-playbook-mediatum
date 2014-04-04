# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "wheezy"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  config.vm.box_url = "https://googledrive.com/host/0B83ZToJ3fGtDVC1DeVVzc3lkc0U/debian-7.4.0-amd64_virtualbox.box"

  ### network
  config.vm.network :forwarded_port, guest: 8081, host: 8081
  config.vm.hostname = "mediatum"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  ### sharing
  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

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
