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
* Bonfire @ Digilent Arty
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



It is based on the LXP32 CPU [https://lxp32.github.io/](https://lxp32.github.io/)

The datapath/pipeline is basically still from LXP32. The main difference is in the instruction decoder which was completly rewritten to implement the RV32IM instruction set. See [https://riscv.org/specifications/]

The implementation also supports a subset of the RISC-V supervisor specification. The processor works only in M-mode. It is not fully compliant yet, because not all mandatory CSR registers are implemented. In addition to the orignal lxp-32 design a "true" instruction cache (16KB, direct-mapped) was implemented. The design works on a Xilinx Spartan-6 LX9 FPGA with about 100Mhz. The fully configured CPU with divider and instruction cache requires about 711 slices, 4 DSP48 blocks (for the multiplier), 2 RAMB8BWER (for the CPU register file) and 9 RAMB16BWER (for the Cache).  The design can be adjusted with various generics, but currently only the full configuration is tested.

The design is intented to work still also as lxp-32 CPU when the generic parameter RISCV is set to false, but currently I don't test this setting. There is no automated test bench yet, I'm preparing to run the RISC-V test suite on it. But there are several test programs in C and assembler which, together with the surrounding bonfire-soc, allow to test the CPU interactivly in a simulator.

The bus specification and also the timing of most cpu operations are still the same as in the LXP32:
 * Most simple arithmetic and logic instructions map 1:1 from RISC-V to the corresponding LXP32 instruction, regardless if they are an RISC-V immediate or register instruction.
 * Shifts take 2 cycle like shifts in LXP32
 * Branches and jumps also keep the same latency
 * SLT/SLTU have two cycle latency (because the LXP32 comparator takes two cycles)
 * CSR instructions also have two cycle latency
 * Load/Store latency is also the same as in LXP32
 * When generic MUL_ARCH is set to spartandsp the latency for multiplication operations is 4 instead of 2 (but slice utilisation is much better, because the adders of the DSP48 blocks are used).

The CPU supports the mtime and mtimecmp timer similar to the RISC-V privilege spec, but both registers are currently limited to 32Bit length.

The following interrupts are supported:
* Timer as defined in RISC-V privileged spec (MT bit in mie mip)
* 8 local external interupts, for this the mie and mip CSRs are extented to contain eight eie and eip bits.

In addition the following traps are supported:
 * Invalid Opcode
 * Misaligned access
 * ebreak
 * ecall


### Bonfire SoCs / Implementation

 Currently there are two implementations:

#### Bonfire @ Papilio Pro
 * 96Mhz Clock
 * 8KB Instruction Cache
 * Support for 8MB SDRAM on the Papilio Pro
 * UART for console I/O
 * SPI Flash to support

 There is currently no turnkey project on GitHub for this implementation. But if you clone https://github.com/bonfireprocessor/bonfire
 with subprojects you get all the source code for the Processor, the SoC and the Boot Monitor.


#### Bonfire @ Digilent Arty
 * 83Mhz Clock
 * 32KB Instruction Cache
 * 32KB Data Cache
 * Support for 256MB DDR3 RAM
 * UART for console I/O
 * SPI Flash Support
 * 100/100MBit Ethernet Support (Xilinx Ethernetlite Core)

 This project needs to be published.

 For both Projects a port of eLua is available at https://github.com/ThomasHornschuh/elua

