---
dg-publish: true
tags:
- public
- windows
- ping
- cmd
- network
---
Asks for hostname and does the rest automatically

```
echo off

set/p host=host Address:   
set logfile=Log_%host%.log

echo Target Host = %host% >%logfile%  
for /f "tokens=*" ****A>>%logfile% && GOTO Ping)  
:Ping  
for /f "tokens=* skip=2" ****A>>%logfile%  
   echo %date% %time:0,2%:%time:3,2%:%time:~6,2% **%%**A  
    timeout 1 >NUL   
   GOTO Ping)
```
