# Week 1  Day 2

# sky130_fd_sc_hd_tt_025C_1v80

Introduction to Timing Libraries (.lib files)

A timing library (.lib file) is essential for any modern digital design flow (synthesis, place and route, static timing analysis). It contains the characterization data for all the standard cells (e.g., inverters, NAND gates, flip-flops) in a technology.

This is a specific cell library used in CMOS (Complementary Metal-Oxide-Semiconductor) technology for designing digital circuits. Let's break down the different parts of its name and the related concepts mentioned in your notes.

The name itself, sky130_fd_sc_hd_tt_025C_1v80, represents a specific set of Process, Voltage, and Temperature (PVT) conditions under which the library cells are characterized.

# Terms related
1. Cell Definition : Begins with the keyword cell and defines the cell's inputs, outputs, and functionality.

   Eg: cell (AND2X1)

2. PVT Corner : Specifies the Process, Voltage, and Temperature conditions under which the cell was characterized.

   Eg: tt_025C_1v80 (Typical Process, 25°C, 1.8V)

3. Timing Model :	Most common is the Non-Linear Delay Model (NLDM), which uses a Look-Up Table (LUT) to model cell delay and output transition time based on:

      1) Input transition time.
   
      2) Output load capacitance.
   
Units	Defines the scale for all parameters within the library.	Time: ns, Voltage: 1V, Capacitance: pF, Power: nW.

# Key Terms and Parameters
sky130_fd: This is the name of the technology node. It refers to the SkyWater 130 nm process, which is an open-source foundry process. FD stands for "fully depleted," a characteristic of the Silicon-on-Insulator (SOI) fabrication process.

sc: This stands for "Standard Cell," which are pre-designed and pre-characterized building blocks (like logic gates, flip-flops, etc.) used to create more complex digital circuits.

hd: This indicates the "High Density" cell variant, meaning the cells are designed to be compact and have a smaller footprint, which is beneficial for reducing the overall chip area.

tt: This is the process corner, which refers to the "typical" process variation. In semiconductor fabrication, there are slight variations in how transistors are manufactured. The tt corner represents the most common and average-case variation. Other corners might include ss (slow-slow) or ff (fast-fast).

025C: This specifies the operating temperature as 25°C (Celsius). This is a typical room temperature at which the cells' performance is measured.

1v80: This denotes the operating voltage as 1.80V (Volts). This is the nominal supply voltage for the logic gates in this library.

# 🧱 The Core Concept: Modularity

Modules and submodules are fundamental concepts in both software engineering and digital hardware design (HDL). They are critical for managing complexity, promoting code reuse, and enabling a hierarchical design approach.

Modularity is the design principle of breaking down a large, complex system into smaller, self-contained, and manageable units.

# Modules and Submodules in Digital Hardware Design (HDL)

In Hardware Description Languages like Verilog or VHDL, a module is the primary container for describing a piece of hardware. This structure directly implements the principle of hierarchical design.

Module (Top-Level/Parent): In Verilog, a module block represents a major hardware component, like a CPU, a Peripheral Bus Interface, or a Memory Controller. It describes the component's external connections (ports) and its internal logic/structure.

Eg: Synthesis of multiple_modules
```
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule

```

Submodule (Component Instantiation): A module that is defined separately and then instantiated (used as a component) inside a higher-level module. The higher-level module connects the submodule's ports to its own internal signals or external ports.

```
module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```
# Hierarchical vs Flat Synthesis

Synthesis is the process of converting RTL (Register Transfer Level) code (like Verilog) into a gate-level netlist using standard cells from a library. The choice between hierarchical and flat synthesis impacts design compilation time and optimization quality.

1. Hierarchical Synthesis 
   
    Each major functional module is synthesized independently (out-of-context), and then the top-level is combined.

    Pros: Significantly faster runtimes and lower memory footprint, making it essential for large-scale SoC designs.

    Cons: Optimization is restricted within module boundaries. Timing closure is harder for paths that cross between modules because the tool relies on conservative

    Interface Logic Models (ILMs) or user-defined timing budgets/constraints.

   Use: The standard approach for large designs to manage complexity and runtime, using a timing budget/floorplan to meet top-level constraints.

⚙️Commands
```
read_verilog mupltiple_modules.v
synth -top multiple_modules
abc -liberty  ../lib/sky130_fd_sc_hd_tt_025C_1v80.lib
show
```
```
write multiple_modules_hier.v
```
<img width="1920" height="909" alt="synthesis multiple_modules" src="https://github.com/user-attachments/assets/23c38bca-5a11-4f21-8b5a-8e6bc3915b79" />

2. Flat Synthesis
   
    The entire design (all modules) is synthesized together as a single, large netlist.

    Pros: Achieves the best overall timing optimization because the tool can optimize paths across module boundaries without constraints.

    Cons: Extremely long runtimes and high memory usage for very large designs.

    Use: Recommended for small to medium-sized designs or for the final optimization of critical, small blocks.
⚙️Commands
```
read_verilog mupltiple_modules.v
synth -top multiple_modules
abc -liberty  ../lib/sky130_fd_sc_hd_tt_025C_1v80.lib

flatten
show
```
<img width="1920" height="909" alt="synthesis multiple_modules_flat" src="https://github.com/user-attachments/assets/34b110f4-5078-4072-8a58-fea882499ef0" />

# Flops and glitches 
Explore the foundational concepts of sequential digital logic—specifically latches, flip-flops, and the critical timing phenomenon known as glitches (or hazards). These elements are the building blocks for memory, registers, and complex finite state machines (FSMs) in all modern digital systems.

1. Latches and Flip-Flops (The Memory Elements)
   
   Latches and flip-flops are bistable multivibrators, meaning they have two stable states (0 and 1) and are the basic units for storing a single bit of information. The       key difference lies in how they are triggered to change their stored state:

   1) Latches (Level-Triggered)
      Triggering: They are level-sensitive or transparent. The output can change (become "transparent" to the input) for the entire duration that the enable signal (or 		  clock level) is active (e.g., when the clock is HIGH).

      Use: Often used in asynchronous circuits or as building blocks for flip-flops.

      Common Types: SR Latch, D Latch.

      Disadvantage: Can lead to unpredictable behavior if inputs change while the enable signal is active, causing race conditions or making it hard to synchronize data.

2) Flip-Flops (Edge-Triggered)
	Triggering: They are edge-triggered. The output only changes state at a single, precise moment—either the rising edge (LOW to HIGH transition) or the falling edge (HIGH 	to LOW transition) of the clock signal.

	Use: Essential components in synchronous circuits, like registers, counters, and FSMs, as they ensure all state changes are synchronized to the clock.

	Common Types:

	-> D (Data) Flip-Flop: The most common type. The output Q takes the value of the input D on the active clock edge. It's used for simple data storage and synchronization.

	-> T (Toggle) Flip-Flop: Toggles its output state on the active clock edge if the input T is HIGH. Useful for building counters and frequency dividers.

	-> JK Flip-Flop: A more versatile type that can Set, Reset, Hold, or Toggle based on the inputs J and K.

2. Glitches and Hazards (Timing Issues)
	A glitch is a temporary, unwanted pulse or "spike" that occurs at the output of a combinational logic circuit. A hazard is the potential for a glitch to occur due to 		unbalanced propagation delays within the circuit.

	Impact of Glitches:
	Combinational Circuits: In pure combinational logic, a glitch is often benign.

	Sequential Circuits (The Danger Zone): If the output of a combinational circuit with a glitch is fed into the data input of a level-sensitive latch or the control input 	of an edge-triggered flip-flop, the momentary spike can be misinterpreted as a valid data change, causing the entire sequential circuit to enter an incorrect state.

	Mitigation Techniques:
	Hazard-Free Design: For combinational logic feeding sensitive circuits, one can add redundant gates to the logic expression (using the consensus theorem on a Karnaugh 		Map) to eliminate static hazards.

	Synchronous Design:
	Using edge-triggered flip-flops (like the D-FF) and ensuring that the clock is the only signal controlling the memory elements prevents intermediate 	glitches from 		combinational logic from being stored. The flip-flop only samples the data input after the combinational logic has settled.
# Difference Between Synchronous and Asynchronous Reset D Flip-Flops ⚙️

1. Synchronous Reset DFF

	Reset is checked only on the active clock edge.

	Output q is reset to 0 when sync_reset=1 at the rising edge of clk.
	
	Advantage: avoids glitches, reset behavior is aligned with the clock.
	
	Drawback: requires clock to respond to reset.

2. Asynchronous Reset DFF

	Reset is checked independently of the clock.
	
	Output q goes to 0 immediately when async_reset=1, no need to wait for clk.
	
	Advantage: immediate response.
	
	Drawback: may cause metastability/glitches if reset is released near the clock edge.

# Example Verilog Modules 📜

Asynchronous Reset
```
module dff_asyncres ( input clk , input async_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
Commands
```
iverilog dff_asyncres.v tb_ dff_asyncres.v
./a.out
gtkwave tb_ dff_asyncres.vcd
```
<img width="1920" height="909" alt="dff_asyncres_syncres gtkwave" src="https://github.com/user-attachments/assets/20afe0fb-3769-4d56-8599-74f6a392b075" />

Asynchronous Set
```
module dff_async_set ( input clk , input async_set , input d , output reg q );
always @ (posedge clk , posedge async_set)
begin
	if(async_set)
		q <= 1'b1;
	else	
		q <= d;
end
endmodule
```
Commands
```
iverilog dff_async_set.v tb_ dff_async_set.v
./a.out
gtkwave tb_ dff_async_set.vcd
```
<img width="1920" height="909" alt="dff_async_set gtkwave" src="https://github.com/user-attachments/assets/36c95f2e-eb59-4c3b-9a63-712d2d827082" />

Synchronous Reset
```
module dff_syncres ( input clk , input sync_reset , input d , output reg q );
always @ (posedge clk )
begin
	if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
Commands
```
iverilog dff_syncres.v tb_ dff_syncres.v
./a.out
gtkwave tb_ dff_syncres.vcd
```
<img width="1920" height="909" alt="dff_syncres gtkwave" src="https://github.com/user-attachments/assets/e49522cd-3213-4497-a9a8-7874f22277fb" />

Combined Async + Sync Reset
```
module dff_asyncres_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk , posedge async_reset)
begin
	if(async_reset)
		q <= 1'b0;
	else if (sync_reset)
		q <= 1'b0;
	else	
		q <= d;
end
endmodule
```
Commands
```
iverilog dff_asyncres_syncres.v tb_dff_asyncres_syncres.v
./a.out
gtkwave tb_ dff_asyncres_syncres.vcd
```
<img width="1920" height="909" alt="sync+async gtkwave" src="https://github.com/user-attachments/assets/eb489cad-0280-40b4-95d8-958109975dd0" />

