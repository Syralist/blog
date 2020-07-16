---
title: "Alfawise U50 -- Unboxing und erster Eindruck"
date: 2020-07-16T11:08:49.228Z
draft: false
toc: true
tocNum: false
tags:
- 3D-Druck
- Prototyping
- Raspberry Pi
---

# Mein erster 3D-Drucker

Das Thema 3D-Druck habe ich vor einigen Jahren im [Hackerspace][1] kennengelernt. Jetzt habe ich mich endlich dazu durchgerungen, mir einen eigenen Drucker zuzulegen.
Nach einem Video von [SchimmerMedia][2] habe ich mich für den **Alfawise U50** für unter 200€ von [Gearbest][3] entschieden.

# Das Unboxing
## Die Verpackung

Der Drucker kommt gut geschützt in Schaumstoff verpackt aus China an.

![In Schaumstoff verpackt](/images/2020-07-15-alfawise-u50-unboxing-01.jpg)

Unter dem Schaumstoff liegt als erstes das schon zusammengebaute Unterteil des Druckers sowie ein Karton mit Schrauben und Werkzeug.

![Unterteil und Werkzeugkarton](/images/2020-07-15-alfawise-u50-unboxing-02.jpg)
![Unterteil und Werkzeugkarton](/images/2020-07-15-alfawise-u50-unboxing-03.jpg)

Man braucht zum Zusammenbau fünf(!) unterschiedliche Innensechskantschlüssel. Zum Glück sind alle Größen mitgeliefert.
Dazu gibt es noch einen Schaber und einen Seitenschneider um die Druckteile später vom Bett zu lösen und nachzubearbeiten, sowie eine Micro-SD-Karte mit USB-Adapter. Darauf findet man eine alte Version von Cura inklusive einer Konfigurationsdatei, die Aufbauanleitung in digitaler Form und einige fertige 3D Modelle.

![Werkzeug und Schrauben](/images/2020-07-15-alfawise-u50-unboxing-04.jpg)

In der unteren Schaumstoffhälfte findet man die restlichen Anbauteile, die auch bereits teilweise montiert sind.

![Anbauteile](/images/2020-07-15-alfawise-u50-unboxing-05.jpg)

Diese Teile gilt es nun nach der Anleitung zusammenzubauen.

![Alle Einzelteile](/images/2020-07-15-alfawise-u50-unboxing-06.jpg)

## Der Aufbau

Der Text der Anleitung ist in einigermaßen verständliches Englisch übersetzt, allerdings sind die Bilder teilweise etwas klein geraten.

![Die Anleitung](/images/2020-07-15-alfawise-u50-unboxing-07.jpg)

Das erste Teil, das es zu montiert gilt, ist die Kombination aus X-Achsen-Antrieb und Extruder-Antrieb. Das Konstrukt muss an die X-Achsen-Strebe geschraubt werden, wobei die Schraube von hinten in die Bohrung gefummelt werden müssen.

![X-Achsen- und Extruder-Antrieb](/images/2020-07-15-alfawise-u50-unboxing-08.jpg)

Das Hotend wird dann einfach nur auf die Schiene gesteckt und die zweite Z-Achsen-Führung wird auf der anderen Seite angeschraubt.

![Hotend und Z-Achsen-Führung](/images/2020-07-15-alfawise-u50-unboxing-09.jpg)

Der Zahnriemen, der das Hotend bewegt, wird mit gedruckten Klammern fixiert. Mal schauen, wie lange die halten.

![X-Achsen-Riemen](/images/2020-07-15-alfawise-u50-unboxing-10.jpg)

Die senkrechten Alu-Profile werden von unten mit je einer Schraube fixiert. Die rechte Seite wird zusätzlich mit einem T-Profil befestigt.

![T-Profil](/images/2020-07-15-alfawise-u50-unboxing-11.jpg)

Die linke Seite ist wieder etwas fummeliger, weil man hier das Anbauteil mit dem Z-Achsen-Antrieb festschrauben muss.

![Z-Achsen-Antrieb](/images/2020-07-15-alfawise-u50-unboxing-12.jpg)
![Z-Achsen-Antrieb](/images/2020-07-15-alfawise-u50-unboxing-13.jpg)

Hinten wird nun der Y-Achsen-Antrieb montiert. Hier braucht man natürlich zwei unterschiedliche Schraubentypen und die dazu passenden Innensechkantschlüssel.

![Y-Achsen-Antrieb](/images/2020-07-15-alfawise-u50-unboxing-14.jpg)

Der Riemen für die Y-Achse ist unter dem Druckbett mit Kabelbindern fixiert. Allerdings musste ich eine Seite davon lösen, um den Riemen mit dem Antrieb zu verbinden.
Beim erneuten Fixieren ist mir eine der gedruckten Klammern zerbrochen.

![Y-Achsen-Riemen](/images/2020-07-15-alfawise-u50-unboxing-15.jpg)
![Y-Achsen-Riemen](/images/2020-07-15-alfawise-u50-unboxing-16.jpg)

Und so sieht das ganze vor dem Verkabeln aus.

![Fertig zusammengebaut - Rückansicht](/images/2020-07-15-alfawise-u50-unboxing-17.jpg)
![Fertig zusammengebaut - Frontansicht](/images/2020-07-15-alfawise-u50-unboxing-18.jpg)

## Das Verkabeln

Alle Komponenten werden mit Steckverbindern verkabeln. 
Durch die unterschiedliche Größe kann man Servoanschluss und Endschalteranschluss nicht verwechseln. Durch die Steckerform ist auch ein Verpolen im Grunde ausgeschlossen.

![Kabel für X-Achse und Extruder](/images/2020-07-15-alfawise-u50-unboxing-19.jpg)

Die einzelnen Kabel sind gut beschriftet und in Kabelschläuchen zusammengefasst.

![Kabel für die Z-Achse](/images/2020-07-15-alfawise-u50-unboxing-20.jpg)

Der Anschluss für das Hotend wird per Schraubverbindung gesichert.

![Kabel für das Hotend](/images/2020-07-15-alfawise-u50-unboxing-21.jpg)

Der Anschluss für den Y-Endschalter ist der einzige, der etwas fummelig anzuschliessen ist.

![Kabel für die Y-Achse](/images/2020-07-15-alfawise-u50-unboxing-22.jpg)
![Kabel für die Y-Achse](/images/2020-07-15-alfawise-u50-unboxing-23.jpg)

So sieht der Aufbau fertig verkabelt aus. Die Leitungen vom Hotend liegen sehr ungünstig und schleifen wie man sieht am Druckbett.
Die habe ich inzwischen mit Kabelbindern nach oben festgemacht.

![Fertig verkabelt](/images/2020-07-15-alfawise-u50-unboxing-24.jpg)

## Das erste Mal Einschalten

Nachdem alles verkabelt war, konnte ich das erste Mal einschalten und da nirgendwo MagicSmoke™ entwichen ist habe ich offensichtlich alles richtig zusammengesteckt.
Hier sieht man auch schon das aufgeklebte Druckbett.

![Das erste Mal eingeschaltet](/images/2020-07-15-alfawise-u50-unboxing-25.jpg)

Das Menü bietet Funktionen zur manuellen Ansteuerung aller Achsen, zur Temperaturvorwahl und zum direkten Drucken von der SD-Karte.
Die Vorinstallierte Firmware mit der Version 195 hatte das Problem, dass die Geschwindigkeit der Achsen nicht eingestellt werden konnte, weil das Eingabefeld nicht funktioniert hat. 
In einem französischen [3D-Druck Forum][4] habe ich ein Firmwareupdate auf Version 217 gefunden, in der das Problem behoben ist. Allerdings ließ sich das rar-Archiv unter Linux nicht entpacken, sondern nur unter Windows.

![Hauptmenü](/images/2020-07-15-alfawise-u50-unboxing-26.jpg)

Der Extruder lässt sich über das Menü nur verfahren, wenn das Hotend aufgeheizt ist. Ob das auch bei externer Steuerung gesperrt ist, habe ich nicht getestet.
Das Filament wird vor dem Extruder-Antrieb durch eine Kammer mit Endschalter gefädelt, über den erkannt wird, wenn das Filament zu Ende ist.
Nach dem Extruder-Antrieb läuft das Filament durch einen Schlauch zum Hotend. Hier musste ich einen Kabelbinder aufschneiden, weil sich das Filament sonst nicht bis zum Ende einfädeln ließ.

![Es kommt Filament unten an](/images/2020-07-15-alfawise-u50-unboxing-27.jpg)

Beim Anfahren der Home-Position zum Einstellen des Druckbetts ist mir aufgefallen, dass der X-Endschalter zu weit aussen sitzt, sodass das Hotend nicht mehr über dem Druckbett steht. 
Leider lässt sich der Endschalter nicht verstellen, so dass ich einen provisorischen Abstandshalter an die Schaltefahne geklebt habe.
Inzwischen habe ich dafür einen passenden Adapter gedruckt. Dazu kommt noch ein eigener Beitrag.

![Provisorischer Abstandshalter](/images/2020-07-15-alfawise-u50-unboxing-28.jpg)

# Mein erster Eindruck

Nachdem ich den Drucker bestellt hatte, war ich auf zwei Blogbeträge bei [China-Gadets][5] und [Drucktipps3D][6] gestoßen, die den U50 ziemlich zerrissen haben.
Nun habe ich natürlich nicht soviel Erfahrung mit anderen Druckern aber ich bin mit dem Aufbau und der Qualität bis jetzt durchaus zufrieden.

Gearbest wirbt damit, dass man den Drucker in 20 Minuten zusammenbauen kann, dass kann ich absolut nicht bestätigen. Ich habe mir Zeit gelassen und zwischendurch die Fotos gemacht, so dass ich vom Öffnen des Kartons bis zum Einschalten ungefähr zwei Stunden gebraucht habe.
Wie oben schon geschrieben, ist es an einigen Stellen etwas fummelig, aber im Großen und Ganzen lässt der Drucker sich leicht zusammenbauen.

Den ersten Druck habe ich von meinem Linux-Laptop direkt aus Cura gestartet. Ich verwende die aktuelle Version von Cura, habe die Voreinstellung des Alfawise U20 genommen und nur die Größe des Druckbetts angepasst.

Mittlerweile habe ich den Drucker an einem Raspberry Pi mit [Octoprint][7] angeschlossen und drucke von da aus. Ich habe noch keine großen, mehrstündigen Drucke gemacht und bin noch bei der beigelegten Filamentprobe, aber bisher habe ich den Kauf nicht bereut.

[1]: http://www.hackerspace-bremen.de "Hackerspace Bremen"
[2]: https://www.youtube.com/watch?v=nQ3ZTZvvC3M "[Alfawise U20 One] 3D-Drucker mit XXL Druckplattform zum schmalen Preis [Review][4K]"
[3]: https://de.gearbest.com/3d-printers--3d-printer-kits/pp_009143693697.html?wid=1895967 "Alfawise U50 DIY FDM 3D Drucker"
[4]: https://www.lesimprimantes3d.fr/forum/topic/30009-mise-a-jour-firmware-u50/ "Les Imprimantes 3D Forum"
[5]: https://www.china-gadgets.de/test-alfawise-u50-3d-drucker/ "China-Gadgets.de"
[6]: https://drucktipps3d.de/ein-ue-ei/ "Drucktipps3D.de"
[7]: https://octoprint.org/ "Octoprint.org"

