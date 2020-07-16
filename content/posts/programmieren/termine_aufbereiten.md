---
title: "Termine mit Python aufbereiten"
date: 2020-06-05T12:03:21.203Z
draft: false
toc: true
tocNum: false
tags: 
- Python 
- Home Assistant
- Datatransformation
- ICS
---

In diesem Beitrag zeige ich, wie ich eine Liste mit Terminen einlese, verarbeite und dann im ICS Format abspeichere.

## Hintergrund
Die Bremer Stadtreinigung bietet die [Müllabfuhrtermine][1] zum Download als ICS Datei und als CSV Datei an. Leider sind dabei für Restmüll und Biomüll jeweils zwei einzelne Termine eingetragen obwohl beides am gleichen Tag abgeholt wird.

Da ich die Termine ausserdem in mein Smarthome einbinde, um darüber Benachrichtungen zu erhalten, möchte ich sie ausserdem im einem bestimmten Format im Kalender stehen haben. Details zur Einbindung folgen in einem späteren Artikel.

## Das Python-Skript
Zu finden ist das fertige Skript hier auf [Github][2]. Man braucht mindestens **Python 3.6**, da ich f-Strings verwende.

### Imports
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

### Einstellungen
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

### Hilfsvariablen
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

### CSV Datei einlesen
Die Einträge in der CSV-Datei sehen folgendermaßen aus:
```text
"Wochentag";"Datum";"Abfuhrart"
"Samstag";"04.01.2020";"Papier / Gelber Sack"
"Mittwoch";"08.01.2020";"Bioabfall"
"Mittwoch";"08.01.2020";"Restmüll"
```

Die CSV-Datei wird zeilenweise eingelesen und die Einträge vorverarbeitet.
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

Dem `csv.reader` muss man dabei das Trennzeichen und den Quotechar mitgeben, damit die Datei korrekt eingelesen wird. 

In der for-Schleife wird zuerst die Überschrift übersprungen. Als nächstes wird mit `if row[1] in d` geprüft, ob das Datum schon in meinem Dictionary eingetragen ist. Wenn das der Fall ist, werden die beiden Abfuhrarten über einen f-String zusammengefasst und der Eintrag im Dictionary aktualisiert.
Ansonsten wird einfach ein neuer Eintrag zum Dictionary hinzugefügt.

Damit habe ich nun ein Dictionary mit dem Datums-String als *key* und der Abfuhrart als *value*.

### Events anlegen
Im nächsten Schritt wird jetzt über das Dictionary iteriert und das oben angelegte Calendar-Objekt mit Events befüllt. `d.items()` gibt dabei jeweils *Key* und *Value* als Tuple zurück.
```python
# Zusammengefasste Termine ins ICS Format umandeln
for datum, abfuhr in d.items():
    # Event anlegen
    e = Event()
    # Titel zusammenbauen
    e.name = f"Müllabfuhr{(' ' + abfuhr) if descriptive_title else ''}{offset_string if offset_hours > 0 else ''}"
    # Startzeitpunkt zusammenbauen
    _date = datetime.datetime.strptime(datum, "%d.%m.%Y").replace(hour=event_hour, tzinfo=tz.gettz("Europe/Berlin"))
    begin = arrow.get(_date)
    if ics_workaround:
        begin = begin.shift(days = 1)
    e.begin = begin.shift(days = -1 if on_day_before else 0)
    # Dauer eintragen bzw. ganztägigen Termin erzeugen
    e.duration = {"hours": duration_hour}
    if all_day:
        e.make_all_day()
    # Art der Abfuhr eintragen
    e.description = abfuhr
    # Event eintragen
    c.events.add(e)
```

Für den Titel des Events benutze ich wieder einen f-String, um mehrere Strings zusammenzusetzen.
Der Ausdruck `(' ' + abfuhr) if descriptive_title else ''` ist ein sogennater *ternärer Operator*.
Wenn `descriptive_title` auf `True` gesetzt ist, wird der Teil vor dem `if` benutzt, ansonsten der Teil hinter dem `else`.

Der Startzeitpunkt wird zunächst als *datetime* Objekt mit `strptime` aus dem Key gelesen.
Gleichzeitig wird mit `.replace` die Stunde auf den oben definierten Wert gesetzt und die Zeitzone eingestellt.
Das *datetime* Objekt wird anschließend in *arrow* Objekt umgewandelt, weil das vom ICS Modul erwartet wird.

Das ICS Modul hat in der verwendeten Version den Bug, dass bei ganztägigen Terminen die Zeitzone nicht berücksichtigt wird und so der Termin einen Tag zu früh erscheint. 
Als Workaround schiebe ich daher den Termin mit `.shift` einen Tag nach vorne, so dass er von ICS am richtigen Tag erzeugt wird. 
Wenn oben konfiguriert ist, dass der Termin am Tag vor der Abfuhr erzeugt werden soll, wird mit dem gleichen Befehl der Zeitpunkt wieder nach hinten geschoben.

Nun wird die oben eingestellte Dauer in den Termin eingetragen und anschließend gegebenenfalls der Termin in einen ganztägigen Termin umgewandelt.

Zu guter Letzt wird die Abfuhrart in die Terminbeschreibung eingetragen und das Event zum Calender Objekt hinzugefügt.

### ICS Datei schreiben
Der letzte Schritt ist dann das Calendar Objekt in eine Datei zu schreiben.
```python
# Kalenderdatei schreiben
with open("abfuhr.ics", "w") as f:
    f.writelines(c)
```

Die entstandene Datei kann man nun zum Beispiel in Google importieren.


[1]: https://www.die-bremer-stadtreinigung.de/privatkunden/entsorgung/bremer_abfallkalender-23080 "Bremer Abfallkalender"
[2]: https://github.com/Syralist/Abfallkalender "Syralist/Abfallkalender"