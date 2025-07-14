---
layout: project
title: Sound Monitoring using Lorfi-WB
---

# Description

The analog sound sensor is mainly used to detect the intensity of surrounding sounds. It features a built-in potentiometer, allowing you to adjust the signal gain as needed. This sensor is great for creating interactive projects, such as a voice-activated switch.

# Specification

- Supply Voltage: 3.3V to 5V
- Interface: Analog
- Size: 30*20mm
- Weight: 4g

## Hardware Setup

|     Sensor    |   Lorfi WB  |
|---------------|-------------|
| Signal        | A0          |
| VCC           | 5V          |
| GND           | GND         |


Connect the S pin of module to Analog A0 of the Lorfi board, connect the negative pin to GND port, positive pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Sensors\12.png" alt="Centered Image" width="900" />
</p>

#### Using directly Lorfi-WB

You can find complete <a href="/docs/Hardware_Guide.html">Lorfi-WB IO pinout here</a>.

#### Using Lorfi Interface board

You can find complete guide for <a href="/docs/Hardware_Guide.html">Lorfi Interface here</a>.

## Software Setup

Lorfi-WB is built around the ESP32 chipset and supports both Wi-Fi and Bluetooth Low Energy (BLE). This must be added to Arduino IDE.

Here's the guide on <a href="/docs/Software_Guide.html">how to add ESP32 board on your Arduino IDE</a>.

Once ESP32 board is added, you can now select it from the board selection.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide4.png" alt="Centered Image" width="900" />
</p>

# Platform(ThingsPH)

## Setup

Input this configurations to the script that is correlated with it:

*INPUT IMAGE HERE*

## Sample Code

```c
#include <WiFi.h>
#include <PubSubClient.h>
#include <ArduinoJson.h>

// === WiFi Settings ===
const char* ssid = "PLDTHOMEFIBRpyh75";
const char* password = "PLDTWIFI939sf";

// === MQTT Settings (ThingsPH) ===
const char* mqtt_server = "mqtt.things.ph";
const int mqtt_port = 1883;
const char* mqtt_user = "68637fdd2ef34a81cc197c5d";
const char* mqtt_pass = "vE77J2xvmI8VHQTh0KacgrxP";

// MQTT Topics
const char* publish_topic = "100004";
const char* subscribe_topic = "200000";  // Optional

// === Pressure Sensor Pin ===
#define Sensor A0

// MQTT client setup
WiFiClient espClient;
PubSubClient client(espClient);

// === Connect to WiFi ===
void setup_wifi() {
  delay(100);
  Serial.println();
  Serial.print("Connecting to WiFi: ");
  Serial.println(ssid);

  WiFi.begin(ssid, password);
  int retries = 0;
  while (WiFi.status() != WL_CONNECTED && retries < 30) {
    delay(500);
    Serial.print(".");
    retries++;
  }

  if (WiFi.status() == WL_CONNECTED) {
    Serial.println("\nWiFi connected. IP address:");
    Serial.println(WiFi.localIP());
  } else {
    Serial.println("\nFailed to connect to WiFi.");
  }
}

// === MQTT Callback Function ===
void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message received [");
  Serial.print(topic);
  Serial.print("]: ");
  for (unsigned int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
}

// === MQTT Reconnect ===
void reconnect() {
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    if (client.connect("ESP32_Client", mqtt_user, mqtt_pass)) {
      Serial.println("connected");
      client.subscribe(subscribe_topic);
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" retrying in 5 seconds");
      delay(5000);
    }
  }
}

// === Setup ===
void setup() {
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
  pinMode(Sensor, INPUT);
}

// === Main Loop ===
void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  int output_value = analogRead(Sensor);
  Serial.println(output_value);
  delay(100);

  // Create JSON payload
  StaticJsonDocument<256> doc;
  doc["hardware_serial"] = "100004";
  JsonObject payload_fields = doc.createNestedObject("payload_fields");
  payload_fields["sound_value"] = output_value;

  // Serialize and publish JSON
  char jsonBuffer[512];
  serializeJson(doc, jsonBuffer);
  client.publish(publish_topic, jsonBuffer);

  Serial.println("Data published:");
  Serial.println(jsonBuffer);

  delay(10000);  // Send data every 10 seconds
}
```

## Dashboard Creation

Go to the dashboard section of ThingsPH. Click create dashboard and input your dashboard name ex: Noise Monitoring.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\33.png" alt="Centered Image" width="900" />
</p>

After creating your own dashboard. Create a panel within the dashboard. Configure your panel by choosing your specific configuration in the Data Source.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\34.png" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\35.png" alt="Centered Image" width="900" />
</p>

## Expected Output

After setting up your dashboard this will be the expected outcome in your platform(thingsPH) and Arduino IDE.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\36.png" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\36.png" alt="Centered Image" width="900" />
</p>

# Web Server

## Sample Code

```c
#include <WiFi.h>

// === Replace with your WiFi credentials ===
const char* ssid = "YOUR SSID";
const char* password = "YOUR SSID PASSWORD";

// === Web Server Port ===
WiFiServer server(80);

#define SOUND_SENSOR_PIN A0
int soundValue = 0;

void setup() {
  Serial.begin(115200);

  // Connect to Wi-Fi
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nWiFi connected.");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  // Start the server
  server.begin();
}

void loop() {
  // Read analog value from sound sensor
  soundValue = analogRead(SOUND_SENSOR_PIN);
  Serial.print("Sound Sensor Value: ");
  Serial.println(soundValue);

  // Handle incoming web client
  WiFiClient client = server.available();
  if (client) {
    Serial.println("New Client.");
    String currentLine = "";

    while (client.connected()) {
      if (client.available()) {
        char c = client.read();
        Serial.write(c);
        currentLine += c;

        if (c == '\n') {
          // End of HTTP request
          client.println("HTTP/1.1 200 OK");
          client.println("Content-type:text/html");
          client.println();

          // Webpage HTML content
          client.println("<!DOCTYPE html><html>");
          client.println("<head><meta charset='utf-8'><title>ESP32 Sound Sensor</title></head>");
          client.println("<body>");
          client.println("<h2>ESP32 Sound Sensor Reading</h2>");
          client.printf("<p>Analog Value: <strong>%d</strong></p>", soundValue);
          client.println("</body></html>");

          break;
        }
      }
    }

    delay(1);
    client.stop();
    Serial.println("Client disconnected.");
  }

  delay(1000); // Read and serve every 1 second
}
```

## Expected Output

# Network Server (Access Point)

## Sample Code

## Expected Output
