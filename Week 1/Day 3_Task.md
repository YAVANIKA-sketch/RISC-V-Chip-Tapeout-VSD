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

🧪 Day 3 Lab – Combinational Optimization Experiments

During the lab, we explored combinational optimization by writing small Verilog modules and observing how logic simplification affects outputs.

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
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```

Demonstrates logic factoring and conditional simplification.

Synthesis can optimize redundant signals and reduce gates.

✅ Key Learning from Lab:

Writing small modules with conditional logic helps understand how synthesis tools optimize combinational circuits.

Observed constant propagation, Boolean simplification, and logic factoring in action.

Reinforces theoretical concepts from Day 3 combinational optimization notes.
