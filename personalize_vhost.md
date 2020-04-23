---
title: Personalize vHost
parent: Tutorial
nav_order: 4
---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

# Prerequisites

You need to have Distrinet installed and configured.

#Installing a new package in the vHost image.
Check that all the hosts have the images installed
```bash
root@master:~# ansible all -m raw -a "lxc image ls"
10.0.0.247 | CHANGED | rc=0 >>
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ALIAS  | FINGERPRINT  | PUBLIC |            DESCRIPTION             |  ARCH  |   SIZE   |         UPLOAD DATE          |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| switch | 5448ace25988 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 278.98MB | Apr 20, 2020 at 2:21pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ubuntu | f5a783211484 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 267.68MB | Apr 20, 2020 at 2:21pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+

10.0.1.110 | CHANGED | rc=0 >>
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ALIAS  | FINGERPRINT  | PUBLIC |            DESCRIPTION             |  ARCH  |   SIZE   |         UPLOAD DATE          |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| switch | 5448ace25988 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 278.98MB | Apr 20, 2020 at 2:21pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ubuntu | f5a783211484 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 267.68MB | Apr 20, 2020 at 2:21pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
Shared connection to 10.0.1.110 closed.
```

Create a new network that allow the container to connect to the external network
```bash
root@master:~# lxc network create inetbr ipv6.address=none ipv4.address=10.0.10.1/24 ipv4.nat=true
```

create a file that describe a new profile that attach the containet to the new network
```bash
vim inet.profile
```
Put the following lines in the file and save
```bash
config: {}
description: inet  profile
devices:
  eth0:
    name: eth0
    nictype: bridged
    parent: inetbr
    type: nic
  root:
    path: /
    pool: default
    type: disk
name: inet
used_by: []
```
Create the new profile and edit with the profile.conf file
```bash
root@master:~# lxc profile create inet 
root@master:~# lxc profile edit inet < profile.conf
```

Create a container with the basic ubuntu image, using the new profile
```bash
root@master:~# lxc launch ubuntu new-ubuntu --profile inet
```
Access inside the container
```bash
root@master:~# lxc exec new-ubuntu -- bash
```
Install and configure the software inside the container
```bash
root@new-ubuntu:~# INSTALL AND CONFIGURE YOUR APPLICATIONS
...
...
root@new-ubuntu:~# exit
```
Stop the updated container
```bash
root@master:~# lxc stop new-ubuntu
```

Publish the new image.
```bash
root@master:~# lxc publish new-ubuntu --alias new-ubuntu
Container published with fingerprint: 73fa4e3f21dc9acd1bdc76228d39fe1d40880e32c425f78b3c950e8d2010b138
```

Check that the container is correcly published
```bash
root@master:~# lxc image ls
+------------+--------------+--------+------------------------------------+--------+----------+------------------------------+
|   ALIAS    | FINGERPRINT  | PUBLIC |            DESCRIPTION             |  ARCH  |   SIZE   |         UPLOAD DATE          |
+------------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| new-ubuntu | 73fa4e3f21dc | no     |                                    | x86_64 | 294.21MB | Apr 23, 2020 at 6:29pm (UTC) |
+------------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| switch     | 5448ace25988 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 278.98MB | Apr 20, 2020 at 2:21pm (UTC) |
+------------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ubuntu     | f5a783211484 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 267.68MB | Apr 20, 2020 at 2:21pm (UTC) |
+------------+--------------+--------+------------------------------------+--------+----------+------------------------------+
```

Export The container and check that is correctly exported in the .tar.gz
```bash
root@master:~# lxc image export new-ubuntu new-ubuntu
Image exported successfully!

root@master:~# ls new-ubuntu.tar.gz
new-ubuntu.tar.gz
```

Distribute the new image with Ansible
```bash
root@ip-10-0-0-247:~# ansible all -m copy -a "src=new-ubuntu.tar.gz dest=new-ubuntu.tar.gz"
10.0.0.247 | SUCCESS => {
    "changed": false,
    "checksum": "0844f55a24b6c712448ab8dea475acfd5ee14a58",
    "dest": "new-ubuntu.tar.gz",
    "gid": 0,
    "group": "root",
    "mode": "0644",
    "owner": "root",
    "path": "new-ubuntu.tar.gz",
    "size": 308497878,
    "state": "file",
    "uid": 0
}
10.0.1.110 | CHANGED => {
    "changed": true,
    "checksum": "0844f55a24b6c712448ab8dea475acfd5ee14a58",
    "dest": "./new-ubuntu.tar.gz",
    "gid": 0,
    "group": "root",
    "md5sum": "06d7e4ee8e3213b234d56a122be070c5",
    "mode": "0644",
    "owner": "root",
    "size": 308497878,
    "src": "/root/.ansible/tmp/ansible-tmp-1587666876.21-5732-35780808233900/source",
    "state": "file",
    "uid": 0
}
```
Clean the image DB in all the hosts(to avoid hash collisions)
```bash
root@master:~# lxc image delete new-ubuntu
root@master:~# ansible all -m raw -a "lxc image delete ubuntu"
```

Import the new image in all the machines.
```bash
root@master:~# ansible all -m raw -a "lxc image import new-ubuntu.tar.gz --alias ubuntu --public"
10.0.1.110 | CHANGED | rc=0 >>
Shared connection to 10.0.1.110 closed.

10.0.0.247 | CHANGED | rc=0 >>
Image imported with fingerprint: 73fa4e3f21dc9acd1bdc76228d39fe1d40880e32c425f78b3c950e8d2010b138
```

```bash
root@master:~# ansible all -m raw -a "lxc image ls"
10.0.0.247 | CHANGED | rc=0 >>
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ALIAS  | FINGERPRINT  | PUBLIC |            DESCRIPTION             |  ARCH  |   SIZE   |         UPLOAD DATE          |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| switch | 5448ace25988 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 278.98MB | Apr 20, 2020 at 2:21pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ubuntu | 73fa4e3f21dc | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 294.21MB | Apr 23, 2020 at 6:37pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+

10.0.1.110 | CHANGED | rc=0 >>
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ALIAS  | FINGERPRINT  | PUBLIC |            DESCRIPTION             |  ARCH  |   SIZE   |         UPLOAD DATE          |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| switch | 5448ace25988 | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 278.98MB | Apr 20, 2020 at 2:21pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
| ubuntu | 73fa4e3f21dc | yes    | Ubuntu 18.04 LTS server (20190514) | x86_64 | 294.21MB | Apr 23, 2020 at 6:37pm (UTC) |
+--------+--------------+--------+------------------------------------+--------+----------+------------------------------+
Shared connection to 10.0.1.110 closed.
```

Now Distrinet will use the new image.