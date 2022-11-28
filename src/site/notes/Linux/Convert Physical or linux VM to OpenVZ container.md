---
{"dg-publish":true,"permalink":"/linux/convert-physical-or-linux-vm-to-open-vz-container/"}
---

#proxmox #linux 

# Prepare a new “empty” container 

Create a new container on your hypervisor, I recomend using a template of the same OS to avoid as many problems as possible later



# Copying the data 

Use tar and excluding some dirs, you could do it like this:

Create a file /tmp/excludes.excl with these contents:

`.bash_history
/dev/*
/mnt/*
/tmp/*
/proc/*
/sys/*
/usr/src/*
`

Then create the tar. But remember, when the system is 'not' using udev, you have to look into /proc/ after creating your container because some devices might not exist. (/dev/ptmx or others) (Not neccesarry if used with Debian 7.x template)

Before running this command make sure all critical services are stopped eg. mysql, tomcat, apache etc. (If there are problems you can also boot from a live cd to this bit)

`# tar --numeric-owner -cjpf /tmp/mysystem.tar.bz2 / -X /tmp/excludes.excl
`


# Extract the data 

Copy the Tar you created to the hypervisor and extract it to the appropriate directory (for proxmox /var/lib/vz/private/<VMID>/ )

`tar -xjf test.tbz -C /tmp/test`

# Adjustments 


# /etc/inittab 

A container does not have real ttys, so you have to disable getty in /etc/inittab (i. e. /vz/private/123/etc/inittab).

`sed -i -e 's/^[0-9].*getty.*tty/#&/g'  /vz/private/123/etc/inittab`

#  

# tty device nodes 

In order for vzctl enter to work, a container needs to have some entries in /dev. This can either be /dev/ttyp* and /dev/ptyp*, or /dev/ptmx and mounted /dev/pts.

# **/dev/ptmx** 

Check that /dev/ptmx exists. If it does not, create with:

`mknod --mode 666 /vz/private/123/dev/ptmx c 5 2
`

# **/dev/pts/** 

Check that /dev/pts exists. It's a directory, if it does not exist, create with:

`mkdir /vz/private/123/dev/pts
`

# **/dev/ttyp* and /dev/ptyp***
 

To copy:

`cp -a /dev/ttyp* /dev/ptyp* /vz/private/123/dev/
`

To recreate with MAKEDEV, either

`/sbin/MAKEDEV -d /vz/private/123/dev ttyp ptyp
`

or

`cd /vz/private/123/dev && /sbin/MAKEDEV ttyp`


Make sure sure /dev/null is not a file or directory; if unsure remove and recreate. If this is not correct sshd will not start correctly.

`rm -f /vz/private/123/dev/null
mknod --mode 666 /vz/private/123/dev/null c 1 3
`

# /dev/urandom 

Check that /dev/urandom exists. If it does not, create with:

`mknod --mode 444 /vz/private/123/dev/urandom c 1 9`


# References; 

http:~/~/openvz.org/Physical_to_container

http:~/~/www.cyberciti.biz/faq/linux-unix-open-bzipped-tbz-archive-file/

