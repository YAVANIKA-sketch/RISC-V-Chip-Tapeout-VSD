# 🖥️ Running the MUX Simulation

I wrote and tested the 2:1 multiplexer (good_mux.v).

🔀 What is a Multiplexer (MUX)?

A Multiplexer (MUX) is a combinational digital circuit that selects one of several input signals and forwards it to a single output line.

It acts like a digital switch.

The selection of which input is passed to the output is controlled by select (sel) lines.

# 💡 Why is MUX important?

Used in processors (CPUs) to select data paths.

Helps in decision-making circuits.

Reduces hardware by allowing multiple inputs to share one output channel.

# 🔧 Steps Taken

Created good_mux.v with RTL design.

Wrote a testbench tb_good_mux.v to apply different inputs and sel.

Compiled and ran the design using Icarus Verilog (iverilog).

Viewed output waveforms in GTKWave.

What is RTL?

RTL (Register Transfer Level) is a way of describing digital circuits in terms of data flow between registers and the logic operations performed on that data.

# RTL code
```
module good_mux (input i0 ,
 input i1 ,
input sel ,
output reg y);

always @ (*)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```

# 📝 Explanation

Module Name: good_mux

Inputs:
```
i0 → first data input

i1 → second data input

sel → selection line (control signal)
```
Output:
```
y → output chosen based on sel
```
# 🔧 Logic:
```
If sel = 0, the output y = i0.

If sel = 1, the output y = i1.
```
This is the standard behavior of a 2-to-1 multiplexer (MUX), which acts like a switch to select one of two inputs.


What is a Testbench (TB)?

A Testbench (TB) is a piece of code written in an HDL (like Verilog/SystemVerilog) that is not part of the final hardware but is used to verify the functionality of your design (DUT – Device Under Test).

# Testbench
```
`timescale 1ns / 1ps
module tb_good_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	good_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_good_mux.vcd");
	$dumpvars(0,tb_good_mux);
	// Initialize Inputs
	sel = 0;
	i0 = 0;
	i1 = 0;
	#300 $finish;
	end

always #75 sel = ~sel;
always #10 i0 = ~i0;
always #55 i1 = ~i1;
endmodule
```
# 📜 Commands Used
  Compile the mux with testbench
  ```
iverilog -o good_mux_tb good_mux.v tb_good_mux.v
```
  Run the simulation
  ```
./good_mux_tb
```
  Open the waveform in GTKWave
  ```
gtkwave dump.vcd
```
<img width="1920" height="909" alt="gtkwave good_mux" src="https://github.com/user-attachments/assets/aea8407d-b62e-4d26-97d5-b2816a9ddf72" />

✅ Result

When sel = 0 → y = i0

When sel = 1 → y = i1


