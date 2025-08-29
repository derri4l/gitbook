---
description: Aliases, Rules, and Logging
icon: block-brick-fire
---

# Part 2 - Firewall setup

### _Aliases_&#x20;

{% hint style="info" %}
Location: Firewall → Aliases (depends on firmware)
{% endhint %}

Setting up aliases is optional but very helpful in OPNsense. In this project aliases allow me to group related IPs, networks, or ports for easier firewall rule management.

What type of aliases did i use?&#x20;

* **Host aliases:** Groups of IPs for admin or service devices ( you can use other types like mac addresses, subnets etc.)
* **Network aliases:** Subnets or management IPs
* **Port aliases:** Grouped services like SSH, HTTPS

<figure><img src="../../../.gitbook/assets/image (15).png" alt="" width="421"><figcaption><p>adding IPs to the admin alias group</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (16).png" alt="" width="435"><figcaption><p>adding ports to the ports alias group</p></figcaption></figure>



### _Firewall Rules_

{% hint style="info" %}
Firewall → Rules → Pick the interface you want to configure
{% endhint %}

In this step what I did was define the firewall rules per interface. In this instance i am configuring basic rules for a Server VLAN(50). &#x20;

{% code title="firewallrules.conf" %}
```markdown
# Allow Admin Devices to Service Ports and IPs in VLAN50
interface: VLAN50
source: Admins			         #this is an alias for a group of admins on across all interfaces
destination: VLAN50_services   	 #this is an alias for a group of mngmt ips on VLAN50
destination_port: ports	         #this is an alias for a group pf mngmt ports on VLAN50
action: pass
log: yes

# Block All to Service Ports in VLAN50
interface: VLAN50
source: VLAN50 net
destination: VLAN50_services
destination_port: ports
action: block
log: yes

# Block VLAN40 to VLAN50. 	       #Here, vlan 40 is a guestwifi VLAN
interface: VLAN50
source: VLAN40 net
destination: VLAN50 net
action: block
log: yes

# Pass All to Internet
interface: VLAN50
source: VLAN50
destination: WAN net
action: pass
log: no
```
{% endcode %}





### _Logging_

{% hint style="info" %}
**Firewall → Log Files → Live View:** for real-time analysis of traffic

**Firewall → Log Files → Normal View:** for structured rule matches

**System → Log Files → Firewall:** for deeper dive into backend matches
{% endhint %}

Logging is an essential part of validating firewall rules and maintaining visibility into how traffic flows through the network. In this project I enabled logging on critical allow and block rules. A sample of both blocked and allowed logs are below.&#x20;

{% code title="Block log " %}
```markdown
Block | VLAN50 | 192.168.40.22 → 192.168..50.4 | TCP 22
Rule: Block VLAN40 to VLAN50 net

```
{% endcode %}

{% code title="Allow log " %}
```markdown
Pass | VLAN40 | 192.168.40.22 → 8.8.8.8 | UDP 53
Rule: Allow VLAN40 to WAN

```
{% endcode %}

