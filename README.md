# Gas-Waage
<img src="https://github.com/user-attachments/assets/dea2768a-bedc-4f4e-ba46-a16eebc33680" width="600">
<img src="https://github.com/user-attachments/assets/aa4500d2-756e-4b84-8dfc-c6fe966331bc" width="600" height=""> <br><br>
<img src="https://github.com/user-attachments/assets/2c11a47e-38d1-4af4-b34c-e7ab45fe0465" width="600" height=""> <br><br>
Annahmen:
* Argon Flaschen verfügbar in<br>
* 10L -> 16kg gefüllt -> Gasmenge 3,67kg -> 2,1m3 -> (25,00 €*/ m³) Gasgewicht bei 20degC: 3,7kg -> 6,8 €/kg<br>
* und <br>
* 20L -> 30kg gefüllt -> Gasmenge 7,18kg ->   -> (16,03 €*/ m³) Gasgewicht bei 20degC: 7,15kg -> 2,23 €/kg<br>
* Stahlflaschen<br><br>

Es wurde eine 60kg Wägezelle (Quelle: Holger G.) in eine Halterung aus Holz (Orlando E.) gebaut. Das Unterteil ist quadratisch und hat 3 dem Boden angepasste Standfüße.
Das Oberteil hat eine runde Form, die 10 und 20 Liter Stahlflaschen aufnehmen kann.<br><br>
Die Wägezelle ist mit einem [HX711](https://github.com/RobTillaart/HX711/tree/master) Interface verbunden, dass seine Messdaten per I2C einem WEMOS D1 mini NodeMCU bereit stellt.<br><br>
Das C++ Programm ist eher rudimentär aufgebaut (Dieter H.) und veröffentlicht (published) die Messwerte und Systemstati per WLAN an unseren MQTT-Broker und in einer EPROM-Emulation (FLASH-Speicherbereich) werden alle wichtigen Systemvariablen gespeichert, sie sind nach einem Neustart verfügbar.
Das aktuelle Gewicht der Gasflasche wird alle 10 Sekunden gesendet und alle 15 Sekunden werden Systemvariablen aktualisiert:
<br><br>
<h4>z.B topic gaswaage/values</h4>   
"Scale Reading": 32409  - alle 10 Sekunden Wert in Gramm<br><br>
<h4>z.B. topic gaswaage/status</h4>  
"revision":"1.0_250612", - SW-Revision<br> 
"ip":"192.168.X.XXX",  - IP<br> 
"rssi":-79,  - WLAN Signalstärke<br> 
"cnt_reconnect":0,  - Anzahl der WLAN-Reconnct Versuche<br> 
"client":"gaswaage_1",  - Client Name<br> 
"scale":74.33300018,  - Waagenkalibrierwert<br> 
"offset":108917,  - TARA Wert für leere Waage<br>  
"Initial Botttle Weight":32298,  - Flaschengewicht wenn neue Flasche aufgestellt wird<br> 
"eeprom_use":1,  - EPROM-Nutzung in %<br> 
"last TS":"17.06.25 11:20:00"  - Zeitmarke der letzten Aktualisierung<br><br>
ausserdem wird der 'Last Will Testament' publiziert, hiermit kann geprüft werden, ob die Waage auch tatsächlich 'online' ist  
<h4>z.B. topic gaswaage/lwt</h4>  
"status":"online"<br>  
"client":"gaswaage_1",<br>   
"ip":"192.168.X.XXX"  
<br><br>
Per MQTT können folgende Aktivitäten ausgelöst werden:<br><br>
topic gaswaage/in<br>
"{setoffset:-104289}"  -> vorher ermittelter Offsetwert setzen (leere Waage - Meßwert = 0)<br>   
"{setscale:-73.7}" -> Waagenkalibrierung setzen (muss eigentlich nur einmal bei Inbetriebnahme gemacht werden)<br> 
"{getoffset}"  -> TARA Funktion setzt Nullwert für leere Waage<br> 
"{newbottle}"  -> muss jedesmal ausgeführt werden, wenn eine neue Flasche aufgestellt wird<br> 
"{reset}"  -> Controller RESET<br> 
"{wipeeprom}" -> Vorsicht - löscht den in Flash Memory emulierten EPROM-Bereich (reset afterwards)<br> 



[HX711 Library Description](https://github.com/RobTillaart/HX711/tree/master)
