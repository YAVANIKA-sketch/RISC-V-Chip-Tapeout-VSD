# RISC-V-Chip-Tapeout-VSD
🌱 Starting a new journey with the India RISC-V Tapeout Program.
India’s largest academic tapeout initiative with 3500+ participants! Excited to contribute to open-source silicon design, verification, and innovation, as we work together to build the nation’s semiconductor future.

#Week 0
#TASK 0
Install Required Tools

Use the machine configuration mentioned below:
RAM: 6 GB
HDD: 50 GB
OS: Ubuntu 20.04+
CPU: 4 vCPU

Installation Instructions
1️⃣ Oracle Virtual Machine
Download VirtualBox: https://www.virtualbox.org/wiki/Downloads

2️⃣ Yosys (Verilog Synthesis Tool)
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


3️⃣ Icarus Verilog (iverilog)
sudo apt-get update
sudo apt-get install iverilog

4️⃣ GTKWave (Waveform Viewer)
sudo apt-get update
sudo apt install gtkwave
