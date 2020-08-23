---
title: "Zigbee Button mit Node-RED"
date: 2020-08-23T13:44:48+02:00
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Zigbee
- Node-RED
- Home Assistant
- Xiaomi
---

In diesem kurzen Beitrag zeige ich, wie ich einen Zigbee Button verwende, um Automationen in Node-RED zu steuern.

## Der Button und Home Assistant

Ich verwende hier den [Aqara Wireless Mini Switch][1] von Xiaomi. Das ist ein Zigbee Button, der bei in meinem Home Assistant per deConz eingebunden ist.

Solche Buttons werden im Home Assistant nicht wie andere Geräte als Entities hinzugefügt. Stattdessen werden beim Drücken des Buttons Events ausgelöst. 
Diese Events können dann als Trigger für Automationen verwendet werden.

## Verwendung in Node-RED
Der Flow in Node-RED besteht aus drei notwendigen Nodes und vier optionalen Nodes.
![Flow Übersicht](/images/2020-08-23-NodeRed-01.png)

Als erstes benutze ich eine *events: all* Node, um die Events vom Typ &bdquo;deconz_event&ldquo; abzufangen.

![Event Node](/images/2020-08-23-NodeRed-02.png)

Als nächstes wird mit einer *switch* Node in `msg.payload.event.id` nach der Id des Buttons gefiltert. 
Die Id leitet sich vom Namen ab, den man in der Phoscon Oberfläche vergeben hat.

![Switch Node für Button ID](/images/2020-08-23-NodeRed-03.png)

Als drittes wird mit einer weiteren *switch* Node die Art des Events gefiltert. Der Aqara Button liefert diese vier Events:

|Event|Beschreibung|
|:---:|:---:|
|1002|einfacher Klick|
|1004|Doppelklick|
|1001|Halten|
|1003|Loslassen|

![Switch Node für Eventnummer](/images/2020-08-23-NodeRed-04.png)

Nun kann man seine Flows direkt an die entsprechenden Ausgänge der zweiten *switch* Node anschliessen.
Da ich mir aber nie merken kann, welche Nummer für welchen Klick steht, habe ich noch *delay* Nodes eingefügt, die auf *Rate Limit* konfiguriert sind.
Dadurch wird der Flow nicht beeinträchtigt, die dienen nur dazu, mir das Programmieren zu erleichtern.

Und hier noch der ganze Flow zum importieren:

```json
[
	{
		"id": "9b4d7173.f7538",
		"type": "server-events",
		"z": "e0fe8a5c.1c4ed8",
		"name": "DeconzEvent",
		"server": "db71ae1a.4969e",
		"event_type": "deconz_event",
		"x": 110,
		"y": 140,
		"wires": [
			[
				"92b4d903.eda7f8"
			]
		]
	},
	{
		"id": "92b4d903.eda7f8",
		"type": "switch",
		"z": "e0fe8a5c.1c4ed8",
		"name": "ID auslesen",
		"property": "payload.event.id",
		"propertyType": "msg",
		"rules": [
			{
				"t": "eq",
				"v": "smart_switch",
				"vt": "str"
			}
		],
		"checkall": "true",
		"repair": false,
		"outputs": 1,
		"x": 310,
		"y": 140,
		"wires": [
			[
				"bf4a2cc8.ced0c"
			]
		]
	},
	{
		"id": "bf4a2cc8.ced0c",
		"type": "switch",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Event auslesen",
		"property": "payload.event.event",
		"propertyType": "msg",
		"rules": [
			{
				"t": "eq",
				"v": "1002",
				"vt": "str"
			},
			{
				"t": "eq",
				"v": "1004",
				"vt": "str"
			},
			{
				"t": "eq",
				"v": "1001",
				"vt": "str"
			},
			{
				"t": "eq",
				"v": "1003",
				"vt": "str"
			}
		],
		"checkall": "true",
		"repair": false,
		"outputs": 4,
		"x": 560,
		"y": 140,
		"wires": [
			[
				"db107f64.bbcf6"
			],
			[
				"7b49a1f5.e5728"
			],
			[
				"2f180c9f.1abab4"
			],
			[
				"cc777d04.e73a4"
			]
		]
	},
	{
		"id": "db107f64.bbcf6",
		"type": "delay",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Klick",
		"pauseType": "rate",
		"timeout": "1",
		"timeoutUnits": "seconds",
		"rate": "1",
		"nbRateUnits": "1",
		"rateUnits": "second",
		"randomFirst": "1",
		"randomLast": "5",
		"randomUnits": "seconds",
		"drop": false,
		"x": 790,
		"y": 120,
		"wires": [
			[
				"5ee59413.880bac",
				"288d2bb8.d00f44"
			]
		]
	},
	{
		"id": "7b49a1f5.e5728",
		"type": "delay",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Doppel-Klick",
		"pauseType": "rate",
		"timeout": "1",
		"timeoutUnits": "seconds",
		"rate": "1",
		"nbRateUnits": "1",
		"rateUnits": "second",
		"randomFirst": "1",
		"randomLast": "5",
		"randomUnits": "seconds",
		"drop": false,
		"x": 810,
		"y": 160,
		"wires": [
			[
				"c0c2b9f1.29de48",
				"288d2bb8.d00f44"
			]
		]
	},
	{
		"id": "2f180c9f.1abab4",
		"type": "delay",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Halten",
		"pauseType": "rate",
		"timeout": "1",
		"timeoutUnits": "seconds",
		"rate": "1",
		"nbRateUnits": "1",
		"rateUnits": "second",
		"randomFirst": "1",
		"randomLast": "5",
		"randomUnits": "seconds",
		"drop": false,
		"x": 790,
		"y": 200,
		"wires": [
			[]
		]
	},
	{
		"id": "cc777d04.e73a4",
		"type": "delay",
		"z": "e0fe8a5c.1c4ed8",
		"name": "Loslassen",
		"pauseType": "rate",
		"timeout": "1",
		"timeoutUnits": "seconds",
		"rate": "1",
		"nbRateUnits": "1",
		"rateUnits": "second",
		"randomFirst": "1",
		"randomLast": "5",
		"randomUnits": "seconds",
		"drop": false,
		"x": 800,
		"y": 240,
		"wires": [
			[]
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

&ldquo;english&rdquo;

[1]: https://www.aqara.com/eu/wireless_mini_switch.html "Aqara Wireless Mini Switch"
