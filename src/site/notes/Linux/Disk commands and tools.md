---
dg-publish: true
tags:
- public
---

#### Expand LVM when resizing virtual disks
```bash
#resize the LVM partition to expand to all available space
pvresize /dev/sdb
#expand the logical volume to desired size
lvextend /dev/elastic/elastiv-lv -L 195G 
#resize the file system to the new logical volumes size
resize2fs /dev/elastic/elastiv-lv
```