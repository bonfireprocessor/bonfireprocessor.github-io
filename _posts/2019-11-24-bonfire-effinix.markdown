---
layout: post
title:  "Bonfire on FireAnt"
#date: "2019-11-24 8:20:25 +0200"
image: /assets/images/FireAnt.jpg

actions:
  - label: "View on GitHub"
    icon: github
    url: https://github.com/ThomasHornschuh/bonfire-soc-fireant

---

## About the FireAnt board
Sometime last summer on CrowdSupply a new FPGA board was anounced: The [FireAnt](https://www.crowdsupply.com/xips-technology/fireant) based on an FPGA I have never heard before: The Trion T8 from Efinix.

I checked our the web site of [Efinix](https://www.efinixinc.com/) and I became curious about their FPGAs. So I backed the campaign and after a long wait the FirAnt board arrived at the 18th of November.

### Hardware
![fpga](/assets/images/FireAnt_closeup.jpg)

The FireAnt Board contains the Trion T8 FPGA, 4LEDs, two user buttons (the thrid resets the FPGA), 35 GPIOs, 8Mbit NOR Flash and a single channel FTDI232H for programming the board. I ordered the board without the headers pre-installed so I soldered them on my own. So the not so perfect locking soldering in the picture is not the vendors fault :-)

#### Programming interface noteworthiness 
An unsual property of the FireAnt is, that the FPGA is not programmed over a JTAG interface like on most FPGA boards, it is done over SPI: The FTDI232H is used in SPI mode by the Trion Software and can either directly program the FPGA in a non-volatile way or program the NOR Flash chip. Because the FTDI232H is only single channel, there is no paralell UART connection as usual in other FPGA boards. I tried to use the FTDU232H in UART mode after programming the FPGA. Unfortunately the standard serial driver hold the DTR line low, which unfortunately is mapped to the CRESET pin of the FPGA. 

#### The Trion T8 FPGA
The T8 has a quite simple structure, the logic cells are just a 4 Input LUT and a D-Flip-Flop. The LUT can also be configured as a 1-Bit Adder with Carry-In/Out. Multi-Bit adders are constructed from LUTs in a row with a dedicated Carry Chain. The Dual-Port Block RAMs are 5Kbit in Size, they support several configurations from 256x20 to 4096x1. In contasz to other FPGAs they don't support byte write enables, this is a limitation I had to work-around when porting Bonfire. 

I/O Cells are just basic (no DDR, SERDES, etc.) and there is one simple PLL.


| T8 IOB | T8 PLL |
|--------|--------|
|![IOB](/assets/images/t8_iob.png)|![IOB](/assets/images/t8_pll.png)|


#### Efinity Software and Documentation
As FireAnt customer you get automatically a Mail with a Registration key. This key can be used to unlock the Efinix Support Portal and get access to the "Efinity Software".
The Support Portal User is unlimited, the Subscription to get Efinix updates is limited to one year. I have no idea yet if there is a reasonable way to extend it another year. Nevertheless to Software itself does not require any additional license keys, or is in any way "node" or "device" locked.


![Efinity IDE](/assets/images/Efinity_Software_01.png)

The Efinity IDE is easy to install and easy to use. Conceptionally it is similar to older FPGA IDEs like Xilinx ISE, but is also considerably less powerful. It is ok to organize the project files and run the synthesis flow, but not much else. The editor for source files is very basic or viewing reports is very basic. There is also no interated simulator, the IDE supports calling iVerilog, VCS and Modelsim, I have not tested it. I work with GHDL and Atom and/or VSCode anyway, so I'm not really missing much. 

The mechanism to move and resize windows in the IDE is a bit weird and obscure, but at least windows can be moved out of the main window e.g, to a second screen. 

I installed the IDE on a VM with LUbuntu 16.04 LTS. The installer script created a desktop Icon to start the IDE. When starting the IDE with this way,everything works, except the Efinity programmer. It works when you start the IDE with the following shell script:

    #!/bin/bash
    cd ~/opt/efinity/2019.2/ # Replace with your installation directory 
    source bin/setup.sh
    efinity

A part from this glitch, everything worked out of the box. Efinix also provides e.h. scripts to setup udev rules, etc.


The IDE is really fast. Starting the IDE is almost instantanous, Reopen a project takes 2-3 seconds. 
A complete rebuild of the [Bonfire SOC](https://github.com/ThomasHornschuh/bonfire-soc-fireant) project runs ~ 3 Minutes on my laptop.  


There are a few manuals for the Trion FPGAs wich can be downloaded from the Efinix support side. There are quite short, you can read all of them in half a day. To be honest, I don't miss much, I learned everything I need to use the T8. 


### Porting Bonfire to FireAnt

Altough the Trion T8 with its limited amount 24*5Kbit Block RAM is not a good target for a Soft CPU I was curious to implement the Bonfire CPU on it. As starting point I used a small top-level I built some time ago: The "Bonfire basic SoC". It is meant as SoC for such type of implementations and also for Simulations. 

My cores are all built with FuseSoC. Of course the is no FuseSoC backend for the Effinity IDE yet, so I just copied a build directory of a ghdl simulation run into the Efinix project. 

For the FireAnt I created a [bonfire_basic_soc_top.vhd](https://github.com/ThomasHornschuh/bonfire-soc-fireant/blob/master/local_src/bonfire_basic_soc_top.vhd). The task of the top entity is also to configure the Memory. Effinity is  not able to synthesize the normal "MainMemory" entity of  Bonfire SOC, because of the lack of byte lane write enables in the Trion Block RAMs. So I added a "laned" Memory with is constructed from "ram8" entities. 

More details can be found in the readme file of the project repository:  [https://github.com/ThomasHornschuh/bonfire-soc-fireant](https://github.com/ThomasHornschuh/bonfire-soc-fireant)

Basically it took me a few hours to get the project running. Besides from quite limited in RAM the T8 FPGA is quite capable of running a 32 Bit Soft-CPU, it consumes about 57% of the Logic Elements of the T8. Compared to Xilinx Series 6 or 7 FPGAs it runs only with about 25% of the clockrate. But the overal implementation experience was smooth, I like the simplicity of Trion Series FPGAs.  


