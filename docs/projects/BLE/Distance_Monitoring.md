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

Go to the dashboard section of ThingsPH. Click create dashboard and input your dashboard name ex: Distance to Object Monitoring.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\16.jpg" alt="Centered Image" width="900" />
</p>

After creating your own dashboard. Create a panel within the dashboard. Configure your panel by choosing your specific configuration in the Data Source.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\17.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\18.jpg" alt="Centered Image" width="900" />
</p>

## Expected Output

After setting up your dashboard this will be the expected outcome in your platform(thingsPH) and Arduino IDE.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\19.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\19.jpg" alt="Centered Image" width="900" />
</p>

# Web Server

## Sample Code

```c
#include <WiFi.h>

// === Ultrasonic Sensor Pins ===
#define TRIG_PIN 2
#define ECHO_PIN 14

// === WiFi Credentials ===
const char* ssid = "YOUR SSID";
const char* password = "YOUR SSID PASSWORD";

// === Web Server Port ===
WiFiServer server(80);

// === Global Distance Variable ===
float distance_cm = 0;

// === Distance Threshold ===
const float object_threshold_cm = 20.0;  // Change this as needed

void setup() {
  Serial.begin(115200);

  // Sensor Pin Modes
  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);

  // WiFi Connection
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

  // Start Server
  server.begin();
}

void loop() {
  measureDistance();  // Update global distance_cm

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

          // HTML Response
          client.println("<!DOCTYPE html><html>");
          client.println("<head><meta charset='utf-8'><title>Distance Monitor</title></head>");
          client.println("<body><h2>ESP32 Distance Sensor</h2>");
          client.printf("<p>Distance: <strong>%.2f cm</strong></p>", distance_cm);

          if (distance_cm > 0 && distance_cm <= object_threshold_cm) {
            client.println("<p style='color:red;'>Object Detected!</p>");
          } else {
            client.println("<p style='color:green;'>No Object Detected.</p>");
          }

          client.println("</body></html>");
          break;
        }
      }
    }
    client.stop();
    Serial.println("Client disconnected.");
  }

  delay(1000);  // Wait before the next loop
}

// === Measure Distance Function ===
void measureDistance() {
  long duration;

  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  duration = pulseIn(ECHO_PIN, HIGH);
  distance_cm = duration * 0.0343 / 2;

  Serial.print("Distance: ");
  Serial.print(distance_cm);
  Serial.println(" cm");
}
```

## Expected Output

# Network Server (Access Point)

## Sample Code

## Expected Output