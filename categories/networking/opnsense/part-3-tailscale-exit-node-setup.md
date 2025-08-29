---
description: Tailscale exit node on OPNsense
icon: block-brick-fire
---

# Part 3 - Tailscale exit node setup

#### Tailscale

Tailscale provides a simple, encrypted, peer-to-peer VPN mesh using WireGuard. In this setup, OPNsense acts as an authenticated Tailscale client and **exit node**, allowing remote devices to route traffic through the firewall securely.

#### Setup

{% hint style="info" %}
Location: VPN → Tailscale → Settings
{% endhint %}

This is a pretty straight foward setup, in OPNsense there is a checkbox for "Advertise Exit Node". Simply enabling it and approving the route on your Tailscale console allows you to use your OPNsense firewall as a exit node.

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

&#x20;But if we had to do this in the terminal;

```sh
#!/bin/sh

# Start the Tailscale service
service tailscaled onestart

# Configure Tailscale as an exit node
tailscale up --advertise-exit-node --reset

# Display the current Tailscale connection status
tailscale status
```



After advertising and authenticating on your Tailscale console you should be able to select your firewall as an exit node on tailscale.

<figure><img src="../../../.gitbook/assets/image (17).png" alt=""><figcaption><p>Tailscale app with exit node enabled </p></figcaption></figure>
