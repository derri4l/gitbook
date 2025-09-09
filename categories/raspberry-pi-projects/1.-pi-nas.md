---
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

# Pi NAS

### _What is the goal?_

This goal of this project was to be able to build a low powered and secured NAS, I also wanted to push it beyond its primary usage as storage for files and have it be a small server to host some lightweight services.



### _Hardware_&#x20;

* Raspberry Pi 5 &#x20;
* GeekPI N16 NVMe expansion board&#x20;
* 3x NVME SSDs
* Ethernet Cable
* MicroSD card&#x20;
* Power Cable

### _Putting it all together_&#x20;

The first thing I did was to assemble the GeekPi board to the Pi itself. The board itself has a USB port that supplies power to both the board and the PI, it also supports 4 NVME SSDs  in various sizes. In this project i only used 3.&#x20;

<figure><img src="../../.gitbook/assets/1745419562288.jpg" alt="" width="375"><figcaption><p>SSD Bay</p></figcaption></figure>

The board connects to the Pi via a flexible PCIe ribbon cable. Power is supplied through PD to the board, which then feeds the Pi through pogo‑pin contacts underneath. Inside the board is an ASM1184e PCIe switch that splits the single x1 lane into four ports.

<figure><img src="../../.gitbook/assets/Screenshot 2025-08-31 170004.png" alt="" width="375"><figcaption></figcaption></figure>



### _Software & Storage configuration_

Since this project is being done on a Pi, there were pretty limited options when it came to a software NAS distro. I ended up going with OMV (OpenMediaVault). OMV is a NAS distro that supports software RAID, and multiple protocols including FTP, SMB, NFS etc.&#x20;

<figure><img src="../../.gitbook/assets/image (43).png" alt=""><figcaption></figcaption></figure>

I chose to go with RAID 5 with the 3 SSDs I had installed. After setting up the array I also created the RAID volume, created multiple shared folders. To make sure our file sharing is cross-platform I enabled SMB/CIFS allowing users on Winodows, mac or linux to be able to map and access the drives.

<figure><img src="../../.gitbook/assets/image (37).png" alt="" width="563"><figcaption><p>RAID configuration</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (38).png" alt="" width="563"><figcaption><p>guest shared folders</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (41).png" alt=""><figcaption><p>Disk health</p></figcaption></figure>

### _User Access_&#x20;

The next thing I did was to create user profiles for the people I wanted to give access to the NAS. Each user had read/write access over the shared folder provisioned for them. I also created assigned groups with tailored permissions just in case I had to add more users or switch permissions for an existing one.

<figure><img src="../../.gitbook/assets/image (39).png" alt=""><figcaption><p>User list</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (40).png" alt=""><figcaption><p>Permissions for Guest shared folder</p></figcaption></figure>

### _Security_

This NAS is behind my firewall and has custom rules targeted to it already but I did add and change a few things that will contribute to security. First thing I did was to change the default port for SSH and GUI login. I also disabled password login for SSH and enabled key authentication only.

I also disabled root login for ssh and only allowed an admin to login via SSH and then switch to root if needed.

<figure><img src="../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FtKCdLx9Q3zfC5X462rKp_2Fimage.avif" alt=""><figcaption></figcaption></figure>

I also installed Fail2ban on top of the NAS. Fail2ban is like a lightweight IPS that monitors logs and creates jails to ban IP addresses, and you can set your own custom rules. I mainly have it work side by side with the firewall to protect against brute-force attacks.

<figure><img src="../../.gitbook/assets/https___files.gitbook.com_v0_b_gitbook-x-prod.appspot.com_o_spaces_2F5CX8xiUREasczFLSoLXp_2Fuploads_2FjfOtWbbHBfB4uoeky0eg_2Fimage.avif" alt=""><figcaption></figcaption></figure>

### _Extra Pictures_

<figure><img src="../../.gitbook/assets/image (42).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>
