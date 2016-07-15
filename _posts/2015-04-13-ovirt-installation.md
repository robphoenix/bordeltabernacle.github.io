---
layout: post
title:  "Installing oVirt"
date:   2015-04-13
tags:   virtualization
---

Installing [oVirt][1], the upstream project of  [Red Hat's Enterprise Virtualization][2] product, and [vSphere][3] alternative.

<!--more-->
I started this install on Fedora, using the latest release, 21, ignoring the oVirt website where it said *"Important: It is recommended that you install oVirt on Fedora 20"*, assuming the latest is the greatest, only to find that oVirt is not available on Fedora 21.  This was the first bump on a rocky road.  I did start off installing on Fedora 20 but ended up using CentOS 7 instead.  If you're interested in using Fedora 20, you can download it [here][4] or [here][5].

The main issue I found was DNS; resolving the server's hostname.  I'm doing this on a private lab network, and haven't got round to setting up a DNS server yet, so I had to configure my hosts file and set up [dnsmasq][6]. From oVirt's troubleshooting page...

*"**When running engine-setup, I get the message "myhost.local did not resolve into an IP address", but setting up bind locally is hard. Is there an easy way to spoof full DNS locally?***
*The easiest solution is to use dnsmasq for DNS. You then use the IP address of your engine as your DNS server, and in /etc/dnsmasq.conf you point to your regular DNS servers with "server=8.8.8.8" (for example). You will also need to open port 53 in iptables to enable computers on your home network to use this DNS server. To do this, add the line "-A INPUT -m state --state NEW -m udp -p udp --dport 53 -j ACCEPT" to your iptables configuration, remembering to add it also to any configuration files required to ensure that the option persists across reboots."*

First things first, after a minimal install of CentOS 7; update, reboot & install a few programs:
- net-tools for the ifconfig command
- vim as my text editor of choice
- bind-utils to check my DNS
- dnsmasq to set up my DNS


```bash
$ sudo yum update
$ sudo reboot
$ sudo yum -y install net-tools vim bind-utils dnsmasq
```

A quick check of the DNS shows the hostname is not resolving to my ip address:

```bash
$ host 10.0.0.5
Host 5.0.0.10.in-addr.arpa. not found: 3(NXDOMAIN)

$ host oVirt-01
oVirt-01.lab-test.local has address 127.0.53.53
oVirt-01.lab-test.local mail is handled by 10 your-dns-needs-immediate-attention.dev.
```

Configure hosts file:

```bash
# ==={ open up the hosts file }===
$ sudo vim /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
# ==={ add in server ip address & fqdn }===
10.0.0.5 oVirt-01.lab-test.local oVirt01
```

Configure dnsmasq:

```bash
$ sudo systemctl enable /usr/lib/systemd/system/dnsmasq.service
$ sudo systemctl start dnsmasq.service

# ==={ open up the dnsmasq.conf file }===
$ sudo vim /etc/dnsmasq.conf
# ==={ add in your DNS servers, here I'm just using Google's }===
server=8.8.8.8
server=8.8.4.4
# ==={ add in the listen-address for the localhost }===
listen-address=127.0.0.1
# ==={ open up the resolv.conf file }===
sudo vim /etc/resolv.conf
# ==={ add in the localhost address as your first nameserver }===
search lab-test.local
nameserver 127.0.0.1
nameserver 8.8.8.8
nameserver 8.8.4.4
# ==={ restart the dnsmasq service }===
$ sudo systemctl restart dnsmasq.service
```

Right, let's check our DNS again:

```bash
$ host 10.90.0.5
5.0.0.10.in-addr.arpa domain name pointer oVirt-01.lab-test.local.

$ host oVirt-01
oVirt-01.lab-test.local has address 10.0.0.5
oVirt-01.lab-test.local mail is handled by 10 your-dns-needs-immediate-attention.dev.
```

Excellent, success!

I have to admit that the next bit concerning firewall settings I don't fully understand, it has been added to my *'Things to research and understand'* list.  But, this is what I did so I'll add it here.  For those more knowledgeable than myself, please feel free to enlighten/correct me.

```bash
# ==={ this stops the firewalld service to use iptables instead }===
$ sudo systemctl mask firewalld.service
$ sudo systemctl stop firewalld
$ sudo yum install iptables-services
$ systemctl enable iptables
$ systemctl start iptables

# ==={ a quick check of iptables }===
$ sudo iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

# ==={ add in the iptables line suggested by oVirt }===
$ sudo iptables -A INPUT -m state --state NEW -m udp -p udp --dport 53 -j ACCEPT
# ==={ and check iptables again }===
$ sudo iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
ACCEPT     udp  --  anywhere             anywhere             state NEW udp dpt:domain

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

# ==={ save the iptables configuration }===
$ sudo iptables-save
# Generated by iptables-save v1.4.21 on Thu Apr  9 16:58:37 2015
*filter
:INPUT ACCEPT [102:8210]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [22:3728]
-A INPUT -p udp -m state --state NEW -m udp --dport 53 -j ACCEPT
COMMIT
# Completed on Thu Apr  9 16:58:37 2015
```

Again I don't fully understand the next configuration concerning Network Manager.  I was aware of it reading Jason Brooks [excellent oVirt documentation][7] but not knowing why it was done, I actually didn't do it initially.  My oVirt setup failed and so I added it in and the setup worked, so it obviously does something! *(Yep, added to the **'Things to research and understand'** list!)*


```bash
$ sudo systemctl stop NetworkManager.service
$ sudo systemctl mask NetworkManager.service
ln -s '/dev/null' '/etc/systemd/system/NetworkManager.service'
$ sudo service network start
Starting network (via systemctl):                                                       [  OK  ]
$ sudo chkconfig network on
```

And on to installing oVirt.  By the way, I'm just installing the All-in-One, on a single server, and pretty much accepting all the defaults.

```bash
$ sudo yum localinstall http://resources.ovirt.org/pub/yum-repo/ovirt-release35.rpm

$ sudo yum install -y ovirt-engine-setup-plugin-allinone

$ sudo engine-setup


[ INFO  ] Stage: Initializing
[ INFO  ] Stage: Environment setup
          Configuration files: ['/etc/ovirt-engine-setup.conf.d/10-packaging-aio.conf', '/etc/ovirt-engine-setup.conf.d/10-packaging-jboss.conf', '/etc/ovirt-engine-setup.conf.d/10-packaging.conf']
          Log file: /var/log/ovirt-engine/setup/ovirt-engine-setup-20150409170730-i9jy0f.log
          Version: otopi-1.3.1 (otopi-1.3.1-1.el7)
[ INFO  ] Hardware supports virtualization
[ INFO  ] Stage: Environment packages setup
[ INFO  ] Stage: Programs detection
[ INFO  ] Stage: Environment setup
[ INFO  ] Stage: Environment customization

          --== PRODUCT OPTIONS ==--

          Configure Engine on this host (Yes, No) [Yes]:
          Configure WebSocket Proxy on this host (Yes, No) [Yes]:

          --== PACKAGES ==--

[ INFO  ] Checking for product updates...
[ INFO  ] No product updates found

          --== ALL IN ONE CONFIGURATION ==--
# ==={ It's important to answer Yes to this next question if you're setting up the oVirt-engine node to also run VMs }===
          Configure VDSM on this host? (Yes, No) [No]: Yes
          Local storage domain path [/var/lib/images]:
          Local storage domain name [local_storage]:

          --== NETWORK CONFIGURATION ==--

          Host fully qualified DNS name of this server [oVirt-01.lab-test.local]:

          --== DATABASE CONFIGURATION ==--

          Where is the Engine database located? (Local, Remote) [Local]:
          Setup can configure the local postgresql server automatically for the engine to run. This may conflict with existing applications.
          Would you like Setup to automatically configure postgresql and create Engine database, or prefer to perform that manually? (Automatic, Manual) [Automatic]:

          --== OVIRT ENGINE CONFIGURATION ==--

          Engine admin password:
          Confirm engine admin password:
          Application mode (Virt, Gluster, Both) [Both]:

          --== PKI CONFIGURATION ==--

          Organization name for certificate [ab-testing.dev]:

          --== APACHE CONFIGURATION ==--

          Setup can configure the default page of the web server to present the application home page. This may conflict with existing applications.
          Do you wish to set the application as the default page of the web server? (Yes, No) [Yes]:
          Setup can configure apache to use SSL using a certificate issued from the internal CA.
          Do you wish Setup to configure that, or prefer to perform that manually? (Automatic, Manual) [Automatic]:

          --== SYSTEM CONFIGURATION ==--

          Configure an NFS share on this server to be used as an ISO Domain? (Yes, No) [Yes]:
          Local ISO domain path [/var/lib/exports/iso]:
          Local ISO domain ACL - note that the default will restrict access to oVirt-01.lab-test.local only, for security reasons [oVirt-01.lab-test.local(rw)]:
          Local ISO domain name [ISO_DOMAIN]:

          --== MISC CONFIGURATION ==--


          --== END OF CONFIGURATION ==--

[ INFO  ] Stage: Setup validation
[WARNING] Less than 16384MB of memory is available

          --== CONFIGURATION PREVIEW ==--

          Application mode                        : both
          Update Firewall                         : False
          Host FQDN                               : oVirt-01.lab-test.local
          Engine database name                    : engine
          Engine database secured connection      : False
          Engine database host                    : localhost
          Engine database user name               : engine
          Engine database host name validation    : False
          Engine database port                    : 5432
          Engine installation                     : True
          NFS setup                               : True
          PKI organization                        : lab-test.local
          NFS mount point                         : /var/lib/exports/iso
          NFS export ACL                          : oVirt-01.lab-test.local(rw)
          Configure VDSM on this host             : True
          Local storage domain directory          : /var/lib/images
          Configure local Engine database         : True
          Set application as default page         : True
          Configure Apache SSL                    : True
          Configure WebSocket Proxy               : True
          Engine Host FQDN                        : oVirt-01.lab-test.local

          Please confirm installation settings (OK, Cancel) [OK]:
[ INFO  ] Stage: Transaction setup
[ INFO  ] Stopping engine service
[ INFO  ] Stopping ovirt-fence-kdump-listener service
[ INFO  ] Stopping websocket-proxy service
[ INFO  ] Stage: Misc configuration
[ INFO  ] Stage: Package installation
[ INFO  ] Stage: Misc configuration
[ INFO  ] Initializing PostgreSQL
[ INFO  ] Creating PostgreSQL 'engine' database
[ INFO  ] Configuring PostgreSQL
[ INFO  ] Creating/refreshing Engine database schema
[ INFO  ] Creating CA
[ INFO  ] Configuring WebSocket Proxy
[ INFO  ] Generating post install configuration file '/etc/ovirt-engine-setup.conf.d/20-setup-ovirt-post.conf'
[ INFO  ] Stage: Transaction commit
[ INFO  ] Stage: Closing up
[ INFO  ] Restarting nfs services
[ ERROR ] Failed to execute stage 'Closing up': Command '/bin/systemctl' failed to execute
[ INFO  ] Stage: Clean up
          Log file is located at /var/log/ovirt-engine/setup/ovirt-engine-setup-20150409170730-i9jy0f.log
[ INFO  ] Generating answer file '/var/lib/ovirt-engine/setup/answers/20150409171042-setup.conf'
[ INFO  ] Stage: Pre-termination
[ INFO  ] Stage: Termination
[ ERROR ] Execution of setup failed
```

This is when I configured NetworkManager, and started again:


```bash
$ sudo engine-cleanup
$ sudo engine-setup
[ INFO  ] Stage: Initializing
[ INFO  ] Stage: Environment setup
          Configuration files: ['/etc/ovirt-engine-setup.conf.d/10-packaging-aio.conf', '/etc/ovirt-engine-setup.conf.d/10-packaging-jboss.conf', '/etc/ovirt-engine-setup.conf.d/10-packaging.conf', '/etc/ovirt-engine-setup.conf.d/20-setup-aio.conf', '/etc/ovirt-engine-setup.conf.d/20-setup-ovirt-post.conf']
          Log file: /var/log/ovirt-engine/setup/ovirt-engine-setup-20150409171624-ry3uj4.log
          Version: otopi-1.3.1 (otopi-1.3.1-1.el7)
[ INFO  ] Stage: Environment packages setup
[ INFO  ] Stage: Programs detection
[ INFO  ] Stage: Environment setup
[ INFO  ] Stage: Environment customization

          --== PRODUCT OPTIONS ==--


          --== PACKAGES ==--

[ INFO  ] Checking for product updates...
[ INFO  ] No product updates found

          --== ALL IN ONE CONFIGURATION ==--


          --== NETWORK CONFIGURATION ==--


          --== DATABASE CONFIGURATION ==--


          --== OVIRT ENGINE CONFIGURATION ==--

          Skipping storing options as database already prepared

          --== PKI CONFIGURATION ==--


          --== APACHE CONFIGURATION ==--


          --== SYSTEM CONFIGURATION ==--


          --== MISC CONFIGURATION ==--


          --== END OF CONFIGURATION ==--

[ INFO  ] Stage: Setup validation
[WARNING] Less than 16384MB of memory is available
[ INFO  ] Cleaning stale zombie tasks

          --== CONFIGURATION PREVIEW ==--

          Update Firewall                         : False
          Host FQDN                               : oVirt-01.lab-test.local
          Engine database name                    : engine
          Engine database secured connection      : False
          Engine database host                    : localhost
          Engine database user name               : engine
          Engine database host name validation    : False
          Engine database port                    : 5432
          Engine installation                     : True
          NFS mount point                         : /var/lib/exports/iso
          Configure WebSocket Proxy               : True
          Engine Host FQDN                        : oVirt-01.lab-test.local

          Please confirm installation settings (OK, Cancel) [OK]:
[ INFO  ] Cleaning async tasks and compensations
[ INFO  ] Checking the Engine database consistency
[ INFO  ] Stage: Transaction setup
[ INFO  ] Stopping engine service
[ INFO  ] Stopping ovirt-fence-kdump-listener service
[ INFO  ] Stopping websocket-proxy service
[ INFO  ] Stage: Misc configuration
[ INFO  ] Stage: Package installation
[ INFO  ] Stage: Misc configuration
[ INFO  ] Backing up database localhost:engine to '/var/lib/ovirt-engine/backups/engine-20150409171646.q2VAjg.dump'.
[ INFO  ] Creating/refreshing Engine database schema
[ INFO  ] Configuring WebSocket Proxy
[ INFO  ] Generating post install configuration file '/etc/ovirt-engine-setup.conf.d/20-setup-ovirt-post.conf'
[ INFO  ] Stage: Transaction commit
[ INFO  ] Stage: Closing up

          --== SUMMARY ==--

[WARNING] Less than 16384MB of memory is available
          SSH fingerprint: D4:A0:EB:C5:5F:20:2D:74:B0:0B:D6:4D:27:22:45:58
          Internal CA C5:6C:90:54:4E:B6:0B:1F:AC:B0:81:42:17:F6:01:48:45:57:9E:12
          Web access is enabled at:
              http://oVirt-01.lab-test.local:80/ovirt-engine
              https://oVirt-01.lab-test.local:443/ovirt-engine
          In order to configure firewalld, copy the files from
              /etc/ovirt-engine/firewalld to /etc/firewalld/services
              and execute the following commands:
              firewall-cmd -service ovirt-postgres
              firewall-cmd -service ovirt-https
              firewall-cmd -service ovirt-fence-kdump-listener
              firewall-cmd -service ovirt-websocket-proxy
              firewall-cmd -service ovirt-nfs
              firewall-cmd -service ovirt-http
          The following network ports should be opened:
              tcp:111
              tcp:2049
              tcp:32803
              tcp:443
              tcp:5432
              tcp:6100
              tcp:662
              tcp:80
              tcp:875
              tcp:892
              udp:111
              udp:32769
              udp:662
              udp:7410
              udp:875
              udp:892
          An example of the required configuration for iptables can be found at:
              /etc/ovirt-engine/iptables.example

          --== END OF SUMMARY ==--

[ INFO  ] Starting engine service
[ INFO  ] Restarting httpd
[ INFO  ] Stage: Clean up
          Log file is located at /var/log/ovirt-engine/setup/ovirt-engine-setup-20150409171624-ry3uj4.log
[ INFO  ] Generating answer file '/var/lib/ovirt-engine/setup/answers/20150409171724-setup.conf'
[ INFO  ] Stage: Pre-termination
[ INFO  ] Stage: Termination
[ INFO  ] Execution of setup completed successfully
```

A look at the iptables configuration:

```bash
$ sudo vim /etc/sysconfig/iptables
=====
# oVirt default firewall configuration. Automatically generated by vdsm bootstrap script.
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [10765:598664]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
# vdsm
-A INPUT -p tcp --dport 54321 -j ACCEPT
# SSH
-A INPUT -p tcp --dport 22 -j ACCEPT
# snmp
-A INPUT -p udp --dport 161 -j ACCEPT
# DNS
-A INPUT -m state --state NEW -m udp -p udp --dport 53 -j ACCEPT

# libvirt tls
-A INPUT -p tcp --dport 16514 -j ACCEPT

# guest consoles
-A INPUT -p tcp -m multiport --dports 5900:6923 -j ACCEPT
-A INPUT -p tcp -m multiport --dports 5634:6166 -j ACCEPT

# migration
-A INPUT -p tcp -m multiport --dports 49152:49216 -j ACCEPT
-A INPUT -p tcp -m state --state NEW

# Reject any other input traffic
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -m physdev ! --physdev-is-bridged -j REJECT --reject-with icmp-host-prohibited
COMMIT
~
~
~
~
~
~
"/etc/sysconfig/iptables" 33L, 969C
```

Let's visit the server in a web browser; http://oVirt-01.lab-test.local:80/ovirt-engine sign in using username *admin* and the password configured during setup and there we have our oVirt management portal:

![oVirt screenshot]({{ site.url }}/images/ovirt-mgmt-portal1.png)


[1]: http://www.ovirt.org/Home
[2]: https://www.redhat.com/en/technologies/virtualization/enterprise-virtualization
[3]: https://www.vmware.com/products/vsphere/
[4]: http://torrents.fedoraproject.org/
[5]: http://www.cyberciti.biz/linux-news/fedora-linux-20-download-cd-dvd-iso/
[6]: http://www.thekelleys.org.uk/dnsmasq/doc.html
[7]: http://blog.jebpages.com/archives/up-and-running-with-ovirt-3-1-edition/
