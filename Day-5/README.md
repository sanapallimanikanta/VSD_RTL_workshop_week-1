# **Day 5: Optimization in Synthesis ⚙️**

Welcome to Day 5 of the RTL workshop\! Today, we will cover optimization in Verilog synthesis, focusing on **if-else** statements, **for** loops, generate blocks, and explore how improper coding can lead to inferred latches. Labs are included for hands-on experience. 🧪

-----

## **1. If-Else Statements in Verilog**

**If-else statements** are used for conditional execution in behavioral modeling, typically within procedural blocks (`always`, `initial`, tasks, or functions).

### **Syntax**

```verilog
if (condition) begin
    // Code block executed if condition is true
end else begin
    // Code block executed if condition is false
end
```

  - `condition`: An expression evaluating to true (non-zero) or false (zero).
  - `begin ... end`: Used to group multiple statements. Omit if only one statement is present.
  - The `else` part is optional.

### **Nested If-Else**

```verilog
if (condition1) begin
    // Code for condition1 true
end else if (condition2) begin
    // Code for condition2 true
end else begin
    // Code if no conditions are true
end
```

-----

## **2. Inferred Latches in Verilog 🔐**

**Inferred latches** occur when a combinational logic block does not assign a value to a variable in all possible execution paths. This causes the synthesis tool to infer a latch, which may not be the designer’s intention.

### **Example of Latch Inference**

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        if (sel == 1'b1)
            y = a; // No 'else' - y is not assigned when sel == 0
    end
endmodule
```

**Problem**: When `sel` is 0, `y` is not assigned, so a latch is inferred.

### **Solution: Add Else or Default Case**

```verilog
module ex (
    input wire a, b, sel,
    output reg y
);
    always @(a, b, sel) begin
        case(sel)
            1'b1 : y = a;
            default : y = 1'b0; // Default assignment
        endcase
    end
endmodule
```

-----

## **3. Labs for If-Else and Case Statements**

### **Lab 1: Incomplete If Statement**

```verilog
module incomp_if (input i0, input i1, input i2, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
end
endmodule
```
<img width="1144" height="688" alt="Image" src="https://github.com/user-attachments/assets/6cfc9155-3665-4e90-a10b-710103d8bac1" />

### **Lab 2: Synthesis Result of Lab 1**
<img width="1144" height="773" alt="Image" src="https://github.com/user-attachments/assets/f679ea2d-5acd-4b83-9d2d-04dd84144427" />

### **Lab 3: Nested If-Else**

```verilog
module incomp_if2 (input i0, input i1, input i2, input i3, output reg y);
always @(*) begin
    if (i0)
        y <= i1;
    else if (i2)
        y <= i3;
end
endmodule
```
<img width="1137" height="679" alt="Image" src="https://github.com/user-attachments/assets/67c1a741-a828-450e-b49e-c8bd0cf3ca62" />

### **Lab 4: Synthesis Result of Lab 3**
<img width="1117" height="694" alt="Image" src="https://github.com/user-attachments/assets/8926164b-8627-4e00-a435-1f00ac56896e" />

### **Lab 5: Complete Case Statement**

```verilog
module comp_case (input i0, input i1, input i2, input [1:0] sel, output reg y);
always @(*) begin
    case(sel)
        2'b00 : y = i0;
        2'b01 : y = i1;
        default : y = i2;
    endcase
end
endmodule
```
<img width="1620" height="844" alt="Image" src="https://github.com/user-attachments/assets/32e5e3cc-555b-40c1-bf66-ae789bb66c5b" />

### **Lab 6: Synthesis Result of Lab 5**
<img width="1315" height="718" alt="Image" src="https://github.com/user-attachments/assets/c3754220-d2ab-4edb-8f40-5d5f2a54bbfa" />

### **Lab 7: Incomplete Case Handling**

```verilog
module bad_case (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
always @(*) begin
    case(sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3; // '?' is a wildcard; be careful with incomplete cases!
    endcase
end
endmodule
```
<img width="1626" height="837" alt="Image" src="https://github.com/user-attachments/assets/522377ba-640e-4017-a764-1aac9c7c0b46" />

### **Lab 8: Partial Assignments in Case**

```verilog
module partial_case_assign (
    input i0, input i1, input i2,
    input [1:0] sel,
    output reg y, output reg x
);
always @(*) begin
    case(sel)
        2'b00: begin
            y = i0;
            x = i2;
        end
        2'b01: y = i1;
        default: begin
            x = i1;
            y = i2;
        end
    endcase
end
endmodule
```

-----

## **4. For Loops in Verilog 🔄**

A **for loop** is used within procedural blocks (`initial`, `always`, tasks/functions) to execute statements multiple times based on a loop counter.

### **Syntax**

`for (initialization; condition; increment) begin`
`// Statements to execute`
`end`

  - Must be inside procedural blocks.
  - Synthesizable only if the number of iterations is fixed at compile time.

### **Example: 4-to-1 MUX Using a For Loop**

```verilog
module mux_4to1_for_loop (
    input wire [3:0] data, // 4 input lines
    input wire [1:0] sel,  // 2-bit select
    output reg y           // Output
);
    integer i;
    always @(data, sel) begin
        y = 1'b0; // Default output
        for (i = 0; i < 4; i = i + 1) begin
            if (i == sel)
                y = data[i];
        end
    end
endmodule
```

-----

## **5. Generate Blocks in Verilog 🏗️**

A **generate block** is used to create hardware structures such as module instances or logic at compile time. Typically used with **for** loops and the `genvar` keyword.

### **Example**

```verilog
genvar i;
generate
    for (i = 0; i < 4; i = i + 1) begin : gen_loop
        and_gate and_inst (.a(in[i]), .b(in[i+1]), .y(out[i]));
    end
endgenerate
```

-----

## **6. What is an RCA (Ripple Carry Adder)?**

An RCA adds binary numbers using a chain of full adders. To add **n** bits, you need **n** full adders. Each carry-out connects to the carry-in of the next stage.

-----
<img width="886" height="374" alt="Screenshot 2025-09-27 214339" src="https://github.com/user-attachments/assets/9898dc5e-828f-412a-9201-b5ec2fc364e6" />


## **7. Labs on Loops and Generate Blocks**

### **Lab 9: 4-to-1 MUX Using For Loop**

```verilog
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);
wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};
integer k;
always @(*) begin
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end
endmodule
```
<img width="1332" height="687" alt="Image" src="https://github.com/user-attachments/assets/98901c57-4b49-4225-b7d2-cb70b084d78c" />

### **Lab 10: 8-to-1 Demux Using Case**

```verilog
module demux_case (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
always @(*) begin
    y_int = 8'b0;
    case(sel)
        3'b000 : y_int[0] = i;
        3'b001 : y_int[1] = i;
        3'b010 : y_int[2] = i;
        3'b011 : y_int[3] = i;
        3'b100 : y_int[4] = i;
        3'b101 : y_int[5] = i;
        3'b110 : y_int[6] = i;
        3'b111 : y_int[7] = i;
    endcase
end
endmodule
```
<img width="1201" height="683" alt="Image" src="https://github.com/user-attachments/assets/aa9bdc5f-f92f-492c-b713-ec268bc5f5b8" />

### **Lab 11: 8-to-1 Demux Using For Loop**

```verilog
module demux_generate (
    output o0, output o1, output o2, output o3,
    output o4, output o5, output o6, output o7,
    input [2:0] sel,
    input i
);
reg [7:0] y_int;
assign {o7, o6, o5, o4, o3, o2, o1, o0} = y_int;
integer k;
always @(*) begin
    y_int = 8'b0;
    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end
endmodule
```

### **Lab 12: 8-bit Ripple Carry Adder with Generate Block**

```verilog
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);
wire [7:0] int_sum;
wire [7:0] int_co;
genvar i;
generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa_1 (.a(num1[i]), .b(num2[i]), .c(int_co[i-1]), .co(int_co[i]), .sum(int_sum[i]));
    end
endgenerate
fa u_fa_0 (.a(num1[0]), .b(num2[0]), .c(1'b0), .co(int_co[0]), .sum(int_sum[0]));
assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```

**Full Adder Module**:

```verilog
module fa (input a, input b, input c, output co, output sum);
    assign {co, sum} = a + b + c;
endmodule
```
<img width="1285" height="741" alt="Image" src="https://github.com/user-attachments/assets/8fd2e97b-6398-4963-9f59-58568207f7c2" />



-----

## **5. Summary**

  * Use complete if-else and case statements to avoid unintended latch inference. 🛑
  * **For loops** and **generate blocks** are powerful for writing scalable, synthesizable code. ✨
  * Always ensure every signal is assigned in every possible execution path for combinational logic. ✅
  * Use labs to reinforce concepts with practical Verilog code and synthesis results. 💻
