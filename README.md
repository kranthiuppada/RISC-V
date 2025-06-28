# 🚀 Pipelined RISC-V CPU in TL-Verilog

This project implements a **5-stage pipelined RISC-V CPU** using **TL-Verilog** on the **Makerchip** platform. The design supports the **RV32I base instruction set** (excluding `FENCE`, `ECALL`, and `EBREAK`) and includes features such as register bypassing, memory access, and jump/branch instructions.

---
## Why TL-Verilog instead of regular Verilog?

✅ Easier to describe pipelined logic

✅ Cleaner syntax for deeply pipelined designs

✅ Reduces boilerplate code (e.g., forward declarations, staging)

✅ Better visualization tools on Makerchip

---

## 📌 What is Pipelining?

Pipelining is a technique that allows overlapping execution of multiple instructions to improve throughput. In our CPU, the instruction path is broken into **five sequential stages**, with intermediate registers to hold partial results. Each stage completes a part of the instruction, and multiple instructions are processed simultaneously.

---
## Block diagram:
  ![Image](https://github.com/user-attachments/assets/d98cca8d-ecff-4777-8946-2698d22d362c)
---

## 🧠 Pipeline Stages Explained

5-stage Pipeline Overview:

        
  PC -->  |  IF  | -> |  ID  | -> |  EX  | -> | MEM  | -> |  WB  |
          

    Each instruction spends one cycle in each stage, and new instructions are fetched every cycle. This allows multiple instructions to execute concurrently.


### `@0 - Instruction Fetch (IF)`
- The Program Counter (`pc`) selects the current instruction.
- Calculates the next `pc` based on jump/branch logic.
- Initiates instruction memory read using `$imem_rd_addr`.

### `@1 - Instruction Decode (ID)`
- Decodes the fetched instruction.
- Extracts:
  - Opcode
  - Register indices (`rs1`, `rs2`, `rd`)
  - Immediate values (`imm`)
  - Instruction type (`R`, `I`, `S`, `B`, `U`, `J`)
- Validates the instruction and determines the operation type.
- Begins register file read.

### `@2 - Register Fetch / Branch Target Calculation`
- Fetches register values (`src1_value`, `src2_value`) with **bypass logic** for data hazards.
- Computes the branch target address (`br_tgt_pc`).
- Passes values forward for execution.

### `@3 - Execute (EX)`
- Performs ALU operations:
  - Arithmetic and logic instructions (`add`, `sub`, `and`, `or`, etc.)
  - Shift operations (`sll`, `srl`, `sra`, etc.)
  - Set comparisons (`slt`, `sltu`, etc.)
- Evaluates conditional branches (`beq`, `bne`, etc.)
- Calculates jump targets (`jal`, `jalr`)
- Determines final result (`$result`) for writing back.

### `@4 - Memory Access (MEM)`
- For `load` and `store` instructions:
  - Writes to memory (`sw`, `sh`, `sb`)
  - Initiates memory read for `load`
- Calculates memory address using ALU result.

### `@5 - Write Back (WB)`
- Writes ALU or memory result to the register file.
- For `load` instructions, `$ld_data` is written to the destination register.

---

🧪 Features Implemented

✅ 5-stage pipelined RISC-V datapath

✅ RV32I instruction set (except FENCE, ECALL, EBREAK)

✅ Register file with bypassing

✅ Hazard detection for control and data hazards

✅ Immediate generation for I, S, B, U, J types

✅ Jump and branch support (jal, jalr, beq, etc.)

✅ Load and store with byte/halfword/word support

---
## 🔄 Data Hazards & Bypassing

A data hazard occurs when an instruction depends on the result of a previous instruction that hasn’t yet written its result. For example:

assembly:

add x5, x1, x2

sub x6, x5, x3    # x5 not yet written!

✅ Solution: Register Bypassing

The design includes bypassing logic to forward results from later stages (EX/MEM/WB) back to earlier ones (ID/EX) to resolve hazards without stalling the pipeline.



## 🚧 Control Hazards

Control hazards occur with branches (beq, bne) or jumps (jal, jalr) because the next instruction address depends on a comparison result that is not yet available in early stages.

✅ Solution:

Branch decisions are made in the EX stage

Next PC is chosen based on resolved condition

Stalls are introduced when necessary


## 🔢 Immediate Generation

TL-Verilog code extracts and sign-extends the immediate values based on instruction type (I, S, B, U, J).

This logic handles the correct shifting and concatenation of instruction bits to form the 32-bit immediate used in ALU or address calculation.



## 💾 Memory Access

Load Instructions (lb, lh, lw) read from memory

Store Instructions (sb, sh, sw) write to memory

Address is calculated using the base register + immediate offset.

Makerchip signals:

$dmem_rd_addr, $dmem_rd_data, $dmem_wr_data

are used for interaction with simulated memory in the TL-Verilog/Makerchip environment.

---
## 🌐 Run It on Makerchip

Steps:
Visit https://www.makerchip.com

Click on Launch Makerchip IDE

Paste the full TL-Verilog code into the editor (replace the //... placeholder)

Click Compile to run and visualize the pipeline

---
| Instruction Type | Examples                         | Supported |
| ---------------- | -------------------------------- | --------- |
| R-type           | `add`, `sub`, `and`, `or`, `slt` | ✅         |
| I-type           | `addi`, `andi`, `ori`, `slli`    | ✅         |
| S-type           | `sw`, `sh`, `sb`                 | ✅         |
| B-type           | `beq`, `bne`, `blt`, `bge`       | ✅         |
| U-type           | `lui`, `auipc`                   | ✅         |
| J-type           | `jal`, `jalr`                    | ✅         |
| System           | `fence`, `ecall`, `ebreak`       | ❌         |

---
## output :

![Image](https://github.com/user-attachments/assets/4099fd92-af77-4020-b147-12fc942ff32f)

---
## 📜 License

This project is licensed under the [MIT License](LICENSE).  
---
## 👩‍💻 Author

**Kranthi Uppada**  
B.Tech ECE | Digital Design Enthusiast  
[GitHub Profile](https://github.com/kranthiuppada)


---
