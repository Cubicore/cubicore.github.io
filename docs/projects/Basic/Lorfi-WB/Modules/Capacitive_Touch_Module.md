---
layout: project
title: Capacitive Touch Sensor using Lorfi-WB
---

# Description

Looking for an alternative to traditional mechanical buttons? Try using a capacitive touch sensor. Commonly found in modern electronic devices, this sensor adds a sleek, interactive element to your Arduino projects.

It detects touch from the human body or metal objects and outputs a corresponding high or low voltage signal. The sensor can even detect touch through materials like cloth or paper, although sensitivity decreases as the thickness of the material increases.

Future improvements to this type of sensor module aim to enhance performance and user experience.


# Specification

- Supply Voltage: 3.3V to 5V
- Interface: Digital
- Size: 30*20mm
- Weight: 3g

## Hardware Setup 

|     Module    |   Lorfi L   |
|---------------|-------------|
| Signal        | GPIO2       |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of sensor to Digital Input of the Lorfi board, negative pin to GND port, positive pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Modules\5.png" alt="Centered Image" width="900" />
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

## **Sample Code**
```c
#define Sensor 2

void setup() {
  pinMode(Sensor, INPUT);      //Set touch sensor pin to input mode
  Serial.begin(9600);
}

void loop() {
  if (digitalRead(Sensor) == HIGH) {  //Read Touch sensor signal
    Serial.println("Sensor Touched!");    // if Touch sensor is HIGH, print "Sensor Touched!"
  } else{
    Serial.println("Sensor Untouched!");  // if Touch sensor is LOW, print "Sensor Untouched!"
  }
  delay(100);
}
```

## Expected Output

Once the code is succesfully uploaded you must see the following output or behavior.

*ADD HERE IMAGE OF SUCCESSFUL UPLOAD*

*ADD HERE IMAGE OF EXPECTED OUTPUT IN SERIAL IF ANY*

## FAQ

