---
{"dg-publish":true,"permalink":"/stuff/arduino-controlpanel-med-server-interface/"}
---

#Github link: https://github.com/autopkg/dataJAR-recipes
## Arduino Konfig

Arduino probrammet er vedlagt [HER](http://libre:8080/bin/download/Main/Arduino%20Controlpanel%20med%20server%20interface/CONTROLPANEL.ino?rev=1.3)

Følgende ekstra biblioteker er brukt;

[FastLED](http://libre:8080/bin/download/Main/Arduino%20Controlpanel%20med%20server%20interface/FastLED-3.0.3.zip?rev=1.1) - [http://fastled.io/](http://fastled.io/)

[Bounce2](http://libre:8080/bin/download/Main/Arduino+Controlpanel+med+server+interface/Bounce2-master.zip) - [http://playground.arduino.cc/Code/Bounce](http://playground.arduino.cc/Code/Bounce)

[aREST](http://libre:8080/bin/download/Main/Arduino%20Controlpanel%20med%20server%20interface/aREST-master.zip?rev=1.1) - [http://arest.io](http://arest.io/)

## Windows server konfig[Edit](http://libre:8080/bin/edit/Main/Arduino%20Controlpanel%20med%20server%20interface?section=2)

Oppsett er gjort i windows server 2012R2, men bør fungere i alle versjoner som har powershell v4+

Konfig er avhengig av et sett med powershell moduler og skript.

### ArduinoREST

Moduler for kommunikasjon med Arduino

Last ned Modulen må plasseres i `C:\Windows\System32\WindowsPowerShell\v1.0\\Modules\ArduinoREST\  
Hvis mappen ArduinoREST ikke finnes må den opprettes.

Modulen er dokumenter med tilhørende eksempler i Get-Help funksjonen.

### HttpListener

HTTP tjeneste som venter på input fra Arduino til server.

Modifisert versjon av [http://blogs.msdn.com/b/powershell/archive/2014/12/19/10561247.aspx](http://blogs.msdn.com/b/powershell/archive/2014/12/19/10561247.aspx)  
Last ned [her](http://libre:8080/bin/download/Main/Arduino%20Controlpanel%20med%20server%20interface/HttpListener.zip?rev=1.1) Modulen må plasseres i C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\Modules\\  
 

Start tjenesten med dette fra en powershell prompt som er kjørt som administrator

Start-HttpListener -Auth Anonymous

Standard port her er 8888

Eksempel på URL kommando Get-Process,

http://SERVERNAVN:8888/?command=Get-Process&format=text  
 

&format=text er valgfri, kan også formateres som json, xml,clixml

Legges i startup på serveren som en schedueld task, [HTTPlistener.xml](http://libre:8080/bin/download/Main/Arduino%20Controlpanel%20med%20server%20interface/Httplistener.xml?rev=1.1) kan importeres i task schedueler

For at knappene skal kunne utføre handlinger, lag en fil som heter button0.ps1, button1.ps1 etc. for hver av knappene som inneholder kommandoer for hva som skal skje når knappene trykkes.

Filene plasseres i samme mappen som er target for powershell session som kjører httplistener, default; c:\\users\\administrator\\

### Test-Port

Powershell modul for å gjøre porttester

Last ned

Eksempel på check 

$Led0=(Test-Port -computer google.no -TCP -port 80).open  
**if** ($Led0 -eq $true ){ Set-ArduinoLed -Server 10.0.0.45 -LedNumber 0 -SimpleColour Green}  
**else** { Set-ArduinoLed -Server 10.0.0.45 -LedNumber 0 -SimpleColour Red}

Lag et skript med en kopi av denne seksjonen til hver host du vil sjekke og led du vil styre i en .ps1 fil.  
Lag en schedueld task i windows og sett interval for hvor ofte du vil sjekke.

## Hardware

[Momentary switch](http://libre:8080/bin/view/Datasheets/12V%2016mm%20Car%20Auto%20Metal%20LED%20Angel%20Eye%20Momentary%20Alloy%20Push%20Button%20Switch)

[5V til 12V step-up converter](http://libre:8080/bin/view/Datasheets/DC%203.3V%203.7V%205V%206V%20to%2012V%20Boost%20Voltage%20Regulator%20Converter%20Step-up) (Til lys i bryterene)

Arduino UNO

[Led String](http://libre:8080/bin/view/Datasheets/WS2801%20RGB%20LED%20pixel%20light%20SJ-1515ICRGB)

### Arduino Rest API

Api er basert på [http://arest.io](http://arest.io/)

Noen spesial tilpassede funkstoner finnes,

http://server/LightsToggle?params=X

X her kan byttes ut med en int

\- 1 betyr skru av leds

\- 0 betyr skru på leds

\- alt annet betyr TOGGLE state

http://Server/SetLeds?params=$LedNumber,$RedValue,$GreenValue,$BlueValue,

Bytt ut dollarverdien med int (0-255) som representerer Led nummeret og ønsket fargenivå.

OBS! komma (,) på slutten må være med (ubrukt for nå, debug switch)

http://Server/looptest

Kjører lysshow for å teste leds + KULT

