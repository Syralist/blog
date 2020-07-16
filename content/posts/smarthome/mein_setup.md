---
title: "Mein Smarthome Setup"
date: 2020-03-21T18:45:02.856Z 
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Home Assistant
- Zigbee
- Homematic
- Raspberry Pi
---

Hier möchte ich einen kurzen Überblick über mein Smarthome Setup geben. Auf Details werde ich dann später in weiteren Beiträgen eingehen.

## Zentrale
Als Smarthome-Zentrale habe ich [Home Assistant][1] auf einem Raspberry Pi 3B+ laufen. Ich benutze die native Version mit HassOS und boote direkt von einer SSD Festplatte. Per USB angeschlossen ist hier ein Conbee Stick für die Zigbee-Anbindung. Daneben läuft ein Raspberry Pi 1 mit [RaspberryMatic][3], um meine Homematic Komponenten anzubinden.

Mein Router ist eine FritzBox 7590 unterstützt durch einen FritzRepeater 1750E zur Anbindung von WLAN Komponenten. 

Sowohl die Fritzbox als auch der Haupt-Raspberry sind durch eine unterbrechungsfreie Stromversorgung gepuffert, da hier gerne mal die Sicherung fliegt, wenn ich meine Steckdosenleiste einschalte.

### Hardwareliste
+ **Raspberry Pi 3B+** mit 2,4A Netzteil.
+ Intenso Portable **SSD mit 128GB**. Zusätzliche Stromversorgung mit 2,4A Netzteil und USB Y-Kabel. 
+ **Conbee Stick** von [Dresden Elektronik][2]
+ Unterbrechungsfreie Stromversorgung **APC BX700U-GR**
+ Raspberry Pi 1 mit **HM-MOD-RPI-PCB** von [ELV][4]

### Software
Ich habe mich beim Umzug auf die SSD Festplatte gegen ein Setup mit z.B. Rasbian entschieden, sondern wieder die native Installation benutzt, weil ich so kein weiteres Betriebssystem administrieren muss, sondern alles aus der Home Assistant Oberfläche verwalten kann.
Neben dem Home Assistant laufen allerdings noch einige Addons auf dem Raspberry:

+ **ADB Bridge** für die Android TV Anbindung
+ **APC USB Daemon** für die Anbindung der USV
+ **Duck DNS** für Fernzugriff per SSL aus dem Internet
+ **ESPhome** für selbstgebaute Komponenten
+ **File Editor** zum Bearbeiten der Konfiguration im Browser
+ **Hass.io Google Drive Backup** für die automatische Sicherung des Systems
+ **MariaDB** als Alternative für die SQLite History Datenbank
+ **Mosquitto broker** für MQTT
+ **Node-RED** für die Automatisierung
+ **Samba-Share** für Datenzugriff von anderen Rechnern
+ **TasmoAdmin** für die Verwaltung der Tasmota Komponenten
+ **Terminal & SSH** für Low Level Zugriff auf das System
+ **chrony** als lokaler NTP Server
+ **deCONZ** für die Verwaltung der Zigbee Komponenten

Zusätzlich ist natürlich auch noch [HACS][5] (Home Assistant Community Store) installiert für Custom Components, Lovelace Plugins und Themes.

## Komponenten
Der Vorteil von Home Assistant ist, dass sich Komponenten von den verschiedensten Herstellern und mit den unterschiedlichsten Technologien integrieren lassen. Dementsprechend sind in der Wohnung diverse Sensoren und Aktoren verteilt.

### Sensoren
In fast jedem Raum werden Temperatur, Luftfeuchtigkeit und Luftdruck erfasst. Dazu kommen zum einen die Aqara WSDCGQ11LM Multisensoren über Zigbee zum Einsatz, zum anderen habe ich ESP8266 mit BME280 Sensoren per ESPhome angebunden. 

Fenster und Türen werden mit Aqara und Homematic Tür-/Fensterkontakten überwacht. Als Bewegungsmelder dient ein IKEA Tradfri über Zigbee.

Im Keller überwacht ein Sonoff POW die Leistungsaufnahme der Waschmaschine.

### Licht
Die Deckenbeleuchtung ist bisher noch nicht smart in meiner Wohnung, allerdings bleibt die sowieso meist aus, weil das Licht von Dekoleuchten oder indireker Beleuchtung kommt.

Die Dekoleuchten haben fast alle ein klassisches Leuchtmittel und sind in smarte Zwischensteckdosen eingesteckt. Die meisten sind von Teckin oder Sonoff und umgeflasht auf Tasmota. Eine weitere kommt von Osram und ist über Zigbee angebunden.

Daneben gibt es einige direkt angebundene Leuchtmittel. Eine Birne von Eufy, angebunden über deren Cloud Dienst, ein LED-Strip von Osram über Zigbee und zwei selbstgebaute LED-Leuchten, angebunden über ESPhome.

### Heizung
Unsere Viessmann Heizungsanlage ist über die Cloud angebunden, an den Heizkörpern sind Homematic Thermostate montiert.

### Rollladen
Für die Steuerung der elektrischen Rollladen habe ich Shelly 2.5 Unterputzaktoren eingebaut und lokal per MQTT angebunden.

### Interaktion
Die meisten Komponenten lassen sich per Alexa Spracheingabe steuern, alle Komponenten sind über die Weboberfläche bzw. Companion App auf dem Handy steuerbar. Für einige Aktionen gibt es zusätzlich physikalische Taster. Neben dem Aqara WXKG11LM Zigbee Taster kommt auch hier wieder ein selbst gebauter Button via ESPhome zum Einsatz.

## Automation
Die Automationen sind natürlich das, was das Smarthome erst smart macht. Hier wieder nur eine kurze Übersicht:

Im Wohnzimmer wird das Licht abhängig vom Fernseher an- und ausgeschaltet. Die Heizkörper sind mit den Fenstern verknüpft und regeln zusätzlich nach Tageszeit rauf oder runter.

Wenn ich ins Bett gehe, wird der Rechner und das NAS heruntergefahren, die Rollladen geschlossen und der Heizkörper heruntergeregelt.

Es gibt Benachrichtigungen, wenn die Waschmaschine fertig ist, wenn es an der Tür klingelt und wenn der Müll rausgestellt muss.

---
Das soll als kurzer Überblick über mein Setup genügen. Wie bereits oben geschrieben, folgen bald weitere Artikel mit Details zu einzelnen Themen.

[1]: http://www.home-assistant.io "Home Assistant"
[2]: https://phoscon.de/de/conbee "Conbee Stick"
[3]: https://raspberrymatic.de/de/home/ "RaspberryMatic"
[4]: https://de.elv.com/elv-homematic-komplettbausatz-funkmodul-fuer-raspberry-pi-hm-mod-rpi-pcb-fuer-smart-home-hausautomation-142141?fs=2908134611&c=499 "ELV Shop"
[5]: https://hacs.xyz/ "HACS"