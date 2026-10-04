# PARS-16: 16-Bit Processor Design

A course project completed for **Computer Structure (EE 25-754)** at Sharif University of Technology.

## Overview

Designed and implemented **PARS-16**, a custom 16-bit single-cycle RISC-like processor based on the Harvard architecture. The processor features an 8-bit program counter, an 8-register register file, and ARM-style predicated execution via condition codes and status flags (Z, N, C, V). The complete datapath, control unit, and memory interfaces were modeled, simulated, and verified in the *Digital* logic design environment.

## Objectives

* Design a functional 16-bit single-cycle datapath and hardwired control unit supporting a complete 16-instruction custom ISA.
* Implement conditional (predicated) execution using a dedicated 4-bit status register (Z, N, C, V) and 2-bit condition codes (`AL`, `EQ`, `LT`, `VS`).
* Build a robust hardware debugging interface (register file and data memory probe ports) to validate program execution against automated test suites.

## Instruction Set Architecture (ISA)

PARS-16 instructions are 16 bits wide, where bits `[15:14]` define the condition code (`AL`, `EQ`, `LT`, `VS`) and bits `[13:10]` specify the opcode.

| Opcode | Mnemonic | Type | Format / Fields | Operation |
|:---:|:---:|:---:|:---|:---|
| `0000` | **ADD**  | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 + Rs2` |
| `0001` | **SUB**  | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 − Rs2` |
| `0010` | **AND**  | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 & Rs2` |
| `0011` | **OR**   | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 \| Rs2` |
| `0100` | **XOR**  | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 ⊕ Rs2` |
| `0101` | **SHL**  | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 << Rs2[3:0]` |
| `0110` | **SHR**  | R  | `CC, Opcode, Rd, Rs1, Rs2, S` | `Rd ← Rs1 >> Rs2[3:0]` |
| `0111` | **ADDI** | I  | `CC, Opcode, Rd, Rs1, Imm4`   | `Rd ← Rs1 + sext(Imm4)` |
| `1000` | **LW**   | I  | `CC, Opcode, Rd, Rs1, Imm4`   | `Rd ← MEM[Rs1 + sext(Imm4)]` |
| `1001` | **SW**   | I  | `CC, Opcode, Rd, Rs1, Imm4`   | `MEM[Rs1 + sext(Imm4)] ← Rs2` |
| `1010` | **MOVI** | M  | `CC, Opcode, Rd, Imm7`        | `Rd ← sext(Imm7)` |
| `1011` | **LUI**  | M  | `CC, Opcode, Rd, Imm7`        | `Rd ← Imm7 << 9` |
| `1100` | **B**    | B  | `CC, Opcode, Offset10`        | `PC ← (PC + 1 + sext(Offset10)) mod 256` |
| `1101` | **JAL**  | J  | `CC, Opcode, Offset10`        | `R7 ← PC + 1`; Branch to Target |
| `1110` | **JR**   | JR | `CC, Opcode, Rs1`             | `PC ← Rs1[7:0]` |
| `1111` | **NOP**  | I  | `CC, Opcode, ...`             | No operation |

> **Synthesized Pseudo-Instructions:** `CMP Rs1, Rs2` $\rightarrow$ `SUB R0, Rs1, Rs2, S`, `MOV Rd, Rs` $\rightarrow$ `ADD Rd, Rs, R0`, `RET` $\rightarrow$ `JR R7`.

## Tools and Technologies

* **Digital (by H. Neemann):** Digital logic schematic capture and cycle-accurate circuit simulation (`.dig`).
* **Assembly / Machine Code:** Hand-assembled machine code and test programs for custom 16-bit instruction formats (R, I, M, B, J, JR types).
* **AI-Assisted Engineering:** Leveraged LLMs for logic design brainstorming, edge-case test vector generation, and control signal verification.
* **Git:** Version control and project artifact tracking.

## Project Structure
```text
Computer-Structure-Course-Project/
├── README.md
├── src/                    # Schematic circuit files (.dig)
│   ├── CPU.dig             # Top-level processor schematic
│   ├── ALU.dig             # 16-bit ALU and flag generator
│   ├── RegisterFile.dig         # 8x16-bit Register File with debug ports
│   └── ControlUnit.dig    # Main decoder and condition evaluation logic
├── tests/             # Test vectors and testbench setups
│   ├── CPU__T1_protocol.dig
│   ├── CPU__T2_isa.dig
│   ├── CPU__T3_sum.dig
│   ├── CPU__T4_sort.dig
│   ├── CPU__T5_branch.dig
│   └── use_of_test_cases.txt   # Guide to using tests
├── docs/                   # Course assignment specification      
│   └── CS_Project_v3.pdf
```

## Testing

Each test fixture runs against the top-level `CPU.dig` using the Digital CLI:

```bash
java -cp "C:\path\to\Digital.jar" CLI test ^
  -circ C:/path/to/src/CPU.dig ^
  -tests C:/path/to/tests/CPU__T2_isa.dig ^
  -allowMissingInputs
```

See [tests/use_of_test_cases.txt](tests/use_of_test_cases.txt) for details. The five fixtures cover the debug-interface protocol, the full ISA, a summation program, a sorting program, and branch/jump behavior.

## Results

* Successfully verified all 16 instructions (arithmetic, logic, memory access LW/SW, immediate loading MOVI/LUI, and branching/jumps B/JAL/JR) with single-cycle execution.
* Passed automated verification tests via the integrated hardware debug interface (DBG_EN, DBG_RSEL, MADDR_DBG).
* Demonstrated correct program counter wrapping (mod 256), sign-extension operations, and predicated flag-update logic (S-bit gating).

## My Contributions

* Designed the complete 16-bit ALU supporting arithmetic, bitwise operations, and dynamic condition flag generation (Z, N, C, V).
* Implemented the dual-read, single-write Register File with dedicated multiplexing for hardware debugging.
* Developed the condition evaluation and hardwired control unit to gate write-enables and PC updates based on 2-bit condition codes.
* Leveraged AI tools to accelerate edge-case test plan formulation, assembly test vector creation, and control-hazard debugging.
* Validated end-to-end functionality across corner cases, subroutine linkages (JAL/JR), and memory operations.

## Notes
This project was completed as part of coursework at EE department, Sharif University of Technology.