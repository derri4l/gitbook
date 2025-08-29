---
description: >-
  This project involves setting up a NAS with a Raspberry PI 5 running OMV
  allowing users on the network to store their data.
icon: pie
layout:
  width: default
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: false
  outline:
    visible: true
  pagination:
    visible: true
  metadata:
    visible: true
---

# Raspberry PI NAS

### _What I'm I building?_

This project involved setting up a Network-Attached Storage (NAS) with a Raspberry PI 5 and a 4 bay NVMe expansion board. In addition, this project was also intended to explore and implement firewall rules, remotely accessing files, and other features to simulate a secure server.



### _Hardware_&#x20;

* Raspberry Pi 5 &#x20;
* GeekPI N16 NVMe expansion board&#x20;
* 3x NVME SSDs
* Ethernet Cable
* MicroSD card&#x20;
* Power Cable

### _Putting it all together_&#x20;

* Assembled the NVMe expansion board and connected it with the PI.&#x20;
* Installed a headless version of Raspberry OS. &#x20;
* Installed OMV (OpenMediaVault) on top of Raspberry OS.

{% hint style="info" %}
Pictures of the NAS is on the Media page
{% endhint %}

### _Storage/user access_

* Implemented RAID 5 using the 3 NVMe SSDs and created and mounted the RAID volume via the OMV GUI.

<figure><img src="../../../.gitbook/assets/IMG_0142.jpeg" alt=""><figcaption><p>RAID configuration</p></figcaption></figure>

<figure><img src="../../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FBeatTOA9Npjt0oumcTeO_2Fimage.avif" alt=""><figcaption><p>Disk health</p></figcaption></figure>



* Created users and provisioned them, I also made shares and assigned what user has access to the shares.
*

    <figure><img src="../../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FBxPwVJ9VfnTHdPLsloHd_2FScreenshot_202025-07-14_20124134.avif" alt=""><figcaption><p>Users and groups</p></figcaption></figure>



    <figure><img src="../../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FMli5kM5eG5A9WgANEvSv_2FScreenshot_202025-07-14_20124558.avif" alt=""><figcaption><p>shared folders </p></figcaption></figure>



<figure><img src="../../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FvRFWLvoM7KpqGQ7nsiUg_2Fimage.avif" alt=""><figcaption><p>permissions for guest shared folder</p></figcaption></figure>



### _Security_

* Switched ssh and dahsboard console ports from default (8443, 22) to custom, also disabled ssh password login, root login and allowed login via keys only.

<figure><img src="../../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FtKCdLx9Q3zfC5X462rKp_2Fimage.avif" alt=""><figcaption></figcaption></figure>



* Installed and configured Fail2Ban (this prevents brute-force attacks) and Whitelisted all trusted IPS in the jail.

<figure><img src="../../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FjfOtWbbHBfB4uoeky0eg_2Fimage.avif" alt=""><figcaption></figcaption></figure>

