---
dg-publish: true
tags:
- public
- linux
- networking
- debian
---

To change hostname, to files needs to be updated
`nano /etc/hostname`
Change the text in the file to the new hostname

Also update the dns reference in `/etc/hosts`
`nano /etc/hosts`

```
127.0.0.1       localhost
127.0.1.1       CHANGETHIS	Changethis.domain.local

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters

```