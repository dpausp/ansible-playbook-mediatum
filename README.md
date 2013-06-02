MediaTUM Installation Playbook
==============================

An Ansible Playbook for installing mediaTUM (http://sourceforge.net/projects/mediatum/) with SQLite or MySQL.
This Playbook is based on the Ubuntu instructions from http://mediatum.sourceforge.net/documentation/installation-instructions/installation-linux/.
It is tested on a Debian 7 "Wheezy" machine (more supported distributions will be added later).
Ansible 1.2 is required.

Installing Ansible
------------------

Simple installation of a current version via git:
    
    # user installation
    git clone git://github.com/ansible/ansible.git
    cd ./ansible
    source ./hacking/env-setup

Run the _source_ command in new terminals before using the _ansible-playbook_ command.

Playbook Preparation
--------------------

* copy secret\_passwords.yml.example to secret\_passwords.yml and set passwords. Passwords must be kept secret! 
The file can be destroyed after a successful installation. Keep the passwords in a safe place!
* copy hosts.example to hosts and set target host.
  
Important:

    Currently, only installing all components on a single host is supported. Please specify the same host for all sections in the hosts file, like in the hosts.example file.

Running The Playbook
--------------------

    ansible-playbook -i hosts site.yml

