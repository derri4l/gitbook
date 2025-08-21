---
description: NAT, IP reservation, Access Point intergration
icon: globe
---

# Day 3

On Day 3 I started out with reserving an IP and assigning a port to the AP on the guests VLAN. I chose 192.168.30.10 for the AP on port 35

```
ip dhcp pool vlan30
ip dhcp pool excluded-address 192.168.30.1 192.168.30.10
```

After adding the AP and going through the configuration, any device that connected to the AP got an IP via DHCP which is good but had no internet. Did a few tests and figured out that NAT maybe the problem so I enabled NAT for vlan30

```
int g0/1.30
ip nat inside 

ip g0/0
ip nat outside 

access-list 1 permit 192.168.30.0 0.0.0.255
```

