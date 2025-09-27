# **Day 4: Gate-Level Simulation (GLS) 🚦, Blocking vs. Non-Blocking in Verilog, and Synthesis-Simulation Mismatch 🧩**

Welcome to Day 4 of the RTL Workshop\! Today’s session focuses on three essential topics in digital design:

  * **Gate-Level Simulation (GLS) ⚙️**
  * **Blocking vs. Non-Blocking Assignments in Verilog**
  * **Synthesis-Simulation Mismatch ⚠️**

You’ll learn both the theory and practical implications, complete with hands-on labs to reinforce your understanding. 🧪

-----
**Table of Contents**
1. Gate-Level Simulation (GLS)
2. Synthesis-Simulation Mismatch
3. Blocking vs. Non-Blocking Assignments in Verilog

   3.1 Blocking Statements

   3.2 Non-Blocking Statements

   3.3 Comparison Table

4. Labs

5. Summary
----
## **1. Gate-Level Simulation (GLS) ⚙️**

**GLS** stands for **Gate-Level Simulation**. It is a critical verification step in the VLSI design flow where the synthesized gate-level netlist of a digital circuit is simulated to validate:

  * Functional correctness ✅
  * Timing behavior ⏱️
  * Power estimates ⚡
  * Test structures (e.g., scan chains for DFT) ⛓️

### **Why Perform GLS?**

  * **Synthesis Validation**: Ensures that synthesis tools faithfully translate RTL into gates.
  * **Timing Verification**: Simulates with realistic delays (from SDF files), allowing you to check for timing violations (e.g., setup/hold errors). ⏰
  * **Testability**: Confirms that scan chains and other test features work post-synthesis.

### **When is GLS Performed?**

  * **After synthesis**: Once the RTL is converted into a gate-level netlist.
  * **Before physical design**: To catch issues early, before layout. 🏗️

### **Types of GLS**

  * **Functional GLS**: Logic-only simulation, often with zero or unit delays.
  * **Timing GLS**: Uses annotated timing data to check real-world timing behavior.

-----

## **2. Synthesis-Simulation Mismatch 🧩**

A **synthesis-simulation mismatch** occurs when the simulation results of RTL (pre-synthesis) do not match simulation results of the gate-level netlist (post-synthesis) or hardware. Reasons include:

  * **Non-synthesizable constructs**: Use of delays, `initial` blocks, or other code not supported by synthesis. 🚫
  * **Incomplete or ambiguous coding**: E.g., missing `else` clauses, improper sensitivity lists.
  * **Tool interpretation differences**: Simulation and synthesis tools may interpret ambiguous RTL differently. 🧠

**Key Point**: Always write synthesizable, unambiguous RTL and follow good coding practices to minimize mismatches. ✍️

-----

## **3. Blocking vs. Non-Blocking Assignments in Verilog**

Verilog offers two types of procedural assignments:

### **3.1 Blocking Statements (`=`)**

  * **Syntax**: `=`
  * **Execution**: Sequential, executes immediately. 🏃
  * **Suitable for**: Combinational logic (e.g., `always @(*)`).
  * **Example**: `always @(*) y = a & b;`

### **3.2 Non-Blocking Statements (`<=`)**

  * **Syntax**: `<=`
  * **Execution**: Scheduled, executes concurrently at the end of the time step. 🏁
  * **Suitable for**: Sequential logic (e.g., `always @(posedge clk)`).
  * **Example**: `always @(posedge clk) q <= d;`

### **3.3 Comparison Table**

| **Blocking (`=`)** | **Non-Blocking (`<=`)** |
| :--- | :--- |
| Uses `=` operator | Uses `<=` operator |
| Sequential, immediate execution | Concurrent, scheduled at end of timestep |
| Updates happen instantly in code order | Updates applied after time step |
| For combinational logic, temp variables | For sequential logic, registers/flip-flops |
| Infers combinational logic (gates) 🚪 | Infers sequential logic (flip-flops) 💾 |

-----

## **4. Labs**

### **Lab 1: Ternary Operator MUX**

Verilog code for a simple 2:1 multiplexer using a ternary operator:

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```

Function: `y = i1` if `sel = 1`; else `y = i0`.
<img width="1170" height="777" alt="Image" src="https://github.com/user-attachments/assets/210beefb-9a46-4d96-a07c-f7fcca4e6108" />

### **Lab 2: Synthesis Using Yosys**

Synthesize the above MUX using Yosys. Follow the standard Yosys synthesis flow. ➡️
<img width="1023" height="727" alt="Image" src="https://github.com/user-attachments/assets/917c4b4b-7268-4717-97b9-1e5933659c43" />

### **Lab 3: Gate-Level Simulation (GLS) of MUX**

Run GLS for the synthesized MUX.

Use this command (adjust paths as needed):

`iverilog /path/to/primitives.v /path/to/sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v`
<img width="1328" height="889" alt="Image" src="https://github.com/user-attachments/assets/61fc6cc0-1f05-4982-a1b6-4ecd2158c536" />

### **Lab 4: Bad MUX Example (Common Pitfalls) ⛔**

Verilog code with intentional issues:

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @ (sel) begin
    if (sel)
      y <= i1;
    else 
      y <= i0;
  end
endmodule
```

**Issues**:

  * **Incomplete sensitivity list**: Should include `i0`, `i1`, and `sel`.
  * **Non-blocking assignment in combinational logic**: Should use blocking assignments (`=`).

**Corrected version**:

```verilog
always @ (*) begin
  if (sel)
    y = i1;
  else
    y = i0;
end
```
<img width="996" height="675" alt="Image" src="https://github.com/user-attachments/assets/a7478940-8ae0-43fe-bd7e-a7318340f46c" />

### **Lab 5: GLS of Bad MUX**

Perform GLS on the `bad_mux`. Expect simulation mismatches or warnings due to above issues. 📉

<img width="1028" height="684" alt="Image" src="https://github.com/user-attachments/assets/0ccf139c-61d6-4f45-b554-c21f52b98c79" />

### **Lab 6: Blocking Assignment Caveat**

Verilog code:

```verilog
module blocking_caveat (input a, input b, input c, output reg d);
  reg x;
  always @ (*) begin
    d = x & c;
    x = a | b;
  end
endmodule
```

**What’s wrong?** ❓

The order of assignments causes `d` to use the old value of `x`—not the newly computed value.

**Best Practice**: Assign intermediate variables before using them. 💡

**Corrected order**:

```verilog
always @ (*) begin
  x = a | b;
  d = x & c;
end
```

### **Lab 7: Synthesis of the Blocking Caveat Module**

Synthesize the corrected version of the module and observe the results. 🔬
<img width="1023" height="768" alt="Image" src="https://github.com/user-attachments/assets/9087b124-3b7b-4561-b215-8e44083221c2" />

-----

## **5. Summary**

  * **Gate-Level Simulation (GLS)**: Validates netlist functionality, timing, and testability after synthesis. ✅
  * **Synthesis-Simulation Mismatch**: Avoid by using synthesizable, unambiguous RTL code. 🚫
  * **Blocking vs. Non-Blocking**: Use blocking (`=`) for combinational, non-blocking (`<=`) for sequential logic. 🤝
  * **Labs**: Reinforce key concepts and highlight common RTL pitfalls. 💡
