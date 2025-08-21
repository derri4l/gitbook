---
icon: router
---

# Physical Lab #1

### _Overview_

This lab simulates the basics of a small enterprise like network. The goal is to practice networking technologies such as topology planning, inter-VLAN routing, etherChanneling, etc.

### _Hardware_

* Cisco switch 3750 48 Port (SW1)
* Cisco Switch 3750 24 Port (SW2)
* Cisco Router 2911 (R1)

### _Initial Device Setup_

Each device had to be reset and had almost the same basic configuration. Each was configured with a hostname, console and VTY login, SSH access, and other interface settings

{% code title="sample basic config" %}
```xml
 hostname SW1 
 enable secret cisco123
 
 line con 0 
 password cisco123 
 login
 
 line vty 0 4 
 password vtypass 
 login 
 transport input ssh 
 
 ip domain-name lab.com
  crypto key generate rsa
  
copy run start 
```
{% endcode %}

{% hint style="info" %}
This is a partial config. I will post full startup-config in the media page for this project
{% endhint %}

### _VLANs and IP Planning_&#x20;

After basic the basic configuration, I went to make a table of static IPs and VLANs.

| VLANs | Name    | Subnet          | Sub-interface |
| ----- | ------- | --------------- | ------------- |
| 10    | HR      | 192.168.10.0/24 | G0/1.10       |
| 20    | IT      | 192.168.20.0/24 | G0/1.20       |
| 30    | Guests  | 192.168.30.0/24 | G0/1.30       |
| 50    | MGMT    | 192.168.50.1/24 | G0/1.50       |
| WAN   | Uplink  | 10.99.40.0/24   | G0/0          |
| 60    | DMZ     | 192.168.60.0/24 | G0/1.60       |

{% code title="router sub-int setup" %}
```xml
conf t 
int g0/1
no shutdown

int g0/1.10
encapsulation dot1q 
ip add 192.168.10.1 255.255.255.0
description HR 

int g0/1.20
encapsulation dot1q 
ip add 192.168.20.1 255.255.255.0
description IT 

int g0/1.30
encapsulation dot1q 
ip add 192.168.30.1 255.255.255.0
description GUESTS

int g0/1.50
encapsulation dot1q
ip add 192.168.50.1 255.255.255.0
description MGMT

ip g0/1.60
encapsulation dot1q
ip add 192.168.60.1 255.255.255.0
description DMZ

```
{% endcode %}

Next was setting up the trunk from the switch 1 to the router&#x20;

{% code title="SW1 trunk to R1 setup" %}
```
int g1/0/48
switchport trunk encapsulation dot1q 
switchport mode trunk 
switchport trunk allowed vlan 10,20,30,50,60
description trunk to G0/1
no shut
```
{% endcode %}

Next up was setting up the LACP bundle for both switches. I bundled port 1-4 on both switches into port channel 1.

{% code title="sw1 and 2 LACP setup" %}
```
int range g1/0/1 -4 
switchport mode trunk 
channel-group 1 mode active 
```
{% endcode %}



### _Port assignments_&#x20;

&#x20;Next I started with assigning ports to their respective VLANs. On SW1 I excluded g1/0/47 as it would be the port connected to the server on the DMZ side.&#x20;

{% code title="adding ports on vlan 10" %}
```
config t 
int range g1/0/5 - 16 
switchport mode access 
switchport access vlan 10 
no shutdown 
```
{% endcode %}

{% hint style="info" %}
g1/0/1 -  4 is excluded because they are trunks and in a port channel
{% endhint %}

Next thing I did was enable port security to limit MAC add learning and restrict ports upon violation.

{% code title="port-sec config" %}
```
int range g1/0/5 - 16 
switchport port-security 
switchport port-security maximum 3
switchport port-security violation restrict 
switchport port-security mac-address sticky
```
{% endcode %}

Next thing i did on the switches were to enable ACL restrictions. This makes it so a specific subnet or IP can access services like SSH on the devices themselves.&#x20;

```
ip access-lists standard MGMT-ACL
permit 192.168.50.0 0.0.0.255
deny any

line vty 0 4
access-class MGMT-ACL in 
transport input ssh 
```

On the router side I started by making DHCP pools, and making reservations for IPs that are already in use or will be static IPs.

{% code title="DHCP pool for vlan 10 " %}
```
ip dhcp pool vlan10 
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
ip dhcp excluded-address 192.168.10.1 192.158.10.2

ip dhcp pool vlan 30 
network 192.168.60.0 255.255.255.0
default-router 192.168.30.1
dns-server 8.8.8.8
ip dhcp excluded-address 192.168.30.1 192.158.30.10 
```
{% endcode %}



&#x20;Next I started out with reserving an IP and assigning a port to the AP on the guests VLAN. I chose 192.168.30.10 for the AP on port 35.

```
ip dhcp pool vlan30
ip dhcp pool excluded-address 192.168.30.1 192.168.30.10
```

After adding the AP and going through the configuration, any device that connected to the AP got an IP via DHCP which is good but had no internet. Did a few tests and figured out that NAT maybe the problem so I enabled NAT for vlan30.

```
int g0/1.30
ip nat inside 

ip g0/0
ip nat outside 

access-list 1 permit 192.168.30.0 0.0.0.255
```

