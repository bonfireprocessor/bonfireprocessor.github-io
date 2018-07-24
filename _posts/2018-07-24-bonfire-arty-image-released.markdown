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
---



![Block Design](/assets/images/block_design.png)

The Image as configured as follows

### Hardware

* Bonfire RISC-V CPU Version 1.20 implementing RV32IM
* 32KB Instruction Cache
* 32KB Data Cache
* 256MB of DDR3 Memory on the Arty Board mapped at Address `0x00000000-0x0ffffffff`
* 32KB of Block RAM for the Boot Monitor

* Arty Onboard Peripherals:
  * USB-UART for Serial I/O
  * Xilinx GPIO Core for Switches and LEDs of the ARTY Board
  * Xilinx AXI Etherlite Core connected to the 10/100MB Ethernet PHY

* External Peripherals:
  * UART on PMOD Port JD
  * Xilinx AXI QSPI Core on PMOD JB, e.g. to use an SDCard PMod

* GPIO
  * [Bonfire-GPIO Peripheral](https://github.com/bonfireprocessor/bonfire-gpio) connected Arduino Digital I/O Headers

### Software
  * Bonfire Boot Monitor
    * Cache and Memory Test
    * Hexdump Utility
    * XModem Download of Binary Images
    * Flash read/write to save a Binary Image on the Arty Onboard Flash and "boot" from the image stored in flash
