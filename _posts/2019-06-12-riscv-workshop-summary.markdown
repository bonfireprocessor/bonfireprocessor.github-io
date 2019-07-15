---
layout: post
title: "RISC-V Workshop Summary"
date: "2019-06-12 11:15:25 +0200"


introduction:
  I took the opportunity to visit the RISC-V Workshop in Zürich at the 11-12.06.2019.
  It was a lot if information, so try to summarize my personal highlights.
---

### RISC-V RTL
The amount of "professional" RISC-V implementations is still increasing. Most of them follow the established model of open source software: The source code is open sourced, the   vendors earn money with selling implementation services.   An RISC-V implementer will have a lot of choice, but this is also most likely one of the  challenges in the RISC-V ecosystem.
There are initiatives like the CORE-V alliance with the goal to address the issue and provides a qualification mechanism for "commercial grade" cores.
Regarding language it seems that System Verilog is becoming the one of the more popular languages to implement RISC-V RTL.

### RISC-V Hardware
When starting with RISC-V in 2016 RISC-V processors in hardware did not really exists besides some test chips from Universities. The situation is changing a lot. SiFive tells that it has around ~100 design wins for their cores. Of course most of them will be embedded in some special function ASIC like storage controllers. But there are also chips available for everyone:

#### Vegaboard
The Vegaboard [(https://open-isa.org)](https://open-isa.org) is a bit of a strange device because it contains already 4 cores: A pair of ARM cores (Cortex-M0 and Cortex-M4) and a pair of PULP cores ( RI5CY and Zero-RISCY ). The chip is from NXP and seems to be a intended as test and development platform for RISC-V and ARM developments. To use the RISC-V chips on the board an JTAG adpater is required. The supported combination is a Segger J-Link hardware together with OpenOCD. A bit strange combination. At least the board can be bought at Amazon for 65 EUR. Together with a J-Link not really a very cheap entry into RISC-V for enthusiasts.

#### Greenwaves GAP8
The GAP8 Chip from [Greenwaves Technologies](https://greenwaves-technologies.com/)  is designed for Edge AI applications (like Computer Vision) and contains a cluster of 8 PULP RISC-V Cores combined local memory. The cores have an extended instruction set with bit manipulation, DSP and other extensions. It is a very interesting architecture but I think not the first choice for just experimenting with RISC-V ISA. Boards with the chip can be bought on their web shop.

#### Kendryte K210
Kendryte was not present at the RISC-V Workshop. I have asked several people wich are active in the RISC-V foundation about Kendryte. They seem not be activly participating in any RISC-V ecosystem organisations. I spoke with People from Western Digital, they ported OpenSBI to the K210 chip and also demonstrated NOMMU Linux on a Kendryte chip. The K210 seems to support S mode but without MMU, which is no something covered by the Spec. Also the CSRs have a lot of quirks.
Documentation of the whole chip is really lacking, they document their SDK API and anything else must be reverse engeneered from the SDK source code. The good thing is that they publish everything on GitHub and the boards are by far the cheapest way to get RISC-V hardware.

#### HiFive 1 Rev. B
The [HiFvie 1 Rev B](https://www.sifive.com/boards/hifive1-rev-b) is SiFive's second batch of the HiFive 1 board and the FE310 chip. The new version integrates a Segger J-Link OB probe instead of an FTDI chip. This makes setup debugging more robust and better documented. SiFive also released "Fredom metal" a bare metal library which is intended to interact with their core generators to automatize BSP generation. I will write about my experience with the HiFive 1 board in an extra article.

### RISC-V Spec and extensions
One of the unique features of the RISC-V ISA is the well defined extension concept. Starting from the small and easy to implement base ISA, a processor implementation is free to implement extensions. Among the established ones, like multiply/div, floating point, compression, atomics there are more specialized ones like vector. The extensions are still a field ongoing work in progress. For me the most interesting one presented at the workshop was the Bit manipulation extension. The tricky part is to find out, which of the propsoed instructions will really help to improve real world code. There are different use cases, like software floating point, cryptography or signal processing.
The current proposal is quite big, regarding number of opcodes. There was some amusement in the audience when Clifford Wolfs showed the "opcode sheet". It is bigger than the original RISC-V greencard.
My personal concern about extensions is, that over the time it will increase the complexity of RISC-V and make it no better than ARM or Intel. Even if all extensions beyond base are optional, in practice a lot of them may become mandatory for a competitive processor core, at least for "General purpose" application level cores.
For Linux already RV64GC is practically mandatory, where "G" stands for "IMAFD".
On the other side there is not much choice, for competing with Intel or ARM  at least support for SIMD and cryptography is requried. 

### Software

#### FreeRTOS  
Richard Barry from Amazon already presented an "official" FreeRTOS port to RISC-V at FOSDEM 2019 in Brussels and demonstrated running it on an NXP Vega board. At FOSDEM Time it was not upstreamed to Github. Now it is and it also mentioned on the FreeRTOS home page. Richards [presentation](https://www.youtube.com/watch?v=ZzPXEOK_sGI) was also a tutorial how to adapt FreeRTOS to a specific RISC-V core/SoC. This was really enlightening. With all other RTOS ports like Zephyr your are left pretty alone with finding out how to adapt to your specific hardware.  Another advantage of FreeRTOS to my opinion, is that it does not have a "intrusive" build system so it is easier to integrate into a project.

#### Compilers and Operating Systems

Jeermy Bennett and Marc Corbin from Embecosm presented the state of Compilers and operating systems for RISC-V. While gcc is really stable and passing all tests except a few corner cases, LLVM is still not at this point. Running the GNU compiler test suite against LLVM/Clang brings still a lot of [failures](https://youtu.be/-Wkvz1wpXT4?t=479).
The second part of the Embecosm presentation was about the status of various Linux distributions, FreeBSD, Zepyhyr and others. There is a lot of progress in that area, but especially the Linux and FreeBSD ports suffer from having no general available target hardware platform. Currently QEMU and the HiFive Unleashed board are the commonly used targets.
