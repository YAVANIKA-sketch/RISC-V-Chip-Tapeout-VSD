# Week 1 Day 3
# Combinational and Sequential Optimizations

# 📘 Introduction to Optimizations

🔹 What is Optimization?
Optimization in digital design is the process of improving an RTL/netlist so that the final circuit is smaller, faster, or more power-efficient without changing its intended functionality.

🔹 Why is it Used?

To reduce area (fewer gates → lower chip cost).

To reduce power (important for low-power/portable devices).

To improve timing (faster circuits meet clock frequency requirements).

To clean up design (remove unused logic, simplify expressions).

🔹 How is it Done?

Synthesis tools automatically apply optimizations such as constant propagation, Boolean simplification, retiming, clock gating, etc.

Designers can also write efficient RTL code to guide tools toward better results.

✅ Key Takeaway: Optimization ensures the design is efficient in area, power, and performance, while still functionally correct.

# ⚡ What is Combinational Optimization?

Combinational Optimization is the process of simplifying combinational logic circuits so that they use fewer gates, less area, and consume less power — while still producing the same correct output for all input combinations.

🔹Why is it needed?

Reduces hardware cost (fewer gates).

Improves speed/timing (less logic delay).

Saves power.

Makes the circuit cleaner and more efficient.

🧮 Methods Used for Combinational Optimization

Boolean Algebra Rules – algebraic simplifications.

Logic Identities – using absorption, distribution, consensus theorems, etc.

Karnaugh Maps (K-map) – visual method to minimize SOP/POS forms.

Quine–McCluskey Algorithm – tabular method for systematic minimization.

✅ Key Takeaway: These techniques simplify combinational circuits, leading to fewer gates, smaller area, lower power, and faster timing.
✅ Key Idea: Combinational optimization means finding the simplest equivalent circuit without changing functionality.

⏱️ What is Sequential Optimization?

Sequential Optimization is the process of improving circuits that use flip-flops, latches, and registers to make them faster, smaller, or lower-power, while keeping the same behavior. Unlike combinational optimization, it focuses on designs with memory elements.

🔹 Why is it needed?

To meet timing requirements (higher clock speeds).

To reduce power and area by removing unused or redundant registers.

To improve performance of finite state machines (FSMs).

To balance critical paths in pipelined designs.

🔹 How is it done?

Retiming – repositioning registers across logic to balance delay.

State Optimization – minimizing or encoding FSM states efficiently.

Register Cloning – duplicating registers to reduce fanout delay.

Removing Unused Outputs – eliminating flops/registers that don’t affect outputs.

📌 Short Notes on Key Techniques:

🔄 Retiming

Moving flip-flops forward or backward across combinational logic.

Balances path delays → improves maximum clock frequency.

Example: move registers closer to inputs/outputs to shorten longest path.

🧩 State Optimization

Used in FSMs.

Removes redundant or equivalent states.

Assigns efficient state encodings (binary, Gray, one-hot) for area/speed tradeoffs.

🪞 Register Cloning

Duplicate a register if it drives many loads (high fan-out).

Each clone drives a smaller group of loads → reduces delay.

Common in high-performance designs.

✅ Key Idea: Sequential optimization improves the timing, area, and power of designs with registers and state machines, ensuring they run efficiently at the target clock speed.

# 🧪 Combinational Optimization Experiments

During the lab, I explored combinational optimization by writing small Verilog modules and observing how logic simplification affects outputs.

🔹 Lab Modules

1) Simple Conditional Logic
```
module opt_check (input a , input b , output y);
	assign y = a ? b : 0;
endmodule
```
Output y depends on a.

If a=1, y=b; else y=0.

⚙️Commands
```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<img width="1920" height="909" alt="opt_check" src="https://github.com/user-attachments/assets/555fc6d9-3a94-4bc9-bd71-431a31089560" />

2) Conditional Logic with Constant
```
module opt_check2 (input a , input b , output y);
	assign y = a ? 1 : b;
endmodule
```
⚙️Commands
```
read_verilog opt_check2.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="opt_check2" src="https://github.com/user-attachments/assets/2fa75752-bc34-4a52-8737-95290dc6896e" />

Demonstrates constant propagation optimization.

If a=1, output is always 1.

3) Nested Conditional Logic
```
module opt_check3 (input a , input b, input c , output y);
	assign y = a ? (c ? b : 0) : 0;
endmodule
````
⚙️Commands
```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check3.v
synth -top opt_check3
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="opt_check3" src="https://github.com/user-attachments/assets/d935250a-37d9-441d-99d1-f13f0966b733" />

Shows nested ternary operators and how synthesis tools simplify them.

Only evaluates b when both a=1 and c=1.

4) Complex Conditional Logic
````
module opt_check4 (input a , input b , input c , output y);
	assign y = a ? (b ? (a & c) : c) : (!c);
endmodule
````
⚙️Commands
```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="opt_check4" src="https://github.com/user-attachments/assets/90384086-ec15-4195-870f-e9ebada36f1b" />

5) Multiple opt
```
module sub_module1(input a , input b , output y);
assign y = a & b;
endmodule


module sub_module2(input a , input b , output y);
assign y = a^b;
endmodule


module multiple_module_opt(input a , input b , input c , input d , output y);
wire n1,n2,n3;

sub_module1 U1 (.a(a) , .b(1'b1) , .y(n1));
sub_module2 U2 (.a(n1), .b(1'b0) , .y(n2));
sub_module2 U3 (.a(b), .b(d) , .y(n3));

assign y = c | (b & n1); 

endmodule
```
⚙️Commands
```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
opt_clean -purge
show
```
<img width="1920" height="909" alt="opt_multiple_module" src="https://github.com/user-attachments/assets/3e06d48a-d25f-4f22-aed8-f2de8e5e8a0e" />

Sub-modules behavior
```
sub_module1 → y = a & b

sub_module2 → y = a ^ b (XOR)
```
Output y = c | (a & b)


- Demonstrates logic factoring and conditional simplification.

- Synthesis can optimize redundant signals and reduce gates.

# ✅ Key Learning from Lab:

:> Writing small modules with conditional logic helps understand how synthesis tools optimize combinational circuits.

:> Observed constant propagation, Boolean simplification, and logic factoring in action.

:> Reinforces theoretical concepts from Day 3 combinational optimization notes.

# 🧪 Sequential Optimization Experiments

During this lab, I implemented and tested various sequential circuits including flip-flops, counters, and finite state machines. The experiments focused on understanding clocked behavior, timing relationships, and state transitions in digital systems.

🔹 Lab Modules

1) D Flip-Flop with Reset to 0

RTL

```
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule

```
Testbench

```

`timescale 1ns / 1ps
module tb_dff_const1;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const1 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const1.vcd");
	$dumpvars(0,tb_dff_const1);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```
Output Q is 0 after reset; becomes 1 after the first clock pulse.

⚙️Commands
```
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const1.vcd
```
<img width="1920" height="909" alt="dff_syncres gtkwave" src="https://github.com/user-attachments/assets/06bfd7d4-303f-464f-b58a-70191a36c8e9" />

Synthesis
```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```

<img width="1920" height="909" alt="dff_const1_synthesis" src="https://github.com/user-attachments/assets/462e6467-fc1a-44d2-be2a-463f9993c135" />

2) D Flip-Flop Always 1

RTL

```
module dff_const2(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b1;
	else
		q <= 1'b1;
end
endmodule

```
Testbench
```

`timescale 1ns / 1ps
module tb_dff_const2;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const2 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const2.vcd");
	$dumpvars(0,tb_dff_const2);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```

⚙️Commands
```
iverilog dff_const2.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const2.vcd

```
<img width="1920" height="909" alt="dff_const2 gtkwave" src="https://github.com/user-attachments/assets/038cd37f-649d-40de-ba7e-bb4599136513" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="dff_const2 synthesis" src="https://github.com/user-attachments/assets/26ab9c71-6e3a-4ba2-a72d-8545ce886458" />


Q is always 1, showing constant propagation through sequential logic.

3) D Flip-Flop with Internal Register 1

RTL

```
module dff_const3(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule

```
Testbench
```

`timescale 1ns / 1ps
module tb_dff_const3;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const3 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const3.vcd");
	$dumpvars(0,tb_dff_const3);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```
⚙️Commands
```
iverilog dff_const3.v tb_dff_const3.v
./a.out
gtkwave tb_dff_const3.vcd
```
<img width="1920" height="909" alt="dff_const3 gtkwave" src="https://github.com/user-attachments/assets/bb7a7bf7-f99a-45a9-9505-a43e13b2e084" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const3.v
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```

<img width="1920" height="909" alt="dff_const3 synth" src="https://github.com/user-attachments/assets/1db2bca9-aa72-4535-ba0f-848caa988899" />

Demonstrates delayed update using an internal register; Q follows q1.

4) D Flip-Flop with Internal Register 2

RTL

```
module dff_const4(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b1;
		q1 <= 1'b1;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule

```

Testbench

```


`timescale 1ns / 1ps
module tb_dff_const4;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const4 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const4.vcd");
	$dumpvars(0,tb_dff_const4);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```
⚙️Commands
```
iverilog dff_const4.v tb_dff_const4.v
./a.out
gtkwave tb_dff_const4.vcd

```
<img width="1920" height="909" alt="dff_const4 gtkwave" src="https://github.com/user-attachments/assets/80dbb4c4-6297-40d4-8f40-6fa422e7e859" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="dff_const 4 synth" src="https://github.com/user-attachments/assets/f87181f4-0761-407d-aed6-27561d099d13" />

Q stays constant at 1 after reset; internal register simplifies sequential behavior.

5) D Flip-Flop with Internal Register 3

RTL

```
module dff_const5(input clk, input reset, output reg q);
reg q1;
always @(posedge clk, posedge reset)
begin
	if(reset)
	begin
		q <= 1'b0;
		q1 <= 1'b0;
	end
	else
	begin
		q1 <= 1'b1;
		q <= q1;
	end
end
endmodule

```
Testbench

```
`timescale 1ns / 1ps
module tb_dff_const5;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	dff_const5 uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_dff_const5.vcd");
	$dumpvars(0,tb_dff_const5);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```
⚙️Commands
```
iverilog dff_const5.v tb_dff_const5.v
./a.out
gtkwave tb_dff_const5.vcd

```
<img width="1920" height="909" alt="dff const 5 gtkwave" src="https://github.com/user-attachments/assets/42d0c426-d376-41ef-8683-38c579a4e4b5" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="dff_const5 synthesis" src="https://github.com/user-attachments/assets/7a974163-b71d-4655-9bc0-33f13808af32" />


Shows a flip-flop that starts at 0 after reset and updates to 1 through internal register.

✅ Key Learning from Lab:

:> Understanding reset behavior and sequential propagation in D flip-flops.

:> Observed how internal registers affect timing and delayed updates.

:> Learned how synthesis tools handle constant outputs and sequential optimizations.

# 🧪 Counter Optimization Experiments

During the lab, I explored sequential counters and how synthesis tools can optimize them for area, speed, and power by reducing redundant logic and using efficient coding styles.

🔹 Lab Modules

1) Counter_opt

RTL

```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule
```

Testbench

```
`timescale 1ns / 1ps
module tb_counter_opt;
	// Inputs
	reg clk, reset   ;
	// Output
	wire q;

        // Instantiate the Unit Under Test (UUT)
	counter_opt uut (
		.clk(clk),
		.reset(reset),
		.q(q)
	);

	initial begin
	$dumpfile("tb_counter_opt.vcd");
	$dumpvars(0,tb_counter_opt);
	// Initialize Inputs
	clk = 0;
	reset = 1;
	#3000 $finish;
	end

always #10 clk = ~clk;
always #1547 reset=~reset;
endmodule

```
⚙️Commands
```
iverilog counter_opt.v tb_counter_opt.v
./a.out
gtkwave tb_dff_const5.vcd

```
<img width="1920" height="909" alt="counter_opt gtk" src="https://github.com/user-attachments/assets/904b770d-af98-417f-b1e5-be87e4c4b78f" />

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="counter_opt synthesis" src="https://github.com/user-attachments/assets/fd4ec0c4-708b-4c9a-a9c9-a9b27619af53" />

2) counter_opt2

RTL

```
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);

always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end

endmodule

```

Synthesis

```
yosys
read_liberty  -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt.v
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
<img width="1920" height="909" alt="counter_opt2 synthesis" src="https://github.com/user-attachments/assets/94130d89-5ea0-47f7-9753-31ffca453da9" />

