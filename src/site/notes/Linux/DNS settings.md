---
{"dg-publish":true,"permalink":"/linux/dns-settings/"}
---

#dns #linux 

This is based on debian, but is similar in most distros

Open the config file
`nano /etc/resolv.conf`

Add entries, for search domain, and for nameservers. The list can contain multiple entries, and is read from top to bottom.
`search domain.local`
`nameserver 8.8.8.8`