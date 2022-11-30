---
dg-publish: true
tags:
- public
---
#### Enable non-admins to change wifi settings
Like to remove configured networks etc.
```bash
#!/bin/bash
#For WiFi
/usr/bin/security authorizationdb write system.preferences.network allow
/usr/bin/security authorizationdb write system.services.systemconfiguration.network allow
/usr/bin/security authorizationdb write com.apple.wifi allow
```

#### Firewall setting
```bash
#Enable Firewall
defaults write /Library/Preferences/com.apple.alf globalstate -int 1
```
#### MacOS update (intel only)
```bash
#!/bin/bash

echo "OS version not monterey"
FILE=/Applications/Install\ macOS\ Monterey.app
if [ ! -d "$FILE" ];then
echo "monterey downloader not detected"
/usr/sbin/softwareupdate --fetch-full-installer --full-installer-version 12.5
fi

if [ -d "$FILE" ];then
echo "monterey downloader detected"
'/Applications/Install macOS Monterey.app/Contents/Resources/startosinstall' --agreetolicense --forcequitapps
fi
```