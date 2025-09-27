# Week 1 Day 4
# GLS, Blocking vs Non-Blocking and Synthesis-Simulation Mismatch
📘Indroduction to Gate Level Simulation
what is GLS
-Running the testbench with Netlist as Design Under Test -Netlist is logically same as RTL code -Same testbench will align with the design

why GLS
-verify the logical correctness of design after synthesis -Ensuring the timing of the design is met -For this GLS needs to be run with delay annotation.

GLS using iverilog
-Rest is similar only Gate level verilog models are feed to iverilog -If the Gate level models are delay annotated, then we can use GLS for timing validation

# Synthesis Simulation Mismatch
-Missing sensitivity list -Blocking Vs Non-Blocking statements -Non standard verilog coding

# Implementation of GLS

1) "ternary_operator_mux.v"
 ```
module ternary_operator_mux (input i0 , input i1 , input sel , output y);
	assign y = sel?i1:i0;
	endmodule
"tb_ternary_operator_mux.v"
`timescale 1ns / 1ps
module tb_ternary_operator_mux;
	// Inputs
	reg i0,i1,sel;
	// Outputs
	wire y;

        // Instantiate the Unit Under Test (UUT)
	ternary_operator_mux uut (
		.sel(sel),
		.i0(i0),
		.i1(i1),
		.y(y)
	);

	initial begin
	$dumpfile("tb_ternary_operator_mux.vcd");
	$dumpvars(0,tb_ternary_operator_mux);
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

⚙️Commands
```
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
````

<img width="1507" height="408" alt="ternary gtk" src="https://github.com/user-attachments/assets/adedcd62-bdca-409b-b262-3edf623db60d" />

GLS command
```
iverilog ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v  tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
<img width="1507" height="523" alt="glc ternary" src="https://github.com/user-attachments/assets/999f4b39-3398-4e77-a0f2-bd1881527354" />

Synthesis
```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
````
<img width="884" height="300" alt="ternary synth" src="https://github.com/user-attachments/assets/8bddf9a5-16d8-49d3-b1a3-9a9be956e863" />
