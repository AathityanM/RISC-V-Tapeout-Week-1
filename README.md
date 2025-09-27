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

# Day 2 - Timing libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles

<details>
  <summary>🔹 Introduction to Timing .libs</summary>
  
  ### What is a .lib file?
  A **.lib** (Liberty format) file is a text file from a standard cell vendor that describes the timing, power, and functional behavior of each cell (like AND, OR, Flip-Flops). It's a critical input for synthesis and timing analysis tools.

  ### What is inside a .lib file?
  * **Cell Definitions**: Logic function, pin directions, etc.
  * **Timing Information**: Propagation delays, setup/hold times, dependent on input slew and output load.
  * **Power Information**: Leakage, switching, and internal power consumption.
  * **Operating Conditions (PVT)**: Specifies the Process, Voltage, and Temperature corner the data is valid for.
  * **Physical Info**: Cell area, pin capacitance.

  ### What is PVT?
  PVT stands for **Process, Voltage, and Temperature**, the three main factors affecting a chip's performance.
  * **Process**: Variations in manufacturing. Corners include **FF** (Fast-Fast), **SS** (Slow-Slow), and **TT** (Typical-Typical).
  * **Voltage**: Fluctuations in the supply voltage. Higher voltage means faster cells but more power.
  * **Temperature**: The operating heat of the chip. Higher temperature usually means slower cells and more leakage.

</details>

<details>
  <summary>🔹 Hierarchical vs Flat Synthesis</summary>

  ### Flat vs. Hierarchical Synthesis
  This refers to how a synthesis tool processes a design with multiple modules.

  | Feature | Flat Synthesis | Hierarchical Synthesis |
  | :--- | :--- | :--- |
  | **Process** | Entire design is flattened into one large module. | Each module is synthesized separately. |
  | **Pros** | Better global optimization (area/timing). | Faster compile times, lower memory usage, easier to debug. |
  | **Cons** | Very slow for large designs, high memory usage. | Less global optimization, might have slightly worse results. |
  | **Use Case** | Small to medium designs. | Large SoCs, designs with multiple teams, IP reuse. |
  
  ### Why is Hierarchical Synthesis Needed?
  * **Scalability**: Manages the complexity of multi-million gate SoCs.
  * **Reusability**: Allows pre-synthesized IP blocks to be easily integrated.
  * **Parallel Development**: Different teams can work on different modules concurrently.
  * **Efficiency**: Drastically reduces runtime during development and debugging.

</details>

<details>
  <summary>🔹 Flop Coding Styles & Optimization</summary>
  
  ### What are Flops?
  A **Flip-Flop (Flop)** is a sequential element that stores one bit of data, capturing its input on a clock edge. They are fundamental for building stateful circuits like counters and state machines.
  
  ### Why are Flops Used?
  * **Avoid Glitches**: They sample data only at the clock edge, ignoring combinational logic glitches.
  * **Synchronization**: They align signals to a common clock.
  * **Break Long Paths**: Adding flops (pipelining) can shorten a critical path, allowing the design to run at a higher frequency.
  * **Data Storage**: They hold the state in registers, counters, and FSMs.

  ### Asynchronous vs. Synchronous Reset

  | Feature | Asynchronous Reset | Synchronous Reset |
  | :--- | :--- | :--- |
  | **Behavior** | Acts immediately, independent of the clock. | Only acts on the active clock edge. |
  | **Verilog Syntax** | `always @(posedge clk or posedge rst)` | `always @(posedge clk)` with an `if (rst)` inside. |
  | **Pros** | Fast, immediate reset. | Predictable, avoids metastability issues related to reset recovery/removal. |
  | **Cons** | Can be sensitive to glitches on the reset line. | Reset signal must be held until the next clock edge. |
  
  ### Optimization: Multiplication by 2ⁿ
  A common synthesis optimization is to replace multiplication by a power of 2 with a simple bit shift.
  * `A * 2` becomes `A << 1` (Shift left by 1)
  * `A * 8` becomes `A << 3` (Shift left by 3)
  This avoids using a complex and resource-intensive multiplier circuit, saving significant area, power, and delay. The synthesis tool implements this by simply re-wiring the connections, which is extremely efficient.

</details>
