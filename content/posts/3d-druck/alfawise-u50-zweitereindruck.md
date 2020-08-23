---
title: "Alfawise U50 -- Modifizierungen und Zweiter Eindruck"
date: 2020-08-08T15:02:23+02:00
draft: false
toc: true
tocNum: false
tags:
- 3D-Druck
- Prototyping
- Raspberry Pi
- Octoprint
---

## Der Eindruck nach drei Wochen

Seit drei Wochen habe ich nun einen Alfawise U50 zu hause. Den Beitrag zum Unboxing und Aufbau könnt Ihr [hier nachlesen][1].

Bisher kann ich sagen, der Drucker funktioniert einfach nur. Ich drucke weiterhin ausschließlich über Octoprint und auch das funktioniert einwandfrei.
In diesem Beitrag stelle ich vor, welche Modifikationen bzw. Anbauteile ich bisher hinzugefügt habe und was ich zum Umgang mit dem Drucker gelernt habe.

## Anbauten

### X-Achsen Abstandshalter

Die erste Modifikation, die ich gedruckt habe, ist ein Abstandshalter für den X-Achsen Endstop.
Im Auslieferungszustand steht die Düse ausserhalb des Druckbetts, wenn der Endstop betätigt wird. Also habe ich einen Adapter in OpenSCAD konstruiert, der auf die vorhandene
Metallfahne gesteckt werden kann und die Düse 4mm weiter innen stoppt.
![Abstandshalter in OpenSCAD](/images/2020-08-08-alfawise-u50-mods-11.png)
![Abstandshalter angebaut](/images/2020-08-08-alfawise-u50-mods-10.jpg)

### Kamerahalter

Das nächste Anbauteil ist ein Kamerahalter für eine Logitech C270 Webcam ([Thingiverse][2]). Die Kamera habe ich damit oben auf der Querstrebe montiert,
so dass ich das komplette Druckbett im Bild habe.
![Kamerahalter](/images/2020-08-08-alfawise-u50-mods-06.jpg)

### Werkzeughalter

Der Werkzeughalter ([Thingiverse][3]) ist eigentlich für den Ender 3 gedacht, funktioniert aber genauso gut auch für den U50.
Das fertige Teil wird einfach seitlich auf die obere Querstrebe aufgesteckt.
![Werkzeughalter](/images/2020-08-08-alfawise-u50-mods-08.jpg)

### Z-Achsen Stabilisator und Filamentführung

Die Gewindestange der Z-Achse hat im Auslieferungszustand am oberen Ende keine Halterung oder Führung. Ich habe zwar bisher kein Problem damit gehabt,
weil ich noch nicht besonders hoch gedruckt habe, aber auf Thingiverse habe [diese Halterung][4] gefunden. Jetzt wird die Gewindestange oben mit einem
Kugellager gehalten.
![Z-Achsen Stabilisator und obere Filamentführung](/images/2020-08-08-alfawise-u50-mods-03.jpg)

Die Filamentführung besteht aus zwei Teilen. Oben habe ich mich für den Arm ([Thingiverse][5]) entschieden, den man auf dem letzten Bild sieht.
Dabei musste ich das eine Bein mit der Zange kürzen, weil es sich mit der Z-Achsen Halterung in die Quere gekommen ist.

Unten habe ich eine Führung angebaut, die direkt für den U50 konstruiert wurde und ein Kugellager aufnimmt ([Thingiverse][6]).
![untere Filamentführung](/images/2020-08-08-alfawise-u50-mods-04.jpg)

### Raspberry Case

Wie schon erwähnt drucke ich ausschießlich über Octoprint, das auf einem Raspberry Pi 3 läuft. Also habe ich ein Case gedruckt, dass im Rahmen unterhalb des Druckbetts
montiert werden kann ([Thingiverse][7]).
![Raspberry Pi Case](/images/2020-08-08-alfawise-u50-mods-07.jpg)

### Halterung für USB-Hub

Zu Beginn hatte ich die Webcam und den Drucker direkt am Raspberry Pi angeschlossen. Das hat allerdings dazu geführt, dass das Netzteil immer mal wieder Aussetzer 
hatte, besonders beim Starten, wenn die Druckerelektronik noch nicht vom eigenen Netzteil versorgt wird.
Daher habe ich jetzt einen USB-Hub mit eigenem Netzteil im Einsatz, der zwischen Drucker-Elektronik und Druckbett montiert ist. Die Halterung dafür habe ich
in OpenSCAD selbst konstruiert.
![USB-Hub-Halterung in OpenSCAD](/images/2020-08-08-alfawise-u50-mods-12.png)
![USB-Hub-Halterung angebaut](/images/2020-08-08-alfawise-u50-mods-02.jpg)

### Kabelmanagement

Damit die vielen zusätzlichen Kabel nicht wild herumfliegen und sich mit den beweglichen Teilen des Druckers verheddern habe ich eine handvoll 
Kabelführungen für Aluprofile ([Thingiverse][8]) ausgedruckt.
![Kabelmanagement im Druckerrahmen](/images/2020-08-08-alfawise-u50-mods-05.jpg)

Für die Spannungsversorgung steckt alles in einer Dreifachsteckdose. Die drei Kabel für den Drucker, den Raspberry Pi und den USB-Hub sind mit einem Spiralschlauch
zusammengefasst. Der Stecker vom Drucker steckt zusätzlich noch in einer Osram Smart Steckdose, damit ich den Drucker notfalls vom Netz trennen kann. 
Für die Anbindung in mein Smarthome folgt noch ein eigener Beitrag.
![Kabelmanagement und Steckdose](/images/2020-08-08-alfawise-u50-mods-09.jpg)

## Erkenntnisse beim Drucken

Die erste wichtige Erkenntnis ist, vor jedem Druck das Leveling zu kontrollieren. Die Druckteile haften an dem mitgelieferten Druckbett bombenfest, so dass ich
teilweise einige Kraft aufwenden muss, um die abzulösen. Dadurch wird oft das Leveling verändert.

Die zweite Erkenntnis ist, immer mit warmem Druckbett zu leveln. Sonst verändert sich durch die Temperatur wieder das Leveling.


[1]: {{< ref "alfawise-u50-unboxing.md" >}} "Alfawise U50 -- Unboxing und erster Eindruck"
[2]: https://www.thingiverse.com/thing:2426231 "Camera Mount for Logitech C270 on 2020 Extrusion"
[3]: https://www.thingiverse.com/thing:4543682 "Ender 3 Tool Holder for TPU"
[4]: https://www.thingiverse.com/thing:4215305 "Guide Axe Z Alfawise U50"
[5]: https://www.thingiverse.com/thing:3950799 "Filament Guide - Ender 3 Pro"
[6]: https://www.thingiverse.com/thing:4214964 "Guide Fil Alfawise U50"
[7]: https://www.thingiverse.com/thing:2889371 "Ender 3 Raspberry Pi 2/3/4 mount with buck converter mount and toolbox"
[8]: https://www.thingiverse.com/thing:4068606 "Cable clamp/holder for alu profile"
