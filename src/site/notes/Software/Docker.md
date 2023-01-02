---
{"dg-publish":true,"permalink":"/software/docker/"}
---


Docker is a piece of software for running and managing [[Container\|Container]]s

### Issues and fixes

- Osascript symlink pop-up on start on macos
	- https://github.com/docker/for-mac/issues/6634
	- Workaround : setting `authDeclinedInstallSettings` to true in `~/Library/Group\ Containers/group.com.docker/settings.json`
	- Should be fixed in the next release, affected release is 4.15.0