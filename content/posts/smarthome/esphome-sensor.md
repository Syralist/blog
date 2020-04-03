---
title: "Umweltsensor mit ESPHome in Home Assistant"
date: 2020-04-03T12:53:16+02:00
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Home Assistant
- ESPHome
- ESP8266
---

# Umweltsensor mit ESPHome in Home Assistant
In diesem Beitrag zeige ich die Schritte, um meinen Umweltsensor mit Button nachzubauen. Der Sensor misst Temperatur, Luftfeuchtigkeit und Luftdruck und hat einen Button um beliebige Aktionen auszulösen.

![Fertige Box](/images/2020-04-03-esphome-hardware-01.jpg)

## Vorbereitung
### Was ist ESPHome?
Laut der Selbstbeschreibung auf [esphome.io][1] ist ESPHome ein System, um ESP8266 bzw. ESP32 Mikrocontroller mit Hilfe von einfachen Konfigurationsdateien zu programmieren.

ESPHome ist in Python geschrieben und lässt sich auch ohne Home Assistant einfach über `pip install esphome` oder mit `docker pull esphome/esphome` installieren. Ich benutze ESPHome als Home Assistant Addon, da bekommt man noch eine Weboberfläche zur Administration dazu.

### Benötigte Hardware
* ESP8266 Mikrocontroller - z.B. Wemos D1 mini
* BME280 Sensor - mit I²C Breakoutboard
* Drucktaster

![Fertige Box offen](/images/2020-04-03-esphome-hardware-02.jpg)

Das Gehäuse ist aus 3mm MDF gelasert. Ein Generator für solche Gehäuse ist zum Beispiel [boxes.py][2].

### Sensor und Button anschließen

Der BME280 wird per I²C angeschlossen, dafür brauchen wir die folgenden Pins:
* 5V &rarr; VIN
* G&nbsp;&nbsp; &rarr; GND
* D4 &rarr; SDA
* D5 &rarr; SCL

Den Pin für den Button kann man frei wählen, allerdings gibt es einige Pins, die spezielle Funktionen beim Booten auslösen. Ich habe hier Pin D3 verwendet, der zweite Pin des Buttons wird mit GND verbunden.

![Platine von unten](/images/2020-04-03-esphome-hardware-04.jpg)

Ich habe das ganze mit Sockeln auf einer Lochrasterplatine aufgebaut, um die Bauteile leicht wieder auseinanderbauen zu können.

![Platine von oben](/images/2020-04-03-esphome-hardware-03.jpg)

## ESPHome Projekt in Home Assistant

### Projekt anlegen
Auf der Weboberfläche klickt man auf den freundlichen roten Button mit dem Plus, um den Wizard für neue Projekte aufzurufen.

![ESPHome Weboberfläche](/images/2020-04-03-esphome-01.png)

Als erstes vergibt man einen Namen.

![ESPHome Wizard Schritt 1](/images/2020-04-03-esphome-02.png)

Zweiter Schritt ist die Auswahl des Mikrocontrollers. Ich benutze für die meisten Projekte den Wemos D1 mini.

![ESPHome Wizard Schritt 2](/images/2020-04-03-esphome-03.png)

Als letztes folgt die Angabe der WLan-Daten und des Passworts für das Over-the-air Update. Ich benutze hier die von Home Assistant bekannte `!secret` Notation. Details dazu weiter unten.

![ESPHome Wizard Schritt 3](/images/2020-04-03-esphome-04.png)

Nun ist der Wizard abgeschlossen und zeigt uns die nächsten Schritte, um den Mikrocontroller zu programmieren.

![ESPHome Wizard Schritt 4](/images/2020-04-03-esphome-05.png)

Nachdem man den Wizard mit **Submit** geschlossen hat, wird man noch auf die Schnittstellenauswahl in der oberen Ecke hingewiesen.

![ESPHome Wizard Schritt 5](/images/2020-04-03-esphome-06.png)

### Dateiorganisation und !secrets

In Home Assistant liegen die Projektdateien im Verzeichnis `/config/esphome`. Das Beispiel oben hat eine `umweltsensor.yaml` erzeugt.

```yaml
esphome:
  name: umweltsensor
  platform: ESP8266
  board: d1_mini
wifi:
  ssid: "!secret wifi-ssid"
  password: "!secret wifi-password"
  # Enable fallback hotspot (captive portal) in case wifi connection fails
  ap:
    ssid: "Umweltsensor Fallback Hotspot"
    password: "lg2kCzecpat7"
captive_portal:
# Enable logging
logger:
# Enable Home Assistant API
api:
  password: "!secret esphome-password"
ota:
  password: "!secret esphome-password"
```

Im gleichen Verzeichnis habe ich eine `secrets.yaml` angelegt. Die Einträge darin können in anderen `.yaml` Dateien mit `!secret` importiert werden. Das hat den Vorteil, dass man die Projektdatei mit anderen teilen kann, so wie ich hier, ohne dass man seine Zugangsdaten weitergibt.

![ESPHome secrets.yaml](/images/2020-04-03-esphome-07.png)

### Das erste Mal Flashen

Zum ersten Flashen muss der Mikrocontroller per USB mit dem Rechner verdunden werden, auf dem ESPHome läuft. In meinem Fall ist das der Raspberry Pi auf dem Home Assistant installiert ist.

Meistens wird der Port des Mikrocontrollers nicht sofort angezeigt, wenn man das USB Kabel anschließt. Das hängt mit dem Docker System zusammen auf dem Home Assistant basiert. Der Workaround ist das Addon neuzustarten, nachdem der Mikrocontroller angeschlossen ist.

Wenn nichts anderes angeschlossen ist, wird wahrscheinlich `/dev/ttyUSB0` angezeigt, in meinem Fall ist der Mikrocontroller an `/dev/ttyUSB1`.

![ESPHome Port Auswahl](/images/2020-04-03-esphome-08.png)

Damit die Zugangsdaten korrekt importiert werden, müssen wir noch die erzeugte `.yaml` Datei bearbeiten und die Anführungszeichen bei allen Einträgen mit `!secret` entfernen. Dazu klickt man auf **EDIT** unter dem entsprechenden Eintrag.

![ESPHome Edit Button](/images/2020-04-03-esphome-09.png)

Als letztes klickt man auf **UPLOAD** um das Flashen zu starten.

![ESPHome Upload](/images/2020-04-03-esphome-10.png)

Wenn alles korrekt funktioniert hat, sollte nun aus der `.yaml` Datei der Arduino-Code generiert werden. Dabei werden im Hintergrund alle benötigten Bibliotheken heruntergeladen, der Code kompiliert und der Upload gestartet.

![ESPHome Upload](/images/2020-04-03-esphome-11.png)

In der folgenden Logging-Ausgabe kann man kontrollieren, ob man die WLAN Daten korrekt eingeben hat.

![ESPHome Upload](/images/2020-04-03-esphome-12.png)

Ab jetzt kann man den Mikrocontroller vom Raspberry Pi trennen und übers WLAN programmieren. Dafür stellt man die Schnittstelle oben auf Over-the-Air um.

### Sensor und Button konfigurieren

Damit der Sensor angesprochen werden kann wird als erstes die I²C Komponente geladen. Die Option `scan: True` sorgt dafür, dass bei jedem Boot der Bus nach Geräten gescannt wird.

```yaml
i2c:
  sda: 4
  scl: 5
  scan: True
```

Da der BME280 drei Größen misst, müssen entsprechend die drei Optionen `temperature`, `pressure` und `humidity` konfiguriert werden.

Unter `filters:` ist beim Temperatursensor ein statischer Offset von -7 Grad eingestellt, den ich im Vergleich mit einem zweiten Sensor ermittelt habe. Ebenso ist bei der Luftfeuchtigkeit ein statischer Offset von +10 Prozent eingestellt.

```yaml
sensor:
  - platform: bme280
    temperature:
      name: "Thomas Zimmer Temperatur"
      oversampling: 16x
      filters:
        - offset: -7.0
    pressure:
      name: "Thomas Zimmer Luftdruck"
    humidity:
      name: "Thomas Zimmer Feuchtigkeit"
      filters:
        - offset: +10.0
    address: 0x76
    update_interval: 60s
```

Der Button wird als Binärsensor konfiguriert. Da der Button mit GND verbunden ist, muss hier der interne Pullup-Widerstand aktiviert werden und mit `inverted: True` der Zustand invertiert werden. Damit liefert der Sensor **on**, wenn der Button gedrückt wird.

```yaml
binary_sensor:
  - platform: gpio
    pin: 
      number: D3
      mode: INPUT_PULLUP
      inverted: True
    name: "Thomas Zimmer Button"
    filters:
      - delayed_on: 10ms
```

### Mit Home Assistant verbinden

Nachdem das vollständige Programm auf den Mikrocontroller geladen ist, müssen die Sensoren noch in Home Assistant einbunden werden. Die Einrichtung erfolgt über die Integrationsseite in den Home Assistant Einstellungen.

![ESPHome Integration](/images/2020-04-03-esphome-13.png)

Als Hostadresse gibt man den Namen des Projekts ein, in diesem Fall `umweltsensor.local`.

![ESPHome Integration](/images/2020-04-03-esphome-14.png)

Nach der API Passwort Abfrage werden automatisch die Entities für die konfigurierten Sensoren zu Home Assitant hinzugefügt.

![ESPHome Integration](/images/2020-04-03-esphome-15.png)


[1]: https://esphome.io/ "ESPHome"
[2]: https://www.festi.info/boxes.py/ "boxes.py"
