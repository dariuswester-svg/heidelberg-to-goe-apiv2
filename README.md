Das Projekt ist inzwischen an einem guten Punkt: Die Heidelberg Energy Control wird über Modbus RTU ausgelesen, das Gateway ist im Netzwerk erreichbar und wird von der Home-Assistant-Integration als Go-E-kompatibles Gerät erkannt. Der folgende Text ist als ausführliche README.md für dein GitHub-Repository formuliert.

Heidelberg Energy Control zu Go-E API v2 Gateway
Open-Source-Gateway für ESP8266, RS485 und Home Assistant

Dieses Projekt stellt eine Heidelberg Energy Control Wallbox über eine HTTP-Schnittstelle bereit, die sich an der Go-E Charger HTTP API v2 orientiert.

Das Gateway liest die Wallbox über Modbus RTU / RS485 aus und stellt die verfügbaren Werte über WLAN bereit. Dadurch kann die Heidelberg-Wallbox mit der Home-Assistant-Custom-Integration ha-goecharger-api2 verwendet werden.

🎯 Projektziel: Eine offene, lokal betriebene und kostengünstige Alternative für die Einbindung der Heidelberg Energy Control in Home Assistant.

Das Gateway läuft vollständig lokal:

keine Cloud
kein externer Server<img width="2048" height="1536" alt="nodemcu" src="https://github.com/user-attachments/assets/b779a290-a2c6-4a0e-ab3e-e9ba638e76aa" />
<img width="1536" height="2048" alt="WhatsApp Image 2026-11-14 at 07 36 46" src="https://github.com/user-attachments/assets/d3c3252a-81f9-4747-a1b4-4a33d6574980" />
<img width="2048" height="1536" alt="WhatsApp Image 2026-11-14 at 07 24 58" src="https://github.com/user-attachments/assets/c6b7245b-ebb4-4aa1-a67e-3af24bf0fe6a" />
<img width="2048" height="1536" alt="WhatsApp Image 2026-11-14 at 07 24 58 (1)" src="https://github.com/user-attachments/assets/bb84a9ca-8d1f-44f0-a553-b1e64a83ca07" />

keine Registrierung
keine laufenden Kosten
keine Änderung an Home Assistant erforderlich
keine Änderung an der verwendeten Go-E-Custom-Integration erforderlich

Hinweis: Dieses Projekt ist weder offiziell von Heidelberg beziehungsweise Amperfied noch von Go-E freigegeben oder unterstützt. Die Bezeichnungen werden ausschließlich zur Beschreibung der technischen Kompatibilität verwendet.

Funktionsumfang
Unterstützte Funktionen

Das Gateway kann unter anderem folgende Werte aus der Heidelberg Energy Control auslesen:

Ladezustand
Strom L1
Strom L2
Strom L3
Spannung L1
Spannung L2
Spannung L3
Gesamtleistung
Leiterplattentemperatur
Gesamtenergie
konfiguriertes Hardware-Stromlimit
Modbus-Kommunikationsstatus
Modbus-Fehlerzähler
Alter des letzten gültigen Messwertes

Zusätzlich unterstützt das Gateway:

Sollstromvorgabe von 6 bis 16 A
Pausieren durch Sollstrom 0 A
Freigabe über Remote Lock
Go-E-kompatible Parameter amp und frc
RAM-basierte Sitzungsenergie
dynamisches Register-Mapping
konfigurierbare Registeradressen
konfigurierbare Skalierungsfaktoren
konfigurierbare Offsets
Aktivieren und Deaktivieren einzelner Register
signed und unsigned Registerwerte
Weboberfläche zur Konfiguration
Modbus-Diagnose je Register
Access-Point-Fallback
persistente Konfiguration in LittleFS
automatische Wiederverbindung mit dem WLAN
Home-Assistant-Anbindung über HTTP API v2
Unterstützte Hardware
ESP8266-Boards

Getestet beziehungsweise vorgesehen sind:

NodeMCU 1.0 / ESP-12E
NodeMCU v2
NodeMCU v3
Wemos D1 Mini
LOLIN D1 Mini
RobotDyn WiFi D1 Mini mit ESP8266 und 4 MB Flash

Für den Wemos beziehungsweise D1 Mini ist keine Änderung am Quellcode erforderlich. In der Arduino IDE muss lediglich das passende Boardprofil ausgewählt werden.

RS485-Transceiver

Vorausgesetzt wird ein:

MAX3485
Betrieb mit 3,3 V
automatische Senderichtungsumschaltung

Das verwendete Modul benötigt daher nur:

VCC
GND
TXD
RXD
RS485 A
RS485 B

Ein separater DE/RE-Steuerpin wird nicht verwendet.

⚠️ Ein herkömmliches MAX3485-Modul ohne automatische Senderichtung ist nicht ohne Anpassung geeignet.

Verdrahtung
NodeMCU zu MAX3485

Die erfolgreich getestete Verbindung lautet:

NodeMCU	ESP8266-GPIO	MAX3485D1	GPIO5	RXD
D2	GPIO4	TXD
3V3	3,3 V	VCC
GND	GND	GND

Wichtig ist die Signalrichtung:

ESP8266 Software-TX, GPIO5 / D1 → MAX3485 RXD
ESP8266 Software-RX, GPIO4 / D2 ← MAX3485 TXD

Die am NodeMCU mit TX und RX beschrifteten Hardware-UART-Pins werden nicht für Modbus verwendet. Die Hardware-UART bleibt für den seriellen Monitor reserviert.

MAX3485 zur Wallbox
MAX3485 A / D+ → Heidelberg RS485 A
MAX3485 B / D− → Heidelberg RS485 B

Falls keine Kommunikation zustande kommt, sollte die A/B-Zuordnung kontrolliert werden. Die Beschriftung günstiger RS485-Module ist nicht immer einheitlich.

DIP-Schalter der Heidelberg Energy Control

Für eine einzelne Wallbox mit Bus-ID 1 wurde folgende Einstellung verwendet:

S4: Modbus-Bus-ID 1
S4/1 = OFF
S4/2 = OFF
S4/3 = OFF
S4/4 = ON

Die in der Weboberfläche eingetragene Slave-ID 1 ist die Adresse der Heidelberg-Wallbox.

Der ESP8266 arbeitet als Modbus-Master und besitzt in dieser Konfiguration keine eigene Slave-ID.

S5: externe Steuerung beziehungsweise Follower-Betrieb

Für den externen Betrieb muss insbesondere gelten:

S5/4 = OFF

Die übrigen Einstellungen müssen zur vorhandenen Installation und zum Handbuch der Wallbox passen.

S6: RS485-Abschluss

Wenn die Wallbox das Ende des RS485-Busses bildet:

S6/2 = ON

Bei einer einfachen Verbindung aus einem Gateway und einer Wallbox befindet sich die Wallbox normalerweise an einem Busende.

⚠️ Arbeiten innerhalb der Wallbox dürfen nur im spannungsfreien Zustand und durch entsprechend qualifizierte Personen durchgeführt werden.

Arduino-IDE-Umgebung
ESP8266-Boardpaket

Empfohlen:

ESP8266 Arduino Core 3.1.2

Zusätzliche Boardverwalter-URL:

https://arduino.esp8266.com/stable/package_esp8266com_index.json

Benötigte Bibliotheken
Bibliothek	VersionArduinoJson	7.4.3
EspSoftwareSerial	8.1.0
ModbusMaster	2.0.1

Folgende Bibliotheken sind bereits im ESP8266-Boardpaket enthalten:

ESP8266WiFi
ESP8266WebServer
LittleFS
Empfohlene Boardeinstellungen

Für NodeMCU:

Board: NodeMCU 1.0 (ESP-12E Module)
CPU Frequency: 80 MHz
Flash Size: 4MB, Dateisystem mindestens 1MB
Debug Port: Disabled
Debug Level: None
IwIP Variant: v2 Lower Memory
Erase Flash: Only Sketch
Upload Speed: 115200 oder 460800

Für Wemos beziehungsweise D1 Mini:

Board: LOLIN(WEMOS) D1 R2 & mini
CPU Frequency: 80 MHz
Flash Size: 4MB, Dateisystem mindestens 1MB

Beim ersten Flashen oder bei einer beschädigten Konfiguration kann einmalig verwendet werden:

Erase Flash: All Flash Contents

Anschließend sollte wieder eingestellt werden:

Erase Flash: Only Sketch

Projektdateien

Das Arduino-Projekt besteht aus exakt drei Dateien.

Heidelberg_GoE_Gateway.ino

Die Hauptdatei enthält:

setup()
loop()
globale Objektinstanzen
Initialisierung der Konfiguration
Initialisierung des Gateways
kooperativen Scheduler
HTTP-Server-Aufrufe
WLAN-Wartung
Modbus-Wartung
Neustartlogik
Watchdog-freundlichen Programmablauf

Der Abstand zwischen einzelnen Modbus-Operationen wurde für die Heidelberg Energy Control auf 200 ms eingestellt.

Diese Verzögerung hat sich als notwendig erwiesen, da kürzere Abstände zu wechselnden Modbus-Timeouts geführt haben.

ConfigManager.h

Diese Datei verwaltet die komplette Konfiguration:

Standardwerte
WLAN-Einstellungen
Fallback-Access-Point
Modbus-Slave-ID
Baudrate
RX- und TX-Pins
Polling-Intervall
Stromgrenzen
Register-Mapping
Register-Skalierungen
Register-Offsets
signed und unsigned Register
LittleFS
/config.json
Validierung
Wiederherstellung der Werkseinstellungen
sicheres Speichern über eine temporäre Datei

Eine leere Passworteingabe in der Weboberfläche bedeutet:

Vorhandenes Passwort beibehalten

Die gespeicherten Passwörter werden nicht im Klartext auf der Webseite angezeigt.

GatewayEngine.h

Diese Datei enthält die eigentliche Gateway-Logik:

WLAN-Verbindungsmanagement
Fallback-Access-Point
Webserver auf Port 8080
Konfigurationsoberfläche
Go-E-kompatible API-Endpunkte
SoftwareSerial mit 8E1
ModbusMaster
schrittweises Modbus-Polling
feste Schreibwarteschlange
RAM-Cache
Fehlerbehandlung
Sitzungsenergie
JSON-Erzeugung
API-Filter
Modbus-Diagnose je Register

Die Diagnose zeigt für jedes abgefragte Register:

Registeradresse
Bezeichnung
Funktionscode
Aktivstatus
Anzahl erfolgreicher Abfragen
Anzahl fehlerhafter Abfragen
letzten Fehlercode

Zusätzlich steht die Diagnose unter folgendem Pfad zur Verfügung:

http://GATEWAY-IP:8080/diagnostics.json

Standardkonfiguration
WLAN
WLAN-SSID: Heidelberg-Wallbox
WLAN-Passwort: 12345678
Fallback-AP: Heidelberg-Gateway
Fallback-AP-Passwort: 12345678

Falls das konfigurierte WLAN nicht erreichbar ist, startet das Gateway den Fallback-Access-Point.

Die Konfigurationsoberfläche ist dann erreichbar unter:

http://192.168.4.1:8080/

Modbus
Slave-ID: 1
Baudrate: 19200
Datenformat: 8E1
RX: GPIO4 / D2
TX: GPIO5 / D1
Polling-Intervall: 2000 ms
Abstand zwischen Einzeloperationen: 200 ms
Minimalstrom: 6 A
Maximalstrom: 16 A

HTTP-Endpunkte
Weboberfläche
GET /

Beispiel:

http://192.168.0.22:8080/

Status
GET /api/status

Beispiel:

http://192.168.0.22:8080/api/status

Gefilterter Status
GET /api/status?filter=car,amp,nrg,tma

Unbekannte Filterfelder werden ignoriert. Lange Filterlisten bis 1.024 Zeichen werden unterstützt, damit auch umfangreiche Home-Assistant-Abfragen verarbeitet werden können.

Sollstrom setzen
GET /api/set?amp=10

Zulässige Werte:

0 A
6 bis 16 A

Dabei gilt:

amp=0 pausiert das Laden
amp=6 bis amp=16 setzen den gewünschten Ladestrom
Force-State
GET /api/set?frc=1

Bedeutung:

frc=1: Laden stoppen beziehungsweise pausieren
frc=0: Normalbetrieb
frc=2: Laden freigeben

Beim erneuten Freigeben wird der zuletzt gültige Ladestrom wiederhergestellt.

Interner Status
GET /status.json

Dieser Endpunkt enthält zusätzliche Gateway-Diagnosedaten, die nicht Teil der Go-E-Schnittstelle sind.

Modbus-Diagnose
GET /diagnostics.json

Home Assistant

Verwendet werden kann die Custom-Integration:

marq24/ha-goecharger-api2

Konfiguration:

Host: IP-Adresse des ESP8266
Port: 8080
Passwort: leer lassen
Aktualisierungsintervall: 15 Sekunden

Beispiel:

Host: 192.168.0.22
Port: 8080

Die Weboberfläche und die API verwenden denselben Port. Die Trennung erfolgt über die URL-Pfade:

/                    Konfigurationsoberfläche
/api/status          API-Status
/api/set             Steuerbefehle
/status.json         interner Gatewaystatus
/diagnostics.json    Modbus-Diagnose

Die Integration kann zusätzliche Go-E-Werte anfordern, die eine Heidelberg Energy Control technisch nicht bereitstellt. Fehlende Werte können in Home Assistant als nicht verfügbare Zusatzentitäten erscheinen.

Die grundlegenden Werte funktionieren unabhängig davon:

Fahrzeugstatus
Ladefreigabe
Stromvorgabe
Hardware-Stromlimit
Phasenspannungen
Phasenströme
Gesamtleistung
Temperatur
Energie
Kommunikationsstatus

Nicht benötigte oder nicht unterstützte Entitäten können in Home Assistant deaktiviert werden.

Unterstützte Modbus-Register
Funktion	Register	FC	SkalierungLadezustand	5	04	1
Strom L1	6	04	0,1 A
Strom L2	7	04	0,1 A
Strom L3	8	04	0,1 A
Temperatur	9	04	0,1 °C, signed
Spannung L1	10	04	1 V
Spannung L2	11	04	1 V
Spannung L3	12	04	1 V
Gesamtleistung	14	04	1 W/VA
Gesamtenergie High	17	04	High-Word
Gesamtenergie Low	18	04	Low-Word
Hardware-Stromlimit	100	04	1 A
Standby Control	258	06	Schreibwert 4
Remote Lock	259	06	1 = Freigabe
Zielstrom	261	06	0,1 A

Die Gesamtenergie wird aus High- und Low-Word zusammengesetzt:

Gesamtenergie = (High-Word << 16) | Low-Word

Bekannte Einschränkungen
Keine vollständige Go-E-Hardwareemulation

Das Gateway bildet nur Werte ab, die sich sinnvoll aus der Heidelberg Energy Control ableiten lassen.

Nicht verfügbar sind unter anderem:

RFID-Verwaltung
Go-E-Zeitpläne
Go-E-Lastmanagement
PV-Überschusslogik des Go-E Chargers
WLAN- und Cloudfunktionen eines echten Go-E Chargers
Phasenumschaltung
Go-E-spezifische Diagnosewerte
OCPP-Funktionen
Go-E-Zähler- und Kartendaten
echte Go-E-Firmwarefunktionen

Das Gateway ergänzt keine erfundenen Messwerte.

Sitzungsenergie

Die Heidelberg stellt keinen separaten, eindeutig nutzbaren Go-E-Sitzungszähler bereit.

Das Gateway merkt sich deshalb beim Beginn eines Ladevorgangs den Gesamtenergiezähler im RAM und berechnet:

Sitzungsenergie = aktuelle Gesamtenergie − Gesamtenergie bei Ladebeginn

Nach einem Neustart des ESP8266 beginnt die Sitzungsenergie wieder bei null.

Modbus-Timeout

ModbusMaster 2.0.1 besitzt keinen öffentlichen Setter für den internen Transaktions-Timeout.

Der in der Konfiguration vorhandene Timeoutwert wird gespeichert und validiert, kann aber ohne Änderung der Bibliothek nicht direkt auf den internen Timeout angewendet werden.

Aktualisierungsgeschwindigkeit

Die Heidelberg Energy Control benötigt ausreichende Pausen zwischen einzelnen Modbus-Anfragen.

Ein Abstand von:

200 ms

hat sich als stabil erwiesen.

Mit zwölf Registern benötigt ein vollständiger Messzyklus daher mehrere Sekunden. Das ist für Home Assistant und typische Ladeanwendungen ausreichend.

Fehlerdiagnose
Fehlercode 226
226 / 0xE2 = Antwort-Timeout

Mögliche Ursachen:

RX und TX zwischen ESP8266 und MAX3485 vertauscht
A und B vertauscht
falsche Bus-ID
falsche Baudrate
falsches Datenformat
Follower-Modus nicht aktiviert
zu kurze Abstände zwischen Modbus-Abfragen
fehlende Versorgung des MAX3485
ungeeignetes MAX3485-Modul ohne automatische Senderichtung
LED-Anzeigen des MAX3485

Abhängig vom verwendeten Modul:

Power-LED leuchtet dauerhaft
TX-LED blinkt bei Anfragen
RX-LED blinkt bei Antworten der Wallbox

Bei stabiler Kommunikation sollten TX und RX regelmäßig kurz aufleuchten.

Sicherheitshinweise

⚠️ Die Heidelberg Energy Control ist fest mit der elektrischen Anlage verbunden.

Arbeiten an beziehungsweise innerhalb der Wallbox dürfen nur:

im spannungsfreien Zustand
nach Feststellung der Spannungsfreiheit
durch eine Elektrofachkraft oder unter deren Verantwortung
entsprechend der Herstellerdokumentation

durchgeführt werden.

Das ESP8266-Gateway und der MAX3485 müssen berührungssicher und für den Einbauort geeignet untergebracht werden.

Die lokale Konfigurationsoberfläche besitzt derzeit keine Benutzeranmeldung. Das Gateway sollte deshalb nur in einem vertrauenswürdigen lokalen Netzwerk verwendet werden.

Projektstatus

✅ WLAN-Verbindung
✅ Fallback-Access-Point
✅ Webkonfiguration
✅ LittleFS-Konfiguration
✅ Modbus RTU mit 8E1
✅ Heidelberg-Registerabfrage
✅ stabile Kommunikation mit 200 ms Abstand
✅ Go-E-API-v2-orientierter Status
✅ Sollstromvorgabe
✅ Pause und Freigabe
✅ Modbus-Diagnose
✅ Home-Assistant-Erkennung
✅ NodeMCU und D1-Mini-Unterstützung

Weitere Realtests während aktiver Fahrzeugladung sind ausdrücklich erwünscht.

Mitwirken

Beiträge, Tests und Verbesserungsvorschläge sind willkommen. Besonders hilfreich sind:

Tests mit unterschiedlichen Firmwareständen der Heidelberg Energy Control
Tests mit NodeMCU und D1 Mini
längere Stabilitätstests
Rückmeldungen zu verschiedenen MAX3485-Modulen
Tests während aktiver Ladung
Home-Assistant-Dashboards
Gehäuse- und Platinenentwürfe
Fehlerberichte mit /diagnostics.json
Pull Requests mit klar dokumentierten Änderungen

Bitte bei Fehlerberichten möglichst angeben:

ESP8266-Board
ESP8266-Core-Version
MAX3485-Modultyp
Wallbox-Firmware
Modbus-Bus-ID
Baudrate
status.json
diagnostics.json
Home-Assistant-Version
Version der verwendeten Integration

Lizenz

Für die Veröffentlichung bietet sich beispielsweise die MIT-Lizenz an. Sie erlaubt anderen Personen, den Code zu verwenden, zu verändern und weiterzugeben, verlangt aber die Beibehaltung des Lizenz- und Urheberrechtshinweises.

Lege dafür zusätzlich eine Datei mit dem Namen:

LICENSE

im Repository an.

Viel Erfolg beim Nachbau! ⚡🔌🏠

Wenn dieses Projekt geholfen hat, sind ein GitHub-Stern, ein Fehlerbericht oder ein dokumentierter Verbesserungsvorschlag willkommen.

Änderungen am Text: Ich habe den Projektstand als direkt verwendbare GitHub-README strukturiert, die erfolgreich getestete Verdrahtung und den stabilen 200-ms-Modbus-Abstand berücksichtigt, die drei Dateien einzeln beschrieben und die Grenzen gegenüber einem echten Go-E Charger klar abgegrenzt.





<img width="1172" height="147" alt="2026-11-14 10_42_36-WDADesktopService" src="https://github.com/user-attachments/assets/5dd92229-07ad-4bf5-8cb8-22de964386fa" />
<img width="1167" height="926" alt="2026-11-14 10_42_31-WDADesktopService" src="https://github.com/user-attachments/assets/4e1953da-6492-4380-a75d-903af6452913" />
<img width="1170" height="424" alt="2026-11-14 10_42_20-Greenshot" src="https://github.com/user-attachments/assets/a47806c0-3026-4cf1-adab-01cad65369c5" />
<img width="1171" height="939" alt="2026-11-14 10_42_05-WDADesktopService" src="https://github.com/user-attachments/assets/0e511680-b263-483a-a79b-b2039517f640" />
<img width="810" height="164" alt="2026-11-14 10_41_40-Greenshot" src="https://github.com/user-attachments/assets/770cdc51-53d9-4f99-a44f-63d2c9057790" />
<img width="956" height="215" alt="2026-11-14 10_43_25-Greenshot" src="https://github.com/user-attachments/assets/fffd8e2a-8e95-4d04-991e-a73464b540c6" />
<img width="1536" height="2048" alt="wemosd1mini" src="https://github.com/user-attachments/assets/60f603d8-9728-4dd5-9459-60e8b1a13cbb" />
<img width="2048" height="1536" alt="rs485tottl" src="https://github.com/user-attachments/assets/9fcdeb89-7622-4bc6-8871-8e349408464f" />
<img width="2048" height="1536" alt="nodemcu" src="https://github.com/user-attachments/assets/0f994a37-bc68-403f-b872-93ee8fb1b836" />

