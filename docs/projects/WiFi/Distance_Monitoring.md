---
layout: project
title: Distance Monitoring using Lorfi-WIFI
---

# Description

This guide covers how to build a distance monitoring system using an ultrasonic sensor and the Lorfi-WIFI board. The ultrasonic sensor accurately measures distances from 2 cm to 450 cm without contact, while the Lorfi-WIFI transmits the data wirelessly. Ideal for learning real-time distance tracking, this project demonstrates how to combine sensors with IoT boards for remote monitoring applications.

# Specification

- Working voltage：0.5V(DC)
- Working current：15mA
- Detecting range：2-450cm
- Detecting angle：15 degrees
- Input trigger pulse：10us TTL Level
- Output echo signal： output TTL level signal(HIGH)，proportional to range.

## Hardware Setup

Next, please refer to the following connection table:

| Ultrasonic ranger | Lorfi WB    | 
|-------------------|-------------|
| ECHO              | GPIO14      |
| TRIG              | GPIO2       |
| VCC               | 5V          |
| GND               | GND         |

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Sensors\8.png" alt="Centered Image" width="900" />
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

// Topics
const char* publish_topic = "100001";
const char* subscribe_topic = "200000";  // optional

// === Ultrasonic Sensor Pins ===
#define TRIG_PIN 2
#define ECHO_PIN 14

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

// === MQTT Callback ===
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
  pinMode(ECHO_PIN, INPUT);
  pinMode(TRIG_PIN, OUTPUT);
}

// === Main Loop ===
void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  // Measure distance
  long duration;
  float distance_cm;

  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  duration = pulseIn(ECHO_PIN, HIGH);
  distance_cm = duration * 0.0343 / 2.0;

  // Display on Serial
  Serial.print("Distance: ");
  Serial.print(distance_cm);
  Serial.println(" cm");

  // Publish JSON data to MQTT
  StaticJsonDocument<256> doc;
  doc["hardware_serial"] = "100001";
  JsonObject payload_fields = doc.createNestedObject("payload_fields");
  payload_fields["distance_cm"] = distance_cm;

  char jsonBuffer[512];
  serializeJson(doc, jsonBuffer);

  client.publish(publish_topic, jsonBuffer);
  Serial.println("Data published:");
  Serial.println(jsonBuffer);

  delay(10000);  // Send every 10 seconds
}
```

## Dashboard Creation

## Expected Output

# Web Server

## Sample Code

## Expected Output

# Network Server (Access Point)

## Sample Code

## Expected Output