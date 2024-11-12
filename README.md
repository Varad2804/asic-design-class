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
---
</details>

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
---
</details>


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



## 32-bit Instruction Encodings

Below are the 32-bit instruction encodings for the given assembly instructions:

1. **ADD r5, r6, r7** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `000`
   - **funct7**: `0000000`
   - **rs1**: `00110` (r6)
   - **rs2**: `00111` (r7)
   - **rd**: `00101` (r5)
   - **Instruction**: `0000000 00111 00110 000 00101 0110011`
   - **Hex**: `0x007302B3`

2. **SUB r7, r5, r6** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `000`
   - **funct7**: `0100000`
   - **rs1**: `00101` (r5)
   - **rs2**: `00110` (r6)
   - **rd**: `00111` (r7)
   - **Instruction**: `0100000 00110 00101 000 00111 0110011`
   - **Hex**: `0x406283B3`

3. **AND r6, r5, r7** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `111`
   - **funct7**: `0000000`
   - **rs1**: `00101` (r5)
   - **rs2**: `00111` (r7)
   - **rd**: `00110` (r6)
   - **Instruction**: `0000000 00111 00101 111 00110 0110011`
   - **Hex**: `0x0072F333`

4. **OR r8, r6, r5** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `110`
   - **funct7**: `0000000`
   - **rs1**: `00110` (r6)
   - **rs2**: `00101` (r5)
   - **rd**: `01000` (r8)
   - **Instruction**: `0000000 00101 00110 110 01000 0110011`
   - **Hex**: `0x00536433`

5. **XOR r8, r5, r4** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `100`
   - **funct7**: `0000000`
   - **rs1**: `00101` (r5)
   - **rs2**: `00100` (r4)
   - **rd**: `01000` (r8)
   - **Instruction**: `0000000 00100 00101 100 01000 0110011`
   - **Hex**: `0x0042C433`

6. **SLT r10, r2, r4** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `010`
   - **funct7**: `0000000`
   - **rs1**: `00010` (r2)
   - **rs2**: `00100` (r4)
   - **rd**: `01010` (r10)
   - **Instruction**: `0000000 00100 00010 010 01010 0110011`
   - **Hex**: `0x00412533`

7. **ADDI r12, r3, 5** (I-Type)
   - **Opcode**: `0010011`
   - **funct3**: `000`
   - **imm**: `0000000000000101` (5)
   - **rs1**: `00011` (r3)
   - **rd**: `01100` (r12)
   - **Instruction**: `000000000101 00011 000 01100 0010011`
   - **Hex**: `0x00518613`

8. **SW r3, r1, 4** (S-Type)
   - **Opcode**: `0100011`
   - **funct3**: `010`
   - **imm[4:0]**: `00100` (lower 5 bits of 4)
   - **imm[11:5]**: `0000000` (upper 7 bits of 4)
   - **rs1**: `00001` (r1)
   - **rs2**: `00011` (r3)
   - **Instruction**: `0000000 00011 00001 010 00100 0100011`
   - **Hex**: `0x0030A223`

9. **SRL r16, r11, r2** (R-Type)
   - **Opcode**: `0110011`
   - **funct3**: `101`
   - **funct7**: `0000000`
   - **rs1**: `01011` (r11)
   - **rs2**: `00010` (r2)
   - **rd**: `10000` (r16)
   - **Instruction**: `0000000 00010 01011 101 10000 0110011`
   - **Hex**: `0x0025D833`

10. **BNE r0, r1, 20** (B-Type)
    - **Opcode**: `1100011`
    - **funct3**: `001`
    - **imm[4:0]**: `10100` (lower 5 bits of 20)
    - **imm[11:5]**: `000000` (upper 7 bits of 20)
    - **rs1**: `00001` (r1)
    - **rs2**: `00000` (r0)
    - **Instruction**: `0000000 00000 00001 001 10100 1100011`
    - **Hex**: `0x00009A63`

11. **BEQ r0, r0, 15** (B-Type)
    - **Opcode**: `1100011`
    - **funct3**: `000`
    - **imm[4:0]**: `01111` (lower 5 bits of 15)
    - **imm[11:5]**: `000000` (upper 7 bits of 15)
    - **rs1**: `00000` (r0)
    - **rs2**: `00000` (r0)
    - **Instruction**: `0000000 00000 00000 000 01111 1100011`
    - **Hex** : `0x000007E3`
   
12. **LW r13, r11, 2** (I-Type)
    - **opcode**: `0000011` (7 bits)
    - **rd** (r13): `01101` (5 bits)
    - **funct3**: `010` (3 bits)
    - **rs1** (r11): `01011` (5 bits)
    - **imm**: `000000000010` (12 bits)
    - **Instruction**: `000000000010 01011 010 01101 0000011`
    - **Hex**: `0x0025A683`

 13. **SLL r15, r11, r2** (R-Type)
 - **opcode**: `0110011` (7 bits)
 - **rd** (r15): `01111` (5 bits)
 - **funct3**: `001` (3 bits)
 - **rs1** (r11): `01011` (5 bits)
 - **rs2** (r2): `00010` (5 bits)
 - **funct7**: `0000000` (7 bits)
 - **Instruction**: `0000000 00010 01011 001 01111 0110011`
 - **Hex**: `0x002597B3`


Below are the 32-bit instruction encodings for the given assembly instructions:

| Instruction       | Type | 32-bit Instruction                            | Hexadecimal  |
|-------------------|------|-----------------------------------------------|--------------|
| ADD r5, r6, r7    | R    | 0000000 00111 00110 000 00101 0110011         | 0x007302B3   |
| SUB r7, r5, r6    | R    | 0100000 00110 00101 000 00111 0110011         | 0x406283B3   |
| AND r6, r5, r7    | R    | 0000000 00111 00101 111 00110 0110011         | 0x0072F333   |
| OR r8, r6, r5     | R    | 0000000 00101 00110 110 01000 0110011         | 0x00536433   |
| XOR r8, r5, r4    | R    | 0000000 00100 00101 100 01000 0110011         | 0x0042C433   |
| SLT r10, r2, r4   | R    | 0000000 00100 00010 010 01010 0110011         | 0x00412533   |
| ADDI r12, r3, 5   | I    | 000000000101 00011 000 01100 0010011          | 0x00518613   |
| SW r3, r1, 4      | S    | 0000000 00011 00001 010 00100 0100011         | 0x0030A223   |
| SRL r16, r11, r2  | R    | 0000000 00010 01011 101 10000 0110011         | 0x0025D833   |
| BNE r0, r1, 20    | B    | 0000000 00000 00001 001 10100 1100011         | 0x00009A63   |
| BEQ r0, r0, 15    | B    | 0000000 00000 00000 000 01111 1100011         | 0x000007E3   |
| LW r13, r11, 2    | I    | 000000000010 01011 010 01101 0000011          | 0x0025A683   |
| SLL r15, r11, r2  | R    | 0000000 00010 01011 001 01111 0110011         | 0x002597B3   |


---

This document provides an overview of the different instruction formats in the RISC-V architecture, explaining how each format is structured and used in various operations.
</details>

# Task-4: AIM : To execute assembly instructions using a given verilog code for a riscV processor and compare the waveform with Hardcoded instructions.
<details>
   There is some variations in the ISA followed by RISCV and the hardcoded ISA for the below given instrucions. The differences are shown in the table below :
      # RISC-V ISA vs. Hardcoded ISA

| Operation         | Standard RISC-V ISA (Hexadecimal) | Hardcoded ISA (Hexadecimal) |
|-------------------|-----------------------------------|-----------------------------|
| ADD r5, r6, r7    | 0x007302B3                        | 0x02208300                  |
| SUB r7, r5, r6    | 0x406283B3                        | 0x02209380                  |
| AND r6, r5, r7    | 0x0072F333                        | 0x0230A400                  |
| OR r8, r6, r5     | 0x00536433                        | 0x02513480                  |
| XOR r8, r5, r4    | 0x0042C433                        | 0x0240C500                  |
| SLT r10, r2, r4   | 0x00412533                        | 0x02415580                  |
| ADDI r12, r3, 5   | 0x00518613                        | 0x00520600                  |
| SW r3, r1, 4      | 0x0030A223                        | 0x00209181                  |
| SRL r16, r11, r2  | 0x0025D833                        | 0x00271803                  |
| BNE r0, r1, 20    | 0x00009A63                        | 0x00f00002                  |
| BEQ r0, r0, 15    | 0x000007E3                        | 0x00210700                  |
| LW r13, r11, 2    | 0x0025A683                        | 0x00208681                  |
| SLL r15, r11, r2  | 0x002597B3                        | 0x00208783                  |

We will be using gtkwave to analyse the waveforms
   ![Alt text](image2/image1.png)

First we start with analysis of our instructions provided for the activity in RISC-V
1.| ADD r5, r6, r7    | 0x007302B3                     

   ![Alt text](image2/image2.png)
   
2.| SUB r7, r5, r6    | 0x406283B3                      

   ![Alt text](image2/image3.png)
   
3. | AND r6, r5, r7    | 0x0072F333                     
   ![Alt text](image2/image4.png)

4. | OR r8, r6, r5     | 0x00536433                      
   
   ![Alt text](image2/image5.png)
   
5. | XOR r8, r5, r4    | 0x0042C433                      

   ![Alt text](image2/image6.png)

6. | SLT r10, r2, r4   | 0x00412533          

   ![Alt text](image2/image7.png)

7. | ADDI r12, r3, 5   | 0x00518613                       

   ![Alt text](image2/image8.png)

8. | SW r3, r1, 4      | 0x0030A223               

   ![Alt text](image2/image9.png)

9. | SRL r16, r11, r2  | 0x0025D833                     

   ![Alt text](image2/image11.png)

10. | BNE r0, r1, 20    | 0x00009A63                       

   ![Alt text](image2/image10.png)

We now analyse the gtkwaves for hardcoded ISA

   ![Alt text](image2/image12.png)

   ![Alt text](image2/image13.png)

   ![Alt text](image2/image14.png)

   ![Alt text](image2/image15.png)

   ![Alt text](image2/image16.png)

   ![Alt text](image2/image17.png)

   ![Alt text](image2/image18.png)

   ![Alt text](image2/image19.png)

   ![Alt text](image2/image20.png)


A difference in waveforms can be observed for both set of instructions
</details>

# Task-5: Compiling C application using GCC and RISC-V
<details>

## Application Name- Fibonacci Sequence Generator.

``` bash
#include <stdio.h>

// Function to print Fibonacci sequence
int main() {
    int terms;

    // Ask the user for the number of terms
    printf("Enter the number of terms in the Fibonacci sequence: ");
    scanf("%d", &terms);
	
    long long int first = 0, second = 1, next;

    // Check if the number of terms is valid
    if (terms <= 0) {
        printf("Number of terms must be a positive integer.\n");
        return 0;
    }

    // Print the Fibonacci sequence
    printf("Fibonacci Sequence:\n");
    for (int i = 0; i < terms; i++) {
        if (i == 0) {
            printf("%lld ", first);
            continue;
        }
        if (i == 1) {
            printf("%lld ", second);
            continue;
        }
        next = first + second;
        first = second;
        second = next;
        printf("%lld ", next);
    }
    printf("\n");

    return 0;
}

```
Compiling the Application using GCC compiler:

   ![Alt text](images3/image1.png)

Compiling using RISC-V Compiler:

   ![Alt text](images3/image2.png)

**Conclusion**:We have infered that the output is same in both the cases.

Also adding the screenshots of creating dumpfile

   ![Alt text](images3/image3.png)

   ![Alt text](images3/image4.png)


</details>

# Task-6: To design a Baisc Risc-V processor core using TL-Verilog on Makerchip, MYTH Day 3_5 activities
<details>
The variour parts of the code are shown below :

   ![Alt text](images4/1.png)

The generated visual is as shown below:

   ![Alt text](images4/24.png)

Here is the log image:

   ![Alt text](images4/2.png)

The signals include the named clock clock: "clk_var":

   ![Alt text](images4/3.png)

The code segments are shown below:

   ![Alt text](images4/4.png)

   ![Alt text](images4/5.png)

   ![Alt text](images4/6.png)

   ![Alt text](images4/7.png)

   ![Alt text](images4/8.png)

   ![Alt text](images4/9.png)

   ![Alt text](images4/10.png)

   ![Alt text](images4/11.png)

   ![Alt text](images4/12.png)

   ![Alt text](images4/13.png)

   ![Alt text](images4/14.png)

   ![Alt text](images4/15.png)

   ![Alt text](images4/16.png)

   ![Alt text](images4/17.png)

   ![Alt text](images4/18.png)

We now have screen shots for xreg14 where we store the value at each steps.

   ![Alt text](images4/19.png)

   ![Alt text](images4/20.png)

   ![Alt text](images4/21.png)

   ![Alt text](images4/22.png)

   ![Alt text](images4/23.png)



</details>


# Task-7: RISC-V Pre-Synthesis Simulation: A Comparative Study using Icarus Verilog (Iverilog), GTKWave, and Makerchip
<details>
	
## Overview

This project involves designing a RISC-V processor using TL-Verilog in the Makerchip IDE and converting it to Verilog using the Sandpiper-SaaS compiler for implementation on an FPGA. Pre-synthesis simulations are then performed using the GTKWave simulator. This README outlines the step-by-step process for running these simulations and compares the output waveforms.



## Conversion and Simulation Steps

1. **Translate TL-Verilog to Verilog:**

   Use the Sandpiper-SaaS compiler to convert the `.tlv` definition of RISC-V into a `.v` Verilog file:

   ```bash
   sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
   ```

2. **Create the `pre_synth_sim.vcd` File:**

   Generate the pre-synthesis simulation file:

   ```bash
   make pre_synth_sim
   ```

3. **Compile and Simulate the RISC-V Design:**

   Compile and simulate the RISC-V design using Iverilog:

   ```bash
   iverilog -o output/pre_synth_sim.out -DPRE_SYNTH_SIM src/module/testbench.v -I src/include -I src/module -
   ```

4. **Run the Simulation:**

   Change the working directory to `output` and execute the simulation:

   ```bash
   cd output
   ./pre_synth_sim.out
   ```

5. **View the Simulation Output in GTKWave:**

   Open the `.vcd` simulation file through the GTKWave simulation tool:

   ```bash
   gtkwave pre_synth_sim.vcd
   ```
Here is the gtkwave:

   ![Alt text](image5/img1.png)


## Comparison of Output Waveforms

After running the above steps, you can compare the output waveforms obtained from GTKWave with those from the Makerchip IDE. The comparison will help validate the accuracy and performance of the RISC-V design.

From the MakerChip:

   ![Alt text](images4/19.png)
   
   ![Alt text](images4/20.png)
   
   ![Alt text](images4/21.png)
   
   ![Alt text](images4/22.png)
   
   ![Alt text](images4/23.png)

From the GtkWave:

   ![Alt text](image5/img1.png)


## Conclusion

This project demonstrates the process of designing, converting, and simulating a RISC-V processor using TL-Verilog, Verilog, Iverilog, and GTKWave. The comparison of output waveforms across different tools ensures the correctness of the design before FPGA implementation.
</details>

# Task-8 Few Installations and BabySoc Simulation
<details>
	In this task we had to install verilog, gtkwave, yosys and opensta and BabySoc Simulation. OpenSTA was installed using the GitHub link given.
	
The screenshots for the others are given below:
	
   ![Alt text](image6/1.png)

   ![Alt text](image6/2.png)

Running the BabySoc on the RiscV_CPU.v file (sum of numbers):
   
   ![Alt text](image6/3.png)

Waveforms:
   
   ![Alt text](image6/4.png)


</details>

# Task -9 RTL design using Verilog with SKY130 Technology
<details>

 ## Initial Set-up

<details>

### The following Commands were to be executed for initial setup:

```bash
sudo -i
sudo apt-get install git
ls
cd /home/Varad
mkdir VLSI
cd VLSI
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
ls
```
We have the image for the same:
	![Alt text](image21/Part2_1.png)
 We also can see the files in verilog_files directory:
 	![Alt text](image21/Part2_2.png)
</details>

## Day-1

<details>
Introduction to iverilog and GTKWave: 
This tutorial involved learning about how to simulate the design and testbench for a 2x1 multiplexer, using iverilog, and displaying the waveform on GTKWave

   ![Alt text](image21/Part2_2.png)
   

   ![Alt text](image21/Part2_3.png)
   

Introduction to Yosys: 
This tutorial involved the use of Yosys for synthesising the design we created in Verilog, viewing its netlists and the cells that are generated for the purpose of creating the circuit. The following commands are used:

Open Yosys and read the standard cell library file and then run the `read_verilog` command to run the good_mux.v file:

   ![Alt text](image21/Part2_4.png)

Next we run the `synth -top` command to synthesise the design to yosys based standard cells:

   ![Alt text](image21/Part2_5.png)

   We can see some info at the end after synthesis.

   ![Alt text](image21/Part2_6.png)

   Now, we run `abc` command to use the sky130 standard cells with optimised design.

   ![Alt text](image21/Part2_7.png)

   run `show` command to see the schematic and how the design maps it to the standard cells of sky130 tech.

   ![Alt text](image21/Part2_8.png)

   Now, we get the netlist using the `write_verilog` command

   ![Alt text](image21/Part2_9.png)

   ![Alt text](image21/Part2_10.png)

   With -noattr flag. To make netlist more readable:
   
   ![Alt text](image21/Part2_12.png)

   



	
</details>

  ## Day-2
  
<details>
	
Yosys Synthesis for Multiple Modules: This tutorial involved the synthesis of a design file that has more than one module.

We follow similar steps again for the multiple_modules.v file

We get the following design for the schematic:

   ![Alt text](image21/Part2_13.png)

   ![Alt text](image21/Part2_14.png)

   ![Alt text](image21/Part2_15.png)

   ![Alt text](image21/Part2_16.png)

   ![Alt text](image21/Part2_17.png)

   With flatten netlist:

   ![Alt text](image21/Part2_18.png)

   schematic for the same when flatten

   ![Alt text](image21/Part2_19.png)


Use of Module Level Synthesis: This method is preferred when multiple instances of same module are used. The synthesis is carried out once and is replicate multiple times, and the multiple instances of the same module are stitched together in the top module. This method is helpful when making use of divide and conquer algorithm 

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog multiple_modules.v
4. synth -top sub_module1
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. show
```

Simulation of D-Flipflop using Iverilog and GTKWave: Performed simulations for 3 types of D-Flipflops - Asynchronous Reset, Asynchronous Set and Synchronous Reset. 

1. Asynchronous Reset

This figure shows asynchronous reset.
   ![Alt text](image21/Part2_20.png)

This figure shows that the dff is posedge.

   ![Alt text](image21/Part2_21.png)

2. Asynchronous Set

This figure shows asynchronus set

   ![Alt text](image21/Part2_22.png)

This figure shows that dff is posedge.

   ![Alt text](image21/Part2_23.png)

3. Synchronous Reset.

The figure below shows the part when synchronous reset is into action.

   ![Alt text](image21/Part2_24.png)

This figure shows that dff is posedge.

   ![Alt text](image21/Part2_25.png)

A screenshot demonstrating that all the above 3 files were run on my system.

   ![Alt text](image21/Part2_26.png)

Now we shall perform synthesis.

Asynchronous Reset:

   ![Alt text](image21/Part2_27.png)

   ![Alt text](image21/Part2_28.png)

   ![Alt text](image21/Part2_29.png)

   ![Alt text](image21/Part2_30.png)

   ![Alt text](image21/Part2_31.png)

   ![Alt text](image21/Part2_32.png)

Asynchronus Set:

   ![Alt text](image21/Part2_33.png)

   ![Alt text](image21/Part2_34.png)

   ![Alt text](image21/Part2_35.png)

Synchronus Set:
   ![Alt text](image21/Part2_36.png)

   ![Alt text](image21/Part2_37.png)

   ![Alt text](image21/Part2_38.png)

   ![Alt text](image21/Part2_39.png)

Multiplication by 2: This tutorial, we get to know that specific multiplier hardware is not required for multiplication of a number by 2. It can simply be achieved by concatenating the number itself with a zero in the LSB.

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog mult_2.v
4. synth -top mul2
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. show
7. write_verilog -noattr mul2_net.v
8. gvim mul2_net.v
```

   ![Alt text](image21/Part2_40.png)

   ![Alt text](image21/Part2_41.png)


Multiplication by 9: This tutorial, we get to know that specific multiplier hardware is not required for multiplication of a number by 9. It can simply be achieved by concatenating the number with itself

```bash
1. yosys
2. read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog mult_9.v
4. synth -top mult9
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. show
7. write_verilog -noattr mul9_net.v
8. gvim mul9_net.v
```
   ![Alt text](image21/Part2_42.png)

   ![Alt text](image21/Part2_43.png)

   ![Alt text](image21/Part2_44.png)

   ![Alt text](image21/Part2_45.png)

</details>

## Day-3
<details>
	
Optimization of Various Designs
Design infers 2 input AND Gate:

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check.v
4. synth -top opt_check
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```
6.Removes unused or redundant logic in the design and purges any dangling wires or gates.

   ![Alt text](image21/Part2_48.png)

   ![Alt text](image21/Part2_49.png)

   ![Alt text](image21/Part2_50.png)

Design infers 2 input OR Gate: 

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check2.v
4. synth -top opt_check2
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```
   ![Alt text](image21/Part2_51.png)

   ![Alt text](image21/Part2_52.png)

   ![Alt text](image21/Part2_53.png)

Design infers 3 input AND Gate: 

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check3.v
4. synth -top opt_check3
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```
   ![Alt text](image21/Part2_54.png)

   ![Alt text](image21/Part2_55.png)

   ![Alt text](image21/Part2_56.png)

Design infers 2 input XNOR Gate (3 input Boolean Logic) :
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog opt_check4.v
4. synth -top opt_check4
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```

   ![Alt text](image21/Part2_57.png)

   ![Alt text](image21/Part2_58.png)

   ![Alt text](image21/Part2_59.png)

Multiple Module Optimization-1 :
```bash

1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog multiple_module_opt.v
4. synth -top multiple_module_opt
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```

   ![Alt text](image21/Part2_60.png)

   ![Alt text](image21/Part2_61.png)

   ![Alt text](image21/Part2_62.png)

Multiple Module Optimization-2:
```bash

1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog multiple_module_opt2.v
4. synth -top multiple_module_opt2
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. show
```

D-Flipflop Constant 1 with Asynchronous Reset (active low) :
```bash
iverilog dff_const1.v tb_dff_const1.v
./a.out
gtkwave tb_dff_const1.vcd
```

   ![Alt text](image21/Part2_63.png)


From the waveform, it can be observed that the Q output is always high when reset is zero, and reset doesn't depend on clock edge.
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog dff_const1.v
4. synth -top dff_const1
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```
   ![Alt text](image21/Part2_64.png)

   ![Alt text](image21/Part2_65.png)

D-Flipflop Constant 2 with Asynchronous Reset (active high) :

```bash
iverilog dff_const2.v tb_dff_const2.v
./a.out
gtkwave tb_dff_const2.vcd
```
   ![Alt text](image21/Part2_66.png)

From the waveform, it can be observed that the Q output is always high irrespective of reset.
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog dff_const2.v
4. synth -top dff_const2
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```
   ![Alt text](image21/Part2_67.png)

   ![Alt text](image21/Part2_68.png)


D-Flipflop Constant 3 with Asynchronous Reset (active low)

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog dff_const3.v
4. synth -top dff_const3
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```
   ![Alt text](image21/Part2_69.png)

   ![Alt text](image21/Part2_70.png)
   
This module defines a D flip-flop, for a positive edge of reset, q is set to 1 and q1 is set to 0. On each clock cycle, q1 is set to 1, and q is updated with the value of q1.

When synthesized, the design will result in a flip-flop where q becomes 1 after the first clock cycle post-reset and stays 1 afterward.

D-Flipflop Constant 4 with Asynchronous Reset (active high) :
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog dff_const4.v
4. synth -top dff_const4
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```

   ![Alt text](image21/Part2_71.png)

   ![Alt text](image21/Part2_72.png)

This module defines a D flip-flop that sets both q and q1 to 1 on a positive edge of reset. On each clock cycle, q1 remains 1, and q is updated with the value of q1 (which is always 1).

When synthesized, the design will result in a flip-flop where q is always 1, regardless of the reset or clock state.


D-Flipflop Constant 5 with Asynchronous Reset :
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog dff_const5.v
4. synth -top dff_const5
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```
   ![Alt text](image21/Part2_73.png)

   ![Alt text](image21/Part2_74.png)

This module defines a D flip-flop that resets both q and q1 to 0 on a positive edge of reset. On each clock cycle, it sets q1 to 1 and then updates q with the value of q1 (which will always be 1 after the first cycle).

When synthesized, the design will result in a flip-flop where q is always 1 after the first clock cycle post-reset.

Counter Optimization 1: 
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog counter_opt.v
4. synth -top counter_opt
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```
   ![Alt text](image21/Part2_75.png)

   ![Alt text](image21/Part2_76.png)
   
Counter Optimization 2:
```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog counter_opt2.v
4. synth -top counter_opt2
5. dfflibmap -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. show
```

   ![Alt text](image21/Part2_77.png)

   ![Alt text](image21/Part2_78.png)

</details>


## Day-4
<details>

```bash
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```


   ![Alt text](image21/Part2_79.png)

   ![Alt text](image21/Part2_80.png)

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog ternary_operator_mux.v
4. synth -top ternary_operator_mux
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. write_verilog -noattr ternary_operator_mux_net.v
8. show
```
   ![Alt text](image21/Part2_81.png)


Design of a Bad 2x1 MUX:

```bash
iverilog bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```
   ![Alt text](image21/Part2_83.png)

   ![Alt text](image21/Part2_84.png)
   
The waveform shows that the output y changes only when the select line changes, ignoring changes in i0 and i1, indicating a faulty MUX design. Proper MUX behavior should reflect changes in i0 and i1 in the output y.


```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog bad_mux.v
4. synth -top bad_mux
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. write_verilog -noattr bad_mux_net.v
8. show
```

   ![Alt text](image21/Part2_85.png)

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```

   ![Alt text](image21/Part2_86.png)


GATE LEVEL SYNTHESIS for the Bad MUX.



Blocking Caveat:
```bash
iverilog blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
   ![Alt text](image21/Part2_87.png)
   ![Alt text](image21/Part2_88.png)


The waveform shows that when A and B are zero, the OR gate output (X) and the AND gate output (D) should both be zero. However, due to the blocking statement, the AND gate input of X retains the previous value of A|B (one), causing a discrepancy in the output.

```bash
1. yosys
2. read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog blocking_caveat.v
4. synth -top blocking_caveat
5. abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
6. opt_clean -purge
7. write_verilog -noattr blocking_caveat_net.v
8. show
```
   ![Alt text](image21/Part2_89.png)
   
```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat.v tb_blocking_caveat.v
./a.out
gtkwave tb_blocking_caveat.vcd
```
   ![Alt text](image21/Part2_90.png)

   ![Alt text](image21/Part2_91.png)

GATE LEVEL SYNTHESIS for the Blocking Caveat.
</details>
</details>

# Task-10 RISCV- Synthesis
<details>

Copy the `src` folder from your `BabySoC` folder to your `sky130RTLDesignAndSynthesisWorkshop` folder in your `VLSI` folder from previous lab.

Now go the following Directory:

```bash
cd /home/Varad/VLSI/sky130RTLDesignAndSynthesisWorkshop/src/module
```
Synthesis:

```bash
1. yosys       
2. read_liberty -lib /home/Varad/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
3. read_verilog clk_gate.v
4. read_verilog rvmyth.v
5. synth -top rvmyth
6. abc -liberty /home/Varad/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
7. write_verilog -noattr rvmyth_net.v
```
   ![Alt text](image22/1.png)

   ![Alt text](image22/2.png)

   ![Alt text](image22/3.png)

   ![Alt text](image22/4.png)

   ![Alt text](image22/7.png)

   ![image](https://github.com/user-attachments/assets/dedbcd6a-5745-459c-9bcc-2328cecc7919)



Now to observe the output waveform of synthesised RISC-V

```bash
iverilog ../../my_lib/verilog_model/primitives.v ../../my_lib/verilog_model/sky130_fd_sc_hd.v rvmyth.v testbench.v vsdbabysoc.v avsddac.v avsdpll.v clk_gate.v
./a.out
gtkwave dump.vcd
```
   ![Alt text](image22/13.png)

   ![Alt text](image22/11.png)

   ![Alt text](image22/10.png)

Observations from Babysoc RTL simulation:

   ![Alt text](image22/8.png)
   
   ![Alt text](image22/9.png)
   


</details>


 # Task-11 Timing Analysis of VSDBabySOC using OpenSTA
<details>

The following commands were run to get the max-min report of the VSDbabysoc design:
``` bash
read_liberty -min ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -min ./lib/avsdpll.lib
read_liberty -min ./lib/avsddac.lib
read_liberty -max ./lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty -max ./lib/avsdpll.lib
read_liberty -max ./lib/avsddac.lib
read_verilog ../output/synth/vsdbabysoc.synth.v
link_design vsdbabysoc
read_sdc ./sdc/vsdbabysoc_synthesis.sdc
report_checks -path_delay min_max -format full_clock_expanded -digits 4
```

   ![Alt text](OpenSTA/sta1.png)

   ![Alt text](OpenSTA/sta2.png)

   ![Alt text](OpenSTA/sta3.png)

Here is the hold report:

   ![Alt text](OpenSTA/sta4.png)
   
Here is the setup report:

   ![Alt text](OpenSTA/sta5.png)

The below is the sdc file (to set the constraints on the design):

```bash
set PERIOD 10.85

set_units -time ns
create_clock [get_pins {pll/CLK}] -name clk -period $PERIOD
set_clock_uncertainty -setup  [expr $PERIOD * 0.05] [get_clocks clk]
set_input_delay -min 0 [get_ports ENb_CP] -clock [get_clocks "clk"]
set_input_delay -min 0 [get_ports ENb_VCO] -clock [get_clocks "clk"]
set_input_delay -min 0 [get_ports REF] -clock [get_clocks "clk"]
set_input_delay -min 0 [get_ports VCO_IN] -clock [get_clocks "clk"]
set_input_delay -min 0 [get_ports VREFH] -clock [get_clocks "clk"]
set_clock_transition [expr $PERIOD * 0.05] [get_clocks clk]
set_clock_uncertainty -hold [expr $PERIOD * 0.08] [get_clocks clk]
set_input_transition [expr $PERIOD * 0.08] [get_ports ENb_CP]
set_input_transition [expr $PERIOD * 0.08] [get_ports ENb_VCO]
set_input_transition [expr $PERIOD * 0.08] [get_ports REF]
set_input_transition [expr $PERIOD * 0.08] [get_ports VCO_IN]
set_input_transition [expr $PERIOD * 0.08] [get_ports VREFH]
```

</details>

# Task-12 Post Synthesis Static Timing Analysis using OpenSTA for all the sky130 lib files
<details>
Snapshot of constraints file:
	
![image](https://github.com/user-attachments/assets/e610f497-4b48-4bf7-a145-8c5277fa3302)

Store all the `lib` files in a folder named `timing_libs`. Go to `VSDBabySoC/src` and create a file `sta_across_pvt.tcl`. The below consists of the contents of the tickle file:

![image](https://github.com/user-attachments/assets/861b6f0c-c9ce-4208-87bb-b0f0c97e7ea8)

Screenshot of the command to be executed in terminal after opening sta:

![image](https://github.com/user-attachments/assets/58fe3332-071e-43a2-87bb-f895b1684c51)


Min values (Worst hold Slack):

![image](https://github.com/user-attachments/assets/f3038b25-f567-4a09-9710-b0da6ea2cde9)

Man values (Worst Negative Slack):
![image](https://github.com/user-attachments/assets/e0ae187e-e5fa-408b-a361-5895c085ddad)

Table summarising all values for different lib files:

![image](https://github.com/user-attachments/assets/64497b4b-09e9-4aa5-ba3a-b612ff408bde)

 
</details>

# Task-13 Advanced Physical Design using OpenLane using Sky130
<details>

## Day-1

 <details>
	
# **Understanding ASIC Design and Flow**

This document provides an overview of various components involved in the design and implementation of an ASIC (Application-Specific Integrated Circuit), from the chip design process to hardware execution. 

---

## **Key Components of ASIC Design**

### 1. **QFN-48 Package**
   A **Quad Flat No-leads (QFN) 48** package is a leadless IC package with 48 connection pads around the perimeter. It offers excellent thermal and electrical performance in a compact form, making it ideal for high-density applications.

   ![QFN-48 Package](https://github.com/user-attachments/assets/8fb258e0-28f5-4f4c-95ab-fb1321aa430e)

### 2. **Chip**
   An **Integrated Circuit (IC)** is a silicon-based device that contains various functional blocks, such as memory, processing units, and I/O, designed for specific applications in electronics.

   ![Chip](https://github.com/user-attachments/assets/bd59a58f-9857-432f-902b-63ed2b6431d6)

### 3. **Pads**
   **Pads** are small metallic areas on a chip or package used to connect the internal circuitry to external connections, enabling signal transfer to and from the IC.

### 4. **Core**
   The **core** is the central part of a chip containing the main processing unit and functional logic, optimized for power and performance.

### 5. **Die**
   The **die** is the section of a silicon wafer that contains an individual IC before it is packaged, housing all active circuits and elements necessary for the chip’s functions.

   ![Die](https://github.com/user-attachments/assets/545bb775-27b3-4c97-879c-702ed430220b)

### 6. **IPs (Intellectual Properties)**
   **IPs** are pre-designed functional blocks or modules within a chip, such as USB controllers or memory interfaces. These IPs are licensed for reuse across various designs, saving time and cost in development.

---

## **From Software Applications to Hardware Flow**

To run an application on hardware, several steps take place, starting from high-level programming to the final execution on hardware.

### **Software Preparation for Hardware Execution:**

1. **System Software:**
   - The **Operating System (OS)** breaks down application functions written in high-level languages (e.g., C, C++, Java).
   
2. **Compiler:**
   - The **compiler** converts these high-level functions into architecture-specific low-level instructions.

3. **Assembler:**
   - The **assembler** translates the low-level instructions into binary machine code, understandable by the hardware.

### **Example: Stopwatch Application**
For a **stopwatch app** running on a RISC-V core:
- The OS generates a small function in C.
- The compiler outputs RISC-V-specific instructions.
- The assembler converts these into binary code for execution on the chip.

This process involves converting the high-level application into machine-readable binary code, allowing hardware (e.g., a chip) to execute the required functionality.

---

## **Components of ASIC Design**

### 1. **RTL Design**
   - **RTL (Register Transfer Level)** design describes the functional behavior of the circuit using HDLs (e.g., Verilog, VHDL), defining its logic and data paths.

### 2. **RTL Synthesis**
   - Converts RTL code into a gate-level netlist, which is a collection of standard cells like AND gates, flip-flops, and multiplexers.
   - Optimized for area, power, and timing.

### 3. **Floor and Power Planning**
   - Partitions the chip area, places major components, and defines the power grid and I/O placement to optimize area, power distribution, and signal flow.

### 4. **Placement**
   - Assigns physical locations to cells in a way that minimizes wire length, reduces signal delay, and meets design constraints.

### 5. **Clock Tree Synthesis (CTS)**
   - Optimizes the clock distribution network, ensuring even distribution to all flip-flops and registers to reduce clock skew.

### 6. **Routing**
   - Connects components based on placement, optimizing wire paths to maintain signal integrity, minimize congestion, and meet design rules.

### 7. **Sign-off**
   - The final verification stage, ensuring the design meets functionality, performance, power, and reliability targets.
   - This includes timing analysis, power analysis, and physical verification.

### 8. **GDSII File Generation**
   - Generates the **GDSII** file containing the complete layout needed for chip fabrication. This file is used by manufacturers to create the photomasks required for chip production.

---

## **Simplified RTL to GDS Flow**

1. **RTL Design:**
   - Describes the circuit’s functionality using HDLs like Verilog or VHDL.

2. **RTL Synthesis:**
   - Converts RTL into a gate-level netlist by mapping it to standard cells and optimizing for area, power, and timing.

3. **Floor and Power Planning:**
   - Plans the chip area, optimizes power grid and I/O placements.

4. **Placement:**
   - Assigns physical locations to the cells to minimize wire length and reduce signal delays.

5. **Clock Tree Synthesis (CTS):**
   - Optimizes the clock network to balance signal distribution and reduce skew.

6. **Routing:**
   - Ensures optimal wire paths between cells, meeting design rules.

7. **Sign-off:**
   - Verifies that all design constraints, including power, performance, and area, are met.

8. **GDSII File Generation:**
   - Final layout files are generated for fabrication.

---

## **OpenLane ASIC Flow**

![image](https://github.com/user-attachments/assets/7df7b8b6-2575-4836-81b7-1bbcc04f49d0)

The **OpenLane ASIC Flow** consists of a set of tools and steps to carry out the design and verification of an ASIC.

### **Key Tools and Steps:**

- **RTL Synthesis, Technology Mapping, and Formal Verification:**
  - Tools: Yosys (for RTL synthesis), ABC (for technology mapping and formal verification).

- **Static Timing Analysis:**
  - Tools: OpenSTA (for static timing analysis).

- **Floor Planning:**
  - Tools: init_fp (initial floorplanning), ioPlacer (I/O placement), pdn (power distribution planning), tapcell (tap cell insertion).

- **Placement:**
  - Tools: RePLace (global placement), Resizer (optional resizing), OpenPhySyn (placement), OpenDP (detailed placement).

- **Clock Tree Synthesis:**
  - Tools: TritonCTS (for clock tree synthesis).

- **Fill Insertion:**
  - Tools: OpenDP (for filler placement).

- **Routing:**
  - Tools: FastRoute (for global routing), TritonRoute (for detailed routing).

- **SPEF Extraction:**
  - Tools: OpenRCX (or SPEF-Extractor for parasitic extraction).

- **GDSII Streaming Out:**
  - Tools: Magic and KLayout (for viewing and editing GDSII files).

- **Design Rule Checking (DRC):**
  - Tools: Magic and KLayout (for DRC checks).

- **Layout vs. Schematic (LVS) Check:**
  - Tools: Netgen (for LVS checks).

- **Antenna Checks:**
  - Tools: Magic (for antenna checks).

---

### Synthesis in Openlane:

Now, follow the commnads below to run synthesis using OpenLane.

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
```


To view the netlist:

```bash
cd designs/picorv32a/runs/09-11_06-33/results/synthesis/
gedit picorv32a.synthesis.v
```

To view the yosys report:
```bash
cd ../..
cd reports/synthesis
gedit 1-yosys_4.stat.rpt
```

Report:

```bash

28. Printing statistics.

=== picorv32a ===

   Number of wires:              14596
   Number of wire bits:          14978
   Number of public wires:        1565
   Number of public wire bits:    1947
   Number of memories:               0
   Number of memory bits:            0
   Number of processes:              0
   Number of cells:              14876
     sky130_fd_sc_hd__a2111o_2       1
     sky130_fd_sc_hd__a211o_2       35
     sky130_fd_sc_hd__a211oi_2      60
     sky130_fd_sc_hd__a21bo_2      149
     sky130_fd_sc_hd__a21boi_2       8
     sky130_fd_sc_hd__a21o_2        57
     sky130_fd_sc_hd__a21oi_2      244
     sky130_fd_sc_hd__a221o_2       86
     sky130_fd_sc_hd__a22o_2      1013
     sky130_fd_sc_hd__a2bb2o_2    1748
     sky130_fd_sc_hd__a2bb2oi_2     81
     sky130_fd_sc_hd__a311o_2        2
     sky130_fd_sc_hd__a31o_2        49
     sky130_fd_sc_hd__a31oi_2        7
     sky130_fd_sc_hd__a32o_2        46
     sky130_fd_sc_hd__a41o_2         1
     sky130_fd_sc_hd__and2_2       157
     sky130_fd_sc_hd__and3_2        58
     sky130_fd_sc_hd__and4_2       345
     sky130_fd_sc_hd__and4b_2        1
     sky130_fd_sc_hd__buf_1       1656
     sky130_fd_sc_hd__buf_2          8
     sky130_fd_sc_hd__conb_1        42
     sky130_fd_sc_hd__dfxtp_2     1613
     sky130_fd_sc_hd__inv_2       1615
     sky130_fd_sc_hd__mux2_1      1224
     sky130_fd_sc_hd__mux2_2         2
     sky130_fd_sc_hd__mux4_1       221
     sky130_fd_sc_hd__nand2_2       78
     sky130_fd_sc_hd__nor2_2       524
     sky130_fd_sc_hd__nor2b_2        1
     sky130_fd_sc_hd__nor3_2        42
     sky130_fd_sc_hd__nor4_2         1
     sky130_fd_sc_hd__o2111a_2       2
     sky130_fd_sc_hd__o211a_2       69
     sky130_fd_sc_hd__o211ai_2       6
     sky130_fd_sc_hd__o21a_2        54
     sky130_fd_sc_hd__o21ai_2      141
     sky130_fd_sc_hd__o21ba_2      209
     sky130_fd_sc_hd__o21bai_2       1
     sky130_fd_sc_hd__o221a_2      204
     sky130_fd_sc_hd__o221ai_2       7
     sky130_fd_sc_hd__o22a_2      1312
     sky130_fd_sc_hd__o22ai_2       59
     sky130_fd_sc_hd__o2bb2a_2     119
     sky130_fd_sc_hd__o2bb2ai_2     92
     sky130_fd_sc_hd__o311a_2        8
     sky130_fd_sc_hd__o31a_2        19
     sky130_fd_sc_hd__o31ai_2        1
     sky130_fd_sc_hd__o32a_2       109
     sky130_fd_sc_hd__o41a_2         2
     sky130_fd_sc_hd__or2_2       1088
     sky130_fd_sc_hd__or2b_2        25
     sky130_fd_sc_hd__or3_2         68
     sky130_fd_sc_hd__or3b_2         5
     sky130_fd_sc_hd__or4_2         93
     sky130_fd_sc_hd__or4b_2         6
     sky130_fd_sc_hd__or4bb_2        2

   Chip area for module '\picorv32a': 147712.918400
```

```bash
Flop ratio = Number of D Flip flops = 1613  = 0.1084
             ______________________   _____
             Total Number of cells    14876

```
  
 </details>

 
</details>
