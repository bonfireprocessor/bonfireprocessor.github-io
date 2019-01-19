---
layout: post
title: "New Bonfire Arty Image Released"
date: "2019-01-17 20:30:00 +0200"
#image: /assets/images/arty.png

introduction: |
  New version of the Bonfire Arty Image contains the new lates eLua Update
  

actions:
- label: "Download"
  icon: download
  url: https://github.com/bonfireprocessor/bonfire/releases
- label: Instructions
  url: /install.html  
---

![Block Design](/assets/images/block_design_v1.2.2.png)

### Hardware
The hardware configuration is identical to 
[the previous release](/2018/07/24/bonfire-arty-image-released.html)

### Software
The image contains the newest version of Bonfire eLua. 
Main features of this eLua release are:

#### Integrated Text Editor
Bonfire eLua now contains an adapted version of the "Kilo" text editor. It can be started with the edit command e.g
````
edit /rom/life_server.lua
````
![Editor screenshot](/assets/images/kilo.png)

The editor supports syntax highlighting for Lua code, you need to enable ANSI colors in your terminal program to see it. For minicom use the option `` --color=on`` to enable it.

Of course on the /rom filesystem saving the file is not possible. So the editor is more usefull if a Micro SD card is connected to the system. 

### Peripherals
![Pmods](/assets/images/pmods.jpg)

The default image supports the following peripherals

| Device   | Connection                    | Address  | IP Core           |   |
|----------|-------------------------------|----------|-------------------|---|
| UART 0   | Arty USB Port                 | 80020000 | zpuino_uart       |   |
| UART 1   | [PMod UART on JD](https://store.digilentinc.com/pmod-sd-full-sized-sd-card-slot/)               | 80030000 | zpuino_uart       |   |
| SD Card  | [Pmod SD on JB](https://store.digilentinc.com/pmod-usbuart-usb-to-uart-interface/)                 | 80040000 | AXI Quad SPI      |   |
| Ethernet | Arty Ethernet Phy             | 80E00000 | AXI Ethernet Lite |   |
| GPIO0    | Arty LEDs                     | 80010000 | AXI GPIO          |   |
| GPIO1    | Arty Dip Switches and Buttons | 80020000 | AXI GPIO          |   |
| GPIO2    | Arty Shield DP0-DP19          | 80050000 | bonfire_gpio_core |   |


#### SD Card
The SD Card can be accesed as device /mmc on the eLua console. It must be formated with FAT32 (exFAT support is disabled by default, but can be enabled when building a custom eLua version). 

##### Issues
Unfortunately the AXI Quad SPI core does not support switching the SPI Clock. This means that the SD Card is not initalized with a 400khz clock, as required by the specificaiton. The clock in the image is configured to 20.825 Mhz, so initalisation takes places with this clock. I have tested with two SD cards, an  2GB from Hama and a Kingston 16GB card. SO I think that most modern SD cards will work.

Ejecting/Inserting the SD card while the device is up is not supported. A reboot is required to detect a new inserted card

There is not really much error handling and error messages for the SD card support. When initalisation fails it will fail silently. The ls command will not show any files, trying to open a file will report an I/O error. 

#### Network
eLua contains a experimental networking support with uIP. It is a bit buggy and limited currently, nevertheless communcation is possible- See eLua documentation for details.


##### Issues
The image is configured to get an IP address with DHCP, if it fails it will fall back to:

```IP: 192.168.26.200/24 gw=192.168.26.2 dns=192.168.26.2```

Changing is only possible with recompiling eLua. 

Currently the default MAC address from the Ethernet Lite core  is used, 00:00:5e:00:fa:ce. Setting another MAC address is currently not implemented. So be aware of this...

### Examples in the ROM File system

| Name     |   Description                            |
|----------|------------------------------------------|
| life.lua  | Game of Life demo, output to console    |
| prime.lua  |   "Classic" prime number benchmark |
| interrupt.lua | eLua Timer/Interrupt demo |
| co.lua | Lua coroutine demo |
| life_server.lua | Game of live, network version. Connecto with telnet to port 5050. Can handle more than one session, stop server with pressing a key  |
| tcp_echo.lua | Simple tcp Echo demo, connect also to port 5050 |
