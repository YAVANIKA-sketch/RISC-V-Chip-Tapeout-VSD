# Day 0
# Task 0

# Install Required Tools
Use the machine configuration mentioned below:
RAM: 6 GB
HDD: 50 GB
OS: Ubuntu 20.04+
CPU: 4 vCPU

Installation Instructions
Oracle Virtual Machine
Download VirtualBox: https://www.virtualbox.org/wiki/Downloads


# Yosys (Verilog Synthesis Tool)
sudo apt-get update
git clone https://github.com/YosysHQ/yosys.git
cd yosys
sudo apt install make           # if not installed
sudo apt-get install build-essential clang bison flex \
libreadline-dev gawk tcl-dev libffi-dev git \
graphviz xdot pkg-config python3 libboost-system-dev \
libboost-python-dev libboost-filesystem-dev zlib1g-dev
make config-gcc
make
sudo make install

![yosys installation](https://github.com/user-attachments/assets/385df0a8-ef89-44fc-8022-a83909236f30)

# Icarus Verilog (iverilog)
sudo apt-get update

sudo apt-get install iverilog

![iverilog installation](https://github.com/user-attachments/assets/1d3edfab-2c55-409c-b55d-254715c9875c)

# GTKWave (Waveform Viewer)
sudo apt-get update

sudo apt install gtkwave

![gtkwave installation](https://github.com/user-attachments/assets/d56f9f8b-d8bb-4c29-8c1c-1ceef5d8bba9)
