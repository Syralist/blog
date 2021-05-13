---
title: "Monitoring plants with Home Assistant"
date: 2021-05-12T21:47:41.255Z
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Home Assistant
- ESPHome
- ESP32
---

In this post I show how I try to use Home Assistant to monitor my plants and remind me to water them.

## Plant Sensor

The sensor used is a MiFlora-compatible soil sensor. These sensors measure temperature, brightness, soil moisture and soil conductivity.
The data is sent via Bluetooth Low Energy (BLE). Originally, I had connected the sensors directly to the Home Assistant Raspberry Pi and 
the custom integration *Passive BLE monitor*. On the one hand, I had the problem that the sensors were too far away and only received data irregularly.
On the other hand, the sensors were `undefined` for a while after restarting Home Assistant.

Now I have switched the integration to ESPhome and it works much better. I am now using an ESP32 with the following configuration:

```yaml
esphome:
  name: ble_node
  platform: ESP32
  board: nodemcu-32s

wifi:
  ssid: !secret wifi-ssid
  password: !secret wifi-password

# Enable logging
logger:

# Enable Home Assistant API
api:
  password: !secret esphome-password

ota:
  password: !secret esphome-password
  
esp32_ble_tracker:
  
sensor:
  - platform: xiaomi_hhccjcy01
    mac_address: 'C4:7C:8D:6B:AD:FF'
    temperature:
      name: "Bauernhortensie Temperature"
    moisture:
      name: "Bauernhortensie Moisture"
    illuminance:
      name: "Bauernhortensie Illuminance"
    conductivity:
      name: "Bauernhortensie Soil Conductivity"
    battery_level:
      name: "Bauernhortensie Battery Level"
  - platform: xiaomi_hhccjcy01
    mac_address: 'C4:7C:8D:6B:95:FB'
    temperature:
      name: "Kleine Lilie Temperature"
    moisture:
      name: "Kleine Lilie Moisture"
    illuminance:
      name: "Kleine Lilie Illuminance"
    conductivity:
      name: "Kleine Lilie Soil Conductivity"
    battery_level:
      name: "Kleine Lilie Battery Level"
  - platform: xiaomi_hhccjcy01
    mac_address: 'C4:7C:8D:6B:94:7A'
    temperature:
      name: "Peace Lily Temperature"
    moisture:
      name: "Peace Lily Moisture"
    illuminance:
      name: "Peace Lily Illuminance"
    conductivity:
      name: "Peace Lily Soil Conductivity"
    battery_level:
      name: "Peace Lily Battery Level"
  - platform: xiaomi_hhccjcy01
    mac_address: 'C4:7C:8D:6C:2F:FC'
    temperature:
      name: "Alpenveilchen Temperature"
    moisture:
      name: "Alpenveilchen Moisture"
    illuminance:
      name: "Alpenveilchen Illuminance"
    conductivity:
      name: "Alpenveilchen Soil Conductivity"
    battery_level:
      name: "Alpenveilchen Battery Level"
```

I combine the individual sensors into *plant* entities in Home Assistant so that they can be visualised with the appropriate Lovelace Card. 
A *plant* configuration looks like this:

```yaml
alpenveilchen:
  sensors:
    moisture: sensor.alpenveilchen_moisture
    temperature: sensor.alpenveilchen_temperature
    conductivity: sensor.alpenveilchen_soil_conductivity
    brightness: sensor.alpenveilchen_illuminance
  min_brightness: 3500
  max_brightness: 30000
  min_temperature: 8
  max_temperature: 35
  min_moisture: 15
  max_moisture: 60
  min_conductivity: 350
  max_conductivity: 2000
```

The values for min and max come from the database of [khronimo][1]. 

In Lovelace it then looks like this with the [lovelace-flower-card][2] from HACS:
![Lovelace Flower Card](/images/2021-05-12-pflanzenstatus-01.png)

## Notification

To make sure that I get something out of the whole thing and am reminded to water, I have set up an automation that 
sends me a notification on my mobile phone with the values of the humidity humidity sensors.

The automation is triggered when I come home.

```yaml
trigger:
  - platform: zone
    entity_id: person.thomas
    zone: zone.home
    event: enter
```

I have entered as a condition that the automation should only be triggered three days a week. I have also created 
an `input_boolean` with which I can prevent the automation from being triggered more than once on the same day.

```yaml
condition:
  - condition: state
    entity_id: input_boolean.pflanzenstatus
    state: 'off'
  - condition: time
    weekday:
      - mon
      - wed
      - fri
```

The content of the notification is assembled via a template that runs through all *plant* entities and reads 
out the moisture attribute for each of them. In addition, the notification is is marked as *persistent* and *sticky* so 
that I cannot simply swipe it away on my mobile phone.

```yaml
action:
  - service: notify.mobile_app_nokia_9
    data:
      message: >-
        {% for state in states.plant -%}   {{ state.name }}
        {{state.attributes.moisture|int}}{{state.attributes.unit_of_measurement_dict.moisture}}<br>
        {%- endfor %}
      title: Pflanzenstatus
      data:
        persistent: true
        sticky: true
        tag: pflanzenstatus
  - service: input_boolean.turn_on
    target:
      entity_id: input_boolean.pflanzenstatus
```

At the end, the above-mentioned `input_boolean` is also set to prevent further executions on the same day. 
The resetting of the `input_boolean` is carried out in a further automation that runs every day at midnight.

## NFC Tag

A notification with the *sticky* flag can only be cleared by triggering a message with the body `clear_notification`.
The same `tag` must be used for the original notification and the *clear* message.

```yaml
action:
  - service: notify.mobile_app_nokia_9
    data:
      message: clear_notification
      data:
        tag: pflanzenstatus
```

This *clear* message is triggered by an NFC tag on my balcony door. Since this is the only way to 
get rid of the notification, I am forced to go near the plants. 

Translated with www.DeepL.com/Translator (free version)

[1]: https://github.com/khronimo/MiFloraDB "MiFloraDB"
[2]: https://github.com/Yolandavdvegt/lovelace-flower-card "Lovelace Flower Card"
