---
layout: post
title: "Using Vagrant to get an Ansible environment set up"
tags: vagrant
---

Vagrant is a tool for creating, managing and sharing Virtual Machines. It's really helpful for creating contained environments with specific requirements that can be easily built up, tore down and shared with others. It's especially helpful if, like me, you have to spend time working on a Windows laptop, because, well, Enterprise.

Following on from my [previous post](https://bordeltabernacle.github.io/2016/08/01/starting-out-with-ansible-cisco-and-network-automation.html), I've found that Vagrant is a fairly painless way to expose the network engineers I work with to Ansible. Once it's installed, I can share a zip file for them to unzip and run `vagrant up` and `vagrant ssh` in from the command line, and they're in a working Ansible environment. And if they mess something up, they can just destroy it and start again, no bother.

Vagrant manages VM's through a hypervisor, with the default being Virtualbox, though you can use VMware if you have the extra cash. Therefore we need to install Virtualbox first, from [here](https://www.virtualbox.org/wiki/Downloads). We're going ot grab the Windows `.exe` file.  Double click on it, and follow the defaults through to the end.

![vbox_download]({{ site.url }}/images/vbox_download.png)

Now we can install Vagrant, from [here](https://www.vagrantup.com/downloads.html), grabbing the Windows `.msi` file, double clicking on it once it's downloaded and following the defaults.

![vagrant_download]({{ site.url }}/images/vagrant_download.png)

Vagrant is a command line tool, so you're gonna have to be comfortable on the command line, not too much, just enough to know what it is, how to access it and how to navigate the directory structure, whether it's through `cmd.exe` or Powershell. I'm not going to go into any of that here, but I would recommend getting [cmder](http://cmder.net/) if you're going to spend any sort of time on the Windows command line.

So, at your command prompt, if all has been successful you should be able to type `vagrant --version` and get the version of vagrant you have installed:

```
Vagrant 1.8.4
```

Grand. The beauty of Vagrant is that it is configured from a simple text file, the *Vagrantfile*.  To create one, start a new directory, and then in it type `vagrant init`. This will define a new, highly commented *Vagrantfile*.  Read it, and get an idea of what the different options are. Like most things, you often start with a bare bones structure like this basic *Vagrantfile*, and build it up as you learn more and your requirements change. Here I'm going to explain the Vagrantfile I use to set up my Ansible machine.

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

vagrant_root = File.dirname(__FILE__)
project_name = "acp"

Vagrant.configure(2) do |config|
    config.vm.box = "ubuntu/trusty64"
    config.vm.box_url = "https://atlas.hashicorp.com/ubuntu/boxes/trusty64"
    config.vm.provider "virtualbox" do |vb|
        vb.memory = "1028"
        vb.cpus = "1"
    end

    config.vm.define "#{project_name}" do |machine|
        machine.vm.hostname = "#{project_name}"
        machine.vm.synced_folder "#{vagrant_root}/shared", "/home/vagrant/shared"
        machine.vm.provision :shell, :path => "#{vagrant_root}/ansible-setup.sh"
    end
end
```

Vagrant is written in Ruby, and the first two lines are to do with the Ruby language. Next up we have a couple of variables:

```ruby
vagrant_root = File.dirname(__FILE__)
project_name = "acp"
```

* `vagrant_root` is the current working directory of the Vagrantfile
* `project_name` is the name of the project, here it is `acp` for Ansible Cisco Playground. 

The next section of the *Vagrantfile* sets up the configuration:

* `config.vm.box = "ubuntu/trusty64"` defines the base operating system, or Vagrant *box*, for the VM, here we're using the official Ubuntu 14.04 Server image, but this can be changed to any one of a multitude of Vagrant *boxes*, that can be found [here](https://vagrantcloud.com/boxes/search)
* `config.vm.box_url = "https://atlas.hashicorp.com/ubuntu/boxes/trusty64"` is the url where this box is
* `config.vm.provider "virtualbox" do |vb|` this says that if the `provider`, the hypervisor, is Virtualbox, do the following
* `vb.memory = "1028"` gives the VM 1 GB of RAM
* `vb.cpus = "1"` gives the VM 1 CPU

The last section is where it gets interesting...

* `config.vm.define "#{project_name}" do |machine|` here we start to define some parameters for the machine
* `machine.vm.hostname = "#{project_name}"` defines the hostname of the machine as the project name
* `machine.vm.synced_folder "#{vagrant_root}/shared", "/home/vagrant/shared"` this sets up a shared folder between the VM and the host computer, my laptop. `"#{vagrant_root}/shared"` is a folder on the host, within the same directory as the *Vagrantfile*, `"/home/vagrant/shared"` is a folder in the vagrant user's home directory on the VM. These folders will be synced, any changes made in one will be reflected in the other. This means I can edit and version control files on my host machine, using my usual workflow, and I can use them easily in the VM, rather than having to do this work inside the VM.
* `machine.vm.provision :shell, :path => "#{vagrant_root}/ansible-setup.sh"` this defines the path to a shell script that provisions the VM, installing Ansible. There are different options for provisioning the Vm, including Ansible itself, woah, meta! but I find for small environments like this a shell script does fine.

This is the `ansible-setup.sh` file:

```bash
#!/bin/bash
TZ=Europe/London

echo "+-----------------------------------------------+"
echo "| Provisioning Ansible Cisco Playground Machine |"
echo "+-----------------------------------------------+"
echo "Setting timezone..."
sudo timedatectl set-timezone $TZ > /dev/null 2>&1
echo "Adding dependencies..."
sudo apt-get install software-properties-common > /dev/null 2>&1
echo "Adding Ansible repo..."
sudo apt-add-repository ppa:ansible/ansible > /dev/null 2>&1
echo "Updating apt-get..."
sudo apt-get -y update > /dev/null 2>&1
echo "Installing Git..."
sudo apt-get -y install git > /dev/null 2>&1
echo "Installing Ansible..."
sudo apt-get -y install ansible > /dev/null 2>&1
echo "Installing ntc-ansible module..."
sudo apt-get -y install python-pip > /dev/null 2>&1
sudo apt-get -y install zlib1g-dev libxml2-dev libxslt-dev python-dev > /dev/null 2>&1
sudo pip install ntc-ansible > /dev/null 2>&1
git clone https://github.com/networktocode/ntc-ansible --recursive > /dev/null 2>&1
sudo rm -r /home/vagrant/shared/library > /dev/null 2>&1
sudo mv /home/vagrant/ntc-ansible/library /home/vagrant/shared/library > /dev/null 2>&1
echo "+----------------------------------------------+"
echo "| Ansible Cisco Playground Machine Provisioned |"
echo "+----------------------------------------------+"
echo "|                Go build stuff!               |"
echo "+----------------------------------------------+"
```

There's a few things going on in there, including the Ansible installation and the installation of [Jason Edelman's](http://jedelman.com/) excellent third party Ansible library [ntc-ansible](https://github.com/networktocode/ntc-ansible/)

Now, if we do a `tree /F` command in the project directory, we can see everything we now have, the *Vagrantfile*, the provision script, the shared folder (currently empty), and a `.vagrant` directory I've never had to do anything with since working with Vagrant.

```
Folder PATH listing for volume Windows
Volume serial number is A879-5C69
C:.
│   Vagrantfile
├───ansible-setup.sh
├───shared
└───.vagrant
    └───machines
        └───acp
```

Right, we're ready to go. I've found the vagrant commands I use most often are...

* `vagrant up` brings the machine up
* `vagrant ssh` establishes an ssh connection to the VM
* `vagrant halt` shuts the VM down
* `vagrant reload` restarts the VM
* `vagrant destroy` destroys the VM

On the command line, inside the vagrant project directory, type in `vagrant up`, and this should, fingers crossed, bring up the new VM.  If this is the first Vagrant machine you've created it's going to take some time to download the Ubuntu image, 15-20 mins maybe.

