---
layout: project
title: Environmental Monitoring using Lorfi-WIFI
---

# Description

This guide shows how to build a simple environmental monitoring system using the DHT11 sensor and the Lorfi-WIFI board. The DHT11 measures temperature and humidity, while the Lorfi-WIFI sends the data wirelessly. With its easy setup and real-time data transmission, this project is ideal for learning how to connect sensors to IoT devices and monitor environmental conditions remotely.

# Specification

- Supply Voltage: +5 V
- Temperature range: 0-50 °C error of ± 2 °C
- Humidity: 20-90% RH ± 5% RH error
- Interface: Digital

## Hardware Setup

|     Module    |   Lorfi WB  |
|---------------|-------------|
| Signal        | GPIO2       |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of the sensor to the Digital Input GPIO2 on the Lorfi board, connect the GND pin to GND port, VCC pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Sensors\4.png" alt="Centered Image" width="900" />
</p>

#### Using directly Lorfi-WB

You can find complete <a href="/docs/Hardware_Guide.html">Lorfi-WB IO pinout here</a>.

*MIGHT NEED TO ADD NOTES ON POWER REQUIREMENTS, PIN CONSIDERATIONS, ETC.*

#### Using Lorfi Interface board

You can find complete guide for <a href="/docs/Hardware_Guide.html">Lorfi Interface here</a>.

*MIGHT NEED TO ADD NOTES ON POWER REQUIREMENTS, PIN CONSIDERATIONS, ETC.*

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
#include <DHT11.h>

// === WiFi Settings ===
const char* ssid = "PLDTHOMEFIBRpyh75";
const char* password = "PLDTWIFI939sf";

// === MQTT Settings (ThingsPH) ===
const char* mqtt_server = "mqtt.things.ph";
const int mqtt_port = 1883;
const char* mqtt_user = "68637fdd2ef34a81cc197c5d";
const char* mqtt_pass = "vE77J2xvmI8VHQTh0KacgrxP";

// Topics
const char* publish_topic = "100000";
const char* subscribe_topic = "device/your_device_id/command"; // optional

// === DHT11 Sensor Setup ===
// Change pin accordingly to your ESP32 GPIO
#define DHT_PIN 2
DHT11 dht11(DHT_PIN);

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

void setup() {
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, mqtt_port);
  client.setCallback(callback);
}

void loop() {
  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  int temperature = 0;
  int humidity = 0;
  int result = dht11.readTemperatureHumidity(temperature, humidity);

  if (result == 0) {
    // Create JSON object
    StaticJsonDocument<256> doc;
    doc["hardware_serial"] = "100000";
    JsonObject payload_fields = doc.createNestedObject("payload_fields");
    payload_fields["temperature"] = temperature;
    payload_fields["humidity"] = humidity;

    // Serialize to JSON string
    char jsonBuffer[512];
    serializeJson(doc, jsonBuffer);

    // Publish to MQTT
    client.publish(publish_topic, jsonBuffer);
    Serial.println("Data published:");
    Serial.println(jsonBuffer);
  } else {
    Serial.print("DHT11 read error: ");
    Serial.println(DHT11::getErrorString(result));
  }

  delay(10000); // Publish every 10 seconds
}
```

## Dashboard Creation

## Expected Output

# Web Server

## Sample Code

```c
#include <WiFi.h>
#include <DHT11.h>

// === WiFi Credentials ===
const char* ssid = "YOUR SSID";
const char* password = "YOUR SSID PASSWORD";

// === Web Server Port ===
WiFiServer server(80);

// === DHT11 Sensor Setup ===
#define DHT11_PIN 2
DHT11 dht11(DHT11_PIN);
int temperature = 0;
int humidity = 0;

void setup() {
  Serial.begin(115200);
  
  // Connect to WiFi
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
  readSensorData();

  WiFiClient client = server.available();  // listen for incoming clients

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
          client.println("<head><meta charset='utf-8'><title>ESP32 DHT11 Monitor</title></head>");
          client.println("<body><h2>ESP32 Environment Monitor</h2>");

          if (temperature != 0 && humidity != 0) {
            client.printf("<p>Temperature: <strong>%d &deg;C</strong></p>", temperature);
            client.printf("<p>Humidity: <strong>%d %%</strong></p>", humidity);
          } else {
            client.println("<p style='color:red;'>Sensor Error or No Data</p>");
          }

          client.println("</body></html>");
          break;
        }
      }
    }
    client.stop();
    Serial.println("Client disconnected.");
  }

  delay(2000); // Read every 2 seconds
}

// === Read DHT11 Sensor Data ===
void readSensorData() {
  int result = dht11.readTemperatureHumidity(temperature, humidity);

  if (result == 0) {
    Serial.print("Temperature: ");
    Serial.print(temperature);
    Serial.print(" °C\tHumidity: ");
    Serial.print(humidity);
    Serial.println(" %");
  } else {
    Serial.print("DHT11 Error: ");
    Serial.println(DHT11::getErrorString(result));
    temperature = 0;
    humidity = 0;
  }
}
```


# Network Server (Access Point)

## Sample Code
