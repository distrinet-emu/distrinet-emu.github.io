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