---
title: "Zigbee Button in Node-RED"
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

In this short article I show how to use a Zigbee button to control automations in Node-RED.

## The Button and Home Assistant

I am using the [Aqara Wireless Mini Switch][1] from Xiaomi. This is a Zigbee button that is integrated in my Home Assistant via deConz.

Such buttons are not added as entities in the Home Assistant like other devices. Instead, events are triggered when the button is pressed. 
These events can then be used as triggers for automations.

## Usage in Node-RED
The Flow in Node-RED consists of three necessary nodes and four optional nodes.
![Flow Overview](/images/2020-08-23-NodeRed-01.png)

First, I use an *events: all* node to catch the events of type &ldquo;deconz_event&rdquo;.

![Event Node](/images/2020-08-23-NodeRed-02.png)

Next, a *switch* node is used to filter for the button's Id in 'msg.payload.event.id'. 
The id is derived from the name given in the Phoscon interface.

![Switch Node for Button ID](/images/2020-08-23-NodeRed-03.png)

Thirdly, the type of event is filtered with another *switch* node. The Aqara button returns these four events:

|Event|Description|
|:---:|:---:|
|1002|single click|
|1004|double click|
|1001|hold|
|1003|release|

![Switch Node for Event Nummer](/images/2020-08-23-NodeRed-04.png)

Now you can connect your flows directly to the corresponding outputs of the second *switch* node.
But since I can never remember which number stands for which click, I added *delay* nodes, which are configured to *rate limit*.
This does not affect the flow, they are just to make programming easier for me.

And here is the whole flow to import:

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

---

Translated with www.DeepL.com/Translator (free version)

[1]: https://www.aqara.com/eu/wireless_mini_switch.html "Aqara Wireless Mini Switch"
