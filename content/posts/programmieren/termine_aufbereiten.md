---
title: "Termine mit Python aufbereiten"
date: 2020-05-26T16:07:40.698Z
draft: true
tags: 
- Python 
- Home Assistant
- Datatransformation
- ICS
---

In diesem Beitrag zeige ich, wie ich eine Liste mit Terminen einlese, verarbeite und dann im ICS Format abspeichere.

# Hintergrund
Die Bremer Stadtreinigung bietet die [Müllabfuhrtermine][1] zum Download als ICS Datei und als CSV Datei an. Leider sind dabei für Restmüll und Biomüll jeweils zwei einzelne Termine eingetragen obwohl beides am gleichen Tag abgeholt wird.

Da ich die Termine ausserdem in mein Smarthome einbinde, um darüber Benachrichtungen zu erhalten, möchte ich sie ausserdem im einem bestimmten Format im Kalender stehen haben. Details zur Einbindung folgen in einem späteren Artikel.

# Das Python-Skript
Zu finden ist das fertige Skript hier auf [Github][2]. Man braucht mindestens **Python 3.6**, da ich f-Strings verwende.

## Imports
Zuerst werden die benötigte Module importiert. `ics` und `arrow` können über `pip` installiert werden, die anderen Module sind in der Standardlibrary enthalten.

```python
import csv
from ics import Calendar, Event
import arrow
from dateutil import tz
import datetime
```

* `csv` : Lesen und Schreiben von CSV Dateien
* `ics` : Lesen und Schreiben von ICS Dateien
* `arrow` : erweiterte Funktionen für Zeitobjekte
* `dateutil.tz` : Zeitzonen-Definitionen
* `datetime` : Standardfunktionen für Zeitobjekte

## Einstellungen
Damit mein Skript auch für andere interessant ist, habe ich versucht, verschiedene Möglichkeiten die Termine zu erstellen zu programmieren und parametrierbar zu machen.

```python
###### Einstellungen ######
# CSV-Datei von der Eno
csv_file = r"Abfuhrtermine für  Auf dem Kamp.csv"
# Soll die Art der Abfuhr in den Titel eingetragen werden? True/False
descriptive_title = True
# Soll für die HomeAssistant Integration ein Offset eingetragen werden? 0..23
offset_hours = 6
# Soll ein ganztägiger Termin erzeugt werden? True/False
all_day = True
# Workaround für fehlerhafte All Day Events in ics.py aktivieren? True/False
ics_workaround = True
# Soll der Termin am Tag vor der Abfuhr erzeugt werden? True/False
on_day_before = False
# Zu welcher Stunde der Termin erzeugt werden, wenn er nicht ganztägig ist? 0..23
event_hour = 18
# Wie lange soll der Termin sein, wenn er nicht ganztägig ist? 0..23
duration_hour = 1
######
```

Eingestellt werden kann, ob der Termin ganztägig oder zu einer bestimmten Stunde mit einer einstellbaren Dauer erzeugt werden soll. Dabei kann man den Termin am Vortag der Abfuhr erzeugen oder am richtigen Tag.
Man kann die Art der Abfuhr mit in den Titel schreiben lassen oder nur &bdquo;Müllabfuhr&ldquo;. Für die Einbindung in Home Assistant kann man einen Offset eintragen lassen.

## Hilfsvariablen
Bevor es an das Einlesen der CSV Datei geht, erzeuge ich einige Hilfsvariablen.

```python
# Hilfsvariablen
offset_string = f" !!-{offset_hours:02d}:00"

# globale Variablen vorbelegen
d = {}
c = Calendar()
```

Der `offset_string` wird für die schon angesprochene Home Assistant Einbindung benötigt. Ich benutze hier einen f-String, um den Inhalt der Variablen in den String einzufügen. Der Ausdruck `offset_hours:02d` sorgt dafür, dass einstellige Zahlen mit führender Null eingefügt werden.

Für den folgenden Code brauche ich dann noch ein leeres Dictionary `d` und ein leeres Calendar-Objekt `c`.

## CSV Datei einlesen
Die Einträge in der CSV-Datei sehen folgendermaßen aus:
```text
"Wochentag";"Datum";"Abfuhrart"
"Samstag";"04.01.2020";"Papier / Gelber Sack"
"Mittwoch";"08.01.2020";"Bioabfall"
"Mittwoch";"08.01.2020";"Restmüll"
```

Als nächstes wird jetzt die CSV-Datei eingelesen und die Einträge vorverarbeitet.
```python
## Datei öffnen
with open(csv_file) as f:
    # CSV parsen
    csv_reader = csv.reader(f, delimiter=';', quotechar='"')
    for row in csv_reader:
        if row[0] == 'Wochentag':
            # Überschrift überspringen
            continue
        # Mehrere Abfuhren an einem Tag zusammenfassen
        if row[1] in d:
            d[row[1]] =f"{d[row[1]]} / {row[2]}"
        else:
            d[row[1]] = row[2]
```

Dem `csv.reader` muss man das Trennzeichen und den Quotechar mitgeben, damit die Datei korrekt eingelesen wird. 

In der for-Schleife wird zuerst die Überschrift übersprungen. Als nächstes wird mit `if row[1] in d` geprüft, ob das Datum schon in meinem Dictionary eingetragen ist. Wenn das der Fall ist, werden die beiden Abfuhrarten über einen f-String zusammengefasst und der Eintrag im Dictionary aktualisiert.
Ansonsten wird einfach ein neuer Eintrag zum Dictionary hinzugefügt.



[1]: https://www.die-bremer-stadtreinigung.de/privatkunden/entsorgung/bremer_abfallkalender-23080 "Bremer Abfallkalender"
[2]: https://github.com/Syralist/Abfallkalender "Syralist/Abfallkalender"