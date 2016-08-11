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

Let's add our first playbook.  Playbooks are written as *yaml* files, a format equivalent to *json* or *xml* but intended to be more human readable. Be warned, *yaml* is very particular about whitespace. If we create a new file alongside out playbook called `get_inventory_info.yaml`, we'll use it to get the output from a `show inventory` command.

~~~yaml
---
- name: Get Inventory Information
  hosts: routers
  tasks:
    - name: Send Show Inventory Command
      ios_command:
        host: "{{ ansible_host }}"
        username: "{{ ansible_user }}"
        password: "{{ ansible_password }}"
        commands:
          - show inventory
      register: inventory
    - debug: var=inventory
~~~

So, what's going on here?

* `---` indicates that it's a *yaml* file.
* `name: Get Inventory Information` is the name of our playbook
* `hosts: routers` is the devices we want to target
* `tasks:` what follows is the list of tasks we want to carry out
* `name: Send Show Inventory Command` is the name of our task
* `ios_command:` this is [the module](am) we are using
* `host: "{{ ansible_host }}"`, `username: "{{ ansible_user }}"` & `      password: "{{ ansible_password }}"` are the connection details we're going to use. The `{{ }}` means that this is a variable. All variables exist within the Ansible environment you're in, in this case these variables are from the *inventory* file above. Doing this means we can loop through all the hosts in our *routers* group, and they can have different username/password combinations if needs be.
* `commands: - show inventory` is the command we'll be using.
* `register: inventory` here we're storing the output of the task in the variable `inventory`
* `debug: var=inventory` this will output the variable `inventory` to our terminal.

To run this we use the `ansible-playbook` command, along with the name of our playbook and the `-i` flag to specify our inventory.

`ansible-playbook get_inventory_info.yaml -i inventory`

OK, let's go...

~~~json
PLAY [Get Inventory Information] ***********************************************

TASK [setup] *******************************************************************
fatal: [router-one]: UNREACHABLE! => {"changed": false, "msg": "SSH Error: data could not be sent to the remote host. Make sure this host can be reached over ssh", "unreachable": true}
fatal: [router-two]: UNREACHABLE! => {"changed": false, "msg": "SSH Error: data could not be sent to the remote host. Make sure this host can be reached over ssh", "unreachable": true}
fatal: [router-three]: UNREACHABLE! => {"changed": false, "msg": "SSH Error: data could not be sent to the remote host. Make sure this host can be reached over ssh", "unreachable": true}
 [WARNING]: Could not create retry file 'get_inventory_info.retry'.         [Errno 2] No such file or directory: ''


PLAY RECAP *********************************************************************
router-one                : ok=0    changed=0    unreachable=1    failed=0
router-two               : ok=0    changed=0    unreachable=1    failed=0
router-three             : ok=0    changed=0    unreachable=1    failed=0
~~~

Arggh, whaa? This is because we're dealing with Cisco pets not server cattle. As I mentioned before, Ansible is trying to run some Python code on our hosts, which is not going to happen with Cisco IOS, we need to run the code on our local machine. So let's add the following to our *inventory* file:

~~~ini
[routers:vars]
ansible_connection=local
ansible_user=vagrant
ansible_password=vagrant
~~~

That line there, `ansible_connection=local`, should take care of this problem for us.

~~~json
PLAY [Get Inventory Information] ***********************************************

TASK [setup] *******************************************************************
ok: [router-one]
ok: [router-one]
ok: [router-two]

TASK [Send Show Inventory Command] *********************************************
ok: [router-one]
ok: [router-one]
ok: [router-two]

TASK [debug] *******************************************************************
ok: [router-two] => {
    "output": {
        "changed": false,
        "stdout": [
            "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ1545706V, Hw Revision: 1.0\"\nPID: CISCO2921/K9      , VID: V05 , SN: FCZ1545706V\n\nNAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"\nPID: PWR-2921-51-AC    , VID: V02 , SN: DCA1536K3WX\n\n"
        ],
        "stdout_lines": [
            [
                "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ1545706V, Hw Revision: 1.0\"",
                "PID: CISCO2921/K9      , VID: V05 , SN: FCZ1545706V",
                "",
                "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
                "PID: PWR-2921-51-AC    , VID: V02 , SN: DCA1536K3WX",
                "",
                ""
            ]
        ]
    }
}
ok: [router-two] => {
    "output": {
        "changed": false,
        "stdout": [
            "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192570GZ, Hw Revision: 1.0\"\nPID: CISCO2921/K9      , VID: V08 , SN: FCZ192570GZ\n\nNAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"\nPID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060PYS\n\n"
        ],
        "stdout_lines": [
            [
                "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192570GZ, Hw Revision: 1.0\"",
                "PID: CISCO2921/K9      , VID: V08 , SN: FCZ192570GZ",
                "",
                "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
                "PID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060PYS",
                "",
                ""
            ]
        ]
    }
}
ok: [router-three] => {
    "output": {
        "changed": false,
        "stdout": [
            "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192560EG, Hw Revision: 1.0\"\nPID: CISCO2921/K9      , VID: V08 , SN: FCZ192560EG\n\nNAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"\nPID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060RV1\n\n"
        ],
        "stdout_lines": [
            [
                "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192560EG, Hw Revision: 1.0\"",
                "PID: CISCO2921/K9      , VID: V08 , SN: FCZ192560EG",
                "",
                "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
                "PID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060RV1",
                "",
                ""
            ]
        ]
    }
}

PLAY RECAP *********************************************************************
router-one                : ok=3    changed=0    unreachable=0    failed=0
router-two                : ok=3    changed=0    unreachable=0    failed=0
router-three              : ok=3    changed=0    unreachable=0    failed=0
~~~

Yessss! Job's a good 'un, let's go home.
Nah, not yet. We can smooth things out a bit. At the beginning of the output you can see a `[setup]` task being run, that is an Ansible default that gathers facts from the hosts, like operating system, ip address etc. However, because we're using `ansible_connection=local` this task is just gathering facts from the local machine, 3 times in this case! Totally useless. So we can add `gather_facts: no` to our playbook to avoid this time waster. We also don't need all of that output so, as this is *json* we can be a bit more selective about it using `debug: var=inventory.stdout_lines[0]`. Ansible has a `provider` argument which can be a dictionary object which defines the connection to the host, meaning we can separate this information from our playbooks, and not have to repeat it in every playbook. Our playbook now looks like this:

~~~yaml
---
- name: Get Inventory Information
  gather_facts: no
  hosts: routers
  tasks:
    - name: Send Show Inventory Command
      ios_command:
        provider: "{{ provider }}"
        commands:
          - show inventory
      register: inventory
    - debug: var=inventory.stdout_lines[0]
~~~

To store group variables, such as `provider`, we can use a directory called *group_vars*. This directory name is pre-defined by Ansible. Within it we create a *yaml* file named after the group, *routers*. While we're at it, if we're creating a *routers* variable file, we can move the variables out of our inventory into it. So, in `group_vars/routers.yaml` we have...

~~~yaml
---
ansible_connection: local
ansible_user: vagrant
ansible_password: vagrant
provider:
  host: "{{ ansible_host }}"
  username: "{{ ansible_user }}"
  password: "{{ ansible_password }}"
~~~

Really, we shouldn't be writing our password down, should we? So let's delete the `ansible_password: vagrant` line and use the command line `--ask-pass` flag instead.

Our inventory file now looks like this, just fyi:

~~~ini
[routers]
192.168.0.1
192.168.0.2
192.168.0.3
~~~

I've removed the hostnames, just because, it doesn't really change anything, except the names in the output.
If we run `ansible-playbook get_inventory_info.yaml -i inventory --ask-pass` we get a prompt for the password...

~~~json
SSH password:

PLAY [Get Inventory Information] ***********************************************

TASK [Send Show Inventory Command] *********************************************
ok: [192.168.0.2]
ok: [192.168.0.3]
ok: [192.168.0.1]

TASK [debug] *******************************************************************
ok: [192.168.0.1] => {
    "inventory.stdout_lines[0]": [
        "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ1545706V, Hw Revision: 1.0\"",
        "PID: CISCO2921/K9      , VID: V05 , SN: FCZ1545706V",
        "",
        "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
        "PID: PWR-2921-51-AC    , VID: V02 , SN: DCA1536K3WX",
        "",
        ""
    ]
}
ok: [192.168.0.2] => {
    "inventory.stdout_lines[0]": [
        "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192570GZ, Hw Revision: 1.0\"",
        "PID: CISCO2921/K9      , VID: V08 , SN: FCZ192570GZ",
        "",
        "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
        "PID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060PYS",
        "",
        ""
    ]
}
ok: [192.168.0.3] => {
    "inventory.stdout_lines[0]": [
        "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192560EG, Hw Revision: 1.0\"",
        "PID: CISCO2921/K9      , VID: V08 , SN: FCZ192560EG",
        "",
        "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
        "PID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060RV1",
        "",
        ""
    ]
}

PLAY RECAP *********************************************************************
192.168.0.1                : ok=2    changed=0    unreachable=0    failed=0
192.168.0.2                : ok=2    changed=0    unreachable=0    failed=0
192.168.0.3                : ok=2    changed=0    unreachable=0    failed=0
~~~

If we want we can add a few command in to our playbook. Let's change the name of it to `get_device-information.yaml` and change it to this:

~~~yaml
---
- name: Get Device Information
  gather_facts: no
  hosts: routers
  tasks:
    - name: Send Show Commands
      ios_command:
        provider: "{{ provider }}"
        commands:
          - show inventory
          - sh run | inc hostname
          - sh ver | inc image
          - sh ip int brief
      register: info
    - debug: var=info.stdout_lines
~~~

Run it. `ansible-playbook get_device_info.yaml -i inventory --ask-pass` and a brief moment later...

~~~json
SSH password:

PLAY [Get Device Information] **************************************************

TASK [Send Show Commands] ******************************************************
ok: [192.168.0.3]
ok: [192.168.0.1]
ok: [192.168.0.2]

TASK [debug] *******************************************************************
ok: [192.168.0.1] => {
    "info.stdout_lines": [
        [
            "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ1545706V, Hw Revision: 1.0\"",
            "PID: CISCO2921/K9      , VID: V05 , SN: FCZ1545706V",
            "",
            "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
            "PID: PWR-2921-51-AC    , VID: V02 , SN: DCA1536K3WX",
            "",
            ""
        ],
        [
            "hostname router-one"
        ],
        [
            "System image file is \"flash0:c2900-IMAGE-ONE.SPA.154-3.M3.bin\""
        ],
        [
            "Interface                  IP-Address      OK? Method Status                Protocol",
            "Embedded-Service-Engine0/0 unassigned      YES NVRAM  administratively down down    ",
            "GigabitEthernet0/0         192.168.0.1     YES NVRAM  up                    up      ",
            "GigabitEthernet0/1         10.10.10.11     YES manual down                  down    ",
            "GigabitEthernet0/2         unassigned      YES NVRAM  administratively down down    "
        ]
    ]
}
ok: [192.168.0.3] => {
    "info.stdout_lines": [
        [
            "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192560EG, Hw Revision: 1.0\"",
            "PID: CISCO2921/K9      , VID: V08 , SN: FCZ192560EG",
            "",
            "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
            "PID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060RV1",
            "",
            ""
        ],
        [
            "hostname router-three"
        ],
        [
            "System image file is \"flash0:c2900-IMAGE-ONE.SPA.154-3.M3.bin\""
        ],
        [
            "Interface                  IP-Address      OK? Method Status                Protocol",
            "Embedded-Service-Engine0/0 unassigned      YES NVRAM  administratively down down    ",
            "GigabitEthernet0/0         192.168.0.3     YES NVRAM  up                    up      ",
            "GigabitEthernet0/1         10.10.10.13     YES manual down                  down    ",
            "GigabitEthernet0/2         unassigned      YES NVRAM  administratively down down    "
        ]
    ]
}
ok: [192.168.0.2] => {
    "info.stdout_lines": [
        [
            "NAME: \"CISCO2921/K9\", DESCR: \"CISCO2921/K9 chassis, Hw Serial#: FCZ192570GZ, Hw Revision: 1.0\"",
            "PID: CISCO2921/K9      , VID: V08 , SN: FCZ192570GZ",
            "",
            "NAME: \"C2921/C2951 AC Power Supply\", DESCR: \"C2921/C2951 AC Power Supply\"",
            "PID: PWR-2921-51-AC    , VID: V03 , SN: QCS19060PYS",
            "",
            ""
        ],
        [
            "hostname router-two"
        ],
        [
            "System image file is \"flash0:c2900-IMAGE-ONE.SPA.154-3.M3.bin\""
        ],
        [
            "Interface                  IP-Address      OK? Method Status                Protocol",
            "Embedded-Service-Engine0/0 unassigned      YES NVRAM  administratively down down    ",
            "GigabitEthernet0/0         192.168.0.2     YES NVRAM  up                    up      ",
            "GigabitEthernet0/1         10.10.10.12     YES manual down                  down    ",
            "GigabitEthernet0/2         unassigned      YES NVRAM  administratively down down    "
        ]
    ]
}

PLAY RECAP *********************************************************************
192.168.0.1                : ok=2    changed=0    unreachable=0    failed=0
192.168.0.2                : ok=2    changed=0    unreachable=0    failed=0
192.168.0.3                : ok=2    changed=0    unreachable=0    failed=0
~~~

Lovely, *now* we can go home.

There's more to do here, such as saving this output to file & sending configuration changes, but we can do that later.


[pp]: https://bordeltabernacle.github.io/2016/08/01/starting-out-with-ansible-cisco-and-network-automation.html
[am]: https://docs.ansible.com/ansible/ios_command_module.html



