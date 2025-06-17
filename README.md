# Gas-Waage
<img src="https://github.com/user-attachments/assets/dea2768a-bedc-4f4e-ba46-a16eebc33680" width="600">
<img src="https://github.com/user-attachments/assets/aa4500d2-756e-4b84-8dfc-c6fe966331bc" width="600" height=""> <br><br>
Annahmen:
* Argon Flaschen verfügbar in 
* 10L -> 16kg gefüllt -> Gasmenge 3,67kg -> 2,1m3 -> (25,00 €*/ m³) Gasgewicht bei 20degC: 3,7kg -> 6,8 €/kg
* und 
* 20L -> 30kg gefüllt -> Gasmenge 7,18kg ->   -> (16,03 €*/ m³) Gasgewicht bei 20degC: 7,15kg -> 2,23 €/kg
* Stahlflaschen

Es wurde eine 60kg Wägezelle (Quelle: Holger G.) in eine Halterung aus Holz (Orlando E.) gebaut. Das Unterteil ist quadratisch und hat 3 dem Boden angepasste Standfüße.
Das Oberteil hat eine runde Form, die 10 und 20 Liter Stahlflaschen aufnehmen kann.<br><br>
Die Wägezelle ist mit einem [HX711](https://github.com/RobTillaart/HX711/tree/master) Interface verbunden, dass seine Messdaten per I2C einem WEMOS D1 mini NodeMCU bereit stellt.<br><br>
Das C++ Programm ist eher rudimentär aufgebaut (Dieter H.) und veröffentlicht (published) die Messwerte und Systemstati per WLAN an unseren MQTT-Broker.
Das aktuelle Gewicht der Gasflasche wird alle 10 Sekunden gesendet und alle 15 Sekunden werden Systemvariablen aktualisiert:
<br><br>
<h4>z.B gaswaage/values</h4>   
"Scale Reading": 32409  - alle 10 Sekunden Wert in Gramm<br><br>
<h4>z.B. gaswaage/status</h4>  
"revision":"1.0_250612", - SW-Revision 
"ip":"192.168.X.XXX",  - IP  
"rssi":-79,  - WLAN Signalstärke  
"cnt_reconnect":0,  - Anzahl der WLAN-Reconnct Versuche  
"client":"gaswaage_1",  - Client Name  
"scale":74.33300018,  - Waagenkalibrierwert  
"offset":108917,  - TARA Wert für leere Waage  
"Initial Botttle Weight":32298,  - Flaschengewicht wenn neue Flasche aufgestellt wird  
"eeprom_use":1,  - EPROM-Nutzung in %  
"last TS":"17.06.25 11:20:00"  - Zeitmarke der letzten Aktualisierung<br><br>
ausserdem wird der 'Last Will Testament' publiziert, hiermit kann geprüft werden, ob die Waage auch tatsächlich 'online' ist  
<h4>z.B. gaswaage/lwt</h4>  
"status":"online",  
"client":"gaswaage_1",  
"ip":"192.168.X.XXX"  
<br><br>
Per MQTT können folgende Aktivitäten ausgelöst werden:<br><br>
"{setoffset:-104289}"  -> vorher ermittelter Offsetwert setzen (leere Waage - Meßwert = 0)  
"{setscale:-73.7}" -> Waagenkalibrierung setzen (muss eigentlich nur einmal bei Inbetriebnahme gemacht werden)  
"{getoffset}"  -> TARA Funktion setzt Nullwert für leere Waage  
"{newbottle}"  -> muss jedesmal ausgeführt werden, wenn eine neue Flasche aufgestellt wird  
"{reset}"  -> Controller RESET  
"{wipeeprom}" -> Vorsicht - löscht den in Flash Memory emulierten EPROM-Bereich (reset afterwards)  



[HX711 Library Description](https://github.com/RobTillaart/HX711/tree/master)
