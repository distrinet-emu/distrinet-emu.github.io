---
title: Mininet Compatibility
parent: Examples
nav_order: 3
---

This is a Mininet example creating s Linear 10 Topology

```python
from mininet.topo import Topo
from mininet.link import TCLink
from mininet.net import Mininet
from mininet.cli import CLI
from mininet.node import OVSSwitch, RemoteController

def int2dpid( dpid ):
   try:
      dpid = hex( dpid )[ 2: ]
      dpid = '0' * ( 16 - len( dpid ) ) + dpid
      return dpid
   except IndexError:
      raise Exception( 'Unable to derive default datapath ID - '
                       'please either specify a dpid or use a '
		       'canonical switch name such as s23.' )

class Linear10(Topo):

    def __init__(self, bwlimit=10, **opts):
        Topo.__init__(self, **opts)
        for i in range(1, 11):
            h = self.addHost("h{}".format(i))
            s = self.addSwitch("s{}".format(i), dpid=int2dpid(i))
            self.addLink(h, s, bw=bwlimit)

        for i in range(1,10):
            s1, s2 = "s{}".format(i), "s{}".format(i+1)
            self.addLink(s1, s2, bw=bwlimit)


topo = Linear10(100)
net = Mininet(topo=topo, link=TCLink, switch=OVSSwitch,controller=RemoteController, build=False)
# Mininet API for the experiment
net.build()
net.start()
# Run the CLI
CLI(net)
net.stop()
```

here is how you adapt the script for Distrinet

```python
from mininet.topo import Topo
from mininet.distrinet import Distrinet
from mininet.cli import CLI
from mininet.cloudcontroller import LxcRemoteController
from mininet.cloudswitch import LxcOVSSwitch
from mininet.cloudlink import CloudLink
from mininet.dutil import default_images
from mininet.mapper.mapper import RoundRobinMapper


def int2dpid( dpid ):
   try:
      dpid = hex( dpid )[ 2: ]
      dpid = '0' * ( 16 - len( dpid ) ) + dpid
      return dpid
   except IndexError:
      raise Exception( 'Unable to derive default datapath ID - '
                       'please either specify a dpid or use a '
		       'canonical switch name such as s23.' )

class Linear10(Topo):

    def __init__(self, bwlimit=10, **opts):
        Topo.__init__(self, **opts)
        for i in range(1, 11):
            h = self.addHost("h{}".format(i))
            s = self.addSwitch("s{}".format(i), dpid=int2dpid(i))
            self.addLink(h, s, bw=bwlimit)

        for i in range(1,10):
            s1, s2 = "s{}".format(i), "s{}".format(i+1)
            self.addLink(s1, s2, bw=bwlimit)


# These are the additional parameters used By Distrinet
#################################################################
physical_hosts_ips = ["Master_IP", "Worker_1IP"]
public_key = "YOUR id_rsa.pub"
private_key_path = "/path/to/your/id_rsa"
master = "Master_IP"
jump = master # tipically the same of Master, in a cloud environment, jump is the public ip, and the master IP is the private one
user = "root" # the username used to connect with the machines
##################################################################
topo = Linear10(100)
mapper = RoundRobinMapper(topo, physical_hosts_ips) # Create a Mapper, for the distribution of the vNodes
default_images(topo=topo)
net = Distrinet(topo=topo, link=CloudLink, switch=LxcOVSSwitch, build=False, controller=LxcRemoteController,
                mapper=mapper, user=user, master=master, jump=jump, client_keys=private_key_path, pub_id=public_key)
# From here you can use mininet API
net.build()
net.start()
# Run the CLI
CLI(net)
net.stop()

```