---
dg-publish: true
tags:
- public
- windows
- activedirectory
---
### About
This script will set ownership for all folders within the specified directory to the user which matches the name of the folder

Useful for setting ownership for homefolders

Type: .bat

### Script
```bat
@echo off  
REM Variabler  
Set BrukerMapper="g:\test"  
Set Domene="[domenenavn.no](http://domenenavn.no/)"  
REM Create list of folders  
dir /a:d /b %BrukerMapper%\ > c:\scripts\ownership\users.txt  
REM Read each line from just created text file…  
for /f "tokens=*" G" /setowner "%Domene%\G\*.* /b /s >c:\scripts\ownership\FileList.txt  
REM Read each line from just created filelist and set ownership  
for /f "tokens=*" F" /setowner "%Domene%\%%G")  
echo.  
)
```