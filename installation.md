---
title: Installation
has_children: False
nav_order: 2
---

# Installation

Here we explain how to install Distrinet in your machine.
You can install it directly on your machine or you can use docker(mandatory if you use windows).

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
You should see something similar to this:
```
MacBook-Pro-di-Giuseppe:Distrinet giuseppe$ docker container run -it --name distrinet distrinet:1.1
root@0a5c5e309ca0:/Distrinet#
```


