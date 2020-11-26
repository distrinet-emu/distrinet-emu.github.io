---
title: Personalized Topology
parent: Examples
nav_order: 1
---

Create a personalized topology is very simple if you are used to Mininet.

```python
from mininet.topo import Topo
# in distrinet the Nodes needs additional parameters, to apply
# them you have to use default_images functions in the PREBUILD
from mininet.dutil import default_images

class MyTopo( Topo ):
    "Simple topology example."

    def build( self ):
        "Create custom topo."

        # Add hosts and switches
        # the optional parmeters cpu and memory can be applied on Hosts and Switches.
        leftHost = self.addHost( 'h1' , cpu=2, memory="2GB")
        rightHost = self.addHost( 'h2' )
        leftSwitch = self.addSwitch( 's3' , cpu=2)
        rightSwitch = self.addSwitch( 's4' )

        # Add links
        #if bw is not speified the link bandwidth is unlimited
        # in this example only the link betweeen the switches is gapped at 100 Mbps
        self.addLink( leftHost, leftSwitch )
        self.addLink( leftSwitch, rightSwitch , bw=100)
        self.addLink( rightSwitch, rightHost )

PREBUILD = [default_images]

topos = { 'mytopo': ( lambda: MyTopo() ) }
```
To test it, make sure that the controller is running on your master:
```bash
root@master:~# ryu-manager /usr/lib/python3/dist-packages/ryu/app/simple_switch_13.py --verbose
```

From your client:
```bash
ubuntu@client:~/Distrinet/mininet# python3 bin/dmn --custom=PATH_TO_CUSTOM_TOPO --bastion=MASTER_IP --workers="MASTER_IP,WORKER_1,...." --controller=lxcremote,ip=192.168.0.1 --topo=mytopo
```
