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
  config.vm.hostname = "testmediatum"
  # forward local port 8081 to port 443 of the guest VM for HTTPS
  config.vm.network :forwarded_port, guest: 443, host: 8081, auto_correct: true

  config.vm.define "inabox", primary: true do |guest|
      ansible_command = "ansible-playbook -c local -e 'group=all db_group=all' /home/vagrant/playbook-mediatum/site.yml"
      va_verbose = ENV["VA_VERBOSE"]
      if va_verbose
          ansible_command += " -" + va_verbose
      end
      guest.vm.synced_folder "./playbook-mediatum", "/home/vagrant/playbook-mediatum"
      guest.vm.provision "shell", keep_color: true, path: "setup-ansible.sh"
      guest.vm.provision "shell", keep_color: true, privileged: false, inline: ansible_command
  end

  config.vm.define "ext" do |guest|
    guest.vm.provision :ansible do |ansible|
      ansible.playbook = "playbook-mediatum/site.yml"
      ansible.groups = {
        "mediatum" => ["ext"],
        "db" => ["ext"]
      }
      ansible.verbose = ENV["VA_VERBOSE"]
      ansible.raw_arguments = ENV["VA_ARGS"]
    end
  end
end
