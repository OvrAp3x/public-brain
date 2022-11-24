---
{"dg-publish":true,"permalink":"/linux/proxmox-backup-client-installation/"}
---

# About

This page contains an excerpt for the installation of the proxmox-backup-client CLI utility.  
This reference is kept here for the sake of quick access and completeness.  
The source reference is: [https://pbs.proxmox.com/docs/installation.html#client-installation](https://pbs.proxmox.com/docs/installation.html#client-installation "https://pbs.proxmox.com/docs/installation.html#client-installation")

## Manual installation in Debian host

### Add repository

Add the following to `/etc/apt/sources.list`

`1deb http://download.proxmox.com/debian/pbs buster pbstest`

#### Install the client

`1# apt-get update 2# apt-get install proxmox-backup-client`