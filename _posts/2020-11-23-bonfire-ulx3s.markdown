---
layout: post
title:  "Bonfire and eLua on ULX3S"
#date: "2019-11-24 8:20:25 +0200"
image: /assets/images/ulx3s_banner.jpg

introduction:
  About a month after my long-awaited ULX3S arrived I have finally my Bonfire Project with embedded Lua running on the ULX3s.
  

actions:
  - label: "View on GitHub"
    icon: github
    url: https://github.com/bonfireprocessor/bonfire-basic-soc

---

## About ULX3s
When I got the message from CrowsSupply at the 14th of March 2019 that the [ULX3S](https://www.crowdsupply.com/radiona/ulx3s) coampain started, I immediatly placed an order for the 85F version. This was a good idea to not wait, thr campain reached its goal only a day later.

The nice thing about the ULX3S is the very unique feature set:
  * The ECP5U FPGA is a capable device, especially the 85K LUT version is among the most powerful chips you can get in the sub 200 USD price range
  * It has all Peripherals on board needed for Softcore Processor and/or retro computing projects, both are the most popular uses cases for FPGA "enthusiasts". 
  * It has 32MB of SDR-SDRAM.  While it is not as fast and as big as DDR Chips there are plenty of open source memory controllers in VHDL and Verilog available. So it is relatively easy to use and the 32MB are way enough to run Linux on it. The avalailabilty of proven solutions is a big advantage also compared to HyperRAM or PSRAM. I think because of its synchronous nature it is even easier to use with an FPGA then SRAMs.
  * The possibilty of programming the FPGA and SPI flash with simple FTP commands over WiFi with the help of the ESP32 is a very unique feature which I like a lot. 
  * Having a proven FTDI chip as UART and fallback JTAG option contribute to the robustness of the board

An additional big plus is the community around ULX3S, there is a Gitter channel where the creators of the board, many engaged users and also people behind the open source toolchain. Compared to working e.g. with a Digilent board you can really get help when you get stuck.

I started my own FPGA journey a few years ago with a Papilio Pro board, but the comminity around this is more or less dead in the meantime. ULX3S seems to be a good replacement, I see also a lot of retro cores once been popular on the Papilio boards ported over to ULX3S.

## First steps
When the board arrived I immeidately tried to get it running. While the comminity is very active and there is plenty of docs in various GitHub repos, the start is not entirely easy. 
Paradoxically, this has to do with the amount of information available; it is too much rather than too little, and often somewhat contradicting itself. Therefore, the first steps are a bit difficult

The [Manual](https://github.com/emard/ulx3s/blob/master/doc/MANUAL.md) provides ~6 methods to program the FPGA, and also several methods to install the toolchain. It is hard to find out which is the best option to start. 
Some of the descriptions, like progrmming the FTDI chip EEPROM, are confusing, because when you get an UL3S from CrowdSupply it is already programmed.

### Tipps for starting
* For JTAG programming over FTDI fujprog on Linux (can be a VM...) seems to be the easiest option. The binaries in the (https://github.com/emard/ulx3s-bin) repo worked for me, but I was also able to build fujprog from here (https://github.com/kost/fujprog). Be aware that the readme for fujprog lists the prerequisites (sudo apt get...) after the build instructions...

* Using ESP32 FTP for programming seems to be the most comfortable way. MicroPyhton seem to be already installed on the CrowdSupply boards, but it need to be configured for your network of course. So the other steps from (https://github.com/emard/esp32ecp5) are required to set FTP up. So you need to install a "passthrough" Bitstream in the FPGA to acccess the ESP32 and install the WiFi and FTP startup script. 


With FTP in place I was able to install SaxonSoC and boot Linux from this instructions: [https://github.com/dok3r/ulx3s-saxonsoc/wiki/SaxonSoc-on-ULX3s](https://github.com/dok3r/ulx3s-saxonsoc/wiki/SaxonSoc-on-ULX3s)

## Porting Bonfire

Because Bonfire is written in VHDL I followed the recommendation from pople on gitter to start with Lattice Diamond first. I have not tried yet to compile the project with Yosys/GHDL, this will be one of the next steps. Lattice Diamond only supports Red Hat Linux. My experience with installing the very similar GoWin toolchain on Ubunutu last year was not so much fun (altough I finally got it working), so I decided this time to install Diamond on Windows (actually on all my Computers I run Windows as host OS and various Linux VMs for my embedded/FPGA work). 

My actual build setup for ECP5 is now [FuseSoC](https://fusesoc.readthedocs.io/en/rtd/fusesoc.html), GHDL and RISC-V toolchain on WSL1 (Windows Subsystem for Linux) and Diamond natively running on Windows. While this setting has some small hickups it works fine in general. 

I started my port with the [bonfire_basic_soc](https://github.com/bonfireprocessor/bonfire-basic-soc) repository. This is a "simple" SoC intended to use FPGA block RAMs and be easy ported to any FPGA family. It run already on Xilinx, Effinix and Gowin FPGAs. So I was expectiting running it on ECP5 should be easy. Synthesis was not really a problem, despite I need to add some patches to the edalize tool which is used to drive the EDA tool (aka Diamond in our case) by FuseSoC.
But the resulting Bitstream did not work. 
I took my a lot debugging hours to track the problem down. I started with small RISC-V assembler program doing status output on LEDs. This showed my that the processor was at least starting and access the memory mapped GPIO pins, but crashed on the first branch. In the next step I simulated the synthesized netlist in Active-HDL and could at least confirm that the simulation crashed at the same point as the hardware. So I encounrtered a Synthesis bug. 
Hours of tedious compares of waveform outputs from GHDL simulation with netlist simulation tracked the problem down to branch displacements screwed up in the decode stage. 
The not working piece of VHDL code was this function:
```
function fill_in(x: std_logic_vector; len:natural) return std_logic_vector is
variable temp : std_logic_vector(len-1 downto 0) := (others=>'0');
begin
  temp(x'high downto x'low) := x;
  -- sign extend x into temp
  for i in temp'high downto x'high+1 loop
    temp(i) := x(x'high);
  end loop;
  return temp;
end;
```
Branch displacements in RISC-V are defined as `subtype t_SB_immediate is std_logic_vector(12 downto 1);` because in RISC-V a branch can only target a 16-Bit aligned address. Synplify Pro Synthesis incorrectly passed the high and low attributes to the function, which leads to the branch displacement mapped right-shifted by one bit. 
It also failed to sign extend the displacement in a correct way. 
I found ohter problems with mapping immediates and displacements also and need to apply a few other patches to the decode stage until everything was right. Finally I verfifed the correct mapping of bits in the RTL diagram from Synpilfy Pro:

![MUX](/assets/images/disp_mux.png)

To my surprise I did not find any other Synthesis bugs so far, after fixing it all my test programs run fine on the hardware, UARTs and SPI flash access were also working immediatly. 

After the basics working I was adding a Clock PLL. While Synthesis estimated my design goof for over 90 Mhz (which I found good, because it is in the range of Xilinx FPGAs) I was a little disappointed to find that after Place & route it only reached around 70 Mhz without timing errors. Actually it worked with 96Mhz despite the timing violations, but I finally decided to stay with 62.5 Mhz. Looking at the critical path I think it will not be easy to make it faster without significant changes. 

Next step after the Basic SoC running was adding DRAM. I just adpated the ["Hamsterworks" SDRAM controller](https://github.com/bonfireprocessor/bonfire-basic-soc/blob/master/sdram/SDRAM_Controller.vhd) which I used on the Papilio Pro version of Bonfire and it worked nearly "out-of-the-box" after tweaking the `delay_line_length` generic.

After the very frustrating start with the displacement problems I was really happy how smooth the next steps worked. 

Next step was to adapt my [Embedded Lua port](https://github.com/ThomasHornschuh/elua) to ULX3S. This was a matter of a few hours to get it running. The convenience of the ESP32 FTP server was also evident, it has never been so easy to load a new build into the flash memory.

I decided to place the eLua Image at flash@600000, so it is not interfering with SBI/uBoot for SaxpnSoC. In this way it is easy to switch between SaxonSoC and Bonfire by just loading a different bistream. 
