# VSD Tapeout Program - Day 3 Progress

Welcome to my documentation of Day 3 in the **VSD Tapeout Program**! This file details my exploration of optimization techniques for combinational and sequential circuits, supported by hands-on Verilog labs and synthesis exercises.

***

## 📚 Table of Contents
1. [Combinational Optimization Techniques](#1-combinational-optimization-techniques)
    - [Constant Propagation](#constant-propagation)
2. [Sequential Optimization Techniques](#2-sequential-optimization-techniques)
    - [State Optimization](#state-optimization)
    - [Retiming](#retiming)
3. [Structural Optimization: Cloning](#3-structural-optimization-cloning)
4. [Lab Experiments & Results](#4-lab-experiments--results)
    - [Combinational Optimization Labs](#combinational-optimization-labs)
    - [Sequential Optimization Labs](#sequential-optimization-labs)
    - [Sequential Unused Output Optimization](#sequential-unused-output-optimization)

***

## 1. Combinational Optimization Techniques

### Constant Propagation
In VLSI, constant propagation substitutes variable values with constants at synthesis time. This reduces circuit size and logic depth.

- **Process:** The tool automatically finds constant-valued signals and replaces variable references
- **Benefits:**
    - Reduced combinational logic
    - Faster execution and decreased delay paths
    - Fewer logic gates in the physical design


***

## 2. Sequential Optimization Techniques

### State Optimization
Finely tunes FSMs (finite state machines) by:
- **State Reduction**: Merges equivalent or unreachable states
- **Encoding Optimization**: Assigns efficient binary encodings
- **Logic Minimization**: Applies Boolean reduction for next-state and output logic
- **Power Optimization**: Integrates techniques like clock gating

### Retiming
Repositions flip-flops (registers) throughout a circuit to balance logic delays and improve critical path timing.

- **Graph-Based Analysis:** Model designs as data-flow graphs
- **Movement Constraints:** Repositions registers while preserving functionality
- **Purpose:** Reduces maximum clock period and enables finer pipelining

***

## 3. Structural Optimization: Cloning
Cloning involves duplicating logic cells or modules along critical paths to lower delay, reduce load, or rebalance nets for timing closure.

- **Flow:**
    - Identify critical signal paths
    - Duplicate key cells/modules
    - Interconnect and validate the new structure

- **Benefits:**
    - Improved timing and load management
    - Enhanced reliability for high-performance circuits


***

## 4. Lab Experiments & Results

### Combinational Optimization Labs

#### Lab 1: Ternary If Simplification
```verilog
module opt_check (input a , input b , output y);
    assign y = a ? b : 0;
endmodule
```
- **Description:** Outputs `b` when `a` is high, else outputs `0`. The synthesizer simplifies unnecessary logic due to constant propagation.
- The logic optimises to 
```verilog
y = a & b ;
```
**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.09.06_a8c71f5c.jpg>)

***

#### Lab 2: Mux Constant Output
```verilog
module opt_check2 (input a , input b , output y);
    assign y = a ? 1 : b;
endmodule
```
- **Description:** Outputs `1` if `a` is high, else outputs `b`.
- The logic optimises to 
```verilog
y = a | b ;
```


**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.18.05_1e123b4c.jpg>)

***

#### Lab 3: Equivalent Logic Mux
```verilog
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```
 The logic optimises to 
```verilog
y = a & b & c ;
```

**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.29.09_18b4ea73.jpg>)

***

#### Lab 4: Nested Ternary and Logic Simplification
```verilog
module opt_check4 (input a , input b , input c , output y);
    assign y = a ? (b ? (a & c) : c) : (!c);
endmodule
```
- **Logic Reduced:** The nested ternary structure simplifies to `y = a ? c : !c` during synthesis.


**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.31.26_96529302.jpg>)

***

### Sequential Optimization Labs

#### Lab 5: D Flip-Flop with Constant Assignment
```verilog
module dff_const1(input clk, input reset, output reg q);
    always @(posedge clk or posedge reset)
        if (reset)
            q <= 1'b0;
        else
            q <= 1'b1;
endmodule
```
- **Analysis:** Synchronous constant drive post-reset; design can be optimized to a fixed `1` output.

**Simulation Waveform:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.44.17_04b1e0e2.jpg>)

**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.59.38_73eb5ac7.jpg>)

***

#### Lab 6: D Flip-Flop Always Set
```verilog
module dff_const2(input clk, input reset, output reg q);
    always @(posedge clk or posedge reset)
        q <= 1'b1;
endmodule
```
- **Analysis:** Flip-flop output is always `1` due to unconditional assignment; synthesis removes/optimizes implementation.

**Simulation Waveform:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.45.48_49b0fac5.jpg>)

**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 22.58.53_593f0fee.jpg>)

***

#### Lab 7: 2 D Flip-Flops with Set and Reset
```verilog
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
endmodule
```
- **Analysis:** Flip-flop output is always `1` but glitches for a clock cycle due to non zero clock to Q delay.

**Simulation Waveform:**  
![alt text](<WhatsApp Image 2025-09-26 at 23.08.58_fb6f45cf.jpg>)

**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-26 at 23.11.09_96322754.jpg>)
***

### Sequential Unused Output Optimization

#### Lab 8
```verilog
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
- **Analysis:** Unused bits of counter which dont play any role in logic of primary output is dissolved.

**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-27 at 07.12.21_0716fcfc.jpg>)

***

#### Lab 9
```verilog
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
- **Analysis:** all bits of intermediate output counter is used in logic of primary output. So, 3 flip flops are inferred.

**Generated Netlist:**  
![alt text](<WhatsApp Image 2025-09-27 at 07.12.21_0716fcfc.jpg>)

***



## 🔑 Key Learnings and Insights
1. **Combinational optimization** greatly reduces circuit complexity and improves speed
2. **Sequential optimization** in FSMs and flops leads to more efficient implementations
3. **Structural techniques** like cloning and retiming further enhance critical path timing
4. **Modern synthesis tools** automate many of these improvements during the mapping flow

***

## 🔗 Project Information
**Program:** VSD Tapeout Program  
**Duration:** 10 weeks  
**Focus:** RISC-V chip tapeout collaboration  
**Technology:** SkyWater 130nm PDK  
**Day 3 Objectives:** Circuit optimization, practical synthesis, and digital design efficiency

***

*This documentation will be updated daily with experimental results, waveform screenshots, and synthesis outputs for each lab covered in the tapeout program.*