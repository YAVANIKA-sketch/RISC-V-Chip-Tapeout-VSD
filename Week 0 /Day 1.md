
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

# 📜 Commands Used
  Compile the mux with testbench
iverilog -o good_mux_tb good_mux.v tb_good_mux.v

  Run the simulation
./good_mux_tb

  Open the waveform in GTKWave
gtkwave dump.vcd


✅ Result

When sel = 0 → y = i0

When sel = 1 → y = i1


# 📝 Explanation

Module Name: good_mux

Inputs:

i0 → first data input

i1 → second data input

sel → selection line (control signal)

Output:

y → output chosen based on sel

# 🔧 Logic:

If sel = 0, the output y = i0.

If sel = 1, the output y = i1.

This is the standard behavior of a 2-to-1 multiplexer (MUX), which acts like a switch to select one of two inputs.
