MediaTUM Installation Playbook
==============================


[Ansible](http://ansible.com) Playbook + Vagrantfile for installing [mediaTUM](http://github.com/mediatum/mediatum/) with MySQL server and nginx SSL reverse proxy.
Three installation methods are available:

* (A) universal [Vagrant](http://www.vagrantup.com) installer (recommended for first tests)
* (B) Vagrant with local Ansible
* (C) running the ansible playbook directly

* (A) and (B) will install mediaTUM to a local virtual machine (VM) provided by VirtualBox and managed by Vagrant (purpose: testing only!)
    * (A) installs everything to the VM and works on all operating systems which are supported by Vagrant (Windows, MacOS, Linux)
    * (B) uses Ansible (installed on your local machine) to provision a VM managed by Vagrant (Linux, MacOS)

* (C) uses Ansible on your local machine to install mediaTUM on a remote host via SSH (purpose: production or testing) 

TL;DR
-----

Install mediaTUM VM ([Vagrant 1.5](http://www.vagrantup.com) and [VirtualBox](https://www.virtualbox.org/) must be installed) with:

    git clone https://github.com/mediatum/ansible-playbook-mediatum && cd ansible-playbook-mediatum && vagrant up inabox

and open [https://localhost:8081](https://localhost:8081)

Playbook Structure
------------------

This Playbook is based on the Ubuntu instructions from [mediaTUM Sourceforge](http://mediatum.sourceforge.net/documentation/installation-instructions/installation-linux/).

* the `playbook-mediatum` subdirectory contains the playbook itself
    * if you like to see what this playbook does, please look at the subdirectories of `playbook-mediatum/roles`. 
    * .yml files in `tasks/` directories contain the actual installation steps
    * if you like to know more about Ansible playbooks, please read the [Ansible Playbook Documentation](http://docs.ansible.com/playbooks.html)
* The `Vagrantfile` contains instructions for Vagrant how to setup a test virtual machine with mediaTUM automatically. 
     * This is described below in [(A)](#universal-vagrant-vm-installation-a) and [(B)](#using-ansible-for-vagrant-vm-provisioning-b).

Playbook Preparation
--------------------

In the `playbook-mediatum` subdirectory:

* (optional) Create a file named `mediatum_db_pass` with the MySQL mediaTUM database password. Passwords must be kept secret! 
The file can be destroyed after a successful installation. If you do not specify a password, it will be generated and written to the file
* (optional) copy `group_vars/all.example` to `group_vars/all` and customize settings

If the settings in `group_vars/all` are left unchanged, mediaTUM will be installed with __mediatum__ as server user and __mediatum_adm__ as admin user with default paths under `/srv`.

  
Universal Vagrant VM Installation (A)
-------------------------------------

If you like to install mediaTUM on your local machine for testing purposes, you can use the [Vagrant](http://vagrantup.com) installation method.
This will install mediaTUM in a separate virtual machine. No changes will be made to your local system. 
VirtualBox and Vagrant (version 1.5+) must be installed.
A Debian Wheezy 7.4 system is used for the VM. You can edit the `Vagrantfile` to choose another system type. (for example, a 32bit Ubuntu)

Just run and wait some minutes:

    vagrant up inabox

The mediaTUM port 8081 on localhost is forwarded to the VM, so mediaTUM can be reached at https://localhost:8081 after the installation
    
You can login with `Administrator` as username and `xadmin1` as password.

If something wents wrong in the provisioning (look for "Running provisioner" in the output) step, you can try running it again in debug mode:

    VA_VERBOSE="vvvv" vagrant provision inabox

This may give you more information about why it fails. SSH problems are the most common cause.

To SSH into the box, use

    vagrant ssh inabox

The vagrant user is able to use passwordless sudo. 

The VM can be removed with:

    vagrant destroy inabox

Installing Ansible
------------------

_needed for method (B) or (C)_

Follow the installation instructions in the [Ansible documentation](http://docs.ansible.com/intro_installation.html#installing-the-control-machine)

Example: a simple installation using git on a Debian / Ubuntu machine:
    
    sudo apt-get install python-pip git # if not installed
    # installation for current user
    pip install paramiko PyYAML jinja2 httplib2  --user --upgrade -I
    git clone git://github.com/ansible/ansible.git
    source ansible/hacking/env-setup

Run the `source` command above in new terminals before using the `ansible-playbook` command.


Using Ansible For Vagrant VM Provisioning (B)
---------------------------------------------

*read [Universal Vagrant VM Installation (A) ](#universal-vagrant-vm-installation-a) first!*

This works like (A), but uses a locally installed Ansible and is a bit faster.
A current Ansible (1.6+) is required. (see instructions above)
The commands work like in (A), but you have to specify the machine name *ext* after each command instead

Run:

    vagrant up ext

To SSH into the box, use

    vagrant ssh ext

The VM can be removed with:

    vagrant destroy ext


Using Ansible For Remote Installation (C)
----------------------------------------

This will run the playbook from your local machine ("control machine") and install mediaTUM, nginx and a MySQL Server on a remote host. 
The control machine must use some operating system which is supported by Ansible, Linux or MacOS, for example.

The target machine should use a supported Linux distribution from the following list (similar debian-based systems could work, too. Try it!)

* Debian 7 "Wheezy" (64bit)
* Ubuntu 12.04 Server "Precise Pangolin" (64 / 32bit)

More supported distributions will be added later on demand.
A current Ansible version (1.6+) is required.

### Setting Target Host

In the `playbook-mediatum` subdirectory:

* copy hosts.example to hosts and set target host in `[db]` and `[mediatum]` (must be the same host in both) sections.

### Host Preparation
The mediaTUM ansible playbook needs SSH access to an remote admin user who is able to sudo (with password is ok).
You can use the helper playbook `access.yml` to create such an user with proper SSH setup on a freshly installed Debian machine.

* configure host for root SSH access via password, if neccessary
* copy `access_vars.yml.example` to `access_vars.yml` and change the variables there
* run access playbook:

        ansible-playbook -i hosts access.yml -u root -k


### Running The Playbook
If the remote admin user has the same name as your current local user and must use sudo, run:

    ansible-playbook -i hosts site.yml -K

For passwordless sudo (if allowed), run without -K. To specify another user, use `-u <username>`.
See `ansible-playbook --help` for more options.

It's possible to specify the host group which is used on the cmdline:

    ansible-playbook -i hosts -e 'group=mediatum_new' site.yml -K
    
You can add -vvvv to the command line to see more information if something wents wrong. SSH problems are the most common cause.

After installation is completed, mediaTUM is running on Port 443 with default settings (HTTPS only). 
You can login with `Administrator` as username and `xadmin1` as password.


Stopping / Starting mediaTUM
----------------------------

The mediaTUM process and the nginx reverse proxy are controlled by [Supervisor](http://supervisor.org). 
`mediatum_adm_user` (default username: *mediatum\_adm*)

To restart both processes, use:
    
    supervisorctl restart mediatum:

To restart only the mediaTUM process:

    supervisorctl restart mediatum:mediatum-backend

To restart only the nginx process:

    supervisorctl restart mediatum:mediatum-nginx


