---
{"dg-publish":true,"permalink":"/windows/user-folder-ownership/"}
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
for /f "tokens=*" %%G in (c:\scripts\ownership\users.txt) do (  
REM Set ownership for each home folder  
icacls "%BrukerMapper%\%%G" /setowner "%Domene%\%%G"  
REM Create a list of all files and sub-folders in the users home folder  
del c:\scripts\ownership\FileList.txt /q  
dir %BrukerMapper%\%%G\*.* /b /s >c:\scripts\ownership\FileList.txt  
REM Read each line from just created filelist and set ownership  
for /f "tokens=*" %%F in (c:\scripts\ownership\FileList.txt) do (icacls "%%F" /setowner "%Domene%\%%G")  
echo.  
)
```