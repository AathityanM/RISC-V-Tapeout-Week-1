# RISC-V-Tapeout-Week-1
# Day 1 - Introduction to Verilog RTL Design and Synthesis

<details>
  <summary>🔹 Introduction to Open-Source Simulator Iverilog</summary>

  ### Introduction to Iverilog: Design & Testbench
  * **Simulator**: A tool that mimics the hardware behavior of your Verilog/SystemVerilog code, allowing you to check functionality before implementation.
  * **Design**: Your RTL code (e.g., counter, ALU) that describes the circuit you want to build.
  * **Testbench**: A verification code that provides inputs to your design and checks if the outputs are correct. It is used only for simulation and is not synthesized.

  ### How Icarus Verilog (iverilog) Works
  1.  **Compile**: `iverilog -o sim_out design.v testbench.v`
  2.  **Run**: `vvp sim_out`
  3.  **Generate Waveform**: Add `$dumpfile("wave.vcd"); $dumpvars;` to your testbench to create a VCD (Value Change Dump) file.

  ### Waveform Viewing with GTKWave
  * **VCD File**: Stores signal transitions over time for debugging.
  * **GTKWave**: A GUI tool to visualize VCD files and debug your logic. Run it with `gtkwave wave.vcd`.

  ### Labs
  * **Lab 1 & 2**: Designing and testing a 2:1 Multiplexer (MUX) using Iverilog and viewing the output waveform in GTKWave.

</details>

<details>
  <summary>🔹 Introduction to Yosys and Logic Synthesis</summary>
  
  ### What is Yosys?
  * **Yosys** is an open-source framework that converts your Verilog RTL code into a gate-level netlist. This is the first step in the RTL to GDSII flow.
  * **Basic Flow**:
      1.  Read design: `read_verilog design.v`
      2.  Run synthesis: `synth`
      3.  Map to cells: `abc`, `dfflibmap`
      4.  Write netlist: `write_verilog gatelevel.v`

  ### What is Logic Synthesis?
  * **Definition**: The process of converting RTL code into a gate-level representation using a **standard cell library** (`.lib` file).
  * **.lib File**: A timing library that describes the function, timing, power, and area for each standard cell (like NAND, NOR, INV, etc.).

  ### Fast Cells vs. Slow Cells
  The synthesis tool chooses different "flavors" (sizes/strengths) of the same gate to meet timing and power goals.

  | Feature | Fast Cell | Slow Cell |
  | :--- | :--- | :--- |
  | **Delay** | Low | High |
  | **Power** | High | Low |
  | **Area** | Large | Small |
  | **Use Case** | Critical timing paths | Fixing hold violations, saving power |

  * **Why use slower cells?** To fix **hold violations**, where data arrives at a flip-flop too quickly. Slower cells add delay to the path, preventing this issue.

  ### Labs
  * **Lab 3**: Synthesizing the MUX design using Yosys and the `sky130` PDK. This involves converting the RTL MUX into a netlist of SKY130 standard cells (inverters, NAND gates, buffers, etc.) and verifying that the logic is still correct.

</details>
