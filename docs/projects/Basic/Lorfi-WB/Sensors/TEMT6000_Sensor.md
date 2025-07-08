---
layout: project
title: Ambient Light Sensor using Lorfi-WB
---

# Description

Eventually, you may need to measure ambient light with greater accuracy than a basic photoresistor can provide—without adding much complexity to your setup. When that time comes, the TEMT6000 ambient light sensor is a great option.

Designed to match the sensitivity of the human eye, the TEMT6000 performs best across a wide range of brightness levels, responding well to even small changes. However, it tends to underperform in low-light environments. Since it’s calibrated to human vision, it’s not responsive to infrared or ultraviolet light—something to keep in mind when planning your project.

# Specification

- Supply Voltage: +5VDC 50mA
- Size: 36.5*16mm
- Weight: 4g

## Hardware Setup

|     Module    |   Lorfi WB  |
|---------------|-------------|
| Signal        | A0          |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of the sensor to the Analog Input A0 on the Lorfi board, connect the GND pin to GND port, VCC pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-WB_Sensors\21.png" alt="Centered Image" width="900" />
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

## Sample Code

```c
///Arduino Sample Code
#define Sensor A0

void setup() {
  Serial.begin(9600);
}
void loop() {
  int value = analogRead(temt6000Pin);
  Serial.println(value);
  delay(100); //only here to slow down the output so it is easier to read
}
```

## Expected Output

Once the code is succesfully uploaded you must see the following output or behavior.

*ADD HERE IMAGE OF SUCCESSFUL UPLOAD*

*ADD HERE IMAGE OF EXPECTED OUTPUT IN SERIAL IF ANY*

## FAQ
