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
# RISC-V Instruction Formats
<details>

In the RISC-V architecture, instruction formats serve as a 'contract' between assembly language and hardware. When an assembly language command is executed, the hardware must understand precisely what to do. These instructions are encoded in a series of 0s and 1s, representing different operations, data locations, and more.

RISC-V defines six instruction formats, each with its own structure and purpose:

## 1. R-Type (Register)

**R** stands for **Register**. This format is used for arithmetic and logical operations involving three registers.

- **Fields:**
  - **funct7 (7 bits):** Function code for additional instruction differentiation.
  - **rs2 (5 bits):** Second source register.
  - **rs1 (5 bits):** First source register.
  - **funct3 (3 bits):** Function code for primary instruction differentiation.
  - **rd (5 bits):** Destination register.
  - **opcode (7 bits):** Basic operation code for R-type instructions (0110011 for integer operations).

- **Examples:** `ADD`, `SUB`, `OR`, `XOR`

## 2. I-Type (Immediate)

I-type instructions are used for operations that involve an immediate value along with one or two registers.

- **Fields:**
  - **immediate (12 bits):** Immediate value used for operations.
  - **rs1 (5 bits):** Source register.
  - **funct3 (3 bits):** Function code for instruction differentiation.
  - **rd (5 bits):** Destination register.
  - **opcode (7 bits):** Basic operation code for I-type instructions.

- **Examples:** `ADDI`, `LW`, `JALR`

## 3. S-Type (Store)

S-type instructions are used for store operations, where data is stored from a register into memory.

- **Fields:**
  - **imm[11:5] (7 bits):** Upper 7 bits of the immediate value.
  - **rs2 (5 bits):** Second source register (contains the data to be stored).
  - **rs1 (5 bits):** First source register (base address register).
  - **funct3 (3 bits):** Function code for instruction differentiation.
  - **imm[4:0] (5 bits):** Lower 5 bits of the immediate value.
  - **opcode (7 bits):** Basic operation code for S-type instructions.

- **Examples:** `SW`, `SB`, `SH`

## 4. B-Type (Branch)

B-type instructions are used for conditional branch operations, altering the flow of execution based on comparisons between two registers.

- **Fields:**
  - **imm[12] (1 bit):** The 12th bit of the immediate value.
  - **imm[10:5] (6 bits):** The 10th to 5th bits of the immediate value.
  - **rs2 (5 bits):** Second source register.
  - **rs1 (5 bits):** First source register.
  - **funct3 (3 bits):** Function code for instruction differentiation.
  - **imm[4:1] (4 bits):** The 4th to 1st bits of the immediate value.
  - **imm[11] (1 bit):** The 11th bit of the immediate value.
  - **opcode (7 bits):** Basic operation code for B-type instructions.

- **Examples:** `BEQ`, `BNE`, `BLT`, `BGE`

## 5. U-Type (Upper Immediate)

U-type instructions are used for operations involving large immediate values, typically for loading upper immediate values or computing addresses.

- **Fields:**
  - **immediate[31:12] (20 bits):** The upper 20 bits of the immediate value.
  - **rd (5 bits):** Destination register.
  - **opcode (7 bits):** Operation code for U-type instructions.

- **Examples:** `LUI`, `AUIPC`

## 6. J-Type (Jump)

J-type instructions are used for jump operations, altering the program control flow by jumping to a specified address.

- **Fields:**
  - **imm[20] (1 bit):** The 20th bit of the immediate value.
  - **imm[10:1] (10 bits):** The 10th to 1st bits of the immediate value.
  - **imm[11] (1 bit):** The 11th bit of the immediate value.
  - **imm[19:12] (8 bits):** The 19th to 12th bits of the immediate value.
  - **rd (5 bits):** Destination register where the return address is stored.
  - **opcode (7 bits):** Operation code for J-type instructions.

- **Examples:** `JAL`

---

This document provides an overview of the different instruction formats in the RISC-V architecture, explaining how each format is structured and used in various operations.
</details>
