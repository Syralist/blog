---
title: "Fensterautomation"
date: 2020-06-06T14:25:23+02:00
lastmod: 2021-01-13T17:22:06.089Z
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Node-RED
- Home Assistant
- Zigbee
- Xiaomi
- Homematic
---

## Fenster steuert Heizkörper

Wie ich schon in meinem Artikel über mein [Smarthomesetup][1] geschrieben habe, sind die Automationen das, was ein Smarthome erst smart macht.
Hier beschreibe ich, wie ich den Heizkörper herunterregle, wenn das Fenster geöffnet wird und wieder zurückstelle, wenn das Fenster geschlossen wird.

**Update vom 13.01.2021:** Mittlerweile lasse ich die Automation nicht mehr über Node-Red laufen, sondern nutze dafür die in Home Assistant eingebauten [Automationen](#automation-in-home-assistant).

### Beteiligte Komponenten

Das Fenster wird mit einem **Xiaomi Aqara Tür-/Fensterkontakt** überwacht. Der sendet den Status per **Zigbee** an den Conbee Stick. Der Conbee ist per Deconz in Home Assistant eingebunden.

![Xiaomi Aqara Tür-/Fensterkontakt](/images/2020-06-06-fensterkontakt.jpg)

Am Heizkörper ist ein **Homematic Heizkörperthermostat** montiert. Als Homematiczentrale läuft bei mir ein Raspberry Pi mit *Raspberrymatic*.
Die Thermostate stehen immer auf *manuell*, weil sie ausschließlich über Home Assistant gesteuert werden.

![Homematic Thermostat](/images/2020-06-06-thermostat.jpg)

Die Absenktemperatur wird über eine *input_number* Entity eingestellt.

![input_number](/images/2020-06-06-input-number.png)

### Automation in Home Assistant

Ich habe für jedes Fenster eine Automation erstellt. Die Automation läuft im Modus **Neu Starten**, weil ich eine *Warten*-Aktion verwende und gegebenenfalls noch laufende Instanzen der Automation so abgebrochen werden.

![Automation erstellen](/images/2021-01-13-automation-01.png)

Der Auslöser ist ganz einfach: Wenn der Fensterkontakt den Zustand zu `on` wechselt, ist das Fenster geöffnet und die Automation soll starten.

![Auslöser einstellen](/images/2021-01-13-automation-02.png)

Ich habe hier noch eine Bedingung eingefügt, dass meine Heizungsanlage laufen muss. Wenn im Sommer die Heizung ausgeschaltet ist, soll die Automation nicht am Heizkörper rumdrehen.

![optionale Bedingung](/images/2021-01-13-automation-03.png)

Nun folgen die Aktionen, die ausgeführt werden sollen. Als erstes wird eine temporäre Szene erstellt, in der der Zustand des Thermostaten gespeichert wird. 
Dafür wird der Dienst `scene.create` aufgerufen und eine `scene_id` sowie die Entities übergeben.

Als nächstes wird der Dienst `climate.set_temperature` aufgerufen. Die Temperatur ist nicht fest eingetragen, sondern wird mit Hilfe eines Templates aus dem *input_number*-Helfer ausgelesen.

![Aktionen Teil 1](/images/2021-01-13-automation-04.png)

Danach wird eine **Auf Auslöser warten** Aktion gestartet, die die Automation pausiert bis der Fensterkontakt wieder geschlossen meldet. Es ist kein Timeout eingetragen, so dass die Automation theoretisch ewig aktiv bleibt.

Wenn das Fenster wieder geschlossen ist, folgt als letzte Aktion das Aktivieren der vorher gespeicherten Szene.

![Aktionen Teil 2](/images/2021-01-13-automation-05.png)

Hier noch die Automation als YAML zum Importieren:
```yaml
alias: Fenster Büro
description: ''
trigger:
  - platform: state
    entity_id: binary_sensor.fenster_buero
    to: 'on'
condition:
  - condition: state
    entity_id: binary_sensor.vicare_heizkreisaktiv
    state: 'on'
action:
  - service: scene.create
    data:
      scene_id: buero_snapshot
      snapshot_entities:
        - climate.00201a49a039a4
  - service: climate.set_temperature
    data:
      temperature: '{{ states(''input_number.temperatur_niedrig'') }}'
    entity_id: climate.00201a49a039a4
  - wait_for_trigger:
      - platform: state
        entity_id: binary_sensor.fenster_buero
        to: 'off'
  - scene: scene.buero_snapshot
mode: restart
```

### NodeRed *(nicht mehr genutzt)*

Die Automation ist in NodeRed umgesetzt und besteht aus sechs Nodes.

![Kompletter Flow](/images/2020-06-06-nodered-01.png)

Die *state node* &bdquo;Fenster&ldquo; reagiert auf einen Zustandswechsel des Fensterkontakts.

![Fensterzustand](/images/2020-06-06-nodered-02.png)

Die *switch node* verzweigt je nach Zustand in den oberen oder unteren Weg. Der Binäre Sensor des Fensterkontakts hat den Zustand `on`, wenn das Fenster geöffnet ist, und `off`, wenn das Fenster geschlossen ist.

![Verzweigung nach Zustand](/images/2020-06-06-nodered-03.png)

Wenn das Fenster geöffnet wurde, wird als erstes der aktuelle Zustand des Thermostat mit der *call service node* in einer temporären Szene gespeichert.
Im *Data* Feld muss der Name der Szene und eine Liste der zu speichernden Entities übergeben werden.
```json
{
	"scene_id": "thomas_snapshot",
	"snapshot_entities": [
		"climate.0012999395e750"
	]
}
```

![Zustand abspeichern](/images/2020-06-06-nodered-04.png)

Als nächstes wird der Wert der *input_number* Entity mit einer *current state node* ausgelesen.

![input_number lesen](/images/2020-06-06-nodered-05.png)

Zuletzt wird der Thermostat mit einer *call service node* auf den ausgelesenen Wert gesetzt. Dafür wird im *Data* Feld `{{payload}}` als Template verwendet.
```json
{
	"temperature": {{payload}}
}
```

![Thermostat setzen](/images/2020-06-06-nodered-06.png)

Beim Schließen des Fensters wird die vorher gespeicherte Szene mit einer weiteren *call service node* aktiviert. Hier muss nur der Name der Szene im *Data* Feld übergeben werden.
```json
{
	"entity_id": "scene.thomas_snapshot"
}
```

![Thermostat wiederherstellen](/images/2020-06-06-nodered-07.png)

Solch einen Flow gibt es für jedes Zimmer, in dem ein smarter Thermostat montiert ist. Der Vorteil dieser Methode mit temporärer Szene liegt in der Erweiterbarkeit.
Wenn weitere Entities berücksichtigt werden sollen, müssen sie nur an einer Stelle hinzugefügt werden.

Hier noch der komplette Flow zum importieren:
```json
[
	{
		"id": "d302a18c.b4a5c",
		"type": "api-call-service",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Temperatur speichern",
		"server": "db71ae1a.4969e",
		"version": 1,
		"debugenabled": false,
		"service_domain": "scene",
		"service": "create",
		"entityId": "",
		"data": "{\"scene_id\":\"thomas_snapshot\",\"snapshot_entities\":[\"climate.0012999395e750\"]}",
		"dataType": "json",
		"mergecontext": "",
		"output_location": "",
		"output_location_type": "none",
		"mustacheAltTags": false,
		"x": 620,
		"y": 1040,
		"wires": [
			[
				"2ba89685.0924aa"
			]
		]
	},
	{
		"id": "68ffd494.662f3c",
		"type": "server-state-changed",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Fenster",
		"server": "db71ae1a.4969e",
		"version": 1,
		"exposeToHomeAssistant": false,
		"haConfig": [
			{
				"property": "name",
				"value": ""
			},
			{
				"property": "icon",
				"value": ""
			}
		],
		"entityidfilter": "binary_sensor.tur_thomas",
		"entityidfiltertype": "exact",
		"outputinitially": false,
		"state_type": "str",
		"haltifstate": "",
		"halt_if_type": "str",
		"halt_if_compare": "is",
		"outputs": 1,
		"output_only_on_state_change": true,
		"x": 100,
		"y": 1060,
		"wires": [
			[
				"8c0ea6fb.5a5d08"
			]
		]
	},
	{
		"id": "8c0ea6fb.5a5d08",
		"type": "switch",
		"z": "e0fe8a5c.1c4ed8",
		"name": "",
		"property": "payload",
		"propertyType": "msg",
		"rules": [
			{
				"t": "eq",
				"v": "on",
				"vt": "str"
			},
			{
				"t": "eq",
				"v": "off",
				"vt": "str"
			}
		],
		"checkall": "true",
		"repair": false,
		"outputs": 2,
		"x": 310,
		"y": 1060,
		"wires": [
			[
				"d302a18c.b4a5c"
			],
			[
				"e5f92e8f.b86af"
			]
		]
	},
	{
		"id": "4acbf5b0.7c7bbc",
		"type": "api-call-service",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Temperatur setzen",
		"server": "db71ae1a.4969e",
		"version": 1,
		"debugenabled": false,
		"service_domain": "climate",
		"service": "set_temperature",
		"entityId": "climate.0012999395e750",
		"data": "{\"temperature\": {{payload}}}",
		"dataType": "json",
		"mergecontext": "",
		"output_location": "",
		"output_location_type": "none",
		"mustacheAltTags": false,
		"x": 1050,
		"y": 1040,
		"wires": [
			[]
		]
	},
	{
		"id": "e5f92e8f.b86af",
		"type": "api-call-service",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Temperatur zurücksetzen",
		"server": "db71ae1a.4969e",
		"version": 1,
		"debugenabled": false,
		"service_domain": "scene",
		"service": "turn_on",
		"entityId": "scene.thomas_snapshot",
		"data": "{\"entity_id\":\"scene.thomas_snapshot\"}",
		"dataType": "json",
		"mergecontext": "",
		"output_location": "",
		"output_location_type": "none",
		"mustacheAltTags": false,
		"x": 630,
		"y": 1100,
		"wires": [
			[]
		]
	},
	{
		"id": "2ba89685.0924aa",
		"type": "api-current-state",
		"z": "e0fe8a5c.1c4ed8",
		"name": "temp_low",
		"server": "db71ae1a.4969e",
		"version": 1,
		"outputs": 1,
		"halt_if": "",
		"halt_if_type": "str",
		"halt_if_compare": "is",
		"override_topic": false,
		"entity_id": "input_number.temperatur_niedrig",
		"state_type": "num",
		"state_location": "payload",
		"override_payload": "msg",
		"entity_location": "data",
		"override_data": "msg",
		"blockInputOverrides": false,
		"x": 840,
		"y": 1040,
		"wires": [
			[
				"4acbf5b0.7c7bbc"
			]
		]
	},
	{
		"id": "db71ae1a.4969e",
		"type": "server",
		"z": "",
		"name": "Home Assistant",
		"legacy": false,
		"addon": true,
		"rejectUnauthorizedCerts": true,
		"ha_boolean": "y|yes|true|on|home|open",
		"connectionDelay": true,
		"cacheJson": true
	}
]
```

[1]: {{< ref "mein_setup.md" >}} "Mein Smarthome Setup"