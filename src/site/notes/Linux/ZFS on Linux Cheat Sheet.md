---
dg-publish: true
tags:
- public
- zfs
- disk
- linux
---

# Memory ARC limits
This will limit arc usage to 8Gb
```
echo "options zfs zfs_arc_max=8589934592" >> /etc/modprobe.d/zfs.conf
echo 8589934592 > /sys/module/zfs/parameters/zfs_arc_max
update-initramfs -u
```