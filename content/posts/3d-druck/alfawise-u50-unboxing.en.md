---
title: "Alfawise U50 -- Unboxing and First Impression"
date: 2020-07-16T11:09:19.468Z
draft: false
toc: true
tocNum: false
tags:
- 3D printing
- Prototyping
- Raspberry Pi
---

# My first 3D Printer

I learned about 3D printing a few years ago at the [ Hackerspace ][1]. Now I finally decided to buy my own printer.
After a video from [SchimmerMedia][2] I decided to buy the **Alfawise U50** for less than 200€ from [Gearbest][3].

# The Unboxing
## The Packaging

The printer arrives from China well protected in foam packaging.

![Packaged in foam](/images/2020-07-15-alfawise-u50-unboxing-01.jpg)

Underneath the foam is the already assembled bottom part of the printer and a box with screws and tools.

![Bottom part and tool box](/images/2020-07-15-alfawise-u50-unboxing-02.jpg)
![Bottom part and tool box](/images/2020-07-15-alfawise-u50-unboxing-03.jpg)

You need five(!) different hexagon socket wrenches for assembly. Fortunately all sizes are included.
In addition there is a scraper and a side cutter to remove the printed parts from the bed later and to trim them, as well as a Micro-SD card with USB adapter. On the card you find an old version of Cura including a configuration file, the assembly instructions in digital form and some example 3D models.

![Tools and screws](/images/2020-07-15-alfawise-u50-unboxing-04.jpg)

In the lower foam half you will find the remaining attachments, which are also already partly assembled.

![Attachments](/images/2020-07-15-alfawise-u50-unboxing-05.jpg)

These parts must now be assembled according to the instructions.

![Alle the parts](/images/2020-07-15-alfawise-u50-unboxing-06.jpg)

## The Assembly

The text of the manual is translated into reasonably understandable English, but the pictures are sometimes a bit small.

![The Manual](/images/2020-07-15-alfawise-u50-unboxing-07.jpg)

The first part to be assembled is the combination of X-axis drive and extruder drive. The construct has to be screwed to the X-axis strut and the screw has to be fiddled into the hole from behind.

![X-axis and extruder drive](/images/2020-07-15-alfawise-u50-unboxing-08.jpg)

The hotend is then simply fitted onto the rail and the second Z-axis guide is screwed on the other side.

![Hotend and Z-axis guide](/images/2020-07-15-alfawise-u50-unboxing-09.jpg)

The toothed belt that moves the hot end is fixed with printed clips. We will see how long they last.

![X-axis belt](/images/2020-07-15-alfawise-u50-unboxing-10.jpg)

The vertical aluminium profiles are fixed from below with one screw each. The right side is additionally fixed with a T-profile.

![T-profile](/images/2020-07-15-alfawise-u50-unboxing-11.jpg)

The left side is more fiddly again, because here you have to mount the attachment with the Z-axis drive.

![Z-axis drive](/images/2020-07-15-alfawise-u50-unboxing-12.jpg)
![Z-axis drive](/images/2020-07-15-alfawise-u50-unboxing-13.jpg)

The Y-axis drive is now mounted at the rear. Of course you need two different types of screws and the matching hexagon socket wrenches.

![Y-axis drive](/images/2020-07-15-alfawise-u50-unboxing-14.jpg)

The belt for the Y-axis is fixed under the printing bed with cable ties. But I had to loosen one side of it to connect the belt to the drive.
When fixing it again one of the printed clamps broke.

![Y-axis belt](/images/2020-07-15-alfawise-u50-unboxing-15.jpg)
![Y-axis belt](/images/2020-07-15-alfawise-u50-unboxing-16.jpg)

And this is what it looks like before the wiring.

![Completely assembled - rear view](/images/2020-07-15-alfawise-u50-unboxing-17.jpg)
![Completely assembled - front view](/images/2020-07-15-alfawise-u50-unboxing-18.jpg)

## The Wiring

All components are wired with plug-in connectors. 
Due to the different size, servo connection and limit switch connection cannot be confused. Due to the shape of the plug, reverse polarity is also basically impossible.

![Cable for X-axis and extruder](/images/2020-07-15-alfawise-u50-unboxing-19.jpg)

The individual cables are well labelled and combined in cable sleeves.

![Cable for Z-axis](/images/2020-07-15-alfawise-u50-unboxing-20.jpg)

The connection for the hot end is secured by a screw connection.

![Cabel for the hotend](/images/2020-07-15-alfawise-u50-unboxing-21.jpg)

The connection for the Y limit switch is the only one that requires some fiddling to connect.

![Cable for Y-axis](/images/2020-07-15-alfawise-u50-unboxing-22.jpg)
![Cable for Y-axis](/images/2020-07-15-alfawise-u50-unboxing-23.jpg)

This is how the assembly looks like with the wiring finished. The wires from the hotend are located very unfavorably and grind as you can see on the printing bed.
Meanwhile I have fixed them with cable ties to the top.

![Fully wired](/images/2020-07-15-alfawise-u50-unboxing-24.jpg)

## Switching on for the first time

After everything was wired, I could switch on for the first time and since no MagicSmoke™ has escaped anywhere, I obviously put everything together correctly.
Here you can already see the glued on printing bed.

![Switched on for the first time](/images/2020-07-15-alfawise-u50-unboxing-25.jpg)

The menu offers functions for manual control of all axes, temperature preselection and direct printing from the SD card.
The pre-installed firmware with version 195 had the problem that the speed of the axes could not be adjusted because the input field did not work. 
In a French [3D printing forum][4] I found a firmware update to version 217, which fixes the problem. Unfortunately the rar archive could not be unpacked under Linux, but only under Windows.

![Main menu](/images/2020-07-15-alfawise-u50-unboxing-26.jpg)

The extruder can only be moved via the menu when the hot end is heated up. I have not tested whether this is also blocked with external control.
Before the extruder drive, the filament is threaded through a chamber with a limit switch, which detects when the filament has run out.
After the extruder drive, the filament runs through a tube to the hot end. Here I had to cut a cable tie, because otherwise the filament could not be threaded to the end.

![Filament arrives at the bottom](/images/2020-07-15-alfawise-u50-unboxing-27.jpg)

When moving to the home position to adjust the printing bed, I noticed that the X limit switch is positioned too far outwards, so that the hotend is no longer above the printing bed.
Unfortunately the limit switch can not be adjusted, so I glued a temporary spacer to the switch lug.
In the meantime I have printed a suitable adapter for it. There will be a separate article on this later.

![Temporary spacer](/images/2020-07-15-alfawise-u50-unboxing-28.jpg)

# My first Impression

After I ordered the printer, I came across two blog posts at [China-Gadets][5] and [Drucktipps3D][6] that tore the U50 up quite a bit.
Of course I don't have that much experience with other printers but I'm quite satisfied with the construction and quality so far.

Gearbest advertises that you can assemble the printer in 20 minutes, I absolutely cannot confirm that. I took my time and took the photos in between, so it took me about two hours from opening the box to turning on the printer.
As already written above, it is a little fiddly in some places, but in general, the printer can be easily assembled.

I started the first print from my Linux laptop directly from Cura. I am using the latest version of Cura, took the default setting of the Alfawise U20 and only adjusted the size of the printing bed.

Meanwhile I have connected the printer to a Raspberry Pi with [Octoprint][7] and print from there. I haven't made any big prints of several hours and I'm still at the provided filament sample, but so far I haven't regretted the purchase.

[1]: http://www.hackerspace-bremen.de "Hackerspace Bremen"
[2]: https://www.youtube.com/watch?v=nQ3ZTZvvC3M "[Alfawise U20 One] 3D-Drucker mit XXL Druckplattform zum schmalen Preis [Review][4K]"
[3]: https://de.gearbest.com/3d-printers--3d-printer-kits/pp_009143693697.html?wid=1895967 "Alfawise U50 DIY FDM 3D Drucker"
[4]: https://www.lesimprimantes3d.fr/forum/topic/30009-mise-a-jour-firmware-u50/ "Les Imprimantes 3D Forum"
[5]: https://www.china-gadgets.de/test-alfawise-u50-3d-drucker/ "China-Gadgets.de"
[6]: https://drucktipps3d.de/ein-ue-ei/ "Drucktipps3D.de"
[7]: https://octoprint.org/ "Octoprint.org"

