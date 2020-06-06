---
title: "Window Automation"
date: 2020-06-06T14:25:23+02:00
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- NodeRed
- HomeAssistant
- Zigbee
- Xiaomi
- Homematic
---

# Window Controls Radiator

As I wrote in my article about my [Smarthome setup][1], automation is what makes a smart home smart.
Here I describe how I turn the radiator down when the window is opened and turn it back up again when the window is closed.

## Involved Components

The window is monitored with a **Xiaomi Aqara door/window contact**. This sends the status via **Zigbee** to the Conbee Stick. The Conbee is integrated into Home Assistant via Deconz.

![Xiaomi Aqara door/window contact](/images/2020-06-06-fensterkontakt.jpg)

A **Homematic radiator thermostat** is mounted on the radiator. As Homematic central I have a Raspberry Pi with *Raspberrymatic* running.
The thermostats are always set to *manual*, because they are controlled exclusively via Home Assistant.

![Homematic Thermostat](/images/2020-06-06-thermostat.jpg)

The reduced temperature is set via an *input_number* entity.

![input_number](/images/2020-06-06-input-number.png)

## NodeRed

The automation is implemented in NodeRed and consists of six nodes.

![Complete Flow](/images/2020-06-06-nodered-01.png)

The *state node* &ldquo;Fenster&rdquo; reacts to a change of state of the window contact.

![Window State](/images/2020-06-06-nodered-02.png)

The *switch node* branches into the upper or lower path, depending on the state. The binary sensor of the window contact has the state `on` when the window is open and `off` when the window is closed.

![Branching with the State](/images/2020-06-06-nodered-03.png)

When the window has been opened, the first thing to do is to save the current status of the thermostat in a temporary scene with the *call service node*.
In the *data* field, the name of the scene and a list of the entities to be saved must be passed.
```json
{
	"scene_id": "thomas_snapshot",
	"snapshot_entities": [
		"climate.0012999395e750"
	]
}
```

![Saving the State](/images/2020-06-06-nodered-04.png)

Next, the value of the *input_number* entity is read with a *current state node*.

![reading the input_number](/images/2020-06-06-nodered-05.png)

Finally, the thermostat is set to the readout value with a *call service node*. For this purpose `{{payload}}` is used as a template in the *data* field.
```json
{
	"temperature": {{payload}}
}
```

![Setting the Thermostat](/images/2020-06-06-nodered-06.png)

When the window is closed, the previously saved scene is activated with another *call service node*. Here only the name of the scene must be passed in the *Data* field.
```json
{
	"entity_id": "scene.thomas_snapshot"
}
```

![Resetting the Thermostat](/images/2020-06-06-nodered-07.png)

Such a flow is used for every room in which a smart thermostat is installed. The advantage of this method with a temporary scene is that it can be expanded easily.
If additional entities are to be considered, they only need to be added at one point.

Here is the complete flow to import:
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

[1]: {{< ref "mein_setup.md" >}} "My Smarthome Setup"