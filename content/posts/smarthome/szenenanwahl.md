---
title: "Szenenanwahl per Taster"
date: 2021-05-16T16:45:22+02:00
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Home Assistant

---

In diesem Beitrag zeige ich, wie man in Home Assistant mehrere Szenen mit einem Taster durchschalten kann.

## Szenen anlegen

Als erstes legt man sich die Szenen an, die man Durchschalten möchte. Man macht sich dabei die Sache einfacher,
wenn man für die Namen keine Leerzeichen oder andere Sonderzeichen benutzt, sondern nur Kleinbuchstaben und Zahlen.
Dadurch ist der Name dann das gleiche wie die Entity-Id, die wir später brauchen.
![Szenen](/images/2021-05-16-szenenanwahl-02.png)

## Dropdown Helfer anlegen

Der eigentliche Trick ist, einen Dropdown Helfer (input_select) anzulegen, bei dem die Optionen genauso heißen, wie die 
Szenen, die man durchschalten möchte. Genauer gesagt, müssen die Optionen so heißen, wie die Entity-IDs der Szenen.
Daher der Tipp weiter oben zu den Namen der Szenen.
![Dropdown Helfer](/images/2021-05-16-szenenanwahl-01.png)

## Automation anlegen

Die Automation ist relativ einfach. Als Auslöser trägt man den Taster ein. In meinem Beispiel hier ist es ein Zigbee-Taster,
der über Deconz angebunden ist, daher muss ich auf das passende Event triggern. Selbstverständlich kann man auch jeden
beliebigen anderen Auslöser verwenden.
![Automation Trigger](/images/2021-05-16-szenenanwahl-03.png)

Die erste Aktion in der Automation schaltet den Dropdown Helfer auf den nächten Eintrag. Dazu wird der passende Dienst
aufgerufen. Wichtig ist hierbei, dass die Option `Cycle` aktiviert wird, damit die Liste immer wieder von vorne duchlaufen wird.

Die zweite Aktion beinhaltet ein Template, deshalb muss man die Aktion als YAML bearbeiten. Dazu klickt man auf die drei Punkte
und wählt die entsprechende Option. 
![Automation Aktionen](/images/2021-05-16-szenenanwahl-04.png)

Das Template liest die aktuelle Option des Dropdown Helfers und setzt daraus die Entitiy-Id der Szene zusammen, die 
dann aktiviert wird.
```yaml
service: scene.turn_on
target:
  entity_id: 'scene.{{ states.input_select.szenenwahl.state }}'
```
