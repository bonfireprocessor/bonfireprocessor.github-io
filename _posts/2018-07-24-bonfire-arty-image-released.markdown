---
layout: post
title: "Bonfire Arty Image Released"
date: "2018-07-24 21:03:25 +0200"
#image: /assets/images/arty.png

introduction: |
  Finally after several months of work I released a "tunrkey" MCS file to be installed
  on a Digilent Arty A35 Board.

actions:
- label: "Download"
  icon: download
  url: https://github.com/bonfireprocessor/bonfire/releases
- label: Instructions
  url: /install.html  
---



![Block Design](/assets/images/block_design.png)

The Image is configured as follows:

### Hardware

#### [Bonfire AXI Core](https://github.com/bonfireprocessor/bonfire_axi)
The Bonfire AXI Core contains the Bonfire Core together with Instruction and Data Cache. It is designed to be used with Xilinx IP Integrator and is "plug-in" compatible with the Microblaze Processor from Xilinx.

In the Arty Image it is configured as follows:

* Bonfire RISC-V CPU Version 1.20 implementing RV32IM
* 32KB Instruction Cache
* 32KB Data Cache

#### Memory
* 256MB of DDR3 Memory on the Arty Board
* 32KB of Block RAM for the Boot Monitor
* SPI Interface to the On Board Flash Memory

#### Arty Onboard Peripherals:
* USB-UART for Serial I/O
* Xilinx GPIO Core for Switches and LEDs of the ARTY Board
* Xilinx AXI Etherlite Core connected to the 10/100MB Ethernet PHY

#### External Peripherals:
* UART on PMOD Port JD
* Xilinx AXI QSPI Core on PMOD JB, e.g. to use an SDCard PMod

#### GPIO
* [Bonfire-GPIO Peripheral](https://github.com/bonfireprocessor/bonfire-gpio) connected Arduino Digital I/O Headers

### Software
  * Bonfire Boot Monitor
    * Cache and Memory Test
    * Hexdump Utility
    * XModem Download of Binary Images
    * Flash read/write to save a Binary Image on the Arty Onboard Flash and "boot" from the image stored in flash
