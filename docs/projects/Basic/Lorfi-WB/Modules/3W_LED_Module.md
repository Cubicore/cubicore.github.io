---
layout: project
---

# 3W LED Module using Lorfi-WB

# Description
This LED module features high brightness, thanks to its 3W lamp beads. It's well-suited for Arduino projects and is especially useful in robotics or search and rescue platforms.

For instance, smart robots can utilize this module for lighting purposes. However, for safety reasons, avoid shining the LED directly into human eyes.

# Specification

- Color temperature: 6000~7000K
- Luminous flux: 180~210lm
- Current: 700~750mA
- Power: 3W
- Light angle: 140 degree
- Working temperature: -50~80℃
- Storage temperature: -50~100℃
- High power LED module, controlled by IO port microcontroller
- IO Type: Digital
- Supply Voltage: 3.3V to 5V
- Size: 40x28mm
- Weight: 6g


## Hardware Setup

|     Module    |   Lorfi WB  |
|---------------|-------------|
| Signal        | GPIO2       |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of sensor to Digital Input of the Lorfi board, negative pin to GND port, positive pin to 5V port.

![3W LED Module](\assets\Images\LORFI_Components\Lorfi-WB_Modules\1.png)

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

![Software Guide 4](\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide4.png)

## **Sample Code**
```c
#define LED 2
void setup() {
  // initialize digital pin as an output.
  pinMode(LED, OUTPUT);
}

// the loop function runs over and over again forever
void loop() {
  digitalWrite(LED, HIGH);   // turn the LED on (HIGH is the voltage level)
  delay(1000);              // wait for a second
  digitalWrite(LED, LOW);    // turn the LED off by making the voltage LOW
  delay(1000);              // wait for a second
}
```

## Expected Output

Once the code is succesfully uploaded you must see the following output or behavior.

*ADD HERE IMAGE OF SUCCESSFUL UPLOAD*

*ADD HERE IMAGE OF EXPECTED OUTPUT IN SERIAL IF ANY*

## FAQ

1. Why button is not responding?
- Check if the header pins are properly connected

2. Why LED is dim?
- Check if you are using 5V or 3.3V