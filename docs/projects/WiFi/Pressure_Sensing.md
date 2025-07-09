---
layout: project
title: Thin-Film Pressure Sensor using Lorfi-WB
---

# Description

This guide demonstrates how to create a pressure sensing system using a thin-film pressure sensor and the Lorfi-WIFI board. The flexible sensor detects pressure through resistance changes, which are converted into electrical signals. With built-in waterproofing and real-time data transmission via Wi-Fi, this setup is ideal for applications like smart surfaces, wearables, and environmental monitoring.

# Specification

- Working Voltage：DC 3.3V—5V
- Range：0-10KG
- Thickness：＜0.25mm
- Response Point：＜20g
- Repeatability：＜±5.8%（50% load）
- Accuracy：±2.5%（85% range interval）
- Durability：＞100 thousand times
- Initial Resistance：＞100MΩ(no load)
- Response Time：＜1ms
- Recovery Time：＜15ms
- Working Temperature：﹣20℃—60℃

## Hardware Setup

|     Module    |   Lorfi WB  |
|---------------|-------------|
| Signal        | AO          |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of the sensor to the Digital Input AO on the Lorfi board, connect the GND pin to GND port, VCC pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Sensors\22.png" alt="Centered Image" width="900" />
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
const char* publish_topic = "100002";
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

  // Read pressure value from analog pin
  float pressure_raw;
  int pressureValue = analogRead(Sensor);
  Serial.print("Pressure reading: ");
  Serial.println(pressureValue);

  if (pressureValue > 0) {
    pressure_raw = 1;
  } else {
    pressure_raw = 0;
  }

// Create JSON payload
StaticJsonDocument<256> doc;
doc["hardware_serial"] = "100002";
JsonObject payload_fields = doc.createNestedObject("payload_fields");
payload_fields["pressure_raw"] = pressure_raw;

// Serialize and publish JSON
char jsonBuffer[512];
serializeJson(doc, jsonBuffer);
client.publish(publish_topic, jsonBuffer);

Serial.println("Data published:");
Serial.println(jsonBuffer);

delay(5000);  // Send data every 10 seconds
}
```

## Dashboard Creation

## Expected Output

# Web Server

## Sample Code

```c
#include <WiFi.h>

// === Replace with your WiFi credentials ===
const char* ssid = "YOUR SSID";
const char* password = "YOUR SSID PASSWORD";

// === Web Server Port ===
WiFiServer server(80);

#define SENSOR_PIN 2 

void setup() {
  Serial.begin(115200);
  pinMode(SENSOR_PIN, INPUT);

  // Connect to Wi-Fi
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  // Connected
  Serial.println("\nWiFi connected.");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  // Start the server
  server.begin();
}

void loop() {
  // Read analog value from pressure sensor
  int pressureValue = analogRead(SENSOR_PIN);
  Serial.print("Pressure Sensor Reading: ");
  Serial.println(pressureValue);

  // Listen for incoming clients
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
          // End of HTTP request — respond with HTML
          client.println("HTTP/1.1 200 OK");
          client.println("Content-type:text/html");
          client.println();

          // HTML Content
          client.println("<!DOCTYPE html><html>");
          client.println("<head><meta charset='utf-8'><title>ESP32 Pressure Sensor</title></head>");
          client.println("<body><h2>ESP32 Pressure Sensor</h2>");
          client.printf("<p>Analog Reading: <strong>%d</strong></p>", pressureValue);
          client.println("</body></html>");

          break;
        }
      }
    }

    // Close connection
    delay(1);
    client.stop();
    Serial.println("Client disconnected.");
  }

  delay(1000);  // Optional: delay between loop iterations
}
```

# Network Server (Access Point)

## Sample Code
