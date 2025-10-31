# Gas-Waage
<img src="https://github.com/user-attachments/assets/dea2768a-bedc-4f4e-ba46-a16eebc33680" width="400"><img src="https://github.com/user-attachments/assets/e477bd58-a699-49a5-98bb-be74471ab537" width="355" height=""><br><br>
<img src="https://github.com/user-attachments/assets/2c11a47e-38d1-4af4-b34c-e7ab45fe0465" width="400" height=""><br><br>

# Annahmen:
* Argon Flaschen verfügbar in<br>
* 10L -> 16kg gefüllt -> Gasmenge 3,67kg -> 2,1m3 -> (25,00 €*/ m³) Gasgewicht bei 20degC: 3,7kg -> 15,14 €/kg<br>
* 20L -> 30kg gefüllt -> Gasmenge 7,18kg ->   -> (16,03 €*/ m³) Gasgewicht bei 20degC: 7,15kg -> 9,63 €/kg<br>
* Volumenmenge Argon Gas: 0,6l/g

Es wurde eine 60kg Wägezelle (Quelle: Holger G.) in eine Halterung aus Holz (Orlando E.) gebaut. Das Unterteil ist quadratisch und hat 3 dem Boden angepasste Standfüße.
Das Oberteil hat eine runde Form, die 10 und 20 Liter Stahlflaschen aufnehmen kann.<br><br>
Die Wägezelle ist mit einem [HX711](https://github.com/RobTillaart/HX711/tree/master) Interface verbunden, dass seine Messdaten per I2C einem WEMOS D1 mini NodeMCU bereit stellt.<br><br>
Das C++ Programm ist eher rudimentär aufgebaut (Dieter H.) und veröffentlicht (published) die Messwerte und Systemstati per WLAN an unseren MQTT-Broker und in einer EPROM-Emulation (FLASH-Speicherbereich) werden alle wichtigen Systemvariablen gespeichert, sie sind nach einem Neustart verfügbar.
Das aktuelle Gewicht der Gasflasche wird alle 10 Sekunden gesendet und alle 15 Sekunden werden die Systemvariablen aktualisiert.
<br><br>

# Achtung - bitte beachten!
Die Sicherungskette für die Gasflasche soll 'locker' sein, um nicht den Wiegevorgang zu beeinflussen. 
Es darf nichts an die Gasflasche gehängt oder sich aufgestützt werden.<br>
Auch ist zu beachten, eine neue Gasflasche vorsichtig und sanft auf die Wägezelle aufzusetzten, damit diese nicht beschädigt wird.
## Gaswaage Display
ist normalerweise gedimmt und wird heller, wenn man den Screen mit dem Finger berührt
### Display Screen #1
<img src="https://github.com/user-attachments/assets/f4a7f5b3-6c9e-4bd0-a814-f1e29df75f4f" width="480"><br><br>
zeigt die aktuellen Informationen:<br>
* Startgewicht(g) - ist das Gesamtgewicht beim Aufstellen einer neuen Flasche
* Flaschentyp - zeigt 10 Liter Flasche oder 20 Liter Flasche je nach aufgestelltem Typ (wird von SYMCON ermittelt)
* Gewicht aktuell (g) - ist das aktuelle Gewicht, dass alle 15 Sekunden aktualisiert wird
* Verbrauch - heute (g) - zeigt den Tagesverbrauch (0:00 - 23:59) in 10g Schritten
* Kosten heute (Euro) - zeigt die errechneten Tageskosten in Euro an (bitte aufgerundet in die Spendenkasse legen)
* Verbrauch Summe (g) - zeigt den akkumulierten Gasverbrauch in Gramm seit dem Aufstellen der Flasche an

Der Button "Admin Bereich" führt auf die nächste Seite zur Eingabe eines Admin-Passcodes (kennen nur Personen mit dem 'need to know')

### Display Screen #2
<img src="https://github.com/user-attachments/assets/61f428e3-88db-435b-8290-c1fe503e7198" width="480"><br><br>
wird hier der korrekte 4-stellige Passcode eingegeben, öffnet sich der Screen #3, von dem aus verschiedene Scripte gestartet werden können<br>
Wird ein falscher Passcode eingegeben, springt die Anzeige zurück auf Screen #1

### Display Screen #3
<img src="https://github.com/user-attachments/assets/c57c1d0f-ffa4-4e31-8c59-bc7335247e2a" width="480"><br><br>
Die Funktionen können fatale Folgen haben, deshalb bitte mit Bedacht ausführen.<br>
Funktionen:
* Waage nullen - Diese Funktion sollte nur bei leerer Waage ausgeführt werden - sie setzt und speichert den Nullpunkt der Waage neu
* Neue Flasche - Nach dem Aufstellen einer neu gefüllten Flasche, muss diese Funktion ausgeführt werden. Es wird das Initial-(Start)Gewicht gemessen und gespeichert. Tages- und Summenverbrauch sowie die 'Kosten heute' auf '0' gesetzt.
* EPROM löschen - Vorsicht! Löscht den in Flash Memory emulierten EPROM-Bereich - sollte nur durch Admins ausgelöst werden, gefolgt von einem RESET.
* Waage neu starten (RESET) - selbsterklärend<br><br>

## MQTT-Topics

### gaswaage/values  
"Scale Reading": 32409  - alle 10 Sekunden Wert in Gramm<br><br>

### gaswaage/status
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

### gaswaage/lwt
"status":"online"<br>
"client":"gaswaage_1",<br>
"ip":"192.168.X.XXX" 
<br><br>
#### Per MQTT können folgende Aktivitäten ausgelöst werden:
### gaswaage/in
"{setoffset:-104289}"  -> vorher ermittelter Offsetwert setzen (leere Waage - Meßwert = 0)<br>   
"{setscale:-73.7}" -> Waagenkalibrierung setzen (muss eigentlich nur einmal bei Inbetriebnahme gemacht werden)<br> 
"{getoffset}"  -> TARA Funktion setzt Nullwert für leere Waage<br> 
"{newbottle}"  -> muss jedesmal ausgeführt werden, wenn eine neue Flasche aufgestellt wird<br> 
"{reset}"  -> Controller RESET<br> 
"{wipeeprom}" -> Vorsicht - löscht den in Flash Memory emulierten EPROM-Bereich (reset afterwards)<br> 
