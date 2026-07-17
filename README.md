# xiao-esp32-s3-dns-sinkhole
An ultra-tiny DNS Sinkhole Replacing a Raspberry Pi 3B running Pi Hole


## About
This is my copy of the ESP32 ADBlocker by [s60sc](https://github.com/s60sc) found [here](https://github.com/s60sc/ESP32_AdBlocker). \n
Some of the variables in utils.cpp are edited with examples. The pins that are selected in utils.cpp reference those found on the Xiao ESP32-S3 from Seeed Studios. You can check that out [here](https://www.seeedstudio.com/XIAO-ESP32S3-p-5627.html) if you're not familiar. [Wiki link](https://wiki.seeedstudio.com/xiao_esp32s3_getting_started/)

The goal was to have fun trying to minimize the footprint of a DNS sinkhole. This project gave an added bonus in that the Xiao plus WIZ850 - A WizNet W5500 already attached to an RJ45 port, can run off the USB port of another host in my homelab, and the RJ45 port removed the need to rely on Wi-Fi to be flawless for the sinkhole to function properly.

While the [Pi Hole](https://github.com/pi-hole/pi-hole) is hands down incredible with a solid GUI, I tend to operate as much on the "set it and forget it" mindset. 
- While the network infrstructure side of my home lab is critical to other work, make changes unless it's down waits until I start a complete overhaul of the system.
