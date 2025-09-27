# **Day 1: Introduction to Verilog RTL Design & Synthesis**

Welcome to **Day 1 of the RTL Workshop!**
Today, you will begin your journey into digital design by exploring **Verilog**, open-source simulation with **Icarus Verilog (iverilog)**, and the basics of **logic synthesis using Yosys**.

This guide includes **practical labs, essential concepts, and clear explanations** to help you build a solid foundation in RTL design.

---

## **Table of Contents**

1. What is a Simulator, Design, and Testbench?
2. Getting Started with Icarus Verilog (iverilog)
3. Lab: Simulating a 2-to-1 Multiplexer
4. Verilog Code Analysis
5. Introduction to Yosys & Gate Libraries
6. Synthesis Lab with Yosys
7. Summary

---

## **1. What is a Simulator, Design, and Testbench?**

* **Simulator**
  A software tool that verifies your digital circuit’s functionality. It applies test inputs and checks outputs before hardware implementation.

* **Design**
  The Verilog code that describes your intended logic functionality.

* **Testbench**
  A simulation environment that generates inputs for your design and validates the outputs.

👉 In short: **Design + Testbench → Simulator → Verified Output**

---

## **2. Getting Started with iverilog**

**iverilog** is an open-source Verilog simulator.

**Simulation Flow:**

1. Provide **design** and **testbench** files to iverilog.
2. Run simulation to generate `.vcd` (waveform) file.
3. View results in **GTKWave**.

---

## **3. Lab: Simulating a 2-to-1 Multiplexer**

### Step 1: Clone the Workshop Repository

```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

### Step 2: Install Required Tools

```bash
sudo apt install iverilog
sudo apt install gtkwave
```

### Step 3: Simulate the Design

```bash
iverilog good_mux.v tb_good_mux.v
./a.out
gtkwave tb_good_mux.vcd
```

✔ Open **GTKWave** to visualize the waveform.

---

## **4. Verilog Code Analysis**

### Multiplexer Code (`good_mux.v`)

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
    always @ (*) begin
        if(sel)
            y <= i1;
        else
            y <= i0;
    end
endmodule
```

**Explanation:**

* **Inputs** → `i0`, `i1` (data inputs), `sel` (select line)
* **Output** → `y` (selected output)
* **Logic** → If `sel = 1`, then `y = i1`; else `y = i0`.

---

## **5. Introduction to Yosys & Gate Libraries**

* **Yosys** → An open-source tool for **synthesis** (converting Verilog RTL into gate-level netlist).

### Key Features

* **Synthesis**: HDL → logic circuit
* **Optimization**: Improves area and speed
* **Technology Mapping**: Matches design to real hardware cells
* **Verification**: Ensures correctness
* **Extensibility**: Supports custom flows

### Why Do Libraries Have Different Gate Flavors?

* **Performance**: Fast gates for critical paths
* **Power**: Low-power gates for efficiency
* **Area**: Smaller gates for compact designs
* **Drive Strength**: Strong gates to handle heavy loads
* **Signal Integrity**: Noise/performance optimized versions

👉 The `.lib` file provides these gate variations for synthesis tools to choose the best match.

---

## **6. Synthesis Lab with Yosys**

### Step-by-Step Flow

1. Start Yosys:

   ```bash
   yosys
   ```

2. Read the Liberty library:

   ```bash
   read_liberty -lib /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

3. Read the Verilog design:

   ```bash
   read_verilog /home/vsduser/VLSI/sky130RTLDesignAndSynthesisWorkshop/verilog_files/good_mux.v
   ```

4. Run synthesis:

   ```bash
   synth -top good_mux
   ```

5. Technology mapping:

   ```bash
   abc -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
   ```

6. Visualize gate-level netlist:

   ```bash
   show
   ```

---

## **7. Summary**

✅ Learned about **simulators, designs, and testbenches**
✅ Simulated your first Verilog design using **iverilog & GTKWave**
✅ Analyzed the **2-to-1 multiplexer** Verilog code
✅ Explored **Yosys** and **gate library flavors**
✅ Performed **logic synthesis** and visualized a **gate-level netlist**

---

✨ That completes **Day 1** of the RTL Workshop!


