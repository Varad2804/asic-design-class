# ASIC Design Class

This is Varad Bharadiya (IMT2021532). Course - Physical Design of ASICs (VLS508)

# Task 1: Compiling the C Code with GCC and RISCV
<details>


This part demonstrates how to compile and run a simple C program that calculates the sum of numbers from 1 to N using GCC
### Steps:

1. **Navigate to Home Directory**
   - Open the terminal and use this command to navigate to the home directory:
   ```bash
   cd
   ```
   - Then open the Leafpad editor for writing the C code.
   
   ![Alt text](images/image1.png)

2. **Create a .c File**
   - Create a C file that calculates the sum of numbers from 1 to N.

   ![Alt text](images/image2.png)

3. **Compile and Run the Program**
   - Compile the program using the GCC compiler:
   ```bash
   gcc sum1ton.c
   ./a.out
   ```

   ![Alt text](images/image3.png)

## Compile Using RISC-V Compiler
This part demonstrates how to compile and run a simple C program that calculates the sum of numbers from 1 to N using RISCV architecture.
### Steps:

1. **Check the Code**
   - Verify the code using the `cat` command:
   ```bash
   cat sum1ton.c
   ```

   ![Alt text](images/image4.png)

2. **Compile the Code with RISC-V GCC**
   - Compile the code using the RISC-V GCC compiler:
   ```bash
   riscv64-unknown-elf-gcc -O1 -o sum1ton.o sum1ton.c
   ```

3. **Generate Assembly Code**
   - Get the assembly code of the C program:
   ```bash
   riscv64-unknown-elf-objdump -d sum1ton.o
   ```
   - For easier viewing, you can add `| less` to the command:
   ```bash
   riscv64-unknown-elf-objdump -d sum1ton.o | less
   ```

   ![Alt text](images/image5.png)

4. **Calculate Number of Instructions**
   - We'll obtain the output once more, and this time we'll focus directly on the main section. To determine the number of instructions, we'll subtract the address of the first instruction of the current section from that of the next section, then divide the result by 4 (since the increment is 4 for each step). This method allows us to conclude that there are 15 sets of instructions when using O1.

   ![Alt text](images/image6.png)

   - To verify, we can do it using a calculator:
   
   ![Alt text](images/image13.png)

5. **Compile with Optimization Flag `Ofast`**
   - Recompile the code using the `Ofast` optimization flag:
   ```bash
   riscv64-unknown-elf-gcc -Ofast -o sum1ton_ofast.o sum1ton.c
   ```

   ![Alt text](images/image7.png)

6. **Compare Number of Instructions**
   - Re-run the command to get the number of instructions, and you'll see that fewer instructions are needed to execute the program with `Ofast` optimization.
   
   ![Alt text](images/image8.png)
</details>
---

# Task 2: Debug Mode in Spike on RISC-V
<details>
This part demonstrates how to use the debug mode in RISCV.(using SPIKE)


### Steps:

1. **Run the Program in Debug Mode**
   - After compiling the code with the RISC-V compiler, run the code using Spike in debug mode:
   ```bash
   spike -d pk sum1ton.o
   ```
   - Then it enters the debug mode. One can run the instructions till a specific address as shown in the example:
   ```bash
   until pc 0 100b0
   ```
   
   ![Alt text](images/image9.png)

2. **Set Breakpoints and Track Registers**
   - Track the values in the register (e.g., register `a2`):
   ```bash
   reg 0 a2
   ```
   - Now press Enter to run the next instruction (`lui a2, 0x1`). Check the content of the `a2` register again, and you'll see that it has been updated.
   
   ![Alt text](images/image10.png)

3. **Step Through Instructions**
   - Step through instructions and check the content of the `sp` (stack pointer) register:
   ```bash
   addi sp, sp, -16
   until pc 0 100b8
   reg 0 sp
   ```
   - After running the next instruction, you'll see that the `sp` value is reduced by 16 (10 in HEX).

   ![Alt text](images/image11.png)
   
   - To verify:

   ![Alt text](images/image12.png)

</details>

---
# Task-3:RISC-V Instruction Formats
<details>

In the RISC-V architecture, instruction formats serve as a 'contract' between assembly language and hardware. When an assembly language command is executed, the hardware must understand precisely what to do. These instructions are encoded in a series of 0s and 1s, representing different operations, data locations, and more.

RISC-V defines six instruction formats, each with its own structure and purpose:

### Core Instruction Formats

1. **R-Type (Register-Register Operations)**
    - **Purpose:** Used for arithmetic and logical operations that involve two source registers and a destination register.
    - **Format:**
    \`\`\`
    opcode[6:0] | rd[11:7] | funct3[14:12] | rs1[19:15] | rs2[24:20] | funct7[31:25]
    \`\`\`
    - **Fields:**
      - **opcode:** Operation code (7 bits)
      - **rd:** Destination register (5 bits)
      - **funct3:** Function code to differentiate operations (3 bits)
      - **rs1:** First source register (5 bits)
      - **rs2:** Second source register (5 bits)
      - **funct7:** Additional function code (7 bits)
    - **Example Instructions:** ADD, SUB, AND, OR, XOR, SLT

2. **I-Type (Immediate Operations)**
    - **Purpose:** Used for operations involving an immediate value and one or two registers, as well as load instructions.
    - **Format:**
    \`\`\`
    opcode[6:0] | rd[11:7] | funct3[14:12] | rs1[19:15] | imm[31:20]
    \`\`\`
    - **Fields:**
      - **opcode:** Operation code (7 bits)
      - **rd:** Destination register (5 bits)
      - **funct3:** Function code (3 bits)
      - **rs1:** Source register (5 bits)
      - **imm:** Immediate value (12 bits)
    - **Example Instructions:** ADDI, LW, JALR

3. **S-Type (Store Operations)**
    - **Purpose:** Used for store instructions where data from a register is stored into memory.
    - **Format:**
    \`\`\`
    opcode[6:0] | imm[11:7] | funct3[14:12] | rs1[19:15] | rs2[24:20] | imm[31:25]
    \`\`\`
    - **Fields:**
      - **opcode:** Operation code (7 bits)
      - **imm[11:7]:** Immediate value (lower 5 bits) (5 bits)
      - **funct3:** Function code (3 bits)
      - **rs1:** Base address register (5 bits)
      - **rs2:** Source register (5 bits)
      - **imm[31:25]:** Immediate value (upper 7 bits) (7 bits)
    - **Example Instructions:** SW, SB

4. **U-Type (Upper Immediate Operations)**
    - **Purpose:** Used for instructions that operate with a 20-bit upper immediate, typically for addressing purposes.
    - **Format:**
    \`\`\`
    opcode[6:0] | rd[11:7] | imm[31:12]
    \`\`\`
    - **Fields:**
      - **opcode:** Operation code (7 bits)
      - **rd:** Destination register (5 bits)
      - **imm:** Upper immediate value (20 bits)
    - **Example Instructions:** LUI, AUIPC

5. **B-Type (Branch Operations)**
    - **Purpose:** Used for conditional branch instructions, which alter the program flow based on comparisons between registers.
    - **Format:**
    \`\`\`
    opcode[6:0] | imm[11] | imm[4:1] | funct3[14:12] | rs1[19:15] | rs2[24:20] | imm[10:5] | imm[12]
    \`\`\`
    - **Fields:**
      - **opcode:** Operation code (7 bits)
      - **imm[11:7]:** Immediate value bits 4:1 (4 bits)
      - **funct3:** Function code (3 bits)
      - **rs1:** First source register (5 bits)
      - **rs2:** Second source register (5 bits)
      - **imm[12]:** Immediate value bit 11 (1 bit)
    - **Example Instructions:** BEQ, BNE, BLT, BGE

6. **J-Type (Jump Operations)**
    - **Purpose:** Used for jump instructions, which direct program control to a new address, optionally storing the return address in a register.
    - **Format:**
    \`\`\`
    opcode[6:0] | rd[11:7] | imm[19:12] | imm[11] | imm[10:1] | imm[20]
    \`\`\`
    - **Fields:**
      - **opcode:** Operation code (7 bits)
      - **rd:** Destination register (5 bits)
      - **imm:** Immediate value (20 bits)
    - **Example Instructions:** JAL

### 32-bit Instruction Encoding for Provided Instructions

Below are the specific 32-bit encodings for a set of given RISC-V instructions, broken down by their type and the corresponding hexadecimal code.

1. **ADD r5, r6, r7 (R-Type)**
    - **Format:** opcode | rd | funct3 | rs1 | rs2 | funct7
    - **Encoding:**
      - **opcode:** 0110011 (7 bits)
      - **rd (r5):** 00101 (5 bits)
      - **funct3:** 000 (3 bits)
      - **rs1 (r6):** 00110 (5 bits)
      - **rs2 (r7):** 00111 (5 bits)
      - **funct7:** 0000000 (7 bits)
    - **32-bit Binary:** 0000000 00111 00110 000 00101 0110011
    - **Hexadecimal:** 0x00c30533

2. **SUB r7, r5, r6 (R-Type)**
    - **Format:** opcode | rd | funct3 | rs1 | rs2 | funct7
    - **Encoding:**
      - **opcode:** 0110011 (7 bits)
      - **rd (r7):** 00111 (5 bits)
      - **funct3:** 000 (3 bits)
      - **rs1 (r5):** 00101 (5 bits)
      - **rs2 (r6):** 00110 (5 bits)
      - **funct7:** 0100000 (7 bits)
    - **32-bit Binary:** 0100000 00110 00101 000 00111 0110011
    - **Hexadecimal:** 0x40b302b3

3. **ADDI r12, r3, 5 (I-Type)**
    - **Format:** opcode | rd | funct3 | rs1 | imm
    - **Encoding:**
      - **opcode:** 0010011 (7 bits)
      - **rd (r12):** 01100 (5 bits)
      - **funct3:** 000 (3 bits)
      - **rs1 (r3):** 00011 (5 bits)
      - **imm:** 000000000101 (12 bits)
    - **32-bit Binary:** 000000000101 00011 000 01100 0010011
    - **Hexadecimal:** 0x00530693

4. **SW r3, r1, 4 (S-Type)**
    - **Format:** opcode | imm[11:7] | rs2 | rs1 | funct3 | imm[4:0]
    - **Encoding:**
      - **opcode:** 0100011 (7 bits)
      - **imm[11:7]:** 00000 (5 bits)
      - **rs2 (r3):** 00011 (5 bits)
      - **rs1 (r1):** 00001 (5 bits)
      - **funct3:** 010 (3 bits)
      - **imm[4:0]:** 00100 (5 bits)
    - **32-bit Binary:** 0000000 00011 00001 010 00100 0100011
    - **Hexadecimal:** 0x00312023

5. **BEQ r1, r0, 20 (B-Type)**
    - **Format:** opcode | imm[12] | imm[10:5] | rs2 | rs1 | funct3 | imm[4:1] | imm[11]
    - **Encoding:**
      - **opcode:** 1100011 (7 bits)
      - **imm[12]:** 0 (1 bit)
      - **imm[10:5]:** 000010 (6 bits)
      - **rs2 (r0):** 00000 (5 bits)
      - **rs1 (r1):** 00001 (5 bits)
      - **funct3:** 000 (3 bits)
      - **imm[4:1]:** 0100 (4 bits)
      - **imm[11]:** 0 (1 bit)
    - **32-bit Binary:** 0000000 00000 00001 000 0100 1100011
    - **Hexadecimal:** 0x00400063

6. **LUI r2, 0x12345 (U-Type)**
    - **Format:** opcode | rd | imm[31:12]
    - **Encoding:**
      - **opcode:** 0110111 (7 bits)
      - **rd (r2):** 00010 (5 bits)
      - **imm[31:12]:** 00010010001101000101 (20 bits)
    - **32-bit Binary:** 00010010001101000101 00010 0110111
    - **Hexadecimal:** 0x12345037

---

This document provides an overview of the different instruction formats in the RISC-V architecture, explaining how each format is structured and used in various operations.
</details>
