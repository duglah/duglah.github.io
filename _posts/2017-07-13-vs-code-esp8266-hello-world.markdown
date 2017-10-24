---
layout: post
title:  "Visual Studio Code + ESP8266"
date:   2017-07-30
image: blog/esp8266helloworldheader.jpg
---

Ein Arduino bietet viele Möglichkeiten mit ihm zu kommunizieren. Jedoch ist es oft nicht einfach die passenden Sensoren zu finden, diese richtig anzusteuern und zu verkablen. In den meisten Fällen will man einfach nur einen Webrequest absetzen um eine Statusänderung zu senden oder abzufragen. Für solche Fälle bietet sich der ESP8266 an!

# Was ist ein ESP8266?

Ein ESP8266 ist, wie ein Arduino, ein Microprozessor. Der Vorteil gegenüber einem Arduino ist, dass er eine Wlan Schnittstelle, mehr Prozessorleistung und Speicher hat.
Ich kann empfehlen die Version *NodeMcu* des Chips zu kaufen. Es gibt ihn zum Beispiel bei [Ebay](https://www.ebay.de/sch/i.html?_nkw=nodemcu+v3+esp8266) oder bekannten chinesischen Shops, wie z.B. [Aliexpress](https://www.aliexpress.com/wholesale?SearchText=nodemcu+v3+esp8266) oder [Banggood](https://www.banggood.com/search/nodemcu-v3-esp8266.html). Diese Version hat die Stromversorgung schon geregelt und die Pins können direkt auf einem [Breadboard](https://de.wikipedia.org/wiki/Steckplatine) gesteckt werden.

Es ist möglich Programme für den Chip u.a. in [Python](https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/intro.html), [Lua](https://learn.adafruit.com/diy-esp8266-home-security-with-lua-and-mqtt/programming-the-esp8266-with-lua) oder in C zu schreiben.
Ich möchte euch gerne zeigen, wie man mit Visual Studio Code und dem Arduino Plugin Programme für ein ESP8266 schreiben kann.

# VS Code + Arduino Plugin + ESP8266

Wie Visual Studio Code und das Arduino Plugin installiert und für den Arduino konfiguriert werden habe ich bereits [hier](https://philippmanstein.com/blog/vs-code-arduino-hello-world/) erklärt.
Nun wollen wir es für den ESP8266 konfigurieren.

## ESP8266 in VS Code konfigurieren

Wenn man die Command Palette mit ⇧⌘P (Ctrl+Shift+P) öffnet und `Arduino: Board Config` aufruft, sieht man keinen ESP8266 in der Boards-Liste.
Falls der ESP8266 doch erscheint und beim Upload folgender Fehler kommt, kann der Fehler mit dieser Anleitung auch behoben werden:

{% highlight console %}
[Starting] Upload sketch - hello_world.ino
Loading configuration...
Initializing packages...
Preparing boards...
Error: esp8266: Unknown package
[Error] Exit with code=3
{% endhighlight %}

Damit er dort auftaucht, muss man eine neue Paketquelle hinzufügen. 
Dies geht, indem man in der `Arduino: Board Config` auf *Additional URLs* klickt. 
Nun öffnen sich die User Settings. Dort kann nun `"arduino.additionalUrls": "https://github.com/esp8266/Arduino/releases/download/2.3.0/package_esp8266com_index.json"` hinzugefügt werden.


Alternativ kann die Paktetquelle auch über die Arduino IDE hinzugeüfgt werden.
Dazu muss die Arduino IDE gestarten werden und dort die Einstellungen geöffnen werden.
Dort gibt es den Menüpunkt *Additional Boards Manager URLs*, in dem die Url *https://github.com/esp8266/Arduino/releases/download/2.3.0/package_esp8266com_index.json* eingetragen wird. 
![Additional Boards Manager URLs](/assets/img/blog/arduinoideaddsources.jpg)

Nun kann in der Arduino IDE unter *Tools > Board: "XYZ" > Boards Manager* nach dem *ESP8266* gesucht wrden und das Board installiert werden.
![Arduino IDE Boardmanger](/assets/img/blog/arduinoideboardmanager.jpg)

Jetzt kann in VS Code im `Arduino: Board Config`-Menü der ESP8266 ausgewählt und konfiguriert werden. In meinem Fall ist es *NodeMCU 0.9 (ESP-12 Module)*.
Je nach Modul müssen die Parameter angepasst werden. Der Punkt *Upload Speed* ist die Baudrate. Hier meine Konfiguration:
![NodeMCU 0.9 (ESP-12 Module) Config](/assets/img/blog/esp8266vscodeboardconfig.jpg)

Um schnell zwischen verschiedenen Boards zu wechseln, die man konfiguriert hat, kann man in der Command Palette `Arduino: Change Board Type` aufrufen und dort das passende Board auswählen.

## Hello World

Nun erstellen wir wieder das Blink Beispiel:

{% highlight c %}
void setup() {
  pinMode(13, OUTPUT); //GPIO 13 = Pin D7
}

void loop() {
  digitalWrite(13, HIGH);
  delay(1000);
  digitalWrite(13, LOW);
  delay(1000);
}
{% endhighlight %}

Und laden es auf den NodeMcu hoch.

## LED verkabeln

Wie wir sehen, sehen wir nichts 😅. Da das NodeMcu Board, im Gegensatz zum Arduino, keine eingebaute LED hat müssen wir eine LED mit zwei Kabeln und einem Widerstand mit dem Board wie folgt verbinden:
![Schematics NodeMCU Hello World](/assets/img/blog/esp8266helloworld.jpg)

Pin D7, der im Code [GPIO](https://de.wikipedia.org/wiki/Allzweckeingabe/-ausgabe) 13 entspricht, wird mit der Anode, dem langen Beinchen, der LED verbunden. Dazwischen kommt ein Widerstand mit mindestens 275 Ω (Wir wollen nicht mehr als 12mA von einem Pin ziehen und der ESP8266 liefert 3,3V ([Ohmsches Gesetz](https://de.wikipedia.org/wiki/Ohmsches_Gesetz))). Ein höherer Widerstand geht natürlich immer, guckt einfach was ihr da habt. Die Kathode, das kurze Beinchen, der LED wird mit Ground verbunden.

Hier ein Bild der kompletten Pin Belegung vom [NodeMCU Repository](https://github.com/nodemcu/nodemcu-devkit-v1.0):
[![NodeMCU DevKit Pins](https://raw.githubusercontent.com/nodemcu/nodemcu-devkit-v1.0/master/Documents/NODEMCU_DEVKIT_V1.0_PINMAP.png)](https://github.com/nodemcu/nodemcu-devkit-v1.0)

# Fazit

![Hello World!](/assets/img/blog/esp8266helloworldblink.gif)

Schade, dass das Arduino Plugin noch nicht alle Möglichkeiten bietet, wie die Arduino IDE. Aber das Plugin ist ja auch noch eine Preview und man kann gespannt sein, was es noch für Möglichkeiten bieten wird.

Mehr Infos über Arduino für den ESP8266 und die neusten Versionen gibt es [hier](https://github.com/esp8266/Arduino).