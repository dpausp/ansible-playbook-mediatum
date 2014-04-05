MediaTUM Installation Playbook
==============================

An Ansible Playbook for installing mediaTUM (http://github.com/mediatum/mediatum/) with SQLite or MySQL.
This Playbook is based on the Ubuntu instructions from http://mediatum.sourceforge.net/documentation/installation-instructions/installation-linux/.
It is tested on Debian 7 "Wheezy" and Ubuntu 12.04 Server "Precise Pangolin" machines (more supported distributions will be added later on demand).
A current ansible from Github is required.

Two installation variants are available:

* (A) running the ansible playbook directly
* (B) using vagrant+ansible for simple and automatic provisioning on a local VM

(A) will install mediaTUM on a host specified in the `hosts` file (purpose: production or testing) 
(B) will install mediaTUM on a local virtual machine provided by VirtualBox and managed by Vagrant (purpose: testing only)

Installing Ansible
------------------

Ansible controls the installation process and must be installed on a "control machine" (your workstation, for example).
Follow the installation instructions at http://docs.ansible.com/intro_installation.html#installing-the-control-machine

Example: a simple installation via git on a Debian machine:
    
    sudo apt-get install python-pip # if not installed
    # installation for current user
    pip install paramiko PyYAML jinja2 httplib2  --user --upgrade
    git clone git://github.com/ansible/ansible.git
    cd ./ansible
    source ./hacking/env-setup

Run the _source_ command above in new terminals before using the _ansible-playbook_ command.

Playbook Preparation
--------------------

* copy `secret_passwords.yml.example` to `secret_passwords.yml` and set passwords. Passwords must be kept secret! 
The file can be destroyed after a successful installation. Keep the passwords in a safe place!
* (only variant A) copy hosts.example to hosts and set target hosts in `[db]` (for mysql database) and `[mediatum]` sections.
* (optional) copy `group_vars/all.example` to `group_vars/all` and customize settings

If the settings in `group_vars/all` are left unchanged, mediaTUM will be installed for user _mediatum_ with default paths under /srv
  
Important:
:
Currently, only installing all components on a single host is supported. Please specify the same host in both sections in the hosts file, like in the hosts.example file.

Host Preparation
----------------

The main playbook needs SSH access to an admin user who is able to sudo (with password is ok).
You can use the helper playbook access.yml to create an admin user with proper SSH setup on a freshly installed Debian machine.

* configure host for root SSH access via password, if neccessary
* change `admin_user` and `admin_user_pubkeyfile` in the [all:vars] section of the hosts file
* run playbook:

    ansible-playbook -i hosts access.yml -u root -k

Running The Playbook (A)
------------------------

    ansible-playbook -i hosts site.yml -K

The -K option asks for a sudo password. For passwordless sudo (if allowed), run without -K.

It's possible to specify the host group which is used on the cmdline:

    ansible-playbook -i hosts -e 'group=mediatum_new' site.yml -K
    
Vagrant VM Installation (B)
---------------------------

If you like to install mediaTUM on your local machine for testing purposes, you can use the vagrant (http://vagrantup.com) installation.
This will install mediaTUM in a separate VirtualBox virtual machine. No changes will be made to your local system. 
The mediaTUM port 8081 on localhost is forwarded to the VM, so the installation can be found under

    http://localhost:8081


VirtualBox and Vagrant must be installed on your control machine.

Just run:

    vagrant up

This VM can be reached through SSH with:

    vagrant ssh

The VM can be removed with:

    vagrant destroy

