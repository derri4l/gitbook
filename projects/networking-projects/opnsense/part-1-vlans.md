---
description: VLAN configuration
icon: block-brick-fire
---

# Part 1- VLANs

#### Devices

{% hint style="info" %}
Location: Interfaces → Devices&#x20;
{% endhint %}

I began by setting up devices for each VLAN, assigning each to its parent interface (in this case, my LAN interface ue0), and specifying a VLAN tag.

<figure><img src="../../../.gitbook/assets/Screenshot from 2025-06-21 12-24-06 (1).png" alt="" width="375"><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (13).png" alt="" width="375"><figcaption><p>VLAN devices list</p></figcaption></figure>

#### Assignments

{% hint style="info" %}
Location: Interfaces → Assignments&#x20;
{% endhint %}

The next step was to make assignments for each of the devices&#x20;

<figure><img src="../../../.gitbook/assets/Screenshot from 2025-06-21 12-32-43 (3).png" alt="" width="375"><figcaption></figcaption></figure>



#### Interfaces



Now that our VLANs are assigned, the next step is to configure each VLAN according to preference. Below is a sample configuration for my VLAN interfaces, each featuring a unique tagged sub-interface on the primary LAN device.&#x20;



{% code title="configs/network/vlan_interfaces.conf" %}
```sh
 config device
    option name 'eth0.40'
    option type '8021q'
    option ifname 'ue0'
    option vid '40'

config device
    option name 'eth0.50'
    option type '8021q'
    option ifname 'ue0'
    option vid '50'

config device
    option name 'eth0.60'
    option type '8021q'
    option ifname 'ue0'
    option vid '60'

config device
    option name 'eth0.70'
    option type '8021q'
    option ifname 'ue0'
    option vid '70'

config device
    option name 'tailscale0'
    option type 'tunnel'
    option ifname 'tailscale0'
    option macaddr '00:00:00:00:00:00'

config interface 'vlan40'
    option proto 'static'
    option ipaddr '192.168.40.1'
    option netmask '255.255.255.0'
    option device 'eth0.40'
    option delegate '0'

config interface 'vlan50'
    option proto 'static'
    option ipaddr '192.168.50.1'
    option netmask '255.255.255.0'
    option device 'eth0.50'
    option delegate '0'

config interface 'vlan60'
    option proto 'static'
    option ipaddr '192.168.60.1'
    option netmask '255.255.255.0'
    option device 'eth0.60'
    option delegate '0'

config interface 'vlan70'
    option proto 'static'
    option ipaddr '192.168.70.1'
    option netmask '255.255.255.0'
    option device 'eth0.70'
    option delegate '0'

config interface 'tailscale'
    option proto 'none'
    option device 'tailscale0'
    option delegate '0'

```
{% endcode %}





