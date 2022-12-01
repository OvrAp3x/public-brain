---
{"dg-publish":true,"permalink":"/linux/check-folder-space-usage/"}
---


To find the size of folders in a dir run
`sudo du -h --max-depth=1 / | sort -n -r`

Change '/' to whatever path you are interested in.

Also see [[Linux/ncdu\|ncdu]] application for a bit more user friendlyness for this kind of task