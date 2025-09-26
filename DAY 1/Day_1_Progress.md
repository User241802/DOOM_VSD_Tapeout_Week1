# VSD Tapeout Program - Day 1 Progress

Welcome to **Day 1** of the VSD Tapeout Program! This README documents my learning journey through digital design fundamentals, including Verilog RTL design, open-source simulation tools, and the basics of logic synthesis.

---

## 📚 Table of Contents

1. [Introduction to Digital Design Fundamentals](#1-introduction-to-digital-design-fundamentals)
2. [Open-Source Simulation with iverilog](#2-open-source-simulation-with-iverilog)
3. [Practical Lab: 2-to-1 Multiplexer Design](#3-practical-lab-2-to-1-multiplexer-design)
4. [Logic Synthesis with Yosys](#4-logic-synthesis-with-yosys)

---

## 1. Introduction to Digital Design Fundamentals

### Overview
Understanding the core components of digital design verification and implementation flow is essential for successful RTL design. This section covers the fundamental building blocks of the design process.

---

### What is a Simulator?

A **simulator** is a software tool that verifies digital circuit functionality by:
- **Input Application**: Applying test vectors to the design
- **Response Analysis**: Monitoring and analyzing output responses
- **Functional Verification**: Ensuring design meets specifications before hardware implementation
- **Debug Capability**: Identifying and isolating design issues early in the flow

#### Key Benefits
- **Early Bug Detection**: Catch issues before expensive hardware fabrication
- **Design Validation**: Verify functionality across different operating conditions
- **Performance Analysis**: Analyze timing and behavioral characteristics

---

### Understanding Design vs Testbench

#### Design
The **design** represents your actual Verilog RTL code that describes:
- **Logic Functionality**: The intended digital circuit behavior
- **Hardware Structure**: How logic elements are interconnected
- **Interface Specification**: Input/output port definitions
- **Implementation Details**: RTL coding style and methodology

#### Testbench
A **testbench** is a specialized simulation environment that:
- **Stimulus Generation**: Creates input test patterns
- **Response Monitoring**: Captures and analyzes design outputs
- **Self-Checking**: Automatically verifies correct behavior
- **Coverage Analysis**: Ensures comprehensive testing

#### Simulation Architecture
```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│  Testbench  │───▶│   Simulator  │───▶│   Results   │
│             │    │              │    │             │
│ ┌─────────┐ │    │ ┌──────────┐ │    │ ┌─────────┐ │
│ │ Design  │ │    │ │ iverilog │ │    │ │ VCD     │ │
│ │ (DUT)   │ │    │ │          │ │    │ │ Files   │ │
│ └─────────┘ │    │ └──────────┘ │    │ └─────────┘ │
└─────────────┘    └──────────────┘    └─────────────┘
```

---

## 2. Open-Source Simulation with iverilog

### Tool Overview
**Icarus Verilog (iverilog)** is a comprehensive open-source Verilog simulator that provides:
- **IEEE 1364 Compliance**: Full Verilog-2001/2005 standard support
- **Cross-Platform**: Works on Linux, Windows, and macOS
- **Integration Ready**: Compatible with various EDA toolchains
- **Active Development**: Continuously updated and maintained

### Simulation Flow Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Verilog RTL   │    │    iverilog     │    │   Executable    │
│   Design File   │───▶│   Compiler      │───▶│   Simulation    │
│   (.v)          │    │                 │    │   Binary        │
└─────────────────┘    └─────────────────┘    └─────────────────┘
                                                        │
┌─────────────────┐    ┌─────────────────┐              │
│   Testbench     │    │    iverilog     │              │
│   File (.v)     │───▶│   Compiler      │──────────────┘
└─────────────────┘    └─────────────────┘
                                │
                                ▼
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   GTKWave       │◀───│   VCD Output    │◀───│   ./a.out       │
│   Waveform      │    │   File          │    │   Execution     │
│   Viewer        │    │   (.vcd)        │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

#### Process Breakdown
1. **Compilation Phase**: iverilog compiles both design and testbench into executable
2. **Simulation Phase**: Executable runs simulation and generates VCD waveform data
3. **Analysis Phase**: GTKWave displays waveforms for analysis and debugging

---

## 3. Practical Lab: 2-to-1 Multiplexer Design

### Lab Setup and Environment

#### Step 1: Repository Setup
```bash
# Clone the workshop repository
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

#### Step 2: Tool Installation  
```bash
# Install simulation tools
sudo apt update
sudo apt install iverilog gtkwave

# Verify installation
iverilog -V
gtkwave --version
```

---

### Design Implementation

#### Multiplexer RTL Code Analysis
```verilog
module good_mux (input i0, input i1, input sel, output reg y);
    always @(*) begin
        if(sel)
            y <= i1;
        else 
            y <= i0;
    end
endmodule
```

#### Design Characteristics
- **Port Configuration**: Two data inputs (i0, i1), one select input (sel), one output (y)
- **Logic Operation**: `y = sel ? i1 : i0`
- **Coding Style**: Combinational always block with sensitivity list `@(*)`
- **Assignment Type**: Non-blocking assignment (`<=`) for proper simulation

#### Functional Truth Table
| sel | i0 | i1 | y | Operation |
|-----|----|----|---|-----------|
| 0   | 0  | X  | 0 | Select i0 |
| 0   | 1  | X  | 1 | Select i0 |
| 1   | X  | 0  | 0 | Select i1 |
| 1   | X  | 1  | 1 | Select i1 |

---

### Simulation Execution

#### Step 3: Compile and Simulate
```bash
# Compile design and testbench
iverilog good_mux.v tb_good_mux.v

# Execute simulation
./a.out

# View waveforms
gtkwave tb_good_mux.vcd
```

#### Simulation Results Analysis
The GTKWave waveform viewer displays:
![alt text](<mux waveform.jpg>)
- **Input Stimulus**: Test pattern variations for all input combinations
- **Output Response**: Multiplexer output following select line control
- **Timing Relationships**: Signal transitions and propagation behavior
- **Functional Verification**: Confirmation of correct logical operation

---

## 4. Logic Synthesis with Yosys

### Synthesis Tool Introduction

#### What is Yosys?
**Yosys** (Yosys Open SYnthesis Suite) is a comprehensive open-source RTL synthesis framework that provides:
- **RTL Processing**: Converts Verilog/VHDL to gate-level netlists
- **Technology Mapping**: Maps logic to target technology libraries
- **Optimization Engine**: Performs area, timing, and power optimizations
- **Formal Verification**: Includes equivalence checking capabilities
- **Extensible Architecture**: Supports custom synthesis flows and optimizations

#### Synthesis Process Overview
```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│   Verilog   │───▶│  Frontend   │───▶│ Synthesis   │───▶│ Technology  │
│   RTL       │    │ Parsing     │    │ Engine      │    │ Mapping     │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
                                               │
                                               ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ Gate-Level  │◀───│ Netlist     │◀───│ Library     │
│ Netlist     │    │ Generation  │    │ (.lib)      │
└─────────────┘    └─────────────┘    └─────────────┘
```

---

### Understanding Standard Cell Libraries

#### Why Multiple Versions of Same Gate Exist

Standard cell libraries contain multiple implementations of each logic function to address different design requirements:

#### Performance Optimization
- **High-Speed Gates**: Faster switching for critical timing paths
- **Low-Power Gates**: Reduced energy consumption for non-critical logic
- **Balanced Gates**: Compromise between speed and power consumption

#### Drive Strength Variations
- **Weak Drivers**: For light loads, smaller area, lower power
- **Strong Drivers**: For heavy loads, multiple fanouts, long interconnects
- **Buffer Chains**: For driving large capacitive loads efficiently

#### Design Trade-offs
| Gate Type | Speed | Power | Area | Use Case |
|-----------|-------|--------|------|----------|
| **High VT** | Slow | Low | Small | Non-critical paths |
| **Standard VT** | Medium | Medium | Medium | General logic |
| **Low VT** | Fast | High | Large | Critical timing paths |

#### Library Selection Process
```
Design Requirements
        │
        ▼
┌─────────────────┐
│ Synthesis Tool  │
│ Analysis        │
└─────────────────┘
        │
        ▼
┌─────────────────┐    ┌─────────────────┐
│ Timing Critical │    │ Area/Power      │
│ Path Selection  │    │ Optimization    │
└─────────────────┘    └─────────────────┘
        │                       │
        ▼                       ▼
┌─────────────────┐    ┌─────────────────┐
│ High Drive      │    │ Low Drive       │
│ Fast Gates      │    │ Small Gates     │
└─────────────────┘    └─────────────────┘
```

---

### Synthesis Laboratory Exercise

#### Yosys Synthesis Flow

##### Step 1: Initialize Yosys Environment
```bash
# Launch Yosys synthesis tool
yosys
```

##### Step 2: Load Standard Cell Library
```tcl
# Read technology library file
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

**Library Analysis**:
- **Technology**: SkyWater 130nm process
- **Process Corner**: TT (Typical-Typical)
- **Operating Conditions**: 25°C, 1.8V nominal
- **Cell Library**: High-density standard cells

##### Step 3: Read RTL Design
```tcl
# Load Verilog design file
read_verilog good_mux.v
```

##### Step 4: Perform RTL Synthesis
```tcl
# Synthesize design with specified top module
synth -top good_mux
```

**Synthesis Statistics Output**:
```
Number of cells:                  1
  sky130_fd_sc_hd__mux2_1         1

Chip area for module 'good_mux': 7.537600
```

##### Step 5: Technology Mapping
```tcl
# Map synthesized logic to library cells
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

##### Step 6: Visualize Results
```tcl
# Generate schematic view
show
```

#### Synthesis Results Analysis

The synthesized netlist demonstrates:

![alt text](<mux netlist.jpg>)
- **Logic Optimization**: RTL multiplexer mapped to single MUX2 standard cell
- **Area Efficiency**: Minimal gate count for required functionality
- **Technology Mapping**: Proper library cell selection for target process
- **Functional Preservation**: Equivalent logic behavior maintained

#### Generated Netlist Structure
```verilog
module good_mux(i0, i1, sel, y);
  input i0, i1, sel;
  output y;
  
  sky130_fd_sc_hd__mux2_1 _0_ (
    .A0(i0),
    .A1(i1),
    .S(sel),
    .X(y)
  );
endmodule
```

## 🔑 Key Learnings Summary

### Technical Concepts Mastered
1. **Simulation Methodology**: Understanding of testbench-driven verification flow
2. **RTL Design Principles**: Proper Verilog coding for combinational logic
3. **Open-Source Tools**: Practical experience with iverilog and GTKWave
4. **Synthesis Fundamentals**: Introduction to RTL-to-gate transformation process
5. **Library Concepts**: Understanding of standard cell varieties and selection criteria

### Practical Skills Developed
- **Tool Setup**: Installing and configuring open-source EDA tools
- **Simulation Flow**: Complete design verification methodology
- **Waveform Analysis**: Debugging using GTKWave viewer
- **Synthesis Execution**: Basic logic synthesis using Yosys
- **Netlist Analysis**: Understanding gate-level implementation results

### Design Methodology Insights
- **Verification-First Approach**: Always simulate before synthesis
- **Tool Chain Integration**: Seamless flow between simulation and synthesis
- **Library Awareness**: Impact of cell selection on design metrics
- **Optimization Understanding**: Trade-offs between area, timing, and power

---

## 🔗 Project Information

**Program**: VSD Tapeout Program  
**Duration**: 10 weeks  
**Focus**: RISC-V chip tapeout collaboration  
**Technology**: SkyWater 130nm PDK  
**Day 1 Objectives**: RTL design fundamentals and open-source tool introduction

---

*This documentation will be updated daily as I progress through the tapeout program. Each section will be expanded with detailed explanations, examples, and key learnings from hands-on laboratory exercises.*