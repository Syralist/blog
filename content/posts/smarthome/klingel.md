---
title: "Einbindung meiner Türklingel in Home Assistant"
date: 2020-10-11T13:33:27+02:00
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- HomeAssistant
- ESPHome
- MQTT
---

## Normale Klingel kann jeder

Bei einer klassischen Türklingel wird durch das Drücken des Klingelknopfs ein Stromkreis geschlossen
und dadurch eine Glocke oder ähnliches betätigt. Für die Einbindung solcher Klingeln gibt es reichlich
Anleitungen und mehr oder weniger fertige Lösungen zur Einbindung in Smarthome Systeme.

Bei Mehrfamilienhäusern gibt es allerdings häufig Klingelanlagen, die etwas komplizierter aufgebaut sind
mit Gegensprechanlage, Türsummer, Lichtsteuerung, etc. 

## TCS Klingelanlagen
Bei mir im Haus ist eine solche Anlage von TCS eingebaut.
Hier hängen alle Wohnungseinheiten parallel an einem proprietären Bus und reagieren auf direkt adressierte Nachrichten.

### Die Klingelanlage verstehen

Der erste Schritt ist, die elektrische Seite zu verstehen. Wenn man die Wohnungseinheit der Klingel von der Wand
nimmt, findet man nur zwei Drähte. Mit dem Multimeter misst man hier 24V, die sich etwas reduzieren, wenn die Klingel 
betätigt wird.
![Verkabelung hinter der Wohnungseinheit](/images/2020-10-11-klingel-02.jpg)

Aus den wenigen Quellen im Internet zu dem TCS System erfährt man, dass die 24V für ein bis zwei Millisekunden auf 0V
gezogen werden, um damit Nullen und Einsen eines binären Protokolls zu codieren. Bei [Aaron Christophel][1] gibt
es ein Video, in dem er mit einem Oszilloskop das Protkoll reverse-engineered. 

Die Kurzversion ist, dass jedes angeschlossene Gerät eine eindeutige Adresse hat und die verschiedenen Aktionen
wie Klingeln, Türsummer oder Licht einschalten als Nachrichten auf den Bus geschickt werden. Die Nachrichten 
sind entweder vier oder acht Byte lang und werden von einem Mikrocontroller in der Wohnungseinheit ausgewertet.

### Den Bus mithören

Da es sich bei dem Bus um ein Spannungssignal handelt, können wir uns einfach parallel mit an den Bus hängen
und die Nachrichten so mithören.
Um den Bus mithören zu können, müssen die 24V auf 3.3V reduziert werden, die ein ESP8266 verarbeiten kann.
In diesem Fall reicht dafür ein einfacher Spannungsteiler aus 1MΩ und 147kΩ.
![Spannungsteiler am ESP8266](/images/2020-10-11-klingel-06.png)
![Spannungsteiler am ESP8266](/images/2020-10-11-klingel-03.jpg)

In meiner ersten Variante hatte ich die Spannungshöhe über den analogen Eingang eingelesen und direkt im loop() ausgewertet.
Dabei war das Problem, dass der Jitter zu groß war, also die Länge der Impulse nicht zuverlässig erkannt wurde. Dadurch
wurde öfter mal das Klingeln nicht erkannt.

In der aktuellen Version lese ich die Spannung als digitalen Eingang ein und benutze den Code von [Aaron][1],
in dem die Flankenwechsel eine Interrupt-Funktion aufrufen in der die Nachrichten dekodiert werden.

Der fertige Code ist wie immer in meinem [Github Repository][2] zu finden.

## Anbindung an Home Assistant

### MQTT Disovery
Die ursprüngliche Idee war, die Anbindung an Home Assistant per ESPHome zu realisieren. Leider funktioniert damit
der Interrupt nicht, also ist die Lösung jetzt eine direkte MQTT Anbindung.

Die Konfiguration in Home Assistant läuft über MQTT Discovery. Dafür wird eine JSON Payload an das Topic
`homeassistant/device_automation/klingel_sensor/config` geschickt. Wichtig ist, das Retain-Flag zu setzen,
sonst vergisst Home Assistant die Konfiguration beim Neustart.

```json
{
    "automation_type": "trigger",
    "payload": "0e22e280",
    "topic": "home/flur/klingel",
    "type": "button_short_press",
    "subtype": "button_1",
    "device": {
        "name": "Klingel Sensor",
        "identifiers": "esp8266-14c4ed",
	"manufacturer": "Syralist",
	"model": "tcs-interrupt"
    }
}
```

Die Payload in dem JSON entspricht der Nachricht, die vom Bus gelesen wird, wenn die eigene Klingel gedrückt wird.
Um die passende Nachricht zu finden, verbindet man sich einfach mit dem MQTT Broker und guckt, was auf dem konfigurierten Topic
gesendet wird, wenn die Klingel gedrückt wird.

Mit dieser Konfiguration hat man im Home Assistant ein Gerät angelegt ohne Entities aber mit einem MQTT Device Trigger,
den man als Auslöser für Automationen verwenden kann.
![Device in Home Assistant](/images/2020-10-11-klingel-04.png)
![Device in Home Assistant](/images/2020-10-11-klingel-05.png)

### Automationen

Ich habe zwei Automationen mit der Klingel verknüpft. Zum einen bekomme ich eine Benachrichtigung auf meinem Handy über die Home Assistant App.
Zum anderen lasse ich eine LED Leiste grün blinken, allerdings nur, wenn mein PC eingeschaltet ist. So bekomme ich das Klingeln auch mit, wenn
ich Kopfhörer benutze.


[1]: https://github.com/atc1441/TCSintercomArduino "Aaron Christophels Github"
[2]: https://github.com/Syralist/tcs-monitor/tree/master/version3/tcs-interrupt "Mein Code auf Github"
