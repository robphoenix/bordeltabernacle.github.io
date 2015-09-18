---
layout: post
title:  "Installing KVM & OVS on Ubuntu"
date:   2015-04-24
tags:   virtualization openvswitch
---

Setting up the lab to explore Linux virtualisation & OpenvSwitch

<!--more-->
KVM & OpenvSwitch are technologies I've wanted to learn for a while now. I think it'll really help my understanding and knowledge of Linux and the underlying mechanics of virtualisation and how it interacts with the physical network.

Installing KVM is already well documented [here][1], but for completeness here is my installation process:

First, update, and [check][2] your hardware can support virtualisation.

```bash
$ sudo apt-get -y update
$ egrep -c '(vmx|svm)' /proc/cpuinfo
8
$ kvm-ok
INFO: /dev/kvm exists
KVM acceleration can be used
```

`egrep` is like `grep`, a command that prints out any line with the given regular expression. So, here we're searching the ***cpuinfo*** file for any lines containing ***vmx*** or ***svm***. ***vmx*** is the flag that virtualisation is enabled in the BIOS for Intel CPUs & ***svm*** is the same for AMD CPUs. The `-c` option is for count, so here we only print out the number of lines that match, rather than the lines themselves.
So, a result of 1 or more means virtualisation is enabled. Here, I've got 8 lines in the ***cpuinfo*** file matching ***vmx***.
If you do get a 0, reboot your computer, go into the BIOS menu and make sure you've got virtualisation enabled.

The [`kvm-ok`][3] program verifies whether your machine is able to run KVM virtual machines. It actually checks the ***cpuinfo*** file, like we did in the previous command. It also checks whether the kernel has detected the CPUs Virtualisation Technology (VT) capability, and looks to see if `/dev/kvm` exists.

The capabilities exist, so let's install KVM.

```bash
$ sudo apt-get install qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils virtinst
```

From the Ubuntu [pages][1]:

- libvirt-bin provides libvirtd which you need to administer qemu and kvm instances using libvirt
- qemu-kvm is the backend
- ubuntu-vm-builder powerful command line tool for building virtual machines
- bridge-utils provides a bridge from your network to the virtual machines
- virtinst provides an easy way to provision operating systems into virtual machines and an API to the virt-manager application for its graphical VM creation wizard.

Ensure the user is added to the `libvirtd` & `kvm` groups.

```bash
$ sudo adduser `id -un` libvirtd && sudo adduser `id -un` kvm
The user `rob' is already a member of `libvirtd'.
The user `rob' is already a member of `kvm'.
```

I found it was good to do a reboot here, just to make sure this membership is in effect.

Verify the installation. When we get round to installing some VMs, they should show up in this list.

```bash
$ virsh -c qemu:///system list
Id Name State
----------------------------------------------------
```

And we're all set.

On a separate machine, I've set up a [Xubuntu][4] VM, and installed ***Virtual Machine Manager (VMM)***, just to give me a visual understanding of my VMs if need be.

```bash
$ sudo apt-get install virt-manager
```


***VMM*** is super simple and intuitive. I love using the command line, but I still find having visual cues really helps my understanding of a situation, too much time in Windows & vSphere perhaps!

For the OpenvSwitch installation I pretty faithfully followed Scott Lowe's [example][5], with a couple of differences.

Right, let's get up-to-date, as per.

```bash
$ sudo apt-get -y update && sudo apt-get -y dist-upgrade
```


Make way for OVS by removing the default ***libvirt*** bridge.

```bash
$ sudo virsh net-destroy default
$ sudo virsh net-autostart --disable default
```


Remove [***ebtables***][6], a Linux ethernet firewall.

```bash
$ sudo aptitude purge ebtables
```


Install OpenvSwitch.

```bash
$ sudo apt-get install openvswitch-controller openvswitch-switch openvswitch-datapath-source
```


The `openvswitch-brcompat` has apparently been removed now from OVS, so it can be ignored.
Once that's finished, try starting OVS, though I found it was already running.

```bash
$ sudo service openvswitch-switch start
start: Job is already running: openvswitch-switch
```


Run the OVS show command, you should just get an empty config.

```bash
$ sudo ovs-vsctl show
7d84d624-632b-4d2c-93ad-56ad3cc543cc
ovs_version: "2.0.2"
```


The `ovs-vsctl` command, according to the [man page][8], is a *"utility for querying and configuring ovs-vswitchd"*. `ovs-vswitchd` is, again according to the [man page][9], the daemon that manages and controls the OVS switch(es). As far as I understand it `ovs-vsctl` queries and configures `ovsdb-server` *(which provides the interface to the OVS databases* [[man page]][11] *)*, and `ovs-vswitchd` then implements the changes. To be honest I'm not entirely sure of this process, I need to spend some more time reading [this][10] I think.
Anyway, `ovs-vsctl show` "Prints a brief overview of the database contents." Of which, currently, we have none.

Now, the OVS bridge has to be created. When I was doing this I was SSH'd into the NIC I was configuring (em1), and creating the bridge brought the interface down. So, either make sure you have physical access to the machine, or SSH into another NIC.

```bash
$ sudo ovs-vsctl add-br br0
$ sudo ovs-vsctl add-port br0 em1
```


Run the show command again, and we should have a configuration.

```bash
$ sudo ovs-vsctl show
7d84d624-632b-4d2c-93ad-56ad3cc543cc
Bridge "br0"
Port "em1"
Interface "em1"
Port "br0"
Interface "br0"
type: internal
ovs_version: "2.0.2"
```


Now, the interfaces need to be configured.

```bash
$ sudo vim /etc/network/interfaces

# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto em1
iface em1 inet static

# The OVS bridge interface
auto br0
iface br0 inet static
address 10.0.0.4
network 10.0.0.0
netmask 255.255.0.0
broadcast 10.0.255.255
gateway 10.0.0.2
dns-nameservers 8.8.8.8 8.8.4.4
dns-search test.local
bridge_ports em1
bridge_fd 9
bridge_hello 2
bridge_maxage 12
bridge_stp off
```

This is just a copy of Scott's configuration, with my network details in it.
At this point I rebooted the server and ran `ifconfig em1 up` which brought my interface back up and I was able to SSH back into the machine.

Where the result of `ifconfig` was previously...

```bash
$ ifconfig
em1 Link encap:Ethernet HWaddr a4:ba:db:4d:0f:82
inet addr:10.0.0.4 Bcast:10.0.255.255 Mask:255.255.0.0
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:74620 errors:0 dropped:0 overruns:0 frame:0
TX packets:2094 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:7873976 (7.8 MB) TX bytes:216238 (216.2 KB)
```

is now...

```bash
$ ifconfig
br0 Link encap:Ethernet HWaddr a4:ba:db:4d:0f:82
inet addr:10.0.0.4 Bcast:10.0.255.255 Mask:255.255.0.0
inet6 addr: fe80::a6ba:dbff:fe4d:f82/64 Scope:Link
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:73075 errors:0 dropped:257 overruns:0 frame:0
TX packets:2101 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:0
RX bytes:6283264 (6.2 MB) TX bytes:199006 (199.0 KB)

em1 Link encap:Ethernet HWaddr a4:ba:db:4d:0f:82
UP BROADCAST RUNNING MULTICAST MTU:1500 Metric:1
RX packets:74620 errors:0 dropped:0 overruns:0 frame:0
TX packets:2094 errors:0 dropped:0 overruns:0 carrier:0
collisions:0 txqueuelen:1000
RX bytes:7873976 (7.8 MB) TX bytes:216238 (216.2 KB)
```

So, I'm actually connecting to the bridge interface rather than the NIC.

Hopefully, this is just the beginning of an interesting adventure, in the meantime I'm going to read [this][7] again.


[1]: https://help.ubuntu.com/community/KVM/Installation
[2]: http://www.cyberciti.biz/faq/linux-xen-vmware-kvm-intel-vt-amd-v-support/
[3]: http://manpages.ubuntu.com/manpages//lucid/man1/kvm-ok.1.html
[4]: http://xubuntu.org/
[5]: http://blog.scottlowe.org/2012/08/17/installing-kvm-and-open-vswitch-on-ubuntu/
[6]: http://linux.die.net/man/8/ebtables
[7]: http://keepingitclassless.net/2013/10/introduction-to-open-vswitch/
[8]: http://openvswitch.org/support/dist-docs/ovs-vsctl.8.pdf
[9]: http://openvswitch.org/support/dist-docs/ovs-vswitchd.8.pdf
[10]: http://networkstatic.net/getting-started-ovsdb/
[11]: http://openvswitch.org/support/dist-docs/ovsdb-server.1.pdf
