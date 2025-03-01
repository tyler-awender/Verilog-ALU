# Behavioral Model of a CPU in Verilog

```
Tyler Awender  
San Jose State University  
tyler.awender@sjsu.edu  
```

## ABSTRACT
This project implements the design of a minimal computer system, DaVinci v1.0, supporting the CS147DV instruction set. This system includes a 32-bit processor and a 64MB memory. For implementation and testing, this project uses Verilog and ModelSim.

## 1. INTRODUCTION
The CPU mentioned above will consist of three parts: a register file (RF), arithmetic logic unit (ALU), and control unit (CU). Below is the list of outcomes for this project:

1. Functional CPU that supports CS147DV instruction set
2. Functional 64MB memory system with pre-loading capability
3. A system that integrates memory and CPU
4. Testing for system using CS147DV set

## 2. SETUP
1. Download the source code from Canvas, called `prj_02`, and unzip it.
2. To import the project, open ModelSim, select `File` => `New` => `Project`.
3. Enter any project name and file location.
4. Select `Add existing file` => `Browse` and select all starter files.
5. Select all starter files, right-click, and select `Compile All`.
6. The project is now set up.

## 3. REQUIREMENTS FOR SYSTEM

### A) Arithmetic Logic Unit
The ALU is one of three parts of this CPU. It provides a CPU the ability to process arithmetic and logic operations. This ALU supports the following operations:

- Addition
- Subtraction
- Multiplication
- Shift Right
- Shift Left
- AND
- OR
- Set Less Than

### B) Register File
A register file is an array of registers in a CPU that allows for immediate access to data during instruction execution. The DaVinci v1.0 register file contains 32 32-bit registers.

### C) Control Unit
The CU orchestrates the CPU, managing the flow of instructions and coordinating the ALU and RF. It ensures that each instruction is executed correctly through generating control signals and managing state transitions.

DaVinci v1.0’s CU operates using a finite state machine. The states are:

1. Fetch: Instruction fetched from memory
2. Decode: Decoded to determine type and registers
3. Execute: ALU performs operation
4. Memory Access: Data is loaded from memory or written to memory
5. Write Back: Result is written to RF

### D) CS147DV Instruction Set
The main task of the processor is to execute instructions as defined in a system’s instruction set. This processor specifically handles the CS147DV instruction set.

## 4. MEMORY DESIGN AND IMPLEMENTATION
The design of the memory is similar to the RF. The `MEMORY_64MB` module contains the following ports:

- **READ**: Enables read operations when set to 1
- **WRITE**: Enables write operations when set to 1
- **ADDR**: A 26-bit input for address of memory location
- **CLK**: Clock signal
- **RST**: Reset signal

### Data Bus
- **DATA**: 32-bit bidirectional bus for input and output

The memory module is designed as an array of registers. The 2D array `sram_32x64m` holds 64MB memory locations. On reset, all memory locations are changed to zero, and the initialization file, `mem_init_file`, is read for initialization.

For read operations, on the positive edge of `CLK`, when `READ = 1` and `WRITE = 0`, data at `ADDR` is placed into `DATA`. For write operations, on the positive edge of `CLK`, when `WRITE = 1` and `READ = 0`, the data on the bus is written to `ADDR`.

## 5. PROCESSOR DESIGN AND IMPLEMENTATION
This processor supports the CS147DV instruction set and consists of three components: the ALU, Register File, and Control Unit.

### A) Arithmetic Logic Unit Design
The ALU, originally implemented in Project 1, retains its core functionality but has additional features such as the ZERO output and ZERO flag. It integrates with the CU and RF.

Supported operations:

- **Arithmetic**: Addition, Subtraction, Multiplication
- **Logical**: AND, OR, NOR
- **Bitwise**: Shift Left, Shift Right, Set Less Than

The module takes two 32-bit operands (`OP & OP2`) and a 6-bit operation code (`OPRN`). Outputs include a 32-bit result (`OUT`) and a zero flag (`ZERO`), which is set when the result is zero.

### B) Register File Design
The register file provides 32 32-bit registers for immediate data access. Unlike main memory, the RF supports dual read and single write operations.

#### Inputs
- **ADDR_R1 & ADDR_R2**: 5-bit addresses for reading
- **ADDR_W**: 5-bit address for writing
- **DATA_W**: 32-bit value to be written
- **READ & WRITE**: Control signals
- **CLK**: Clock signal
- **RST**: Reset signal

#### Outputs
- **DATA_R1 & DATA_R2**: 32-bit outputs corresponding to stored data

The RF initializes all registers to zero at startup and resets on the negative edge of `RST`.

### C) Control Unit and State Machine Design
The CU coordinates the RF, ALU, and memory to ensure correct instruction execution.

#### State Machine
Inputs:
- **CLK**: Clock signal
- **RST**: Reset signal

Outputs:
- **STATE**: 3-bit signal representing the current state

The state machine cycles through:
1. Fetch: Fetch next instruction from memory
2. Decode: Decode fetched instruction
3. Execute: Perform operation
4. Memory: Handle load/store operations
5. Write Back: Store results in RF or update PC

#### Control Unit
The CU integrates with the state machine via the `proc_state` wire, which defines the current instruction cycle state.

At initialization and on the negative edge of `RST`, the CU sets the program counter and stack pointer. The instruction cycle starts with the fetch stage, followed by decode, execute, memory access, and write back stages.

## 6. TESTING
The processor is tested using three test bench files:
- `register_file_tb.v`: Tests register file functionality.
- `alu_tb.v`: Tests ALU functionality.
- `da_vinci_tb.v`: Runs six tests and outputs `.dat` files for comparison with expected outputs.

Comparison of output data dump files with golden files helps determine correctness.

## 7. CONCLUSION
This project successfully implemented a CPU that supports the CS147DV instruction set. While most components performed well, some unresolved issues remain, highlighting areas for future debugging. Overall, this project provided deeper insights into Verilog and processor design.

## REFERENCES
[1] D. A. Patterson and J. L. Hennessy, _Computer Organization And Design: The Hardware/Software Interface_. Cambridge, MA: Morgan Kaufmann Publishers, 2018.

[2] K. Patra, _Lecture Slides_, Department of Computer Science, San Jose State University, 2024.

