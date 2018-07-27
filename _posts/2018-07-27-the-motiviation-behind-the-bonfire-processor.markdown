---
layout: post
title: "The Story behind the Bonfire Processor - Part I"
date: "2018-07-27 20:53:01 +0200"
---

In my professional live I'm a software development manager at [ORTEC B.V.](www.ortec.com) We are focusing
on Software solutions for all sorts of logistics optimization, e.g. optimization of transportation
routes or workforce schedules. If you want to know more about it, visit our Website.

While starting on hardware level already a school time, the twists and events in my life have moved me more and more up on the technology stack, so that today I have to do with cloud platforms, virtualization and a lot of other stuff many levels above the hardware.

So about 2.5 years ago I started to experiment with FPGAs. Just as a hobby, and as an idea to go back to my "roots", but with modern technologies. I was always been interested in hardware and especially in processor architecture. I never stopped to read literature, articles, white papers from processor vendors, etc. over all the years.

FPGAs are a great tool to democratize access to hardware design. As a single person I can basically design my own chip. Of course not a processor core competing with a Intel processor or an ARM application class core, but something comparable with a Cortex-M micro controller core.

While doing research about the topic I stumbled over RISC-V. I also experimented with an open source RISC-V core on VHDL, the "Potato Processor". It is nice design, I tried to adapt it to the Papilio Pro Spartan-6 Board I was using at that time (originally it is designed for the Arty  board). It was not complicated to adapt to ISE/Spartan-6 and after one or two days of work I could successfully simulate it. Unfortunately I did not get it running on the real hardware, for some reason it crashed after a few instructions.

On of the lessons I learned is that there are a lot of subtle differences between simulation and synthesis, at least with the Xilinx tools. To be on the save side it is important to simulate and test on hardware in parallel as early as possible. It is a lot of work, because hardware "test benches" and simulation test benches are completely different things and testing and debugging is much harder than in software development.

Finally I gave up on Potato, also because the timing closure I was able to reach on the Spartan-6 was also not very promising. I had seen from other designs that 100Mhz should be no problem for a 32 Bit pipelined RISC design.

During my research I also found the [LXP32 CPU](https://lxp32.github.io/), a simple 32 Bit 3 Stage design. It easily reached about 128Mhz on the Spartan-6 and I was also able to get it running (it can only be programmed in its special assembly language). I also found it quite well documented and easy to understand. After some thinking about it, I found it reasonable to just replace the decode stage with a RISC-V decoder.
And indeed it was easy, after a few days the first RISC-V assembly programs run on it, after around a month I had the full RV32IM instruction set implemented so I could run a "Hello World" C program. The LXP32 execution stage had a few things missing for RISC-V, e.g. 16Bit Load/Stores, or the upper 32 Bit Multiplication. But this was easy to add.

So after a short time a had a full working RISC-V 32Bit user level implementation.
