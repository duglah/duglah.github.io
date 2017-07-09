---
layout: post
title:  "Visual Studio Code + Arduino"
date:   2017-07-09
image: blog/arduinovsstudiomac.jpg
---

Visual Studio Code überzeugt nicht nur wegen seiner Einfachheit und Crossplattform-Fähigkeit, sondern auch seit kurzem gibt es ein Arduino Plugin von Microsoft. Jetzt können auch Projekte für den beliebten Micro Controller und seine Klone in einer komfortablen IDE realisiert werden.

# Installation VS Code

Visual Studio Code kann für Mac, Windows und Linux [hier](https://code.visualstudio.com/download) heruntergeladen werden. Für den Mac entpackt ihr einfach die Zip und zieht die entpackte Visual Studio Code.app in eure Programme.

# Installation Arduino Plugin

## Download Arduino IDE

Die Arduino IDE wird nicht komplett abgelöst, sie wird weiterhin benötigt, daher nicht deinstallieren oder von [hier](https://www.arduino.cc/en/main/software#download) downloaden und installieren, falls deine Version vor 1.6.x ist.
Ich hatte die Version 1.6.12, musste jedoch auch updaten. Sollte bei euch auch folgender Fehler beim Uploaden erscheinen, solltet ihr auch updaten:

{% highlight console %}
[Starting] Upload sketch - Blink.ino
java.lang.NullPointerException
	at processing.app.BaseNoGui.selectSerialPort(BaseNoGui.java:1088)
	at processing.app.helpers.CommandlineParser.parseArgumentsPhase1(CommandlineParser.java:143)
	at processing.app.Base.<init>(Base.java:273)
	at processing.app.Base.guardedMain(Base.java:219)
	at processing.app.Base.main(Base.java:132)
[Error] Exit with code=255
{% endhighlight %}

## Plugin

Plugins werden über den Extension Marketplace heruntergeladen.
Entweder öffnet ihr die Command Palette mit ⇧⌘P (Ctrl+Shift+P) und gebt `ext install vscode-arduino` ein.
Oder ihr öffnet den Marketplace aus der Seitenleite oder mit ⇧⌘X geöffnet (Crt+Shift+X).
Dort sucht ihr dann nach *Arduino* und installiert die Version von Microsoft.

<img src="{{ '/assets/img/blog/arduinovsstudiomac.jpg' | prepend: site.baseurl }}" alt="">

Und bestätigt den Dialog, wenn Abhänigkeiten installiert werden sollen.
Startet nun die VS Code neu.

# Hello World

Nun möchte ich ein Programm auf den Arduino bringen. Dafür bietet sich am besten das *Hello World* in der Elektronik-Welt an: Eine blinkende LED!

## Hello World erstellen

Hierzu wird eine neue Datei mit dem folgendem Inhalt erstellet:

Code:
{% highlight c %}
void setup() {
  pinMode(13, OUTPUT);
}

void loop() {
  digitalWrite(13, HIGH);
  delay(1000);
  digitalWrite(13, LOW);
  delay(1000); 
}
{% endhighlight %}

Die Datei wird als *Blink.ino* gespeichert. Nun ändert sich das Highlighting von VS Code, da es weiß, dass **.Ino** Dateien zu Arduino gehören.

## Board auswählen

Als nächstes muss eingestellt werden, für welches Board das Porgramm kompilieret werden soll. Dazu kann unten, in der rechten Ecke auf *\<Select Board Type>* geklickt werden, oder mit ⇧⌘P (Ctrl+Shift+P) nach *Arduino: Board Config* gesucht werden und im Dropdown Menü das passenden Board ausgewählt werden.

In meinem Fall ist es *Arduino/Genuino Uno (Arduino AVR Boards)*.

## Serial Port

Nun sollte man den Arduino über ein USB Kabel mit dem Computer verbinden, falls er nicht schon vorher angeschlossen wurde.

In der rechten unteren Ecke, neben *\<Select Board Type>*, kann nun der Serial Port für den Arduino ausgewählt werden über den Punkt *\<Select Serial Port>* oder wieder über ⇧⌘P (Ctrl+Shift+P) suchen.

<img src="{{ '/assets/img/blog/arduinovsstudiomacserial.jpg' | prepend: site.baseurl }}" alt="">

Bei mir ist es */dev/cu.usbmodem1411*.

## Upload

Mit ⌥⌘U (Alternativ über die Command Palette nach *Upload* suchen) wird der Code kompiliert und auf den Arduino übertragen.
Nun solltet ihr die eingebaute LED auf dem Board blinken sehen.

<div style="text-align:center"><img src="{{ '/assets/img/blog/arduinohelloworld.gif' | prepend: site.baseurl }}" alt=""></div>

# Fazit

Das Plugin steckt noch in den Kinderschuhen. Code Vorschläge gibt es noch nicht,aber dafür schon mal eine Vervollständigung (Beides liefert die Arduino IDE nicht). Es wird sicherlich interessant, was hier noch in nächster Zeit kommen wird, besonders, wenn das [MXChip IoT Developer Kit](https://microsoft.github.io/azure-iot-developer-kit/) herauskommt.