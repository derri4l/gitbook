---
description: User creation and provisioning, Groups creation
icon: block-brick-fire
---

# Part 4 - User-access

#### Users&#x20;

{% hint style="info" %}
Location: System → Access → Users&#x20;
{% endhint %}

In this project, I created two new users: an admin named "johndoe" with root-level permissions, and an observer named "Hardwig" who has read-only access to specific pages in OPNsense.

<figure><img src="../../../../.gitbook/assets/image (19).png" alt="" width="465"><figcaption><p>johndoe - admin</p></figcaption></figure>

User "johndoe" has been added to the admin group and given shell access for SSH login. Additionally, the user "Hardwig" was created.

{% hint style="info" %}
In OPNsense there is a tool called tester that you can use to make sure OPNsense recognizes any user and if they exists in its local database.&#x20;
{% endhint %}



Here I'm creating a group called "Owls" for observers, granting them read-only access. This group can view pages with brief stats and logs. Hardwig has been added to this group.

<figure><img src="../../../../.gitbook/assets/image (20).png" alt="" width="409"><figcaption><p>creating Owls group</p></figcaption></figure>

