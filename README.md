# CSE306 Computer Architecture Sessional

This repository collects the four major off-line design exercises completed for the CSE306 Computer Architecture Sessional course. Each assignment focuses on a different hardware design theme—building from a combinational arithmetic logic unit to a fully pipelined MIPS-like processor—with supporting assemblers, reports, and simulation artefacts. The designs are implemented in Logisim 2.7.1 circuits and complemented by C++ utilities where necessary.

## Repository layout

The repository is organised by assignment. Each assignment directory contains Logisim circuit files, documentation (PDF/DOCX), and any supplementary tooling or submissions used during the course.

```
Assignment 1 test cases.xlsx   Shared spreadsheet of ALU validation vectors
Offline 1 (ALU)/               4-bit ALU Logisim design and reports
Offline 2 (FP Adder)/          IEEE-754-style floating point adder design variants
Offline 3 (MIPS)/              8-bit single-cycle MIPS-like processor
Offline 4 (Pipelining)/        Five-stage pipelined version of the processor
```

The `B1_Group3_Submission` sub-folders mirror the artefacts handed in for grading (assembler sources, ROM initialisation files, and simulation-ready circuits) while the `Resources` folders capture working notes such as opcode tables and control unit truth tables.

## Toolchain prerequisites

* **Logisim Evolution 2.7.1** (or classic Logisim 2.7.1) to open and simulate the `.circ` files. Every circuit file records `source="2.7.1"`, ensuring compatibility with that release.
* **A modern C++ compiler** (e.g., `g++`) to build the custom assemblers. Both assembler programs are single-source C++11 applications that depend only on the standard library headers included via `<bits/stdc++.h>`.
* **Spreadsheet/PDF viewer** to inspect the provided reports, test cases, and documentation bundles.

## Working with the Logisim designs

1. Launch Logisim 2.7.1 and open the relevant `.circ` file for the assignment (for example, `Offline 3 (MIPS)/MIPS.circ`). Each circuit makes extensive use of hierarchies and labelled tunnels—enable the "View ▸ Show Labels" option in Logisim to see the annotations cited below.
2. Load ROM images where required. The provided `control_unit_rom.txt` files are formatted as Logisim hexadecimal dumps that can be imported into ROM components via the device’s context menu.
3. Initialise register files and memories as annotated in the schematics—many circuits expose `Reg INIT` tunnels and clock pins to prime the machine state before stepping the clock.

## Building the assemblers

Each processor assignment ships with a bespoke assembler that converts assembly programs into Logisim ROM images:

1. Change into the assembler directory (`Offline 3 (MIPS)/Assembler` or `Offline 4 (Pipelining)/Assembler`).
2. Compile the tool with `g++ Assembler.cpp -O2 -std=c++11 -o assembler`.
3. Run `./assembler` beside `B1_Group3_assembly.txt`. The assembler performs a two-pass translation, counting instruction slots (including macro-expansions for `push`/`pop`) and emitting hexadecimal opcodes into `machine_code.txt` in Logisim’s `v2.0 raw` format.

The opcode mapping and register file conventions are shared across both assemblers, with the mapping declared in-source and echoed in `Resources/Opcodes.txt`. Labels, jump targets, and branch offsets are resolved automatically during the second pass.

## Assignment summaries

### Offline 1 – 4-bit ALU

* **Interface.** The circuit exposes operand pins `A1`–`A4` and `B1`–`B4`, along with three control signals `CS0`–`CS2` that select the active operation.
* **Outputs and flags.** Four result pins `F1`–`F4` deliver the combinational output, while labelled indicators provide carry (`C`), overflow (`V`), sign (`S`), and zero (`z`) status for downstream modules.
* **Implementation notes.** The design composes elementary logic gates (XNOR, AND, OR, NOR, XOR) and 1-bit adders into a 4-bit ALU sub-circuit, with structured wiring captured in the `4 bit ALU` hierarchy section of the file.
* **Testing resources.** The root of the repository includes `Assignment 1 test cases.xlsx`, containing the manually curated validation vectors used during development.

### Offline 2 – Floating-Point Adder

* **Word format.** Inputs are 16-bit wide, reflecting a custom floating-point layout with separate sign, exponent, and fraction fields that are split via Logisim splitters.
* **Alignment and normalisation.** Annotated text labels document the alignment pipeline: the circuit computes the index of the highest-order 1 in the smaller exponent, decrements the exponent difference, and right-shifts the smaller mantissa before addition.
* **Sign handling.** Dedicated logic covers sign extraction, two’s-complement negation for subtraction cases, and mux-based selection of the output sign when operands differ.
* **Special conditions.** The circuit flags underflow, performs left-shift renormalisation when the carry-out is zero, and increments the exponent when carries occur after same-sign addition.
* **Variants.** Alternate circuit attempts, reports, and question/answer scans are preserved in sibling folders such as `B1_Group3` and `Senior's_Circuit` for reference or comparison.

### Offline 3 – 8-bit MIPS-like Processor

* **Architecture overview.** The `MIPS_desc.txt` brief enumerates the major components (program counter, instruction ROM, control unit ROM, register file, ALU) and explains single-cycle execution with manual clocking.
* **Instruction set.** The opcode table defines sixteen instructions spanning arithmetic, logical, memory, and control-flow operations, which align with the assembler’s opcode map.
* **Control signalling.** A LaTeX-formatted control table specifies the control-word bits (Shift, RegDst, ALUSrc, RegWrite, MemRead, MemWrite, Branch, BeqOne, Jump, ALUOp[2:0]) for each opcode, guiding the contents of `control_unit_rom.txt`.
* **Assembler workflow.** The two-pass assembler resolves labels, expands `push`/`pop` macros into multiple machine words, and writes a ROM image prefixed with the `v2.0 raw` signature expected by Logisim.
* **Simulation artefacts.** The `Logisim Files` folder bundles reusable sub-circuits for the ALU and register file, while the `B1_Group3_Simulation` directory holds the integrated processor and ROM initialisation ready for playback in Logisim.

### Offline 4 – Five-Stage Pipelined Processor

* **Pipeline registers.** The pipelined circuit adds explicit IF/ID, ID/EX, EX/MEM, and MEM/WB registers with labelled buses to track register operands, ALU results, and control signals across stages.
* **Forwarding and hazard mitigation.** A dedicated `Forwarding Unit` block, along with `ForwardA`/`ForwardB` selectors and register-ID comparisons, routes results from later stages back to the ALU inputs to resolve data hazards without stalling.
* **Control ROM updates.** The control ROM and accompanying `control_unit_code.txt` encode the expanded control word required by the pipeline, preserving the same hexadecimal image format used in Offline 3 for easy swapping between designs.
* **Assembler parity.** The pipelined processor reuses the single-cycle assembler without modification, ensuring assembly sources remain compatible across assignments.
* **Submission package.** The `B1_Group3_Submission` folder includes the final pipelined circuit (`Pipelining.circ`), the ROM image, and the exact assembler sources shipped for assessment.

## Supporting documents

Every assignment folder stores the original PDF reports, presentation materials, and supplementary notes created during the course (for example, `B1_Group3_8bit_MIPS_Processor_Report.pdf`). These artefacts capture the design rationale, performance analysis, and screenshots that complement the circuit files.

## Reusing the designs

* Start from the corresponding `.circ` file, import the provided ROM images, and exercise the datapaths using Logisim’s tick controls. Labels such as `Instruction`, `RegWrite`, and `Forwarding Unit` highlight the key buses to probe with the `Poke Tool`.
* Modify or extend the instruction set by editing the assembler opcode maps, regenerating the ROM images, and updating the control tables. Because both assemblers centralise the opcode/register definitions at the top of the source file, adding a new mnemonic requires a single change per map plus any necessary encoding logic.
* When porting to a different Logisim version, preserve the `v2.0 raw` ROM format emitted by the assemblers to maintain compatibility with memory components.

With these resources and notes, the repository serves as a complete reference for reproducing, studying, or extending the course projects.
