---
title: "Some Ansible Lessons"
date: 2018-01-11T23:30:22+08:00
draft: false
---

I have been working with Ansible for some time at work now. This new project
uses quite a bit of Ansible so I have been learning a lot. I had touched
Ansible before but that was just to put together something to help me with
copying stuff over and over to a bunch of servers.

The requirement of current project is to programmatically run an ansible
playbook on a server newly provisioned. Going in without knowing much about
Ansible, one could easily believe that it's as easy as passing the new host.
Well that's not how things turned out for me.

My colleague who was working on it when I joined has settled with a dynamic
inventory solution but I was not convinced it's the way to go. I couldn't
exactly say how else to do it either. Ofcourse I did venture into deep dark
webs couple times, determined to find a solution that didn't involve dynamic
inventory, only to return.. empty handed. Things are extra complicated when you
have to work with old versions of software and that too in offline
environments. But no one is here for excuses.

Finally, a solution came together. I must note down what I have learned
while working hard to crack this one with one of my colleagues.

## Dynamic inventory

First, to understand dynamic inventory, I wanted to find the simplest form of
dynamic inventory script there is (The official documentation could do better
IMHO). And [I did](https://adamj.eu/tech/2016/12/04/writing-a-custom-ansible-dynamic-inventory-script/).
Following is an inventory file in ini format and the dynamic inventory
representation of it.

hosts.ini
```ini
[all]
10.92.168.2 ansible_ssh_user=user

[all:vars]
var_in_all=var_in_all_value

[my_group]
10.92.168.2

[my_group:vars]
can_refer_other_vars={{ var_in_all }}
```

hosts.py
```python
#!/usr/bin/env python
from __future__ import (absolute_import, print_function)

import json


def main():
    print(json.dumps(inventory(), sort_keys=True, indent=2))

def inventory():
    ip_address = '10.92.168.2'

    return {
        'all': {
            'hosts': [ip_address],
            'vars': {'var_in_all': 'var_in_all_value'},
        },
        'my_group': {
            'hosts': [ip_address],
            'vars': {
                'can_refer_other_vars': '{{ var_in_all }}',
            },
        },
        '_meta': {
            'hostvars': {
                ip_address: {
                    'ansible_ssh_user': 'user',
                }
            },
        },
    }

if __name__ == '__main__':
    main()
```

This way you can fetch the hosts from somewhere, like an API or a database and
populate the inventory dynamically.


# Combining dynamic and static inventories

I wanted to do this from the beginning so we could move some static variables
out of dynamic inventory but my google foo failed me yet again. Anyhow, you can
provide a [directory as the inventory](http://allandenot.com/devops/2015/01/16/ansible-with-multiple-inventory-files.html).
Ansible will process all the files in that, including executable scripts like
the dynamic inventory file shown above. This way, you can free the dynamic
inventory from statis variables.

```
% tree .
.
├── inventory
│   ├── hosts.ini
│   └── hosts.py
└── sample-playbook.yml

% ansible-playbook -i inventory sample-playbook.yml
```

All these are great. But do we really need the dynamic inventory? :D. The great
search ended one day.. amidst much self loathing.

## In-memory inventory

This was dismissed as an option by both myself and my colleague because we both
misread (half-read) the documentation and thought it's only available in the
latest version. A new set of eyes figured that's not the case.

sample-playbook.yml
```
---
- hosts: localhost
  connection: local
  gather_facts: False

  tasks:
    - name: "Add a host to the inventory"
      add_host:
      name: {{ new_host }}
      groups: {{ host_group }}

- hosts: {{ new_host }}

  tasks:
    - name: "Run task on newly added host"
      shell: echo "Hello Node!"
```

Run above playbook as follows

```
ansible-playbook sample-playbook.yml -e "new_host=192.168.10.2 host_group=web"
```

We ended up eliminating the dynamic inventory since we do not need that, at
least for now.
