---
{"dg-publish":true,"permalink":"/mac-os/munki/"}
---

## About munki
Munki is a tool fordeploying software to macos with automated updates

### links
https://github.com/munki/munki - git
https://github.com/munki/munki/wiki - docs
https://github.com/munki/munki/releases - downloads

### Useful commands

#### Force install of pending updates
```bash
/usr/bin/local/managedsoftwareupdate
/usr/bin/local/managedsoftwareupdate --installonly
```

#### Extra verbose debugging
Can be used with any other option
```bash
/usr/bin/local/managedsoftwareupdate -vvv
```

