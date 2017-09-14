---
layout: post
title:  "Autocomplete für Arduino/ESP8266 Projekte mit Visual Studio Code"
date:   2017-09-08
image: blog/vscode_autocomplete.png
---

Projekte für den Arduino oder ESP8266 mit Visual Studio Code und dem Arduino Plugin lassen sich noch einfacher mit Autocomplete und dem Einsehen von Definitionen realisieren.
Wie man diese Hilfen auch für Klassen aus anderen Packages, wie für den [ESP8266](/blog/vs-code-esp8266-hello-world/), aktiviert und konfigurieren kann wird im Folgenden erklärt.

# Projekt einrichten

Sobald man einen Ordner in Vscode öffnet kann man mit der Command Palette (⇧⌘P oder Ctrl+Shift+P)
*Arduino: Initialize* ausführen. Schneller findet man es mit *init*.
Nun kann der Name für das Sketch und das Board ausgewählt werden.
Außderm wird der Ordner *.vscode* in dem Projekt erstellt.
In diesem Ordner befinden sich die Dateien *arduino.json* und *c_cpp_properties.json*.

*arduino.json* enthält die Build Informationen, dort ist beschrieben welches Sketch für welches Board kompiliert werden soll.

*c_cpp_properties.json* enthält Informationen, welche Ordner nach Dateien für das Autocomplete durchsucht werden sollen.

# Plugins einrichten

Damit auch Plugins erkannt werden muss die *c_cpp_properties.json* bearbeitet werden.
Für den Mac und dem installierten Esp8266 Arduino Plugin sieht meine Konfiguration so aus:

{% highlight javascript %}
{
    "configurations": [
        {
            "name": "Mac",
            "includePath": [
                "/Users/pmanstein/Library/Arduino15/packages/arduino/hardware/avr/1.6.19/cores/arduino",
                "/Users/pmanstein/Library/Arduino15/packages/esp8266/hardware/esp8266/2.3.0/cores/esp8266",
                "/Users/pmanstein/Library/Arduino15/packages/esp8266/hardware/esp8266/2.3.0/libraries",
                "/Users/pmanstein/Documents/Arduino/libraries",
                "${workspaceRoot}"
            ],
            "browse": {
                "limitSymbolsToIncludedHeaders": false,
                "path": [
                    "/Users/pmanstein/Library/Arduino15/packages/arduino/hardware/avr/1.6.19/cores/arduino",
                    "/Users/pmanstein/Library/Arduino15/packages/esp8266/hardware/esp8266/2.3.0/cores/esp8266",
                    "/Users/pmanstein/Library/Arduino15/packages/esp8266/hardware/esp8266/2.3.0/libraries",
                    "/Users/pmanstein/Documents/Arduino/libraries",
                    "${workspaceRoot}"
                ]
            },
            "intelliSenseMode": "clang-x64"
        }
    ],
    "version": 2
}
{% endhighlight %}

Damit ist es auch möglich sich die Definitionen von Klasse, Funktionen und Variablen anzeigen zu lassen. Alle Optionen und Shortcuts kann man mit einem Rechtsklick auf eine Klasse, Funktion oder Variable anzeigen.

![Vscode peek definition](/assets/img/blog/vscode_peekdefinition.png)