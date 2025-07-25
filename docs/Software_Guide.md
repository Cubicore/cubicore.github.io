---
layout: project
title: Software Guide
---

This section covers the software used in the CubiKit One, including how to install and configure it for your development needs.

# Installation of Arduino IDE

Go to the official [Arduino website](www.arduino.cc.)

Hover over the Software tab and select Downloads.

Download the installer for your operating system (e.g., Windows Installer).

Open the downloaded file and follow the on-screen instructions:

Agree to the license terms.

Choose installation options (default selections are recommended).

Select the installation path or leave it as default.

Click Install and wait for the process to complete.

Once installed, launch the Arduino IDE from your desktop or Start menu

# Adding Boards Lorfi-L RAK3172

Open Arduino IDE.

Go to File > Preferences.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide1.png" alt="Centered Image" width="900" />
</p>

In the Additional Board Manager URLs field, add the following URL:

```c

https://raw.githubusercontent.com/RAKWireless/RAKwireless-Arduino-BSP-Index/main/package_rakwireless_com_rui_index.json

```

If there are existing URLs, add this on a new line or separate with a comma. Click OK to save.

Restart the Arduino IDE.

Go to Tools > Board > Boards Manager. In the search bar, type RAK.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide2.png" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide3.png" alt="Centered Image" width="900" />
</p>

Locate RAKwireless RUI STM32 Boards and click Install.

After installation, select your RAK3172 board via Tools > Board > RAKWireless RUI STM32 Modules > WisDuo RAK3172 Evaluation Board.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide4.png" alt="Centered Image" width="900" />
</p>

# Adding Boards Lorfi-WB ESP32

Open Arduino IDE.

Go to File > Preferences.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide5.jpg" alt="Centered Image" width="900" />
</p>

In the Additional Board Manager URLs field, add the following URL:

```c

https://raw.githubusercontent.com/espressif/arduino-esp32/gh-pages/package_esp32_index.json

```

If there are existing URLs, add this on a new line or separate with a comma. Click OK to save.

Restart the Arduino IDE.

Go to Tools > Board > Boards Manager. In the search bar, type RAK.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide2.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide6.jpg" alt="Centered Image" width="900" />
</p>

Locate RAKwireless RUI STM32 Boards and click Install.

After installation, select your RAK3172 board via Tools > Board > RAKWireless RUI STM32 Modules > WisDuo RAK3172 Evaluation Board.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\Software-Guide_Images\Software_Guide7.jpg" alt="Centered Image" width="900" />
</p>

# ThingsPH setup and configuration with Arduino IDE

Go to the official [ThingsPH website](https://things.ph/) and create an account.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\2.jpg" alt="Centered Image" width="900" />
</p>

After signing up go to the docs section of the platform.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\3.jpg" alt="Centered Image" width="900" />
</p>

Go to the documentation section, then click the MQTT client section. There you will see here the information or configurations that you'll use in your script. Copy the Host, Port, Username, and Password settings to allow the board to connect to the MQTT server.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\4.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\5.jpg" alt="Centered Image" width="900" />
</p>

After that go to device types, and create your own device type. And add telemetries for the specific data that you're going to transfer from your device to the Platform(ThingsPH) via MQTT.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\6.jpg" alt="Centered Image" width="900" />
</p>

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\7.jpg" alt="Centered Image" width="900" />
</p>

Lastly create you're own device with the device type that you've created. Then you're all set.

<p style="text-align: center;">
  <img src="\assets\Images\LORFI_Components\ThingsPH_Images\8.jpg" alt="Centered Image" width="900" />
</p>

# MIT App Inventor setup and configuration with Arduino IDE