---
{"dg-publish":true,"permalink":"/mac-os/mac-os-preferences/"}
---

## Enable non-admins to change wifi settings
Like to remove configured networks etc.
```bash
#!/bin/bash
#For WiFi
/usr/bin/security authorizationdb write system.preferences.network allow
/usr/bin/security authorizationdb write system.services.systemconfiguration.network allow
/usr/bin/security authorizationdb write com.apple.wifi allow
```
