# CSE306 Computer Architecture Sessional

This repository collects the four major off-line design exercises completed for the CSE306 Computer Architecture Sessional course. Each assignment focuses on a different hardware design theme—building from a combinational arithmetic logic unit to a fully pipelined MIPS-like processor—with supporting assemblers, reports, and simulation artefacts. The designs are implemented in Logisim 2.7.1 circuits and complemented by C++ utilities where necessary.【F:Offline 1 (ALU)/ALU.circ†L1-L24】【F:Offline 2 (FP Adder)/FP_Adder.circ†L1-L46】

## Repository layout

The repository is organised by assignment. Each assignment directory contains Logisim circuit files, documentation (PDF/DOCX), and any supplementary tooling or submissions used during the course.【8f9a98†L1-L64】

```
Assignment 1 test cases.xlsx   Shared spreadsheet of ALU validation vectors
Offline 1 (ALU)/               4-bit ALU Logisim design and reports
Offline 2 (FP Adder)/          IEEE-754-style floating point adder design variants
Offline 3 (MIPS)/              8-bit single-cycle MIPS-like processor
Offline 4 (Pipelining)/        Five-stage pipelined version of the processor
```

The `B1_Group3_Submission` sub-folders mirror the artefacts handed in for grading (assembler sources, ROM initialisation files, and simulation-ready circuits) while the `Resources` folders capture working notes such as opcode tables and control unit truth tables.【8f9a98†L21-L58】

## Toolchain prerequisites

* **Logisim Evolution 2.7.1** (or classic Logisim 2.7.1) to open and simulate the `.circ` files. Every circuit file records `source="2.7.1"`, ensuring compatibility with that release.【F:Offline 1 (ALU)/ALU.circ†L1-L24】【F:Offline 2 (FP Adder)/FP_Adder.circ†L1-L40】
* **A modern C++ compiler** (e.g., `g++`) to build the custom assemblers. Both assembler programs are single-source C++11 applications that depend only on the standard library headers included via `<bits/stdc++.h>`.【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L1-L37】【F:Offline 4 (Pipelining)/Assembler/Assembler.cpp†L1-L37】
* **Spreadsheet/PDF viewer** to inspect the provided reports, test cases, and documentation bundles.【8f9a98†L1-L64】

## Working with the Logisim designs

1. Launch Logisim 2.7.1 and open the relevant `.circ` file for the assignment (for example, `Offline 3 (MIPS)/MIPS.circ`). Each circuit makes extensive use of hierarchies and labelled tunnels—enable the "View ▸ Show Labels" option in Logisim to see the annotations cited below.【F:Offline 3 (MIPS)/MIPS.circ†L1-L56】【F:Offline 4 (Pipelining)/Pipelining.circ†L320-L520】
2. Load ROM images where required. The provided `control_unit_rom.txt` files are formatted as Logisim hexadecimal dumps that can be imported into ROM components via the device’s context menu.【F:Offline 3 (MIPS)/Control Unit/control_unit_rom.txt†L1-L17】【F:Offline 4 (Pipelining)/Control Unit/control_unit_rom.txt†L1-L17】
3. Initialise register files and memories as annotated in the schematics—many circuits expose `Reg INIT` tunnels and clock pins to prime the machine state before stepping the clock.【F:Offline 4 (Pipelining)/Pipelining.circ†L349-L415】【F:Offline 4 (Pipelining)/Pipelining.circ†L520-L620】

## Building the assemblers

Each processor assignment ships with a bespoke assembler that converts assembly programs into Logisim ROM images:

1. Change into the assembler directory (`Offline 3 (MIPS)/Assembler` or `Offline 4 (Pipelining)/Assembler`).【8f9a98†L17-L38】【8f9a98†L64-L84】
2. Compile the tool with `g++ Assembler.cpp -O2 -std=c++11 -o assembler`.
3. Run `./assembler` beside `B1_Group3_assembly.txt`. The assembler performs a two-pass translation, counting instruction slots (including macro-expansions for `push`/`pop`) and emitting hexadecimal opcodes into `machine_code.txt` in Logisim’s `v2.0 raw` format.【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L54-L226】【F:Offline 4 (Pipelining)/Assembler/Assembler.cpp†L53-L224】

The opcode mapping and register file conventions are shared across both assemblers, with the mapping declared in-source and echoed in `Resources/Opcodes.txt`. Labels, jump targets, and branch offsets are resolved automatically during the second pass.【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L10-L37】【F:Offline 3 (MIPS)/Resources/Opcodes.txt†L1-L18】【F:Offline 4 (Pipelining)/Assembler/Assembler.cpp†L11-L169】

## Assignment summaries

### Offline 1 – 4-bit ALU

* **Interface.** The circuit exposes operand pins `A1`–`A4` and `B1`–`B4`, along with three control signals `CS0`–`CS2` that select the active operation.【F:Offline 1 (ALU)/ALU.circ†L247-L337】
* **Outputs and flags.** Four result pins `F1`–`F4` deliver the combinational output, while labelled indicators provide carry (`C`), overflow (`V`), sign (`S`), and zero (`z`) status for downstream modules.【F:Offline 1 (ALU)/ALU.circ†L296-L383】【F:Offline 1 (ALU)/ALU.circ†L343-L463】
* **Implementation notes.** The design composes elementary logic gates (XNOR, AND, OR, NOR, XOR) and 1-bit adders into a 4-bit ALU sub-circuit, with structured wiring captured in the `4 bit ALU` hierarchy section of the file.【F:Offline 1 (ALU)/ALU.circ†L240-L520】
* **Testing resources.** The root of the repository includes `Assignment 1 test cases.xlsx`, containing the manually curated validation vectors used during development.【8f9a98†L1-L4】

### Offline 2 – Floating-Point Adder

* **Word format.** Inputs are 16-bit wide, reflecting a custom floating-point layout with separate sign, exponent, and fraction fields that are split via Logisim splitters.【F:Offline 2 (FP Adder)/FP_Adder.circ†L1-L78】【F:Offline 2 (FP Adder)/FP_Adder.circ†L600-L716】
* **Alignment and normalisation.** Annotated text labels document the alignment pipeline: the circuit computes the index of the highest-order 1 in the smaller exponent, decrements the exponent difference, and right-shifts the smaller mantissa before addition.【F:Offline 2 (FP Adder)/FP_Adder.circ†L618-L756】
* **Sign handling.** Dedicated logic covers sign extraction, two’s-complement negation for subtraction cases, and mux-based selection of the output sign when operands differ.【F:Offline 2 (FP Adder)/FP_Adder.circ†L683-L756】【F:Offline 2 (FP Adder)/FP_Adder.circ†L970-L1158】
* **Special conditions.** The circuit flags underflow, performs left-shift renormalisation when the carry-out is zero, and increments the exponent when carries occur after same-sign addition.【F:Offline 2 (FP Adder)/FP_Adder.circ†L655-L756】【F:Offline 2 (FP Adder)/FP_Adder.circ†L1094-L1344】
* **Variants.** Alternate circuit attempts, reports, and question/answer scans are preserved in sibling folders such as `B1_Group3` and `Senior's_Circuit` for reference or comparison.【8f9a98†L9-L28】

### Offline 3 – 8-bit MIPS-like Processor

* **Architecture overview.** The `MIPS_desc.txt` brief enumerates the major components (program counter, instruction ROM, control unit ROM, register file, ALU) and explains single-cycle execution with manual clocking.【F:Offline 3 (MIPS)/Resources/MIPS_desc.txt†L1-L18】
* **Instruction set.** The opcode table defines sixteen instructions spanning arithmetic, logical, memory, and control-flow operations, which align with the assembler’s opcode map.【F:Offline 3 (MIPS)/Resources/Opcodes.txt†L1-L18】【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L10-L169】
* **Control signalling.** A LaTeX-formatted control table specifies the control-word bits (Shift, RegDst, ALUSrc, RegWrite, MemRead, MemWrite, Branch, BeqOne, Jump, ALUOp[2:0]) for each opcode, guiding the contents of `control_unit_rom.txt`.【F:Offline 3 (MIPS)/Resources/CONTROLUNIT.txt†L1-L36】【F:Offline 3 (MIPS)/Control Unit/control_unit_rom.txt†L1-L17】
* **Assembler workflow.** The two-pass assembler resolves labels, expands `push`/`pop` macros into multiple machine words, and writes a ROM image prefixed with the `v2.0 raw` signature expected by Logisim.【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L54-L226】
* **Simulation artefacts.** The `Logisim Files` folder bundles reusable sub-circuits for the ALU and register file, while the `B1_Group3_Simulation` directory holds the integrated processor and ROM initialisation ready for playback in Logisim.【8f9a98†L21-L48】

### Offline 4 – Five-Stage Pipelined Processor

* **Pipeline registers.** The pipelined circuit adds explicit IF/ID, ID/EX, EX/MEM, and MEM/WB registers with labelled buses to track register operands, ALU results, and control signals across stages.【F:Offline 4 (Pipelining)/Pipelining.circ†L320-L520】【F:Offline 4 (Pipelining)/Pipelining.circ†L520-L620】
* **Forwarding and hazard mitigation.** A dedicated `Forwarding Unit` block, along with `ForwardA`/`ForwardB` selectors and register-ID comparisons, routes results from later stages back to the ALU inputs to resolve data hazards without stalling.【F:Offline 4 (Pipelining)/Pipelining.circ†L498-L605】【F:Offline 4 (Pipelining)/Pipelining.circ†L600-L640】
* **Control ROM updates.** The control ROM and accompanying `control_unit_code.txt` encode the expanded control word required by the pipeline, preserving the same hexadecimal image format used in Offline 3 for easy swapping between designs.【F:Offline 4 (Pipelining)/Control Unit/control_unit_code.txt†L1-L19】【F:Offline 4 (Pipelining)/Control Unit/control_unit_rom.txt†L1-L17】
* **Assembler parity.** The pipelined processor reuses the single-cycle assembler without modification, ensuring assembly sources remain compatible across assignments.【F:Offline 4 (Pipelining)/Assembler/Assembler.cpp†L11-L224】
* **Submission package.** The `B1_Group3_Submission` folder includes the final pipelined circuit (`Pipelining.circ`), the ROM image, and the exact assembler sources shipped for assessment.【8f9a98†L64-L104】

## Supporting documents

Every assignment folder stores the original PDF reports, presentation materials, and supplementary notes created during the course (for example, `B1_Group3_8bit_MIPS_Processor_Report.pdf`). These artefacts capture the design rationale, performance analysis, and screenshots that complement the circuit files.【8f9a98†L9-L58】

## Reusing the designs

* Start from the corresponding `.circ` file, import the provided ROM images, and exercise the datapaths using Logisim’s tick controls. Labels such as `Instruction`, `RegWrite`, and `Forwarding Unit` highlight the key buses to probe with the `Poke Tool`.【F:Offline 3 (MIPS)/MIPS.circ†L29-L477】【F:Offline 4 (Pipelining)/Pipelining.circ†L425-L640】
* Modify or extend the instruction set by editing the assembler opcode maps, regenerating the ROM images, and updating the control tables. Because both assemblers centralise the opcode/register definitions at the top of the source file, adding a new mnemonic requires a single change per map plus any necessary encoding logic.【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L10-L170】【F:Offline 4 (Pipelining)/Assembler/Assembler.cpp†L11-L170】
* When porting to a different Logisim version, preserve the `v2.0 raw` ROM format emitted by the assemblers to maintain compatibility with memory components.【F:Offline 3 (MIPS)/Assembler/Assembler.cpp†L87-L224】

With these resources and notes, the repository serves as a complete reference for reproducing, studying, or extending the course projects.
