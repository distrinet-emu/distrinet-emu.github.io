---
title: Hadoop Benchmarck
parent: Examples
nav_order: 2
---

# Hadoop Benchmarck

For Hadoop benchmark, you need the images of the Hadoop master and worker.

## Prepare the Master/Workers
Connect to your Master host and download the new images with:

```bash
root@master:~# cd ~
root@master:~# wget http://www-sop.inria.fr/members/Damien.Saucez/images/ubuntu-hadoop-slave.tar.gz
root@master:~# wget http://www-sop.inria.fr/members/Damien.Saucez/images/ubuntu-hadoop-master.tar.gz
```
The images are quite big, so if you don't have a good connection you have to wait a little bit.


You can distribute the new images in the workers with Ansible

```bash
root@master:~# cd ~
root@master:~# ansible all -m copy -a "src=ubuntu-hadoop-slave.tar.gz dest=ubuntu-hadoop-slave.tar.gz"
root@master:~# ansible all -m copy -a "src=ubuntu-hadoop-master.tar.gz dest=ubuntu-hadoop-master.tar.gz"
```

Import the images in LXD:

```bash
root@master:~# ansible all -m raw -a "lxc image import ubuntu-hadoop-master.tar.gz --alias ubuntu-hadoop-master --public"
root@master:~# ansible all -m raw -a "lxc image import ubuntu-hadoop-slave.tar.gz --alias ubuntu-hadoop-slave --public"
```

Check that all images are imported on all the hosts

```bash
root@master:~# ansible all -m raw -a "lxc image ls"
```

## Run the benchmark

To test it, make sure that the controller is running on your master:

```bash
root@master:~# ryu-manager /usr/lib/python3/dist-packages/ryu/app/simple_switch_13.py --verbose
```

From your client you can run the experiment.

```
ubuntu@client:~/Distrinet/mininet# python3 bin/dmn --bastion=BASTION_IP --workers="MASTER_IP,WORKER1...." --controller=lxcremote,ip=192.168.0.1 --topo=linear,10 --custom=custom/hadoop_test.py --test=hadoop
``` 


