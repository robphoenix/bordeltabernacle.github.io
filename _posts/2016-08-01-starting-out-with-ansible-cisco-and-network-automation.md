---
layout: post
title: "Starting out with Ansible, Cisco and Network Automation"
tags: ansible cisco
---

Recently I've been spending more time exploring network automation, using
homegrown scripts, Cisco's APIC-EM, and Ansible. I like Ansible, and since
release 2.1, they've started including dedicated networking modules, which is
great. I've only scratched the surface of Ansible, but I've already had great
success with it in the lab. So, let's get it set up and doing our work for us,
yeah?

<!--more-->

Ansible requires Python 2, and doesn't support Python 3. It also doesn't work
with Windows as the control machine. So I'm going to install Ansible on an Ubuntu
14.04 virtual machine, though you could do the same on the more recent Ubuntu
16.04 as well.

```bash
sudo apt-get install software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get -y update
sudo apt-get -y install ansible
```

If that all goes well, typing in `ansible --version` should show the following
output:

```bash
ansible 2.1.0.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = Default w/o overrides
```

The first thing we need to do is specify our target devices that we want to
connect to. This is done using an inventory file. In my lab I have 3 Cisco
2921 routers, each with a minimal configuration, enough to ssh to.  So,
hostname, username/secret, ip domain name, and ssh crypto.
These are hooked up to a Cisco switch with no config on, and
then my laptop is connected by ethernet, with a static ip address on
the same `192.168.0.0/24` network, so my Ansible VM will just get NAT'd through
this address. Pretty basic, but enough for these purposes.

![routers]({{ site.url }}/images/IMAG0131.jpg)

So, in a directory created for this Ansible project, I've created a file
called *hosts*, without a file extension. As far as I understand, this file can
be called anything, and can have a file extension if it makes your life easier,
but convention has it named *hosts*, or sometimes *inventory*, and the contents
have to be in an INI format. This is where we're going to list our hosts. My
hosts file contains my 3 2921 routers and looks like so:

```ini
[routers]
router-one ansible_host=192.168.0.1
router-two ansible_host=192.168.0.2
router-three ansible_host=192.168.0.3

[routers:vars]
ansible_user=vagrant
ansible_password=vagrant
```

Here, I have a group called `routers` that lists each host's name,
followed by the `ansible_host` variable, which is the ip address of the host.
Below that are listed a couple of variables for every host in the `routers`
group, under the heading `[routers:vars]`; `ansible_user` is the username on
the host, and `ansible_password` is the password on the device, these correspond
with this configuration on each router:

```text
username vagrant privilege 15 secret 5 $1$lGlV$sF4MKyPYZlteNVgGHo9tL1
```

We can now use the `ansible` command line client to interact with our routers.
Most Ansible guides deal with servers, and start off by using the Ansible `ping`
module. We can't do that here. Like most Ansible modules, the `ping` module
works by bundling up some Python code, which it delivers to the device via SSH
and executes. We can't run Python code on our Cisco routers, so we'll take a
different approach to get started.

The raw module *'executes a low-down and dirty SSH command'*, perfect.

So, let's run the following comand:

```bash
ansible all -m raw -a "executable='' sh run | inc hostname" -i hosts
```

* `ansible` is the command line client
* `all` specifies we want to target all hosts in the hosts file, we
could just as easily have used `routers` to only target the routers group, if we
had multiple groups
* `-m raw` specifies the raw module
* `-a "executable='' sh run | inc hostname"` specifies the module's arguments,
the command we want to send; `sh run | inc hostname`
* `-i hosts` specifies the inventory file we want to use

And we get the following output:

```
router-one | SUCCESS | rc=0 >>
hostname router-one
Connection to 192.168.0.1 closed by remote host.


router-two | SUCCESS | rc=0 >>
hostname router-two
Connection to 192.168.0.2 closed by remote host.


router-three | SUCCESS | rc=0 >>
hostname router-three
Connection to 192.168.0.3 closed by remote host.
```

Success, excellent! We get back the hostname of each router, in a second or so.
You may have noticed the command sent to the module includes `executable='' `,
this is because the raw module is expecting to encounter bash on a server,
rather than the IOS command line. If we run it without this we get the following
error:

```
router-three | SUCCESS | rc=0 >>
Line has invalid autocommand "/bin/sh -c 'sh run | inc hostname && sleep 0'"Connection to 192.168.0.3 closed by remote host.


router-one | SUCCESS | rc=0 >>
Line has invalid autocommand "/bin/sh -c 'sh run | inc hostname && sleep 0'"

router-two | SUCCESS | rc=0 >>
Line has invalid autocommand "/bin/sh -c 'sh run | inc hostname && sleep 0'"Connection to 192.168.0.2 closed by remote host.
```

There was [an issue](https://github.com/ansible/ansible-modules-core/issues/3332)
on the Ansible repo, so I'm not sure if this is something that will change in
the future. Anyway, we won't be using the `raw` module much, so it doesn't really matter.
This is a quick way to get facts off your devices, and you can send configuration
changes this way if you really want. It's always worth messing around on a lab
router that doesn't support critical company infrastructure, if you can, but
Ansible has a better way to design and implement repeatable tasks, called
*Playbooks*, which I'll look at in another post.

Some further interesting sources of information:

* [Ansible Network Automation Webinar](https://www.ansible.com/webinars-training/automating-your-network)
* [Kirk Byers](https://pynet.twb-tech.com/blog/automation/cisco-ios.html)
* [Jason Edelman](http://jedelman.com/home/network-automation-with-ansible-dynamically-configuring-interface-descriptions/)
* [Patrick Ogenstad](https://networklore.com/ansible-device-versions/)
* [Michael Kashin](https://networkop.github.io/blog/2015/06/24/ansible-intro/)
* [Packet Pushers](http://packetpushers.net/ansible-cisco-snmp/)





