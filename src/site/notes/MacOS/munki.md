---
dg-publish: true
tags:
- public
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

#### Munki installation
```bash
#!/bin/sh

#Periodically run this script to check for updates on a mac because the default update checking interval is to slow

FILE=/usr/local/munki/managedsoftwareupdate
if [ ! -f "$FILE" ]; then
cd /var/root
curl -OL https://github.com/munki/munki/releases/download/v5.7.3/munkitools-5.7.3.4444.pkg
installer -pkg /var/root/munkitools-5.7.3.4444.pkg -target /
fi

#set repo address
defaults write /Library/Preferences/ManagedInstalls SoftwareRepoURL "https://munki.hub.fintechinnovation.no/"

defaults write /Library/Preferences/ManagedInstalls AdditionalHttpHeaders -array "Authorization: Basic bXVua2k6MndzeC54c3cy"

#set munkireports passphrase
defaults write /Library/Preferences/MunkiReport Passphrase 'Secretbase64string'

#enable macosupdates
defaults write /Library/Preferences/ManagedInstalls InstallAppleSoftwareUpdates -bool False

#enable unattended macos updates for things that do not require a restart
defaults write /Library/Preferences/ManagedInstalls UnattendedAppleUpdates -bool True

#update nofification settings

#This is stored in mobileconfig file

#set client identifier

#defaults write /Library/Preferences/ManagedInstalls ClientIdentifier $(hostname -s)
```