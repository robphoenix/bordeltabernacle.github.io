---
layout: post
title: "Network Automation with Ansible Playbooks"
date: 2016-08-11
tags: ansible cisco
---

Following on from my [previous post](pp) I want to dig a little deeper into
building and managing the automation of tasks with Ansible. This is done with a
mechanism called *playbooks*, and within them a series of *plays*. What this
essentially means is that you can declare what you want to happen in a text file, the
playbook, as a series of actions, the plays, and run it. This text file can then
be re-used, shared, and version controlled easily. Let's have a look then yeah?

<!--more-->

Okay so [previously][pp] we had an inventory file that listed the hosts in our
lab network.

~~~ini
[routers]
router-one ansible_host=192.168.0.1
router-two ansible_host=192.168.0.2
router-three ansible_host=192.168.0.3

[routers:vars]
ansible_user=vagrant
ansible_password=vagrant
~~~





[pp]: https://bordeltabernacle.github.io/2016/08/01/starting-out-with-ansible-cisco-and-network-automation.html



