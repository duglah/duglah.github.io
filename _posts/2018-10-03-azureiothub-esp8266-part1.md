---
layout: post
title:  "Azure IoT Hub mit dem ESP8266 - Teil 1: Erstellen + Verbinden"
date:   2018-10-03
image: blog/azure_iothub.png
---

Azure bietet die Möglichkeit schnell, kostengünstig und einfach ein Hosting in der Cloud zu betreiben. 
Unter anderem durch das IoT Hub, mit dem Geräte Daten in die Cloud senden bzw. Daten aus der Cloud empfangen können.
Wie man den ESP8266 mit dem Azure IoT Hub verbindet, möchte ich in diesem Beitrag erklären.

# Azure IoT Hub erstellen

So legt man einen Azure IoT Hub an:

Zuerst loggt man sich auf dem Azure Portal unter [portal.azure.com](https://portal.azure.com) ein. Für einen Azure Account kann eine Kreditkarte nötig sein. 
Unter *Create a resource* kann man nach *IoT Hub* suchen oder unter dem Menü Punkt *Internet of Things* gibt es das *IoT Hub*. 
![Azure IoT Hub Resource](/assets/img/blog/azure_iothub.png)

Nun kann man die Einstellungen zu dem IoT Hub vornehmen. Ich empfehle, für ein Projekt eine Resourcen Gruppe zu erstellen, damit man eine Übersicht hat. 
Zum Experimentieren reicht der *F1: Free Tier* völlig aus. Hier sind meine Einstellungen:
![Azure IoT Hub Settings](/assets/img/blog/azure_iothub_settings.png)

# Gerät hinzufügen

Um die Arbeit mit dem Azure IoT Hub zu vereinfachen, gibt es das [Azure IoT Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) für Visual Studio Code. 
Nach der Installation des Plugins kann der IoT Hub Connection String in der Azure IoT Hub Ressource unter *Shared access policies* eingesehen werden. Das Plugin braucht dann den Connection String für den *iothubowner*.
![Azure IoT Hub Connection String](/assets/img/blog/azure_iothub_connectionstring.png)

Danach kann man über das Plugin unter anderem neue Geräte hinzufügen, Cloud-to-Device Nachrichten verschicken oder die Device-to-Cloud Nachrichten mitlesen. Dies geht auch über das Azure Portal.

Über *Create Device* kann man nun, entweder über das Plugin, was an der Seite angezeigt wird oder über die Command Palette ⇧⌘P (Ctrl+Shift+P) ein neues Gerät erstellen. 
Das neue Gerät erscheint in der Azure IoT Hub Devices Leiste. 
![Azure IoT Hub Plugin](/assets/img/blog/azure_iothub_plugin.png)

# ESP8266 verbinden

Nun möchten wir den ESP8266 mit dem Azure IoT Hub verbinden.

## Vorbereitung

Wie man Visual Studio Code für den Arduino konfiguriert, habe ich [hier](https://philippmanstein.com/blog/vs-code-arduino-hello-world/) erklärt.
Wie man Visual Studio Code für den ESP8266 konfiguriert, habe ich [hier](https://philippmanstein.com/blog/vs-code-esp8266-hello-world/) erklärt.
Wie man den ESP8266 mit einem Wlan verbindet, erkläre ich [hier](https://philippmanstein.com/blog/esp8266-connect-and-request/).

Um den ESP8266 mit dem Azure IoT Hub zu verbinden, müssen erst ein paar Bibliotheken installiert werden. Über die Command Palette ⇧⌘P (Ctrl+Shift+P) müssen mit *Arduino Library Manager* folgende Bibliotheken installiert werden:
- AzureIoTHub
- AzureIoTUtility
- AzureIoTProtocol_MQTT

## Code Beispiel

Ein Code-Beispiel für das Verbinden mit dem Azure IoT Hub:

{% highlight c %}
#include <ESP8266WiFi.h>
#include <AzureIoTHub.h>
#include <AzureIoTProtocol_MQTT.h>
#include <AzureIoTUtility.h>

const char *ssid = "SSIDNAME";
const char *password = "WIFIPW";

const char *connectionString = "CONNECTIONSTRING";

IOTHUB_CLIENT_LL_HANDLE iotHubClientHandle;

void setup()
{
    Serial.begin(115200);

    connect();

    initTime();

    iotHubClientHandle = IoTHubClient_LL_CreateFromConnectionString(connectionString, MQTT_Protocol);
    if (iotHubClientHandle == NULL)
    {
        Serial.println("Failed on IoTHubClient_CreateFromConnectionString.");
        while (1);
    }
}

void loop()
{
    // Process iot hub data
    IoTHubClient_LL_DoWork(iotHubClientHandle);

    delay(250);
}

void connect()
{
    if (WiFi.status() == WL_CONNECTED)
    {
        Serial.println("Already connected...");
        return;
    }

    Serial.print("Connecting to ");
    Serial.println(ssid);

    WiFi.begin(ssid, password);

    while (WiFi.status() != WL_CONNECTED)
    {
        delay(500);
        Serial.print(".");
    }

    Serial.println("WiFi connected");
    Serial.println(WiFi.localIP());
}

void initTime()
{
    time_t epochTime;
    configTime(0, 0, "pool.ntp.org", "time.nist.gov");

    while (true)
    {
        epochTime = time(NULL);

        if (epochTime == 0)
        {
            Serial.println("Fetching NTP epoch time failed! Waiting 2 seconds to retry.");
            delay(2000);
        }
        else
        {
            Serial.printf("Fetched NTP epoch time is: %lu.\r\n", epochTime);
            break;
        }
    }
}
{% endhighlight %}

## Erklärung:

Die Konstanten *ssid* und *password* werden für das Wlan benötigt. 

*connectionString* steht für den Connection String des Geräts. Er kann über das Portal oder bequem über das Azure IoT Toolkit Plugin kopiert werden. 

In der *setup*-Methode wird die serielle Kommunikation gestartet, sich mit dem Wlan verbunden, die aktuelle Zeit abgefragt - da der ESP8266 keine RTC hat - und sich mit dem Azure IoT Hub verbunden über das MQTT Protokoll.

In der *loop*-Methode werden Aufgaben für das Azure IoT SDK behandelt. Da keine Nachrichten geschickt oder empfangen werden, ist diese Methode im Moment nicht relevant.

## Nächster Teil:

Im nächsten Teil möchte ich erklären, wie man Nachrichten von dem Gerät an das Azure IoT Hub sendet (Device-to-Cloud).