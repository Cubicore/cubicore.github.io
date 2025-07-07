---
layout: project
title: Photo Interruptor Module using Lorfi-L
---

# Description

The vertical section of this sensor contains an infrared emitter, while the opposite side houses a shielded infrared detector. When the emitter sends a beam of infrared light across to the detector, the sensor can identify the presence of an object if it interrupts the beam.

This type of sensor is commonly used in various applications such as optical limit switches, pellet dispensing systems, and general object detection.


# Specification

- Supply Voltage: 3.3V to 5V
- Interface: Digital
- Size: 30*20mm
- Weight: 3g


## Hardware Setup

|     Module    |   Lorfi L   |
|---------------|-------------|
| Signal        | PB5         |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of sensor to Digital Input of the Lorfi board, negative pin to GND port, positive pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-L_Modules\9.png" alt="Centered Image" width="900" />
</p>

#### Using directly Lorfi-L

You can find complete <a href="/docs/Hardware_Guide.html">Lorfi-L IO pinout here</a>.

*MIGHT NEED TO ADD NOTES ON POWER REQUIREMENTS, PIN CONSIDERATIONS, ETC.*

#### Using Lorfi Interface board

You can find complete guide for <a href="/docs/Hardware_Guide.html">Lorfi Interface here</a>.

*MIGHT NEED TO ADD NOTES ON POWER REQUIREMENTS, PIN CONSIDERATIONS, ETC.*

## Software Setup

Lorfi-L is based on RAK3172 LoRaWAN module. This must be added to Arduino IDE.

Here's the guide on <a href="/docs/Software_Guide.html">how to add RAK3172 on your Arduino IDE</a>.

Once RAK3172 is added, you can now select it from the board selection.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide4.png" alt="Centered Image" width="900" />
</p>

If failing, test the UART connection by checking the version. Use `AT+VER=?` command with baud rate of 115200. In case of MCU brick, revive the RAK3172 by following this [guide from RAKwireless](https://learn.rakwireless.com/hc/en-us/articles/26687606549911-How-To-Guide-STM32CubeProgrammer-for-RAK-Modules).

## Sample Code:
```c
#define Sensor PB5
int val;  // define numeric variables val

void setup() {
  pinMode(Sensor, INPUT);  // define the photo interrupter sensor output interface
}
void loop() {
  val = digitalRead(Sensor);
  if (val == HIGH) {  // When the light sensor detects a signal is interrupted, it prints alerts.
    Serial.print("Object interupts");
  } else {
    Serial.print("Space is clear");
  }
  delay(100);
}
```

## Expected Output

Once the code is succesfully uploaded you must see the following output or behavior.

*ADD HERE IMAGE OF SUCCESSFUL UPLOAD*

*ADD HERE IMAGE OF EXPECTED OUTPUT IN SERIAL IF ANY*

## FAQ
