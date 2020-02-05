---
title: Installation
has_children: False
nav_order: 2
---

# Installation

Here we explain how to install Distrinet in your machine.
You can install it directly on your machine or you can use docker(mandatory if you use windows).

IMPORTANT: You need to install distrinet only on your Client machine.

## Installation in a Linux machine
### Prerequisites
Make sure to have git, python3 and pip installed.
If not you can run:
```
sudo apt update && sudo apt install -y git python3-minimal python3-pip
```
### Installation
Move into your home directory, and clone Distrinet repository.
```
cd ~
git clone https://github.com/Giuseppe1992/Distrinet.git
```
Move inside the directory with:
```
cd ~/Distrinet
```
Now we start the real installation.
Run 
```
pip3 install -r requirements.txt
python3 setup.py install
```

Update your PYTHONPATH with:
```
export PYTHONPATH=$PYTHONPATH:mininet:
```

### Check the configuration directory
You should have a new folder (.distrinet) in your home directory.

```
cd ~/.distrinet/
ls -al

giuseppe@MacBook-Pro-di-Giuseppe .distrinet % ls -la
total 48
drwxr-xr-x   7 giuseppe  staff   224  4 Feb 14:09 .
drwxr-xr-x+ 85 giuseppe  staff  2720  5 Feb 13:37 ..
-rw-r--r--@  1 giuseppe  staff  1262 30 Gen 15:17 conf.yml
-rw-r--r--   1 giuseppe  staff   426 30 Gen 14:18 general_purpose.json
-rw-r--r--@  1 giuseppe  staff  1375 31 Gen 14:10 gros_partial.json
```

We will modify the conf.yml later

## Installation via Docker
### Prerequisites
Make sure to have git and Docker installed in your pc, if you don't have it you can go to https://docs.docker.com.
If you don't have git you can run:
```
sudo apt update && sudo apt install -y git
```
### Installation
Move into your home directory, and clone Distrinet repository.
```
cd ~
git clone https://github.com/Giuseppe1992/Distrinet.git
```
Move inside the directory with:
```
cd ~/Distrinet
```
Now we can create the container with:
```
docker build -t distrinet:1.1 .
```
The creation can take a couple of minutes.
We can check that the image is correctly created with
```
docker image ls
```
If you see the image "distrinet:1.1" everything is fine. Now we can create the container with
```
docker container run -it --name distrinet distrinet:1.1
```
You can check that it is correctly installed with:
```
giuseppe@MacBook-Pro-di-Giuseppe Distrinet % docker container run -it --name distrinet distrinet:1.1
root@30363028511c:/Distrinet# cd ~/.distrinet/
root@30363028511c:~/.distrinet# ls -al
total 20
drwxr-xr-x 2 root root 4096 Feb  5 12:52 .
drwx------ 1 root root 4096 Feb  5 12:52 ..
-rw-r--r-- 1 root root  856 Feb  5 12:52 conf.yml
-rw-r--r-- 1 root root  426 Feb  5 12:52 general_purpose.json
-rw-r--r-- 1 root root 1375 Feb  5 12:52 gros_partial.json
root@30363028511c:~/.distrinet# 

```


