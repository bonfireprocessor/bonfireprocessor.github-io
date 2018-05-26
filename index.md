---

---


## Welcome to the Bonfire Processor Project


Bonfire is a Softcore Processor and SoC designed for use on FPGAs. It is targeted to be "ready-to-use" on Low-Cost FPGA Boards.
An implementation of eLua makes the resulting systems easy to use and self-contained

## Features

####  RISC-V RV32IM compatible
* Supports RV32IM based on RISC-V User Level  ISA 2.1. (M-Spec is optional)
* Supports M-Mode according to RISC-V privilege Spec 1.10 and a Subset of CSR Registers

   + Implements all CSRs for Trap and Interrupt Handling (e.g. mtvec, mcause)
   + mcycle counter
   + Supports timer, external and up to 16 local Interrupts

####  Easy to understand and use
* Implemented  in  readable VHDL code
* Clean module structure
* Leverages FuseSoC as build system
* Testbenches can be run with Xilinx ISE, Xilinx Vivado and GHDL

#### Architecture
* 3-Stage Pipeline
* Separate Instruction and Data Buses
* Fast Multiplier (4 Cycles latency)
* Barrel Shifter (2 Cycle latency)
* Optional Instruction and Data Caches

## Configurations
### Flexible interfacing - Several Toplevel configurations available

#### Basic Core
* CPU Core only
* Low Latency Instruction Bus Interface
* Wishbone Data Bus Interface

#### Extended Core
* Instruction Cache
* Separate Wishbone Instruction and Data Buses
* Direkt Block RAM Interface

#### Bonfire AXI4
* Designed for Use in Xilinx Vivado IP Integrator
* Instruction and Data Caches
* Up to 128 Bit Wide AXI4 Busses for Fast Cache Refills

## Turnkey Implementations


#### Bonfire @ Papilio Pro

* 96Mhz Clock
* Bonfire Extended Core
* 8KB Instruction Cache
* SDRAM Controller for 8MB SDRAM on the Papilio Pro
* UART for console I/O
* SPI Flash for Program store
* 32 GPIO
* 2nd UART for Debugging


#### Bonfire @ Digilent Arty

* Bonfire AXI4 Core
* 83Mhz Clock
* 32KB Instruction Cache
* 32KB Data Cache
* Xilinx © MIG DDR RAM Controller
  * 256MB RAM
* UART for console I/O
* SPI Flash for program store
* 100/100MBit Ethernet Support (Xilinx© Ethernetlite Core)
* SD Card Interface with optinal Digilent© PMod SD
* 2nd UART on PMod for Debugging


## eLua

 For both Projects a port of eLua is available at https://github.com/ThomasHornschuh/elua

