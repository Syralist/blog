---
title: "Pflanzen mit Home Assistant überwachen"
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

In diesem Beitrag zeige ich, wie ich versuche mit Home Assistant meine Pflanzen zu überwachen und mich ans Wässern zu erinnern.

## Pflanzensensor

Als Sensor kommt ein MiFlora-kompatibler Bodensensor zu Einsatz. Diese Sensoren messen Temperatur, Helligkeit, Bodenfeuchtigkeit und Bodenleitfähigkeit.
Die Daten werden per Bluetooth Low Energy (BLE) gesendet. Ursprünglich hatte ich die Sensoren direkt mit dem Home Assistant Raspberry Pi und der Custom Integration 
*Passive BLE monitor* ausgelesen. Dabei hatte ich zum einen das Problem, dass die Sensoren zu weit weg waren und nur unregelmäßig Daten empfangen wurden.
Zum anderen waren die Sensoren nach einem Neustart von Home Assistant erstmal eine Zeit lang `undefined`.

Nun habe ich die Integration auf ESPhome umgeschwenkt und das funkioniert viel besser. Ich setze jetzt einen ESP32 mit folgender Konfiguration ein:

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

Die einzelnen Sensoren fasse ich in Home Assistant zu *plant* Entities zusammen, um sie mit der passenden Lovelace Card visualisieren zu können. Eine
*plant* Konfiguration sieht beispielhaft so aus:

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

Die Werte für min und max kommen aus der Datenbank von [khronimo][1]. 

In Lovelace sieht das dann mit der [lovelace-flower-card][2] von HACS so aus:
![Lovelace Flower Card](/images/2021-05-12-pflanzenstatus-01.png)

## Notification

Damit mir das ganze auch etwas bringt und ich an das Giessen erinnert werde, habe ich eine
Automation eingerichtet, die mir eine Notification auf mein Handy schickt mit den Werten der
Feuchtigkeitssensoren.

Die Automation wird getriggert, wenn ich nach Hause komme.

```yaml
trigger:
  - platform: zone
    entity_id: person.thomas
    zone: zone.home
    event: enter
```

Als Bedingung habe ich eingetragen, dass die Automation nur an drei Tagen in der Woche
ausgelöst werden soll. Ausserdem habe ich ein `input_boolean` angelegt, mit dem ich 
verhindere, dass die Automation mehrfach am gleichen Tag ausgelöst wird.

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

Der Inhalt der Notification wird über ein Template zusammengebaut, das alle *plant* Entities
durchläuft und jeweils das Feuchtigkeitsattribut ausliest. Ausserdem wird die Notification
als *persistent* und *sticky* markiert, damit ich sie nicht einfach wegwischen kann am Handy.

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

Am Ende wird noch der oben genannte `input_boolean` gesetzt, um weitere Ausführungen am 
gleichen Tag zu verhindern. Das Zurücksetzen des `input_boolean` erfolgt in einer weiteren
Automation, die jeden Tag um Mitternacht läuft.

## NFC Tag

Eine Notification mit *sticky*-Flag kann man nur ausblenden, indem man eine Nachricht mit 
der Message `clear_notification` auslöst. Dabei muss man bei der ursprünglichen 
Notification und bei der *clear*-Message den gleichen `tag` vergeben.

```yaml
action:
  - service: notify.mobile_app_nokia_9
    data:
      message: clear_notification
      data:
        tag: pflanzenstatus
```

Diese *clear*-Message wird bei mir durch einen NFC-Tag ausgelöst, der an der Balkontür
klebt. Da das die einzige Möglichkeit ist die Notification loszuwerden, werde ich so 
gezwungen, in die Nähe der Pflanzen zu gehen. 

[1]: https://github.com/khronimo/MiFloraDB "MiFloraDB"
[2]: https://github.com/Yolandavdvegt/lovelace-flower-card "Lovelace Flower Card"
