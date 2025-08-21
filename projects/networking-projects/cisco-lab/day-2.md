---
description: Port assignment/security, ACL's
icon: globe
---

# Day 2

### Port assignments&#x20;

On day 2 I started with assigning ports to their respective VLANs. On SW1 I excluded g1/0/47 as it would be the port connected to the server on the DMZ side.

{% code title="adding ports to vlan 10" %}
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

{% code title="port-sec config " %}
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

On the router side I started by making DHCP pools, and making reservations for IPs that are already in use or will be static IPs

{% code title="dhcp pool config " %}
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
