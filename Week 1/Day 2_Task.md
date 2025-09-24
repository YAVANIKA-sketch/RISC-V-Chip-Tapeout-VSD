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

1. Module is the foundational building block of a system. 

    It is a major, self-contained unit that provides a specific, cohesive function.

    Represents a broad functionality, major component, or subsystem.

    Sits at a higher level; it may contain one or more submodules.

    Interacts with other modules and the top-level system via a well-defined interface (ports, APIs).

2. Submodule is a nested unit or a child of a main module. It handles a more specific or detailed aspect of the parent module's overall function.

   Represents a detailed function, internal component, or sub-function within the parent module.

   Sits at a lower level; it is instantiated or called by its parent module.

   Primarily interacts with its parent module and other submodules within the same parent.

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


module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```

Submodule (Component Instantiation): A module that is defined separately and then instantiated (used as a component) inside a higher-level module. The higher-level module connects the submodule's ports to its own internal signals or external ports.

Synthesis of submodule 1
<img width="1920" height="909" alt="synthesis sub_module1" src="https://github.com/user-attachments/assets/899aea46-a396-4087-a5a6-f8b10f99b44d" />


# Hierarchical vs Flat Synthesis

Synthesis is the process of converting RTL (Register Transfer Level) code (like Verilog) into a gate-level netlist using standard cells from a library. The choice between hierarchical and flat synthesis impacts design compilation time and optimization quality.

1. Hierarchical Synthesis 
   
    Each major functional module is synthesized independently (out-of-context), and then the top-level is combined.

    Pros: Significantly faster runtimes and lower memory footprint, making it essential for large-scale SoC designs.

    Cons: Optimization is restricted within module boundaries. Timing closure is harder for paths that cross between modules because the tool relies on conservative

    Interface Logic Models (ILMs) or user-defined timing budgets/constraints.

   Use: The standard approach for large designs to manage complexity and runtime, using a timing budget/floorplan to meet top-level constraints.
```
Generated by Yosys 0.9 

module multiple_modules(a, b, c, y);
  input a;
  input b;
  input c;
  wire net1;
  output y;
  sub_module1 u1 (
    .a(a),
    .b(b),
    .y(net1)
  );
  sub_module2 u2 (
    .a(net1),
    .b(c),
    .y(y)
  );
endmodule

module sub_module1(a, b, y);
  wire _0_;
  wire _1_;
  wire _2_;
  input a;
  input b;
  output y;
  sky130_fd_sc_hd__and2_0 _3_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );
  assign _1_ = b;
  assign _0_ = a;
  assign y = _2_;
endmodule

module sub_module2(a, b, y);
  wire _0_;
  wire _1_;
  wire _2_;
  input a;
  input b;
  output y;
  sky130_fd_sc_hd__or2_0 _3_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );
  assign _1_ = b;
  assign _0_ = a;
  assign y = _2_;
endmodule
```
<img width="1920" height="909" alt="synthesis multiple_modules" src="https://github.com/user-attachments/assets/23c38bca-5a11-4f21-8b5a-8e6bc3915b79" />

2. Flat Synthesis
   
    The entire design (all modules) is synthesized together as a single, large netlist.

    Pros: Achieves the best overall timing optimization because the tool can optimize paths across module boundaries without constraints.

   Cons: Extremely long runtimes and high memory usage for very large designs.

   Use: Recommended for small to medium-sized designs or for the final optimization of critical, small blocks.

```
 Generated by Yosys 0.9 

(* top =  1  *)
(* src = "multiple_modules.v:10" *)
module multiple_modules(a, b, c, y);
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire _0_;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire _1_;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire _2_;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire _3_;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire _4_;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire _5_;
  (* src = "multiple_modules.v:10" *)
  input a;
  (* src = "multiple_modules.v:10" *)
  input b;
  (* src = "multiple_modules.v:10" *)
  input c;
  (* src = "multiple_modules.v:11" *)
  wire net1;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire \u1.a ;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire \u1.b ;
  (* src = "multiple_modules.v:12|multiple_modules.v:5" *)
  wire \u1.y ;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire \u2.a ;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire \u2.b ;
  (* src = "multiple_modules.v:13|multiple_modules.v:1" *)
  wire \u2.y ;
  (* src = "multiple_modules.v:10" *)
  output y;
  sky130_fd_sc_hd__and2_0 _6_ (
    .A(_1_),
    .B(_0_),
    .X(_2_)
  );
  sky130_fd_sc_hd__or2_0 _7_ (
    .A(_4_),
    .B(_3_),
    .X(_5_)
  );
  assign \u1.a  = a;
  assign \u1.b  = b;
  assign net1 = \u1.y ;
  assign _1_ = \u1.b ;
  assign _0_ = \u1.a ;
  assign \u1.y  = _2_;
  assign \u2.a  = net1;
  assign \u2.b  = c;
  assign y = \u2.y ;
  assign _4_ = \u2.b ;
  assign _3_ = \u2.a ;
  assign \u2.y  = _5_;
endmodule
```
<img width="1920" height="909" alt="synthesis multiple_modules_flat" src="https://github.com/user-attachments/assets/34b110f4-5078-4072-8a58-fea882499ef0" />


