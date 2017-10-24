---
layout: post
title:  "ESP8266: Wlan + Requests"
date:   2017-10-24
image: blog/esp8266helloworld.jpg
---

Die Vorteile des ESP8266 sind unter anderem, dass er direkt Wlan fähig ist. Daher wird keine weitere Hardware benötigt, um mit dem Internt zu kommunizieren. 
Wie man sich mit dem Wlan verbindet und einen Request abschickt ist mit Vs Code und dem Arduino Plugin sehr einfach.

# Was brauche ich?

- [Vs Code mit dem Arduino Plugin](/blog/vs-code-arduino-hello-world/) und dem [Plugin für den ESP8266](/blog/vs-code-esp8266-hello-world/) konfiguriert
- Hardware:
    - NodeMcu oder ein anderes ESP8266 Board
    - Breadboard
    - LED
    - Widerstand mit mind. 275 Ω
    - 2x Steckverbindungen

# Hardware Aufbau

Die Hardware wird, wie im [Beitrag für den ESP8266](/blog/vs-code-esp8266-hello-world/) verkabelt.
Hier noch einmal das passende Bild:
![Schematics NodeMCU Hello World](/assets/img/blog/esp8266helloworld.jpg)

# Mit dem Wlan verbinden

Der Esp8266 unterstüzt verschiedene Betriebsmodi. Mithilfe der *WiFi* Klasse ist es möglich sich mit dem Wlan zu verbinden.
Dazu wird *ESP8266WiFi.h* eingebunden und wichtig sind die Methoden *WiFi.status()* und *WiFi.begin(ssid, password)*.
Hier das Code-Beispiel:

{% highlight c %}
#include <ESP8266WiFi.h>

const char* ssid = "SSID";
const char* password = "PW";

void setup() {
  Serial.begin(115200); //Serielleausgabe starten -> Command Palette: Arudino: Open Serial Monitor
}

void loop() {
  connect();
  delay(1000);
}

void connect() {  
  delay(10);

  if(WiFi.status() == WL_CONNECTED) {
    Serial.println("Already connected...");
    return;
  }
  
  Serial.print("Connecting to ");
  Serial.println(ssid);
 
  WiFi.begin(ssid, password);
 
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println("WiFi connected");
}
{% endhighlight %}

Die Methode *connect()*, die in der *loop()*-Methode aufgerufen wird als erstes überprüft, ob sich bereits mit einem Wlan verbunden wurde.
Falls dies nicht der Fall ist, wird sich mit dem Wlan verbunden, dass in der Varibale *ssid* mit dem *password*.
Danach wird in 500ms Schritten getestet, ob die Verbidung erfolgreich ist.

# Get Request abschicken

Um einen Reuqest abzuschicken wird der Klasse *HTTPClient* aus *ESP8266HTTPClient.h* benötigt.
Der *HTTPClient* kann nicht nur Get, sondern auch [andere Methoden](https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266HTTPClient/src/ESP8266HTTPClient.h).
Hier das Codebeispiel, das zeigt, wie sich mit dem Wlan verbunden wird und dann eine Adresse aufgerufen und der Inhalt ausgegeben wird.

{% highlight c %}
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

const String serverAdress = "adress";
const char* ssid = "SSID";
const char* password = "PW";

void setup() {
  Serial.begin(115200); //Serielleausgabe starten -> Command Palette: Arudino: Open Serial Monitor
}

void loop() {
  connect();
  getStatus();
  delay(1000);
}

void connect() {
  delay(10);

  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("Already connected...");
    return;
  }

  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println("WiFi connected");
}

void getStatus() {
  HTTPClient http;
  http.begin(serverAdress);
  
  int httpCode = http.GET();

  if (httpCode > 0) {
    Serial.println(http.getString());
  }
  else {
    Serial.printf("Request failed: %s\n", http.errorToString(httpCode).c_str());
  }
  
  http.end();
}
{% endhighlight %}

Die *loop()*-Methode wurde um *getStatus()* erweitert. Diese führt ein *GET* auf die *serverAdress* aus.
Wenn der Aurfu erfoglreich war, wird über die serielle Ausgabe der empfangene Inhalt ausgegeben.
Falls ein Fehler auftritt (httpCode ist dann -1), wird der Fehler ausgegeben.

# Status LED abfragen

Nun können wir das Hello World Beispiel aus den ersten Beiträgen mit diesem zusammenführen.
Dazu muss nun die *serverAdress* ein Int zurückgeben. Wenn es eine *1* zurück gibt, wird die LED angeschaltet, falls nicht, dann wird sie ausgeschaltet.
Hier reicht es, wenn eine Textdatei auf einem Server ist, die einfach eine Zahl enthält.

{% highlight c %}
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>

const String serverAdress = "adress";
const char* ssid = "SSID";
const char* password = "PW";

void setup() {
  pinMode(13, OUTPUT); //GPIO 13 = Pin D7
  Serial.begin(115200); //Serielleausgabe starten -> Command Palette: Arudino: Open Serial Monitor
}

void loop() {
  connect();
  getStatus();
  delay(1000);
}

void connect() {
  delay(10);

  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("Already connected...");
    return;
  }

  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  
  Serial.println("WiFi connected");
}

void getStatus() {
  HTTPClient http;
  http.begin(serverAdress);
  
  int httpCode = http.GET();

  if (httpCode > 0) {
    String answer = http.getString();
    Serial.println(answer);
    if(answer.toInt() == 1)
      digitalWrite(13, HIGH);
     else
      digitalWrite(13, LOW);
  }
  else {
    Serial.printf("Request failed: %s\n", http.errorToString(httpCode).c_str());
  }
  
  http.end();
}
{% endhighlight %}

Im *setup()* wird nun der Pin für die LED konfiguriert.
Die Methode *getStatus()* versucht nun die erhaltene Antwort als Integer zu interpretieren und schaltet dementsprechend die LED.

# Fazit

Der ESP8266 ermöglicht es günstig und schnell IoT Geräte zu entwickeln. Wenn man für das letzte Beispiel einfach eine Api nimmt, kann man darüber die LED steuern. Wenn man statt einer LED ein Relais verwendet kann man auch Geräte mit einer größeren Spannung, wie eine Zimmerlampe, steuern.
Auch könnte der ESP8266 mit z.B. einem *POST* Daten eines Sensors verschicken.