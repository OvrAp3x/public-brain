---
dg-publish: true
tags:
- public
- linux
- fix
- domain
- activedirectory
- ubuntu
---
There is a known issue with apparmor stopping applications from ubuntu snap launching on domain joined machines.

The root of the problem is that the home directory is stored in the /home/MYDOMAIN.LAN directory.

To solve the issue add the following to /etc/apparmor.d/tunables/home.d/site.local

Open the file in text editor: **sudo nano /etc/apparmor.d/tunables/home.d/site.local**

```
Add the following to the end of the file: (after added: ctrl x and save) @{HOMEDIRS}+=/home/MYDOMAIN.LAN/
```