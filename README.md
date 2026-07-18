# xiao-esp32-s3-dns-sinkhole
An ultra-tiny DNS Sinkhole replacing a Raspberry Pi 3B running Pi-hole.

## About
This is my copy of the ESP32 AdBlocker by [s60sc](https://github.com/s60sc), found [here](https://github.com/s60sc/ESP32_AdBlocker).

Some of the variables in `utils.cpp` are edited with examples. The pins selected in `utils.cpp` reference those found on the Xiao ESP32-S3 from Seeed Studio. You can check that out [here](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html) if you're not familiar, or read the [Wiki](https://wiki.seeedstudio.com/xiao_esp32s3_getting_started/).

The goal was to have fun trying to minimize the footprint of a DNS sinkhole. This project gave an added bonus in that the Xiao plus WIZ850 (a WizNet W5500 already attached to an RJ45 port)  can run off the USB port of another host in my homelab, and the RJ45 port removes the need to rely on Wi-Fi being flawless for the sinkhole to function properly. \*note that you can make the ESP32 AdBlocker a wireless node on your system.

While [Pi-hole](https://github.com/pi-hole/pi-hole) is hands-down incredible with a solid GUI, I tend to operate as much as possible on a "set it and forget it" mindset. My homelab's network infrastructure is critical to everything else running on it, so I avoid making changes unless something's actually broken; meaning improvements wait until I'm doing a deliberate, complete overhaul rather than making small, frequent ad hoc changes.

## Hardware

### Seeed Studio XIAO ESP32-S3

| Spec | Detail |
|---|---|
| Processor | Xtensa dual-core, 32-bit, 240MHz |
| Wireless | Complete 2.4 GHz spectrum, BLE 5.0/BT Mesh |
| On-chip Memory | 8MB PSRAM, 8MB Flash |
| Interfaces | 1x UART, 1x IIC, 1x SPI, 11x GPIO, 9x ADC, 1x User LED, 1x Charge LED |
| Operating Voltage | Type-C: 5V@19mA / Battery: 3.8V@22mA |

**Docs:** [Seeed Studio XIAO ESP32-S3 Datasheet](https://files.seeedstudio.com/Bazaar/product_pdf/113991114.pdf)

**Purchase:**
- [Seeed Studio](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html)
- Mouser: [Option 1](https://mou.sr/4f95PYX) / [Option 2](https://mou.sr/3RHRglZ)
- Digikey: [Option 1](https://www.digikey.com/short/wzn1h7j7) / [Option 2](https://www.digikey.com/short/892j3ntn)
- Amazon: [Option 1](https://a.co/d/0e6cru2V) / [Option 2](https://a.co/d/037d3VQH)

### WIZnet WIZ850io (W5500)

**Docs:** [W5500 Datasheet](https://docs.wiznet.io/assets/files/W5500_ds_v110e226ffec190c588b69f88d629789585e1.pdf) | [Official WIZnet Driver Library](https://github.com/Wiznet/ioLibrary_Driver)

**Purchase:**
- [Amazon](https://a.co/d/0ixdAfWn)
- [AliExpress](https://www.aliexpress.us/item/3256811966613867.html)
- [Digikey](https://www.digikey.com/short/8ptmdd71)

Alternative WIZ850io part options:  
[Option 1](https://www.digikey.com/short/7r393hjr) [Doc](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/6546/333039%20Ethernet%20controller%20W5500%20board%20datasheet.pdf)  
[Option 2](https://www.digikey.com/short/bjm8p37p) [Doc](https://download.mikroe.com/documents/standards/mikrobus/mikrobus-standard-specification-v200.pdf)  
[Option 3](https://www.digikey.com/short/d8t8vnqt) [Doc](https://cdn-learn.adafruit.com/downloads/pdf/adafruit-wiz5500-ethernet-co-processor-breakout-board.pdf)  
\*Be warned that my documentation does not cover wiring diagrams for these parts, I am providing these links as options solely because they fit the super-tiny form factor goal, and you may want a variety of options. The Espressif ESP32 in it's many forms from devkit to SFF options isn't far off, either. I just wanted a DNS sinkhole I could toss in my pocket and standup a travel network in a hotel room, per se.

## Wiring
<img width="685" height="724" alt="image" src="https://github.com/user-attachments/assets/51ce75ff-c247-435a-b651-6ae02c3f1e3a" />


### XIAO ESP32-S3 Pinout (as used in this build)

| Label | Pin for build | Usage |
|---|---|---|
| 5V | Not Used | — |
| GND | Ground | Negative |
| 3V3 | Power | Positive/Hot |
| D10 | MOSI | SPI |
| D9 | MISO | SPI |
| D8 | SCK | SPI |
| D2 | GPIO3 | SCN (chip select) |
| D1 | GPIO2 | Interrupt |
| D0 | GPIO1 | Remote reset for W5500 |
| D3–D7, MTDO, MTDI, EN, MTCK, MTMS, D+, D- | Not Used | — |
| BAT+ / BAT- | Not Used | If a mobile DNS sinkhole were ever wanted, these could in theory be programmed for that |

### WIZ850io Pinout

| Label | Pin for build | Usage |
|---|---|---|
| J1-1 / J1-2 (GND) | Not Used | Shares ground rail |
| J1-3 | MOSI | SPI Master-Out/Slave-In |
| J1-4 | SCLK (SCK) | SPI Clock Input |
| J1-5 | SCNn | SPI Chip Select (active low) |
| J1-6 | INTn | Interrupt Output (active low) — socket events: connect, disconnect, data reception, WOL |
| J2-1 | GND | Ground |
| J2-2 / J2-3 | 3V3 | 3.3V power (shared rail) |
| J2-4 | NC | Not Connected |
| J2-5 | RSTn | Hardware Reset input (active low) |
| J2-6 | MISO | SPI Master-In/Slave-Out |

### Wiring Diagrams
<img width="685" height="637" alt="image" src="https://github.com/user-attachments/assets/17c15415-d7b8-491d-8764-c352e51aca8a" />


### Wiring Explanation

| ESP32 Pin | WIZ850io Pin |
|---|---|
| 5V | Not Connected |
| GND | GND |
| 3V3 | 3V3 |
| MOSI | MOSI |
| MISO | MISO |
| SCK | SCK |
| RX | Not Connected |
| GPIO1 | RST |
| GPIO2 | INT |
| GPIO3 | SCN |

## Firmware Configuration

In `utils.cpp`, locate the section detailing the SPI pins:

```cpp
// SPI pins for Ethernet
int ethCS   = 3; // W5500 chip select / LAN8720 MDC
int ethInt  = 2; // W5500 interrupt / LAN8720 MDIO
int ethRst  = 1; // W5500 reset / LAN8720 POWER
int ethSclk = 7; // W5500 SPI clock / LAN8720 CLOCK
int ethMiso = 8; // W5500 SPI data pin
int ethMosi = 9; // W5500 SPI data pin
```
