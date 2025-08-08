---
layout: project
title: Ambient Light Monitoring using Lorfi-WiFi
---

# Description

This guide shows how to use the TEMT6000 ambient light sensor with the Lorfi-WB board to monitor light levels accurately. The sensor mimics human eye sensitivity and works well in varying light conditions, except in very dark environments. It filters out infrared and UV light for more precise visible light readings.

With Lorfi-WB’s built-in Wi-Fi and LoRa, you can send real-time light data to local or cloud platforms—perfect for smart lighting, indoor automation, or remote monitoring projects.

# Specification

- Supply Voltage: +5VDC 50mA
- Size: 36.5*16mm
- Weight: 4g

## Hardware Setup

|     Module    |   Lorfi WB  |
|---------------|-------------|
| Signal        | GPIO2       |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of the sensor to the Digital Input GPIO2 on the Lorfi board, connect the GND pin to GND port, VCC pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Sensors\21.png" alt="Centered Image" width="900" />
</p>

#### Using directly Lorfi-Wifi

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

First setup the MQTT configuration of your device. Click <a href="/docs/Software_Guide.html"> to view the proper setup for ThingsPH.

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
const char* publish_topic = "100003";
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

  int light_Value = analogRead(Sensor);
  Serial.println(light_Value);
  delay(100);

  // Create JSON payload
  StaticJsonDocument<256> doc;
  doc["hardware_serial"] = "100003";
  JsonObject payload_fields = doc.createNestedObject("payload_fields");
  payload_fields["light_value"] = light_Value;

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

Go to the dashboard section of ThingsPH. Click create dashboard and input your dashboard name ex: Ambient Light Monitoring.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\27.jpg" alt="Centered Image" width="900" />
</p>

After creating your own dashboard. Create a panel within the dashboard. Configure your panel by choosing your specific configuration in the Data Source.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\28.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\29.jpg" alt="Centered Image" width="900" />
</p>

## Expected Output

After setting up your dashboard this will be the expected outcome in your platform(thingsPH) and Arduino IDE.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\30.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\31.jpg" alt="Centered Image" width="900" />
</p>

# Local Web Server

## Sample Code

```c
#include <WiFi.h>

// === WiFi Credentials ===
const char* ssid = "YOUR SSID";
const char* password = "YOUR SSID PASSWORD";

// === Web Server Port ===
WiFiServer server(80);

// === Ambient Light Sensor Pin ===
#define LIGHT_SENSOR_PIN A0

void setup() {
  Serial.begin(115200);
  pinMode(LIGHT_SENSOR_PIN, INPUT);

  // Connect to Wi-Fi
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  // Wi-Fi Connected
  Serial.println("\nWiFi connected.");
  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  // Start Web Server
  server.begin();
}

void loop() {
  int lightValue = analogRead(LIGHT_SENSOR_PIN);  // Read ambient light level
  Serial.print("Ambient Light Reading: ");
  Serial.println(lightValue);

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
          // Send HTML response
          client.println("HTTP/1.1 200 OK");
          client.println("Content-type:text/html");
          client.println();

          client.println("<!DOCTYPE html><html>");
          client.println("<head><meta charset='utf-8'><title>Ambient Light Sensor</title></head>");
          client.println("<body><h2>ESP32 Ambient Light Monitor</h2>");
          client.printf("<p>Analog Light Value: <strong>%d</strong></p>", lightValue);
          client.println("</body></html>");
          break;
        }
      }
    }

    client.stop();
    Serial.println("Client disconnected.");
  }

  delay(1000);  // Optional: Update every 1 second
}
```

## Expected Output

# Network Server (Access Point)

## Sample Code

### Lorfi-WB #1: Access Point

```c
#include <WiFi.h>

const char* ssid = "ESP_Hub";
const char* password = "12345678";

WiFiServer server(80);

// Use pin A0 for analog light sensor
#define LIGHT_SENSOR_PIN A0

void setup() {
  Serial.begin(115200);
  pinMode(LIGHT_SENSOR_PIN, INPUT);

  // Start the access point
  WiFi.softAP(ssid, password);
  Serial.println("Access Point started");

  Serial.print("AP IP address: ");
  Serial.println(WiFi.softAPIP());

  server.begin();
}

void loop() {
  int lightValue = analogRead(LIGHT_SENSOR_PIN); // Read from sensor
  Serial.print("Ambient Light: ");
  Serial.println(lightValue);

  WiFiClient client = server.available();
  if (client) {
    Serial.println("Client connected");

    // Wait for HTTP request
    while (client.connected()) {
      if (client.available()) {
        String req = client.readStringUntil('\n'); // Read until newline
        Serial.println("Request: " + req);

        // Send plain-text response
        client.println("HTTP/1.1 200 OK");
        client.println("Content-Type: text/plain");
        client.println("Connection: close");
        client.println();
        client.print(lightValue); // Send light value

        break;
      }
    }

    delay(1);
    client.stop();
    Serial.println("Client disconnected");
  }

  delay(1000); // Optional: slow down the loop
}

```

## Sample Code

### Lorfi-WB #2: Client

```c
#include <WiFi.h>
#include <HTTPClient.h>

const char* ssid = "ESP_Hub";               // Must match the AP
const char* password = "12345678";
const char* serverIP = "192.168.4.1";       // Default IP of ESP AP

void setup() {
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  Serial.print("Connecting to ESP_Hub");

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  Serial.println("\nConnected to ESP_Hub");
}

void loop() {
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;

    String url = "http://" + String(serverIP); // Target the AP's IP
    http.begin(url);
    int httpCode = http.GET();

    if (httpCode == 200) {
      String payload = http.getString();
      Serial.print("Received Ambient Light: ");
      Serial.println(payload);
    } else {
      Serial.print("GET request failed. HTTP code: ");
      Serial.println(httpCode);
    }

    http.end();
  } else {
    Serial.println("WiFi not connected");
  }

  delay(5000); // Repeat every 5 seconds
}
```

## Expected Output

