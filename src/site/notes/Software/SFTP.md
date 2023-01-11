---
{"dg-publish":true,"permalink":"/software/sftp/"}
---


**SSH File Transfer Protocol** (also known as **Secure File Transfer Protocol** or **SFTP**) is a [network protocol](https://en.wikipedia.org/wiki/Network_protocol "Network protocol") that provides [file access](https://en.wikipedia.org/wiki/File_access "File access"), [file transfer](https://en.wikipedia.org/wiki/File_transfer "File transfer"), and [file management](https://en.wikipedia.org/wiki/File_management "File management") over any reliable [data stream](https://en.wikipedia.org/wiki/Data_stream).


### Useful links 
https://github.com/emberstack/docker-sftp - good docker sftp implementation.
 - The user is `chrooted` to the `/home/demo` directory. Upon connect, the start directory is `sftp`.
 - Remove the "password" field in the config for a user and add publickey to use publickey instead of password.