---
icon: pie
---

# Part 2 - Security

* Switched ssh and dahsboard console ports from default (8443, 22) to custom, also disabled ssh password login, root login and allowed login via keys only&#x20;

<figure><img src="../../../.gitbook/assets/image (1).png" alt="" width="563"><figcaption></figcaption></figure>

* Installed and configured Fail2Ban (this prevents brute-force attacks) and Whitelisted all trusted IPS in the jail.

<figure><img src="../../../.gitbook/assets/image.png" alt="" width="563"><figcaption></figcaption></figure>
