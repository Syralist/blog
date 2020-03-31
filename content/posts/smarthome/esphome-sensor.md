---
title: "Umweltsensor mit ESPHome in Home Assistant"
date: 2020-03-29T12:53:16+02:00
draft: true
toc: true
tocNum: false
tags:
- Smarthome
- Home Assistant
- ESPhome
- ESP8266
---

# Umweltsensor mit ESPHome in Home Assistant

## Was ist ESPHome?
Laut der Selbstbeschreibung auf [esphome.io][1] ist ESPHome ein System, um ESP8266 bzw. ESP32 Mikrocontroller mit Hilfe von einfachen Konfigurationsdateien zu programmieren.

ESPHome ist in Python geschrieben und lässt sich auch ohne Home Assistant einfach über `pip install esphome` oder mit `docker pull esphome/esphome` installieren. Das Docker Image ist auch als Home Assistant Addon verfügbar.

## ESPHome Projekt mit Home Assistant
### Projekt anlegen

Als Beispiel zeige ich hier die Schritte, um meinen Umweltsensor mit Button nachzubauen.

Auf der Weboberfläche klickt man auf den freundlichen roten Button mit dem Plus, um den Wizard für neue Projekte aufzurufen.

![ESPHome Weboberfläche](/images/2020-03-29-esphome-01.png)

Als erstes vergibt man einen Namen.

![ESPHome Wizard Schritt 1](/images/2020-03-30-esphome-02.png)

Zweiter Schritt ist die Auswahl des Mikrocontrollers. Ich benutze für die meisten Projekte den Wemos D1 mini.

![ESPHome Wizard Schritt 2](/images/2020-03-30-esphome-03.png)

Als letztes folgt die Angabe der WLan-Daten und des Passworts für das Over-the-air Update. Ich benutze hier die von Home Assistant bekannte `!secret` Notation. Details dazu weiter unten.

![ESPHome Wizard Schritt 3](/images/2020-03-30-esphome-04.png)

Nun ist der Wizard abgeschlossen und zeigt uns die nächsten Schritte, um den Mikrocontroller zu programmieren.

![ESPHome Wizard Schritt 4](/images/2020-03-30-esphome-05.png)

Nachdem man den Wizard mit **Submit** geschlossen hat, wird man noch auf die Schnittstellenauswahl in der oberen Ecke hingewiesen.

![ESPHome Wizard Schritt 5](/images/2020-03-30-esphome-06.png)

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

![ESPHome secrets.yaml](/images/2020-03-30-esphome-07.png)

### Das erste Mal Flashen

Zum ersten Flashen muss der Mikrocontroller per USB mit dem Rechner verdunden werden, auf dem ESPHome läuft. In meinem Fall ist das der Raspberry Pi auf dem Home Assistant installiert ist.

Meistens wird der Port des Mikrocontrollers nicht sofort angezeigt, wenn man das USB Kabel anschließt. Das hängt mit dem Docker System zusammen auf dem Home Assistant basiert. Der Workaround ist das Addon neuzustarten, nachdem der Mikrocontroller angeschlossen ist.

Wenn nichts anderes angeschlossen ist, wird wahrscheinlich `/dev/ttyUSB0` angezeigt, in meinem Fall ist der Mikrocontroller an `/dev/ttyUSB1`.

![ESPHome Port Auswahl](/images/2020-03-30-esphome-08.png)

Damit die Zugangsdaten korrekt importiert werden, müssen wir noch die erzeugte `.yaml` Datei bearbeiten und die Anführungszeichen bei allen Einträgen mit `!secret` entfernen. Dazu klickt man auf **EDIT** unter dem entsprechenden Eintrag.

![ESPHome Edit Button](/images/2020-03-30-esphome-09.png)

Als letztes klickt man auf **UPLOAD** um das Flashen zu starten.

![ESPHome Upload](/images/2020-03-30-esphome-10.png)

Wenn alles korrekt funktioniert hat, sollte nun aus der `.yaml` Datei der Arduino-Code generiert werden. Dabei werden im Hintergrund alle benötigten Bibliotheken heruntergeladen, der Code kompiliert und der Upload gestartet.

![ESPHome Upload](/images/2020-03-30-esphome-11.png)

In der folgenden Logging-Ausgabe kann man kontrollieren, ob man die WLAN Daten korrekt eingeben hat.

![ESPHome Upload](/images/2020-03-30-esphome-12.png)

### Mit Home Assistant verbinden



[1]: https://esphome.io/ "ESPHome"
