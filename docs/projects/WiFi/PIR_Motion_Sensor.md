---
layout: project
title: PIR Motion Sensor using Lorfi-L
---

# Description

The pyroelectric infrared motion sensor detects infrared radiation emitted by moving people or animals and produces a corresponding switch signal. It's suitable for various applications involving human motion detection. Traditional pyroelectric infrared sensors typically rely on a separate infrared detector, dedicated chip, and complex circuitry, resulting in larger size, more complicated design, and reduced reliability.

This updated version, designed specifically for Arduino, features an all-in-one digital pyroelectric infrared sensor. It offers a more compact form factor, improved reliability, lower power consumption, and a simplified circuit, making it easier to integrate into projects.

# Specification

- Input Voltage: 3.3 ~ 5V, Maximum for 6V
- Working Current: 15uA
- Working Temperature: -20 ~ 85 ℃
- Output Voltage: High 3V, Low 0V
- Output Delay Time (High Level): About 2.3 to 3 Seconds
- Detection Angle: 100 °
- Detection Distance: 7 meters
- Output Indicator LED (if output HIGH, it will be ON)
- Limit Current for Pin: 100mA
- Size: 30*20mm
- Weight: 4g

## Hardware Setup

|     Module    |   Lorfi L   |
|---------------|-------------|
| Signal        | PB5         |
| VCC           | 5V          |
| GND           | GND         |

Connect the Signal pin of the sensor to the Digital Input PB5 on the Lorfi board, connect the GND pin to GND port, VCC pin to 5V port.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-L_Sensors\18.png" alt="Centered Image" width="900" />
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

void setup()
{
  pinMode(Sensor,INPUT);
  Serial.begin(9600);
}
void loop()
{
  byte state = digitalRead(Sensor);
  if(state == 1){
    Serial.println("Somebody is in this area!");
  }else if(state == 0){
    Serial.println("No one!");
    delay(500);
  }
}

```

Once the code is succesfully uploaded you must see the following output or behavior.

*ADD HERE IMAGE OF SUCCESSFUL UPLOAD*

*ADD HERE IMAGE OF EXPECTED OUTPUT IN SERIAL IF ANY*

## FAQ
