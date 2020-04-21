---
title: Limitations
nav_order: 9
---

# Limitations

We build Distrinet to be compatible with Mininet, but there are some limitation in the current versions.

## No Underscore in the vSwitch and vHost names
LXD do not support containers names with underscore(_), 
i.e.
```python
from mininet.topo import Topo

class Personalized(Topo):
    def build(self):
        self.addSwitch('s_1')
        self.addSwitch('s2')
```

's_1' is a valid name in Mininet, but not for Distrinet;
's2' is a valid name.

[github discussion](https://github.com/Giuseppe1992/Distrinet/issues/51)

## Compatibility with Mininet switch=ovsk

Distrinet uses by defoult OVS for the virtual switches.
This can be a problem when the dpid is not directly set in the addSwitch() function.

[Github discussion](https://github.com/Giuseppe1992/Distrinet/issues/50)

##Os Supported
For now we support Ubuntu 18.04 for the Master and the workers, while the client is compatible with any system using Docker.
We are working to support more Linux Distributions.



