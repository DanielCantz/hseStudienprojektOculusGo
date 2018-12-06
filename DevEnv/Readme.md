# Oculus Go Debugging Environment

In diesem Dokument werden verschiedene Varianten zur Einrichtung einer Debugging Umgebung vorgestellt.
- [Debugging mit Chrome DevTools](#head-dmcdt)
- [Debugging mit VS Code Live Server](#head-dmvscls)
- [Debugging mit Live Video Übertragung](#head-dmlvu)

## Entwickleroptionen Aktivieren

Um die Oculus Go auf einem verbundenen Computer debuggen zu können müssen zunächst die Entwickleroptionen auf der Oculus Go aktiviert werden.



## ADB Tool Installieren

For live video debugging adb tool is required. You may install adb tool with installation of Android Studio [link]

If you just want to install adb tools without android studio download and install [tools] from this [link]. After installation run [tool insaller] from admin cmd and run 
```
install platform-tools android28???
```


## 

# <a id="head-dmcdt"></a>Debugging mit Chrome DevTools

Zum Debugging mit Chrome DevTools sind keine weiteren Schritte als das Aktivieren der Entwickleroptionen nötig.
Lediglich eine Kabelverbindung zwischen Oculus Go und Computer ist notwendig.

Bevor sie den Debug modus starten öffnen sie Oculus Browser in der Oculus Go.

Um den Chrome Debug modus zu starten öffnen sie Chrome und dücken sie `strg+shift+i`. Anschließend drücken sie `...` >> `More Tools` >> `Remote Devices`.

In der neuen Ansicht sollten sie die Oculus Go [name] sehen zusammen mit einer Liste an geöffneten Oculus Browser Tabs.
Bei Auswahl eines dieser Tabs öffnet sich ein neues Fenster mit Ansicht dieses Tabs.

# <a id="head-dmvscls">Debugging mit VS Code Live Server

Mit Visual Studio Code ist es möglich mit Hilfe des Plugins Live Server eine Webseite auf einem gewünschten Port zu hosten. Solange sich der Computer mit VS Code Live Server und die Oculus Go im gleichen Netzwerk befinden, kann man diese Webseite auf der Oculus Go aufrufen.

## Live Server einrichten

Öffnen sie die Erweiterungen Ansich in VS Code und installieren sie Live Server. von Ritwick Dey.

Zum erleichterten Aufruf der Webseite in der Oculus Go ändern wir den Standard Port des Live Server auf Port 80:
Drücken sie `strg+,` um die Einstellungen zu öffnen. Suchen sie dort nach `Liver Server Port` und wählen sie dann `in "settings.json" bearbeiten`. Dort ersetzen sie dann den Standardport 5050 durch 80.

Damit der Port 80 nun auch nach außen hin, also für die Oculus Go, sichtbar ist müssen wir ihn noch in der Firewall freischalten. Dazu öffnen wir in den Systemeinstellungen die Firewall Einstellungen >> Erweiterte Einstellungen. Hier richten wir nun eine neue eingehende Regel ein:
> Regeltyp: Port
> 
> Typ: TCp
> 
> Portnr: 80
> 
> Verbindung zulassen
>
> Anwendung der Regel: je nach dem in welchem Netzwerk wir uns befinden müssen wir hier "Domäne", "Privat" und "Öffentlich" auswählen.
>
> Name: VS Code Live Server

Nach Erstellung der Regel müssen wir diese noch auf VS Code beschränken. Dazu Rechtklick auf die Regel >> Eigenschaften >> Tab Programme und Dienste. Hier können wir nun von das Programm VS Code auswählen.

In VS Code können sie nun mit eine html Seite im Live Server starten via Rechtsklick >> Open with Live Server.

## WLAN hosten für die Oculus Go

Da sich die Oculus Go nicht im Eduroam Wlan anmelden kann müssen wir, sofern wir uns nicht in einem anderen Wlan befinden ein zusätzliches Wlan Netzwerk zwischen Computer und Oculus Go einrichten. Wenn sich ihr Computer und die Oculus Go schon in einem gemeinsamen Netzwerk befinden fahren sie mit Punkt [Verbinden zum Live Server](#head-vzls) fort.

Öffnen sie die Windows Kommandozeile als Administrator und geben sie folgende Befehlen ein:
```
netsh wlan set hostednetwork mode=allow ssid=NETZWERKNAME key=PASSWORT
NETSH WLAN start hostednetwork
```

Nun können sie die Oculus Go mit dem neuen Netzwerk verbinden.

## <a id="head-vzls">Verbinden zum Live Server

Öffnen sie eine Kommandozeile und geben sie `ipconfig` ein. Suchen sie die ihnen zugehörige ipv4 Adresse für das Netzwerk indem sie und die Oculus Go sich befinden heraus.

In der Oculus Go könenn sie num im Oculus Browser zu der unter dem Live Server gehosteten Webseite navigieren:
> http://\<ipv4-Adresse>/\<Name-der-Html-Datei>

Wichtig ist hierbei mit `http:// ` zu beginnen. Sofern sich ihre html Datei nich im Top Level Verzeichnis in VS Code befindet müssen sie die übergeordneten Ordner mit in den http-Aufruf angeben.

# <a id="head-dmlvu">Debugging mit Live Video Übertragung

Vorbedingung zur Live Video Übertragung von der Oculus Go zum Debugging Pc ist, dass sie adb tools und entweder vlc Player oder mplayer installiert haben. Aus Performance Gründen wird mplayer empfohlen.

Schließen sie die Oculus Go über Kabel an den Computer an.

Öffnen sie eine Kommandozeile. Sofern sie die verwendeten Tools nicht in ihrer Path Variable haben müssen sie die Absoluten Pfade davon angeben.

Geben sie folgende Befehle ein:

```
adb devices //Dies sollte den Deamon service starten und ein device auflisten
```

Für mplayer:

```
adb -d exec-out "while true; do screenrecord --bit-rate=2m --output-format=h264 --size 800x600 -; done" | "C:\Program Files (x86)\MPlayer for Windows\mplayer.exe" -demuxer h264es -fps 60 -fs -
```

Der parameter -fps 60 bestimmt die Frames die mplayer pro Sekunde abspielt. Die Oculus Go zeigt in 60/72/75 fps an. Erhöhen oder Senken der fps Zahl in Mplayer kann die Verzögerung der Videoübertragung positiv oder negativ beeinflussen. Unsere Tests ergaben, dass ein fps Wert von 200 nahezu perfekte live Übertragung ermöglicht, 1000 oder sogar 10000 bringen keine bis kaum sichtbare Verbesserung.

Für vlc Player:

```
adb -d exec-out "while true; do screenrecord --bit-rate=2m --output-format=h264 --time-limit 180 -; done" | vlc --demux h264 -  vlc://quit 
```
