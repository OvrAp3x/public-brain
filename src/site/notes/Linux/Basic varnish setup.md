---
{"dg-publish":true,"permalink":"/linux/basic-varnish-setup/"}
---

# Install varnish
Follow download and install instructions here https://www.varnish-cache.org/releases
# Configure varnish
This setup is intended for caching of http content via varnish from any web server serving http on port tcp 80

- You need to modify the startup parameters in either `/etc/default/varnish` or `/etc/sysconfig/varnish` (depending on Operating System) and set the listening port to port 80 (-a :80).

-Then you will want to edit `/etc/varnish/default.vcl` to reflect what web server you use. 

- Once you’ve configured it, you can restart Varnish with “`service varnish restart`”.

Now you can access your sites via http://serverip