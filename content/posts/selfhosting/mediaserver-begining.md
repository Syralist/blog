---
title: "Von &bdquo;Ich probier nur mal Jellyfin aus&ldquo; zu &bdquo;Ich baue einen Mediaserver&ldquo;"
date: 2023-10-31T13:41:07+01:00
draft: false
toc: true
tocNum: false
tags: 
    - Selfhosted
    - Mediaserver
    - Proxmox
    - Jellyfin
---

## Die Vorgeschichte

Eigentlich habe ich immer gedacht, wozu brauche ich einen eigenen Mediaserver?
Es gibt doch alles, was ich schauen möchte auf Netflix, Disney, Paramount oder sonst wo zu streamen. 

Allerdings bin ich dann immer mal wieder auf Filme gestoßen, die ich gerne schauen wollte, die aber nirgendwo gestreamt wurden.
Grade ältere Filme sind meist nicht verfügbar. Dann hab ich mir halt die BluRay gekauft. Also wozu ein Mediaserver?

Ich höre regelmäßig den [Selfhosted Podcast][1] und dort wird in fast jeder Folge über [Jellyfin][2] gesprochen.
Jellyfin ist eine Software, die selbst gehostete Filmdateien in einer Streaming-App auf fast jedes Endgerät bringen kann.
Man braucht erstmal keinen Server dafür, sondern kann es einfach unter Windows installieren und sofort loslegen. Also dachte ich mir, probier ich mal aus.

Natürlich braucht man Mediendateien, die Jellyfin ausspielen kann. In meiner Erinnerung war es immer so, dass BluRay rippen Hexenwerk und mächtig kompliziert ist.

Spoiler: Ist es natürlich nicht mehr.

Mit [MakeMKV][3] gibt es ein simples Werkzeug, um eine Sicherheitskopie eurer gekauften BluRays und DVDs zu machen. 
Man muss nur darauf achten, dass das Laufwerk auch für UHD zertifiziert ist, wenn man 4K Filme kopieren möchte.

Alles in allem war das erste Testsetup extrem einfach aufzusetzen!
Wenn man sich an die vorgeschlagene Namensgebung aus der Jellyfin Doku hält, muss man eigentlich nichts weiter tun, als die Dateien in einem Ordner abzulegen,
der Jellyfin bekannt gemacht wurde. Die Software holt sich dann Metadaten, Coverbilder etc. aus dem Internet und stellt alles in einer übersichtlichen App dar.

## Das Rabbithole

Nachdem ich dann einige Filme auf die Festplatte gezogen hatte, hab ich sehr schnell gemerkt: ich brauche mehr Speicher. Viel mehr Speicher!

Ich habe schon seit längerer Zeit immer mal wieder drüber nachgedacht, mein Synology NAS abzulösen. 
Das tut zwar noch seinen Zweck als Netzwerkspeicher für Dateien, ist aber nicht leistungsfähig genug, um mehr damit machen zu können.

Also nochmal zurück auf Anfang: braucht man einen Mediaserver? Nein! Will ich jetzt trotzdem einen bauen? Ja!

Und damit fing der unübersichtliche Teil der Recherche an. Denn grade beim Thema Selfhosting gibt es tausende Beiträge im Internet. 
Erster Anlaufpunkt für mich war die Seite [Perfect Media Server][4], die ich aus dem schon genannten [Selfhosted Podcast][1] kannte.
Leider ist auch diese Seite an einigen Stellen schon etwas veraltet, so dass ich viele Stunden versenkt habe auf diversen Seiten und Foren.

## Wie gehts weiter?

Mittlerweile habe ich Hardware bestellt, um einen Server aufzubauen, und mich auf einen grundlegenden Softwarestack festgelegt. 
Ich habe mich für Proxmox als Basis-Betriebssystem entschieden, weil ich glaube damit lässt sich der Server am besten erweitern. 
Jellyfin werde ich als LXC Container installieren und die Festplatten werde ich mit MergerFS zusammenfassen.

Ich werde versuchen möglichst viel hier im Blog festzuhalten, auch als Gedächtnisstütze für mich selbst.

[1]: http://selfhosted.show "Selfhosted Podcast"
[2]: http://jellyfin.org "Jellyfin Homepage"
[3]: https://www.makemkv.com/ "MakeMKV Homepage"
[4]: https://perfectmediaserver.com/ "Perfect Media Server Homepage"