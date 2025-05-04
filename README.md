> [!CAUTION]
> Heavy work in progress, project is to be considered non-functional and incomplete.

# BlackBerry Q10 Display Assembly Driver

This repo contains the schematics and code for a display driver meant to provide compatibility with the BlackBerry Q10 display and touch screen. This is part of my [BlackBerry Pi project](https://github.com/gagne-3/blackberry-pi-cm4).

## Background Information

This project intends to provide a way to use a Q10 display assembly with a Raspberry Pi 4, but can be adapted to work with any computer that supports MIPI DSI.

The specific panel included in the Q10 display assembly, AMS309PW01, was specially made for the BlackBerry Q10 by Samsung SMD.  This display is a 3.1" AMOLED display with a resolution of 720x720. It communicates to the device using MIPI. Touch inputs are handled by an [Atmel mXT224S](https://www.microchip.com/en-us/product/atmxt224s#Documentation) connected to the built in digitizer.

## The Story

Initially, I fell down a rabbit hole and ended up looking into the wrong panel for this project. If you search for 3.1" 720x720 displays, the only readily available one is the LT031MDZ4000. These panels can be found readily on Aliexpress and Alibaba with MIPI driver boards. Others have also worked on creating custom drivers for this panel, notably [Jamwu](https://github.com/jamwu/RPI-LCD-LT031MDZ4000) and [Will-TM](https://github.com/will-tm/RPI-LCD-LT031MDZ4000).

Unfortunately, once I got my hands on a Q10 display module, I realized that it does not have a LT031MDZ4000 panel. Despite being the same size and resolution, the LT031MDZ4000 is an LCD panel that was made for the BlackBerry Q5, not the Q10. Upon tearing apart my Q10 display, I found the real panel model, AMS309PW01. Unfortunately for me, the AMS309PW01 is practically impossible to find information about. Only very basic information can be found on places such as [Panelook](https://www.panelook.com/AMS309PW01-0_Samsung_3.1_OLED_overview_21542.html). There do not appear to be any resellers of this panel, and there are no schematics or notes about how these panels work.

This caused me to lose hope on this project. Without a schematic, it was nearly impossible to create a driver board to make this display functional. I was considering going in with a multimeter and painstakingly reverse engineering the electronics attached to the display assembly. Thankfully, I caught a lucky break. While searching for schematics, I decided to search for general BlackBerry Q10 Schematics. That's when I fell upon a random blog that had a bunch of PDFs uploaded to it. These PDFs contained schematics for the entire mainboard of the Q10, which was exactly what I needed.

I will not link to the blog in question since it appeared to be a reupload. The PDFs are watermarked with a URL to what seems to be a dead website. a copy of the schematic can be found in my main [BlackBerry Pi repo](https://github.com/gagne-3/blackberry-pi-cm4/tree/main/schematics/SQN100-1).

# Technical Details

## Q10 Display Connector Pinout

The display connector on the Q10 display assembly is a `Hirose BM14B(0.8)-30DP-0.4V(51)`, which means that the driver board requires a `Hirose BM14B(0.8)-30DS-0.4V(51)` socket to connect. As far as I could tell, there is no difference between the `(51)` and `(53)` versions of the connector.

Below is a pinout of the Q10 motherboard display connector socket compiled from information present on Page 28 of the schematic.

| Pin | Signal Name | Description |
| --- | ----------- | ----------- |
| MP1 | GND | Ground |
| MP2 | GND | Ground |
| 1 | VSYS_F | Main system power |
| 2 | V2_85TSP_F | Touchscreen AVDD (2.85V) |
| 3 | VSYS_F | Main system power |
| 4 | V1_8TSP_F | Touchscreen VDD (1.8V) |
| 5 | GND | Ground |
| 6 | GND | Ground |
| 7 | — | Not Connected |
| 8 | I2C_SCL_TS | Touchscreen I²C Clock |
| 9 | GND | Ground |
| 10 | I2C_SDA_TS | Touchscreen I²C Data |
| 11 | OLED_MIPI:D1_P | MIPI DSI Data Lane 1+ |
| 12 | GND | Ground |
| 13 | OLED_MIPI:D1_N | MIPI DSI Data Lane 1− |
| 14 | TSCRN_INT_N | Touchscreen Interrupt |
| 15 | GND | Ground |
| 16 | TSCRN_RST_N | Touchscreen Reset |
| 17 | OLED_MIPI:CLK_P | MIPI DSI Clock+ |
| 18 | GND | Ground |
| 19 | OLED_MIPI:CLK_N | MIPI DSI Clock− |
| 20 | V3_10LED_F | Display power (3.1V) |
| 21 | GND | Ground |
| 22 | V3_10LED_F | Display power (3.1V) |
| 23 | OLED_MIPI:D0_P | MIPI DSI Data Lane 0+ |
| 24 | GND | Ground |
| 25 | OLED_MIPI:D0_N | MIPI DSI Data Lane 0− |
| 26 | V2_20LED_F | Display secondary power (2.2V) |
| 27 | GND | Ground |
| 28 | V2_20LED_F | Display secondary power (2.2V) |
| 29 | OLED_RST_N | Display Reset |
| 30 | GND | Ground |
| MP3 | GND | Ground |
| MP4 | GND | Ground |

Note: the pinout from Hirose is different to how the Blackberry schematic labels the pins. The official pinout from the manufacturer has the edge pins labeled as "MP#" while the Blackberry schematic includes them in the pin count. These edge pins are all grounds anyway, so I have included the official manufacturer pinout to match to the schematic.

## Raspberry Pi Display Connector Pinout

The display connector on the Raspberry Pi 4b is a 15 pin 1mm pitch FFC connector, specifically SFW15R-2STE1LF.

The following pinout of the Raspberry Pi 4b display connector is compiled from information from the [official Raspberry Pi 4b Schematic.](https://datasheets.raspberrypi.com/rpi4/raspberry-pi-4-reduced-schematics.pdf)

| Pin | Signal Name | Description |
| --- | ----------- | ----------- |
| 1 | GND | Ground |
| 2 | DSI1_DN1 | MIPI DSI Data Lane 1- |
| 3 | DSI1_DP1 | MIPI DSI Data Lane 1+ |
| 4 | GND | Ground |
| 5 | DSI1_CN | MIPI DSI Clock- |
| 6 | DSI1_CP | MIPI DSI Clock+ |
| 7 | GND | Ground |
| 8 | DSI1_DN0 | MIPI DSI Data Lane 0- |
| 9 | DSI1_DP0 | MIPI DSI Data Lane 0+ |
| 10 | GND | Ground |
| 11 | SCL0 | Touchscreen I²C Clock |
| 12 | SDA0 | Touchscreen I²C Data |
| 13 | GND | Ground |
| 14 | 3V3 | Display power (3.3V) |
| 15 | 3V3 | Display power (3.3V) |

## Display Panel Controller IC

Based on some basic googling and ChatGPT-ing, I believe the most likely controller IC for this panel is the Samsung S6E63M0.

Drivers for this controller IC already exist within the [Linux Kernel](https://github.com/torvalds/linux/blob/master/drivers/gpu/drm/panel/panel-samsung-s6e63m0.c)
