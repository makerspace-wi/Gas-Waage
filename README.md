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
<img width="479" height="479" alt="Image" src="https://github.com/user-attachments/assets/dfcea5bc-332b-43d2-a13a-5e49b0dc6c04" /><br><br>
zeigt die aktuellen Informationen:<br>
* Startgewicht(g) - ist das Gesamtgewicht beim Aufstellen einer neuen Flasche
* Flaschentyp - zeigt 10 Liter Flasche oder 20 Liter Flasche je nach aufgestelltem Typ (wird von SYMCON ermittelt)
* Gewicht aktuell (g) - ist das aktuelle Gewicht, dass alle 15 Sekunden aktualisiert wird
* Verbrauch - heute (g) - zeigt den Tagesverbrauch (0:00 - 23:59) in 10g Schritten
* Kosten heute (Euro) - zeigt die errechneten Tageskosten in Euro an (bitte aufgerundet in die Spendenkasse legen)
* Verbrauch Summe (g) - zeigt den akkumulierten Gasverbrauch in Gramm seit dem Aufstellen der Flasche an
* Restgas in %

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
* Neue Flasche - Nach dem Aufstellen einer gefüllten Flasche und der Montage des Druckminderers, muss diese Funktion ausgeführt werden. Es wird das Initial-(Start)Gewicht gemessen und gespeichert. Tages- und Summenverbrauch sowie die 'Kosten heute' auf '0' gesetzt.
* EPROM löschen - Vorsicht! Löscht den in Flash Memory emulierten EPROM-Bereich - sollte nur durch Admins ausgelöst werden, gefolgt von einem RESET.
* Waage neu starten (RESET) - selbsterklärend<br><br>

# GasWaage (ESP8266 + HX711)

Firmware fuer eine WLAN-faehige Gasflaschen-Waage auf Basis eines `Wemos D1 mini` (ESP8266) mit `HX711`-Loadcell-ADC.

Das Projekt misst kontinuierlich das Gewicht, publiziert Messwerte und Status per MQTT, speichert Kalibrierdaten im EEPROM und bietet OTA-Updates ueber ein Webinterface.

## Features

- Gewichtsmessung ueber HX711
- MQTT Telemetrie und Steuerbefehle
- Last-Will-Status (online/offline) via MQTT
- Persistente Kalibrierung (Offset, Scale, Startgewicht) im EEPROM
- NTP-Zeitstempel fuer Statusmeldungen
- OTA-Updates mit ElegantOTA (`/update`)
- Asynchroner Webserver auf Port 80

## Hardware

- Wemos D1 mini (ESP8266)
- HX711 Modul
- Load Cell (passend zur gewuenschten Last)

### Pinbelegung (aktuell im Code)

- HX711 `DT` -> `D2` (GPIO4)
- HX711 `SCK` -> `D1` (GPIO5)

## Projektstruktur

- `src/main.cpp`: Hauptlogik (WiFi, MQTT, Messung, EEPROM, OTA)
- `src/Credentials.h` / `src/Credentials_ms.h`: WLAN/MQTT Zugangsdaten
- `platformio.ini`: Board, Framework, Abhaengigkeiten, Build-Flags

## MQTT

### Topics

- Publish Werte: `gaswaage/values`
- Publish Status: `gaswaage/status`
- Last Will: `gaswaage/lwt`
- Subscribe Befehle: `gaswaage/in`

### Beispiel-Payloads fuer `gaswaage/in`

- `{offset:-104289}` -> Offset setzen
- `{setscale:-73.7}` -> Scale-Faktor setzen
- `{getoffset}` -> Tara/Offset neu bestimmen
- `{newbottle}` -> aktuelles Gewicht als Startgewicht speichern
- `{reset}` -> ESP Neustart
- `{wipeeprom}` -> EEPROM loeschen (anschliessend Neustart empfohlen)

## MQTT-Stabilitaet (aktueller Stand)

Fuer den Fall "Device ist online, publisht aber nicht mehr" wurden in der Firmware Schutzmechanismen ergaenzt:

- Alle Publishes werden geprueft (Rueckgabewert von `client.publish()` wird ausgewertet).
- Bei fehlgeschlagenem Publish wird die MQTT-Verbindung aktiv getrennt, damit ein sauberer Reconnect erfolgt.
- MQTT-Buffer ist auf `512` gesetzt, damit groessere JSON-Nachrichten nicht still verworfen werden.
- KeepAlive ist auf `30s` gesetzt.
- Reconnect-Zaehler wird bei Fehlschlag erhoeht und in den Statusdaten publiziert.
- Der initiale MQTT-Connect (beim Boot) nutzt ebenfalls den LWT-Connect-Pfad.
- LWT-Connect funktioniert sowohl mit als auch ohne MQTT-Authentifizierung.

Damit wird ein stilles "haengt online, sendet aber nichts" deutlich robuster abgefangen.

## Aenderungen (22.05.2026)

- Fix: `gaswaage/lwt` wird jetzt bereits beim Erststart korrekt gesetzt.
- Fix: "online aber keine Publishes" wird durch Publish-Checks + erzwungenen Reconnect abgefangen.
- Verbesserung: robustes Connect-Verhalten bei Brokern mit und ohne User/Pass.

## Erste Inbetriebnahme

1. Zugangsdaten in `src/Credentials_ms.h` oder `src/Credentials.h` eintragen.
2. Sicherstellen, dass in `src/main.cpp` die richtige Credentials-Datei eingebunden ist.
3. Hardware anschliessen (HX711 + Load Cell).
4. Firmware bauen und flashen.
5. Serielle Konsole oeffnen und WLAN/MQTT-Status pruefen.

## Build, Upload, Monitor

### Mit PlatformIO im PATH

```bash
platformio run
platformio run -t upload
platformio device monitor -b 115200
```

### Falls `platformio` nicht im PATH liegt

```bash
~/.platformio/penv/bin/platformio run
~/.platformio/penv/bin/platformio run -t upload
~/.platformio/penv/bin/platformio device monitor -b 115200
```

## OTA-Update

Nach erfolgreichem WLAN-Connect ist das OTA-Interface erreichbar unter:

- `http://<IP-der-GasWaage>/update`

Die IP-Adresse wird beim Start im seriellen Monitor ausgegeben.

## Hinweise

- `monitor_speed` ist auf `115200` gesetzt.
- Kalibrierdaten werden dauerhaft im EEPROM gespeichert.
- MQTT-Broker, Port und optional User/Pass werden in den Credentials definiert.

## Troubleshooting

### Geraet ist online, aber es kommen keine MQTT-Werte

1. Serielle Ausgabe pruefen (`115200`) und auf Reconnect-Aktivitaet achten.
2. Topic-Subscription am Broker pruefen:

```bash
mosquitto_sub -h <broker-ip> -p <port> -t gaswaage/status -v
mosquitto_sub -h <broker-ip> -p <port> -t gaswaage/values -v
mosquitto_sub -h <broker-ip> -p <port> -t gaswaage/lwt -v
```

3. Sicherstellen, dass `clientId` im Netzwerk eindeutig ist (kein zweites Geraet mit gleicher ID).
4. In den Credentials Broker/Port/User/Pass gegenpruefen.
5. Testweise `{reset}` an `gaswaage/in` schicken und Reconnect/Publishes beobachten.

### Es wird nichts auf `gaswaage/lwt` publiziert

1. Auf LWT-Topic abonnieren:

```bash
mosquitto_sub -h <broker-ip> -p <port> -t gaswaage/lwt -v
```

2. Geraet neu starten.
3. Erwartung nach erfolgreichem Connect: retained Meldung mit `status: online`.
4. Strom oder WLAN kurz unterbrechen (unsauberer Disconnect).
5. Erwartung: Broker setzt LWT retained auf `status: offline`.
6. Nach Wiederverbindung: retained Meldung wechselt wieder auf `status: online`.

Hinweis: Falls nur bei einem bestimmten Broker keine LWT-Meldung erscheint, Broker-Logs auf ACL/Retain-Policy pruefen.

## Lizenz

Aktuell keine explizite Lizenzdatei im Projektroot vorhanden. Bei Bedarf `LICENSE` ergaenzen.

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
