---
title: "My Smarthome Setup"
date: 2020-03-25T18:17:45.379Z
draft: false
toc: true
tocNum: false
tags:
- Smarthome
- Home Assistant
- Zigbee
- Homematic
- Raspberry Pi
---
# My Smarthome Setup

In this post I want to give a brief overview of my Smarthome Setup. In later posts I will go into more detail.

## Central Hub
As my Smarthome Hub I'm running [Home Assistant][1] on a Raspberry Pi 3B+. I'm using the native Version with HassOS and the system boots directly from an SSD drive. There is a Conbee stick attached via USB for Zigbee connectivity. Additionally I run another Rasberry Pi 1 with [RaspberryMatic][3] to connect my Homematic components.

My router is a FritzBox 7590 supported by a FritzRepeater 1750E for connecting WiFi components.

Both the Fritzbox and the main Raspberry are buffered by an uninterruptible power supply, because the fuse regularly blows in my flat when I switch on my power strip.

### Hardwarelist
+ **Raspberry Pi 3B+** with 2.4A power supply.
+ Intenso Portable **SSD with 128GB**. Additional power supply with 2.4A and USB Y-cable.
+ **Conbee Stick** by [Dresden Elektronik][2]
+ Uninterruptible power supply **APC BX700U-GR**
+ Raspberry Pi 1 with **HM-MOD-RPI-PCB** by [ELV][4]

### Software
When I moved to the SSD drive I decided against setting up for example Rasbian, but used the native installation. That way there is no extra operating system to maintain, but everything can be managed from the Home Assistant interface.
Parallel to Home Assistant there are of course a bunch of Addons on the Raspberry:

+ **ADB Bridge** for Android TV control
+ **APC USB Daemon** for reading the UPS
+ **Duck DNS** for secure remote access via internet
+ **ESPhome** for DIY components
+ **File Editor** to edit the configuration from within the browser
+ **Hass.io Google Drive Backup** for automated back up
+ **MariaDB** as replacement for the SQLite history database
+ **Mosquitto broker** for MQTT
+ **Node-RED** for automations
+ **Samba-Share** for access from other computers
+ **TasmoAdmin** for managing the Tasmota devices
+ **Terminal & SSH** for low level access to the system
+ **chrony** as local NTP server
+ **deCONZ** for managing the Zigbee components

Of course [HACS][5] (Home Assistant Community Store) is also installed for custom components, lovelace plugins, and themes.

## Components
The advantage of Home Assistant is that components from a wide variety of manufacturers and with a wide variety of technologies can be integrated. Accordingly, various sensors and actuators are distributed in the apartment.

### Sensors
Temperature, humidity and air pressure are recorded in almost every room. On the one hand, the Aqara WSDCGQ11LM multi-sensors via Zigbee are used, on the other hand, I have connected ESP8266 with BME280 sensors via ESPhome. 

Windows and doors are monitored with Aqara and Homematic door / window contacts. An IKEA Tradfri via Zigbee serves as the motion detector.

In the basement, a Sonoff POW monitors the power consumption of the washing machine.

### Light
The ceiling lighting is not yet smart in my apartment, but it usually stays off anyway because the light comes from decorative lights or indirect lighting.

The decorative lights almost all have a classic light source and are plugged into smart intermediate sockets. Most of them are from Teckin or Sonoff and flashed to Tasmota. Another comes from Osram and is connected via Zigbee.

There are also some directly connected lamps. A bulb from Eufy connected via their cloud service, an LED strip from Osram via Zigbee and two self-made LED lights connected via ESPhome.

### Heating
Our Viessmann heating system is connected via the cloud; Homematic thermostats are mounted on the radiators.

### Shutters
For the control of the electric roller shutters, I installed Shelly 2.5 flush-mounted actuators and connected them locally via MQTT.

### Interaction
Most components can be controlled via Alexa voice input, all components can be controlled via the web interface or companion app on the mobile phone. There are also physical buttons for some actions. In addition to the Aqara WXKG11LM Zigbee button, a self-made button via ESPhome is also used here.

## Automation
The automations are of course what makes the smart home smart. Here is just a brief overview:

In the living room, the light is switched on and off depending on the television. The radiators are linked to the windows and also regulate up or down according to the time of day.

When I go to bed, the computer and the NAS are shut down, the roller shutters are closed and the radiator is turned down.

There are notifications when the washing machine is ready, when the doorbell rings and when the garbage has to be put out.

---
That should suffice as a brief overview of my setup. As written above, soon followed by other articles with details on specific topics.

[1]: http://www.home-assistant.io "Home Assistant"
[2]: https://phoscon.de/de/conbee "Conbee Stick"
[3]: https://raspberrymatic.de/de/home/ "RaspberryMatic"
[4]: https://de.elv.com/elv-homematic-komplettbausatz-funkmodul-fuer-raspberry-pi-hm-mod-rpi-pcb-fuer-smart-home-hausautomation-142141?fs=2908134611&c=499 "ELV Shop"
[5]: https://hacs.xyz/ "HACS"