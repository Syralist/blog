---
title: "Processing Events with Python"
date: 2020-06-05T12:03:21.203Z
draft: false
tags: 
- Python 
- Home Assistant
- Datatransformation
- ICS
---

In this article I show how to import a list of appointments, process them and then save them in ICS format.

# Background
Bremer Stadtreinigung offers the [waste collection dates][1] for download as ICS file and as CSV file. Unfortunately there are two separate dates for residual waste and organic waste although both are collected on the same day.

Since I also integrate the dates into my Smarthome to receive notifications about them, I also want them to be in a certain format in the calendar. Details about the integration will follow in a later article.

# The Python script
The finished script can be found here on [Github][2]. You need at least **Python 3.6** because I use f-strings.

## Imports
First the required modules are imported. `ics` and `arrow` can be installed via `pip`, the other modules are included in the standard library.

```python
import csv
from ics import Calendar, Event
import arrow
from dateutil import tz
import datetime
```

* `csv` : Read and write CSV files
* `ics` : Read and write ICS files
* `arrow` : extended functions for time objects
* `dateutil.tz` : Time zone definitions
* `datetime` : Standard functions for time objects

## Configuration
To make my script also interesting for others, I tried to program different possibilities to create the dates and make them configurable.

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

It can be set whether the appointment is to be created for a whole day or for a specific hour with a configurable duration. You can create the appointment on the day before the collection or on the correct day.
You can have the type of collection written in the title or just &ldquo;Müllabfuhr&rdquo;. For the integration in Home Assistant you can enter an offset.

## Helper Variables
Before we start reading the CSV file, I create some helper variables.

```python
# Hilfsvariablen
offset_string = f" !!-{offset_hours:02d}:00"

# globale Variablen vorbelegen
d = {}
c = Calendar()
```

The `offset_string` is required for the Home Assistant integration already mentioned. I use an f-string here to insert the content of the variable into the string. The expression `offset_hours:02d` ensures that one-digit numbers are inserted with leading zero.

For the following code I additionally need an empty Dictionary `d` and an empty Calendar object `c`.

## Import CSV file
The entries in the CSV file look like this:
```text
"Wochentag";"Datum";"Abfuhrart"
"Samstag";"04.01.2020";"Papier / Gelber Sack"
"Mittwoch";"08.01.2020";"Bioabfall"
"Mittwoch";"08.01.2020";"Restmüll"
```

The CSV file is read in line by line and the entries are pre-processed.
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

The `csv.reader` requires the separator and the quote-character to read the file correctly. 

In the for loop, the heading is skipped first. Next, `if-row[1] in d` is used to check whether the date is already entered in my dictionary. If this is the case, the two collection types are combined via an f-string and the entry in the dictionary is updated.
Otherwise, a new entry is added to the dictionary.

Now I have a dictionary with the date string as *key* and the collection type as *value*.

## Create Events
The next step is to iterate over the dictionary and fill the Calendar object created above with events. `d.items()` returns *Key* and *Value* as a tuple.
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

For the title of the event I use an f-string again to put several strings together.
The expression `(' ' + abfuhr) if descriptive_title else ''` is a so-called *ternary operator*.
If `descriptive_title` is set to `True`, the part before the `if` is used, otherwise the part after the `else` will be used.

The start time is first read from the key as *datetime* object with `strptime`.
At the same time, the hour is set to the value defined above with `replace` and the time zone is set.
The *datetime* object is then converted to *arrow* object because this is expected by the ICS module.

The ICS module has a bug in the used version, which causes the time zone to be ignored for all-day appointments, so that the appointment appears one day too early. 
As a workaround I therefore move the appointment one day forward with `shift` so that it is created by ICS on the right day. 
If it is configured above that the appointment is to be created on the day before the collection, the same command is used to move the appointment back again.

Now the duration set above is entered into the appointment and then, if necessary, the appointment is converted into an all-day appointment.

Finally, the collection type is entered in the event description and the event is added to the calendar object.

## Writing the ICS file
The last step is to write the Calendar object to a file.
```python
# Kalenderdatei schreiben
with open("abfuhr.ics", "w") as f:
    f.writelines(c)
```

The resulting file can now be imported into Google, for example.


[1]: https://www.die-bremer-stadtreinigung.de/privatkunden/entsorgung/bremer_abfallkalender-23080 "Bremer Abfallkalender"
[2]: https://github.com/Syralist/Abfallkalender "Syralist/Abfallkalender"