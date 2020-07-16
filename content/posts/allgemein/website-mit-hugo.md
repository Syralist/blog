---
title: "Website mit Hugo erstellen -- Einstieg"
date: 2020-05-19T20:20:55.280Z
draft: false
toc: true
tocNum: false
tags:
- Hugo
- Website
---

Dieser Blog wird mit [Hugo][1] erstellt. In diesem Beitrag erzähle ich was es damit auf sich hat und wie ich dazu gekommen bin.

## Was ist Hugo?

Hugo ist nach eigener Aussage das weltweit schnellste Framework für die Erstellung statischer Webseiten.
Hugo ist in **Go** programmiert und verfügbar für alle gängigen Betriebssysteme. 

Die Beiträge werden in **Markdown** geschrieben und dann &bdquo;kompiliert&ldquo; um die HTML Seiten zu erstellen. Das Aussehen wird dabei über **Themes** festgelegt.

Damit ist der Workflow mit dem von $\LaTeX$ vergleichbar.

## Warum Hugo?

An den meisten Content-Management-Systemen wie Wordpress, Drupal, Typo3, etc. hat mich immer gestört, dass man dafür ein mehr oder weniger mächtiges Backend aufsetzen muss.
Man braucht immer PHP oder Python und eine Datenbank, auch wenn man eigentlich nur einen einfachen Blog schreiben möchte.

Im [Self Hosted][2] Podcast habe ich dann von Hugo gehört. 

## Wie geht es los?

### Vorbereitung
Für Hugo gibt es diverse Installationsmethoden. Auf meinem Windowsrechner habe ich das Programm als ZIP heruntergeladen, unter Linux habe ich es mit Snap installiert. Details sind auf der [Hugo Webseite][3] zu finden.

Es ist nicht zwingend erforderlich, aber es erleichtert einige Vorgänge, wenn man Git benutzt. Daher gehe ich in diesem Artikel davon aus, dass ihr Git installiert habt und über einen Github Account verfügt.

### Seite anlegen
Das Grundgerüst der Seite legt man mit dem folgenden Kommando auf der Konsole an:

```console
hugo new site meinblog
```

Hugo erzeugt damit ein Unterverzeichnis mit dem Namen `meinblog` und diesem Inhalt:

```console
archetypes/
config.toml
content/
data/
layouts/
static/
themes/
```

Als nächstes sollte man sich ein Theme aus der [Hugo Theme Liste][4] aussuchen. Ich habe einige ausprobiert und mich schließlich für das MemE Theme entschieden. Die Installation funktioniert am einfachsten mit Git wie im [README][5] beschrieben.
Die `config.toml` muss man an seine eigenen Bedürfnisse anpassen. Dazu folgt noch ein weiterer Artikel.

### Artikel anlegen
Artikel legt man im Verzeichnis `content` als .md-Dateien an. Dabei kann man optional Unterordner als Kategorien anlegen. Man kann die Datei manuell anlegen oder diesen Hugo-Befehl dafür benutzen:

```console
hugo new posts/my-first-post.md
```

Der Vorteil gegenüber dem manuellen Anlegen ist, dass gleich ein Frontmatter mit angelegt wird. Das ist der Konfigurationsbereich des Artikels, wo Aussehen und Verhalten angepasst werden kann. Auch dazu folgt noch ein weiterer Artikel.

In diesem Zustand kann man jetzt schon den integrierten Server starten und die (leere) Seite anzeigen:

```console
hugo server -D
```

Die Option `-D` an dieser Stelle ist wichtig, damit auch Artikel die als Draft (also Entwurf) gekennzeichnet sind erzeugt und angezeigt werden.

Jetzt kann man seine Artikel schreiben. Wenn der Server im Hintergrund läuft, wird die Seite automatisch neu erzeugt und im Browser aktualisiert, sobald man eine Datei ändert und speichert.

### Seite veröffentlichen
Wenn man fertig mit seinen Artikeln ist ruft man `hugo` ohne weitere Optionen auf. Damit wird im Unterverzeichnis `public` die fertige Webseite mit allen nötigen Dateien erzeugt. Dieses Verzeichnis kann jetzt auf jedem Webserver gehostet werden. Der Workflow für das Hosten als github.io Seite wird auf der [Hugo-Webseite][6] gezeigt.

[1]: https://gohugo.io/ "Hugo"
[2]: https://selfhosted.show/ "Self-Hosted Podcast"
[3]: https://gohugo.io/getting-started/installing "Hugo Installation"
[4]: https://themes.gohugo.io/ "Hugo Themes List"
[5]: https://github.com/reuixiy/hugo-theme-meme "MemE Readme"
[6]: https://gohugo.io/hosting-and-deployment/hosting-on-github/ "Hosting-on-Github"