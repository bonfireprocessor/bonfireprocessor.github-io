---
title: Installation How-to-Guide
introduction:
    Installation of all the tools needed (e.g. Vivado, RISC-V gcc ) can be a challenge for the beginner. 
    To make it easer this guide will give  step-by-step instructions for the most important tools required to work with the Bonfire project
---

## Prerequisites
### Install Git
```
sudo apt install git
```
### Install Python 3.7
```
sudo apt install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt update
sudo apt install python3.7
sudo apt install python3-pip
sudo apt install python3-setuptools
```

### Install FuseSoC
```
sudo pip3 install --upgrade fusesoc ``
fusesoc --version
fusesoc init
fusesoc list-cores
```
### Install RISCV GCC Toolchain
```
cd ~/
git clone --recursive https://github.com/riscv/riscv-gnu-toolchain
sudo apt-get install autoconf automake autotools-dev curl python3 libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev
cd riscv-gnu-toolchain
mkdir build
cd build
../configure --prefix=/opt/riscv32 --with-arch=rv32im --with-abi=ilp32
sudo make
export TARGET_PREFIX=/opt/riscv32/bin/riscv32-unknown-elf
```
### Install Xilinx Vivado
```
chmod +x ./Xilinx_Unified_2019.2_1106_2127_Lin64.bin
./Xilinx_Unified_2019.2_1106_2127_Lin64.bin
/tools/Xilinx/Downloads/2019.2/xsetup
```
Install the Vivado HL WebPACK. For a more detailed guide follow Digilent's [Installing Vivado, Xilinx SDK, and Digilent Board Files](https://reference.digilentinc.com/vivado/installing-vivado/start).
### Install Serial Drivers
```
sudo /tools/Xilinx/Vivado/2019.2/data/xicom/cable_drivers/lin64/install_script/install_drivers/install_drivers
sudo adduser $USER dialout
```
### Install Digilent Board Files
```
mkdir ~/vivado
cd ~/vivado
wget https://github.com/Digilent/vivado-boards/archive/master.zip
sudo cp -ar ~/vivado/vivado-boards-master/new/board_files/* /tools/Xilinx/Vivado/2019.2/data/boards/board_files/
```

