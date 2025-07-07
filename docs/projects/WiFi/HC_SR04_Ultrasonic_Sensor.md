---
layout: project
title: HC-SR04 Ultrasonic Sensor using Lorfi-L
---

# Description

Ultrasonic waves have strong directionality, low energy loss, and can travel long distances through a medium, making them ideal for distance measurement applications such as range finders and position sensors. This ultrasonic sensor module offers a non-contact detection range of 2 cm to 450 cm, with a high measurement accuracy of up to 3 mm, making it suitable for most standard needs. The module integrates both an ultrasonic transmitter and receiver along with the necessary control circuitry.


# Specification

- Working voltage：0.5V(DC)
- Working current：15mA
- Detecting range：2-450cm
- Detecting angle：15 degrees
- Input trigger pulse：10us TTL Level
- Output echo signal： output TTL level signal(HIGH)，proportional to range.

## Hardware Setup

Next, please refer to the following connection table:

| Ultrasonic ranger | Arduino Uno |
|-------------------|-------------|
| ECHO              | PB5         |
| TRIG              | PB3         |
| VCC               | 5V          |
| GND               | GND         |

Note: D4、D5 are the digital pins(PIN 5(PB5) and PIN 12(PB3) ).

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Lorfi-L_Sensors\10.png" alt="Centered Image" width="900" />
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
#define TRIG_PIN PB3
#define ECHO_PIN PB5

void setup() {
  Serial.begin(9600);
  pinMode(ECHO_PIN, INPUT);
  pinMode(TRIG_PIN, OUTPUT);
}
void loop() {
  long duration;
  float distance_cm;

  // Clear the trigger
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);

  // Trigger the sensor with a 10 microsecond pulse
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  // Read the echo time
  duration = pulseIn(ECHO_PIN, HIGH);

  // Calculate the distance
  distance_cm = duration * 0.0343 / 2;

  // Print the distance
  Serial.print("Distance: ");
  Serial.print(distance_cm);
  Serial.println(" cm");

  delay(500);
}
```

## Expected Output

Once the code is succesfully uploaded you must see the following output or behavior.

*ADD HERE IMAGE OF SUCCESSFUL UPLOAD*

*ADD HERE IMAGE OF EXPECTED OUTPUT IN SERIAL IF ANY*

## FAQ