MediaTUM Installation Playbook
==============================

An Ansible Playbook for installing mediaTUM (http://sourceforge.net/projects/mediatum/) with SQLite or MySQL.
This Playbook is based on the Ubuntu instructions from http://mediatum.sourceforge.net/documentation/installation-instructions/installation-linux/.
It is tested on Debian 7 "Wheezy" and Ubuntu 12.04 Server "Precise Pangolin" machines (more supported distributions will be added later on demand).
A current ansible from Git is required.

Installing Ansible
------------------

Simple installation of a current version via git:
    
    # user installation
    git clone git://github.com/ansible/ansible.git
    cd ./ansible
    source ./hacking/env-setup

Run the _source_ command above in new terminals before using the _ansible-playbook_ command.

Playbook Preparation
--------------------

* copy secret\_passwords.yml.example to secret\_passwords.yml and set passwords. Passwords must be kept secret! 
The file can be destroyed after a successful installation. Keep the passwords in a safe place!
* copy hosts.example to hosts and set target host.
* (optional) edit group\_vars/all
* (optional) edit group\_vars/mediatum

If the files in `group\_vars` are left unchanged, mediaTUM will be installed for user `mediatum` with default paths
  
Important:

Currently, only installing all components on a single host is supported. Please specify the same host for all sections in the hosts file, like in the hosts.example file.

Host Preparation
----------------

The main Playbook needs access via SSH to a user who is able to sudo (with password is ok). To configure a freshly installed Debian machine for proper access, use access.yml.
This playbook creates an admin user.

* configure host for root SSH access via password, if neccessary
* change host, admin\_user and admin\_user\_pubkeyfile in hosts file
* run playbook:

    ansible-playbook -i hosts access.yml -u root -k

Running The Playbook
--------------------

    ansible-playbook -i hosts site.yml -K

The -K option asks for a sudo password. For passwordless sudo (if allowed), don't specify -K.

It's possible to specify the group which is used on the cmdline:

    ansible-playbook -i hosts -e 'group=mediatum_new' site.yml -K
    
