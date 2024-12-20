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

# Day-1

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
   ![Alt text](DAY1/Day11.png)


To view the netlist:

```bash
cd designs/picorv32a/runs/09-11_06-33/results/synthesis/
gedit picorv32a.synthesis.v
```

   ![Alt text](DAY1/Day12.png)


To view the yosys report:
```bash
cd ../..
cd reports/synthesis
gedit 1-yosys_4.stat.rpt
```

   ![Alt text](DAY1/Day13.png)

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

 Percentage  of  DFF′s = 0.108429685 ∗ 100 = 10.84296854   % 

```

   ![Alt text](DAY1/Day14.png)

 </details>


# Day-2
<details>

**Utilization Factor and Aspect Ratio** : In IC floor planning, utilization factor and aspect ratio are key parameters. The utilization factor is the ratio of the area occupied by the netlist to the total core area. While a perfect utilization of 1 (100%) is ideal, practical designs target a factor of 0.5 to 0.6 to allow space for buffer zones, routing channels, and future adjustments. The aspect ratio, defined as height divided by width, indicates the chip’s shape; an aspect ratio of 1 denotes a square, while other values result in a rectangular layout. The aspect ratio is chosen based on functional, packaging, and manufacturing needs.

```bash
Utilisation Factor =  Area occupied by netlist
                     __________________________
                         Total area of core
                         

Aspect Ratio =  Height
               ________
                Width
```

**Pre-placed cells** : Pre-placed cells are essential functional blocks, such as memory, custom processors, and analog circuits, positioned manually in fixed locations. These blocks are crucial for the chip’s performance and remain fixed during placement and routing to preserve their functionality and layout integrity.

**Decoupling Capacitors** : Decoupling capacitors are placed near logic circuits to stabilize power supply voltages during transient events. Acting as local energy reserves, they help reduce voltage fluctuations, crosstalk, and electromagnetic interference (EMI), ensuring reliable power delivery to sensitive circuits.

**Power Planning**: A robust power planning strategy includes creating a power and ground mesh to distribute VDD and VSS evenly across the chip. This setup ensures stable power delivery, minimizes voltage drops, and improves overall efficiency. Multiple power and ground points reduce the risk of instability and voltage drop issues, supporting the design’s power needs effectively.

**Pin Placement**: Pin placement (I/O planning) is crucial for functionality and reliability. Strategic pin assignment minimizes signal degradation, preserves data integrity, and helps manage heat dissipation. Proper positioning of power and ground pins supports thermal management and enhances signal strength, contributing to overall system stability and manufacturability.

**Floorplaning using OpenLANE:**

Run the following commands:
```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
```

Snapshots of image showing synthesis was successful and floorplan was being run now.

   ![Alt text](Day2/1.png)


```bash
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
run_floorplan
```
   ![Alt text](Day2/2.png)


Now, run the below commands in a new terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs12-11_16-11/results/floorplan
gedit picorv32a.floorplan.def
```
   ![Alt text](Day2/3.png)


According to floorplan definition:

```bash
1000 Unit Distance = 1 Micron
Die width in unit distance = 660685−0 = 660685
Die height in unit distance = 671405−0 = 671405
Distance in microns = Value in Unit Distance/1000
​Die width in microns = 660685/1000 = 660.685 Microns
Die height in microns = 671405/1000 = 671.405 Microns
Area of die in microns = 660.685 × 671.405 = 443587.212425 Square Microns
To view the floorplan in magic. Open a new terminal and run the below commands:
```

Now we can view the floorpan using magic. This can be done in new terminal:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/12-11_16-11/results/floorplan/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
   ![Alt text](Day2/4.png)



Command to run placement:

```bash
run_placement
```
   ![Alt text](Day2/5.png)


```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/12-11_16-11/results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

   ![Alt text](Day2/6.png)


# **Cell Design and Characterization Flow**

## **Introduction to Library and Cell Design**

A **library** is a collection of standard cells used in digital designs, each with different sizes, functionalities, and threshold voltages. These cells serve as building blocks in an integrated circuit (IC). The cell design flow follows a sequence of steps to create, characterize, and validate cells for use in ASIC or FPGA designs.

### **Inputs for Cell Design Flow:**

- **PDKs (Process Design Kits):** Contains essential design data such as:
  - DRC (Design Rule Check) & LVS (Layout Versus Schematic) files
  - SPICE models for analog simulations
  - Libraries containing predefined cells and user-defined specifications
- **Design Steps:**
  1. **Circuit Design:** The initial design of the circuit based on functionality.
  2. **Layout Design:** The art of arranging the components on the silicon wafer. It involves:
     - **Euler’s Path** (to ensure no shorts or open circuits)
     - **Stick Diagram** (a graphical representation of the layout)
  3. **Extraction of Parasitics:** Identifying the parasitic components like resistance and capacitance that can affect the circuit's performance.
  4. **Characterization:** Involves measuring timing, noise, and power consumption characteristics of the cell.

### **Outputs:**
- **CDL** (Circuit Description Language) files for circuit representation
- **LEF** (Library Exchange Format) for physical layout
- **GDSII** for chip layout and fabrication
- **Extracted SPICE netlist** (.cir) files
- **Characterization Results:** Timing, noise, and power data in `.lib` files

---

## **Standard Cell Characterization Flow**

A typical standard cell characterization flow involves several steps that are essential to characterize the behavior and performance of cells in a digital design. The general flow includes the following stages:

1. **Read in the Models and Technology Files:** These files contain process technology data required for cell characterization.
2. **Read Extracted SPICE Netlist:** The netlist describes the electrical connectivity of the cell.
3. **Recognize Behavior of the Cells:** The behavior of each cell is analyzed based on its functionality.
4. **Read the Subcircuits:** Extract the subcircuits from the netlist.
5. **Attach Power Sources:** Supply necessary power to the cells during characterization.
6. **Apply Stimulus to the Characterization Setup:** A voltage or current stimulus is applied to simulate the circuit.
7. **Provide Necessary Output Capacitance Loads:** Set up load conditions for the output of the cell to simulate real-world conditions.
8. **Provide Necessary Simulation Commands:** These define the simulation conditions, such as transient analysis and timing measurements.

Once all these steps are defined, the configuration file is fed into a **characterization software** called **GUNA**, which generates the **timing**, **power**, and **noise** models. The results are saved as `.lib` files and classified into:
- **Timing Characterization**
- **Power Characterization**
- **Noise Characterization**

---

## **Timing Parameters**

In digital designs, several timing parameters are defined to assess the performance of standard cells. The following parameters are commonly used:

| **Timing Parameter**         | **Value**          |
|------------------------------|--------------------|
| `slew_low_rise_thr`           | 20% value          |
| `slew_high_rise_thr`          | 80% value          |
| `slew_low_fall_thr`          | 20% value          |
| `slew_high_fall_thr`         | 80% value          |
| `in_rise_thr`                | 50% value          |
| `in_fall_thr`                | 50% value          |
| `out_rise_thr`               | 50% value          |
| `out_fall_thr`               | 50% value          |

### **Propagation Delay**

**Propagation delay** refers to the time it takes for a change in an input signal to propagate through a circuit and cause a corresponding change in the output signal. The time is measured when the input and output signals reach 50% of their final values.

- **Rise Delay:** 
```bash
rise_delay = time(out_fall_thr) - time(in_rise_thr)
```


### **Transition Time**

Transition time is the time it takes for a signal to transition between states. It is usually measured from 10% to 90% or 20% to 80% of the signal's voltage levels.

- **Fall Transition Time:**
```bash
fall_transition_time = time(slew_high_fall_thr) - time(slew_low_fall_thr)
```


- **Rise Transition Time:**
```bash
rise_transition_time = time(slew_high_rise_thr) - time(slew_low_rise_thr)
```

---

## **Conclusion**

This flow and the techniques described are part of the process used to design, simulate, and validate the behavior of standard cells in digital circuit design. By properly characterizing these cells, engineers can ensure that the cells perform optimally in terms of timing, noise, and power in real-world applications.

The **GUNA software** plays a key role in generating the required .lib files that are used for further optimization and integration into larger ASIC or FPGA designs.



</details>


# Day-3
<details>
	
### CMOS inverter ngspice simulations
Creating a SPICE Deck for a CMOS Inverter Simulation

1. Netlist Creation:

    Define component connections (netlist) for the CMOS inverter.
    Label nodes for input, output, ground, and supply.

2. Device Sizing:

    Specify Width-to-Length (W/L) ratios for PMOS and NMOS transistors.
    Ensure PMOS width is 2x to 3x larger than NMOS width.

3. Voltage Levels:

    Set gate and supply voltages.

4. Node Naming:

    Assign node names (e.g., VDD, GND, IN, OUT) for clarity in the SPICE netlist.
   
 ![image](https://github.com/user-attachments/assets/fb4232d1-bfc3-4041-9cc0-933e747750c9)

 ```bash
***syntax for PMOS and NMOS desription***
[component name] [drain] [gate] [source] [substrate] [transistor type] W=[width] L=[length]

 ***simulation commands***
.op --- is the start of SPICE simulation operation where Vin sweeps from 0 to 2.5 with 0.5 steps
tsmc_025um_model.mod  ----  model file which contains the technological parameters for the 0.25um NMOS and PMOS
```

Commands to simulate in SPICE:

```bash
source [filename].cir
run
setplot 
dc1 
plot out vs in
```

![image](https://github.com/user-attachments/assets/3b386b0b-3b97-41b7-9892-4a7037cefc5a)

Switching Threshold VmVm​ of a CMOS Inverter

Definition:

    The switching threshold VmVm​ is the critical voltage at which a CMOS inverter switches between outputting a "0" and a "1".
    At VmVm​, both PMOS and NMOS transistors are in saturation, resulting in high leakage current.

Effects of Transistor Sizing:

    If PMOS is thicker (wider) than NMOS, VmVm​ is higher (e.g., 1.2V vs. 1V).
    If NMOS is thicker than PMOS, VmVm​ is lower.

Leakage Current:

    At VmVm​, both transistors are turned on, causing current to flow directly from VDD to Ground, known as leakage current.

Simulation to Find VmVm​:
```bash
Vin in 0 2.5
*** Simulation Command ***
.op
.dc Vin 0 2.5 0.05
```

![image](https://github.com/user-attachments/assets/81c726ff-ce36-47bf-9185-acd1673a2814)

Transient analysis is used for finding propagation delay. SPICE transient analysis uses pulse input shown below:

![image](https://github.com/user-attachments/assets/cfbea221-7c7c-46ed-9701-5fb45e4a30e1)

The simulation commands:
```bash
Vin in 0 0 pulse 0 2.5 0 10p 10p 1n 2n 
*** Simulation Command ***
.op
.tran 10p 4n
```
Result of SPICE simulation for transient analysis:
![image](https://github.com/user-attachments/assets/ffc7ca1d-c01a-42b9-bae6-e0d610e74799)

Now, we clone the custom inverter:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
git clone https://github.com/nickson-jose/vsdstdcelldesign
cd vsdstdcelldesign
cp /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech .
ls
magic -T sky130A.tech sky130_inv.mag &
```
   ![Alt text](Day3/1.png)

   ![Alt text](Day3/2.png)



### Inception of Layout CMOS Fabrication Process

1. Substrate Preparation:

    Start with a silicon wafer substrate.

2. N-Well Formation:

    Create N-well regions using phosphorus ion implantation or diffusion.

3. P-Well Formation:

    Form P-well regions using boron ion implantation or diffusion.

4. Gate Oxide Deposition:

    Deposit a thin silicon dioxide layer as the gate oxide.

5. Poly-Silicon Deposition:

    Deposit polysilicon on the gate oxide for the gate electrode.

6. Poly-Silicon Masking and Etching:

    Use a photoresist mask to define polysilicon areas and etch the rest.

7. N-Well Masking and Implantation:

    Mask N-well areas and implant phosphorus impurities.

8. P-Well Masking and Implantation:

    Mask P-well areas and implant boron impurities.

9. Source/Drain Implantation:

    Implant dopants to create source and drain regions.

10. Gate Formation: - Define the gate electrode by etching the polysilicon layer.

11. Source/Drain Masking and Etching: - Mask source/drain regions and etch the oxide layer.

12. Contact/Via Formation: - Etch contact holes/vias to expose underlying regions.

13. Metal Deposition: - Deposit a metal layer (aluminum or copper) for interconnects.

14. Metal Masking and Etching: - Mask and etch to define metal interconnect patterns.

15. Passivation Layer Deposition: - Deposit a protective silicon dioxide or nitride layer.

16. Final Testing and Packaging: - Test the wafer, separate working chips, and package them.

![image](https://github.com/user-attachments/assets/523de5eb-63b3-41c2-8e96-f17d24b8365a)

Inverter layout:

   ![Alt text](Day3/2.png)

Identify NMOS:

![image](https://github.com/user-attachments/assets/45335bb9-a0a8-4b3b-b371-69ad5800f33b)

Identify PMOS:

![image](https://github.com/user-attachments/assets/fc42ec24-942f-48ea-9a57-0caaf59c8676)

Output Y:

![image](https://github.com/user-attachments/assets/d08d4ed0-c83e-410a-b9a3-2ddb149b5367)




Spice extraction of inverter in Magic. Run these in the tkcon window:
```bash
# Check current directory
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```
   
   ![Alt text](Day3/3.png)


To view the spice file:

   ![Alt text](Day3/4.png)


Now modify the `sky130_inv.spice` file to find the transient response:

```bash
* SPICE3 file created from sky130_inv.ext - technology: sky130A

.option scale=0.01u
.include ./libs/pshort.lib
.include ./libs/nshort.lib

//.subckt sky130_inv A Y VPWR VGND
M1000 Y A VGND VGND nshort_model.0 w=35 l=23
+  ad=1.44n pd=0.152m as=1.37n ps=0.148m
M1001 Y A VPWR VPWR pshort_model.0 w=37 l=23
+  ad=1.44n pd=0.152m as=1.52n ps=0.156m

VDD VPWR 0 3.3V
VSS VGND 0 0V
Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)

C0 A VPWR 0.0774f
C1 VPWR Y 0.117f
C2 A Y 0.0754f
C3 Y VGND 2f
C4 A VGND 0.45f
C5 VPWR VGND 0.781f
//.ends

.tran 1n 20n
.control
run
.endc
.end
```

Now, simulate the spice netlist:

```bash
ngspice sky130_inv.spice
```
   ![Alt text](Day3/5.png)

```bash
plot y vs time a
```
   ![Alt text](Day3/6.png)


Using this transient response, we will now characterize the cell's slew rate and propagation delay:

Rise Transition: Time taken for the output to rise from 20% to 80% of max value Fall Transition: Time taken for the output to fall from 80% to 20% of max value Cell Rise delay: difference in time(50% output rise) to time(50% input fall) Cell Fall delay: difference in time(50% output fall) to time(50% input rise)

```bash
Rise Transition : 2.24638 - 2.18242 =  0.06396 ns = 63.96 ps
Fall Transition : 4.0955 - 4.05536 =  0.0419 ns = 41.9 ps
Cell Rise Delay : 2.21144 - 2.15008 = 0.06136 ns = 61.36 ps
Cell Fall Delay : 4.07807 - 4.05 =0.02 ns = 20 ps
```
Magic Tool options and DRC Rules:

Now, go to home directory and run the below commands:
```bash
cd
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests
ls -al
gvim .magicrc
magic -d XR &
```
   ![Alt text](Day3/7.png)

   ![Alt text](Day3/8.png)


First load the poly file by load poly.mag on tkcon window.

   ![Alt text](Day3/9.png)


We can see that Poly.9 is incorrect.

Add the below commands in the sky130A.tech

   ![Alt text](Day3/10.png)

   ![Alt text](Day3/11.png)


```bash
tech load sky130A.tech
drc check
drc why
```
   ![Alt text](Day3/12.png)

</details>

# Day-4:
<details>
	
	
 ###  Pre-layout timing analysis and importance of good clock tree:

 Commands to extract tracks.info file:

 ```bash
cd Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
cd ../../pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/
less tracks.info
```
   ![Alt text](Day3/13.png)

Commands for tkcon window to set grid as tracks of locali layer

```bash
grid 0.46um 0.34um 0.23um 0.17um
```
   ![Alt text](Day4/1.png)

With grid:
   
   ![Alt text](Day4/2.png)

Now, save it by giving a custom name:
```bash
save sky130_varinv.mag
```
   ![Alt text](Day4/3.png)

Now, open it by using the following commands:
```bash
magic -T sky130A.tech sky130_varinv.mag &
```
   ![Alt text](Day4/4.png)

Now, type the following command in tkcon window:
```bash
lef write
```
   ![Alt text](Day4/5.png)

   ![Alt text](Day4/6.png)

Modify config.tcl:
```bash
# Design
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILES) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT)

set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

# Add the missing brackets for the glob command
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

# Correct the missing environment variable expansions
set filename "${::env(OPENLANE_ROOT)}/designs/${::env(DESIGN_NAME)}/${::env(PDK)}_${::env(STD_CELL_LIBRARY)}_config.tcl"
if { [file exists $filename] == 1 } {
    source $filename
}

```
Now, run openlane flow synthesis:
```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
```
   ![Alt text](Day4/7.png)

```bash
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```
   ![Alt text](Day4/8.png)
   
   ![Alt text](Day4/9.png)
   
   ![Alt text](Day4/10.png)

Delay Tables

Role of Delay:
Critical in cell timing, influenced by input transition and output load.
Same cell types can have varying delays due to wire resistance and capacitance differences.

Delay Tables:
Use 2D arrays with input slew and load capacitance for each buffer size.
Timing models compute buffer delays from these tables.
Algorithms interpolate to estimate delays accurately, maintaining signal integrity across varying load conditions.

![image](https://github.com/user-attachments/assets/3f67e39d-8351-4f1f-97cb-19c85e507403)

Fixing slack:
```bash
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a -tag 13-11_20-00 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
echo $::env(SYNTH_STRATEGY)
set ::env(SYNTH_STRATEGY) "DELAY 3"
echo $::env(SYNTH_BUFFERING)
echo $::env(SYNTH_SIZING)
set ::env(SYNTH_SIZING) 1
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```
   ![Alt text](Day4/11.png)
   
   ![Alt text](Day4/12.png)
   
   ![Alt text](Day4/13.png)
   
Now, run floorplan:
```bash
run_floorplan
```
   ![Alt text](Day4/14.png)

   ![Alt text](Day4/15.png)

Since we are facing unexpected un-explainable error while using run_floorplan command, we can instead use the following set of commands available based on information from Desktop/work/tools/openlane_working_dir/openlane/scripts/tcl_commands/floorplan.tcl and also based on Floorplan Commands section in Desktop/work/tools/openlane_working_dir/openlane/docs/source/OpenLANE_commands.md

```bash
init_floorplan
place_io
tap_decap_or
```

Now, do placement
```bash
run_placement
```
   ![Alt text](Day4/16.png)

   ![Alt text](Day4/17.png)

Now, open a new terminal and run the below commands to load placement def in magic

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_20-00/results/placement/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```
   ![Alt text](Day4/18.png)

   ![Alt text](Day4/19.png)

Custom inverter inserted in placement def

   ![Alt text](Day4/20.png)

Expaned view:
   ![Alt text](Day4/21.png)

   

Timing analysis with ideal clocks using openSTA

Pre-layout STA will include effects of clock buffers and net-delay due to RC parasitics (wire delay will be derived from PDK library wire model).

![image](https://github.com/user-attachments/assets/de77e261-1847-4c93-a44f-a5250e1dbd2f)

Since we are getting 0 wns after improved timing run, we will be doing the timing analysis on initial run of synthesis which has lots of violations and no parameters added to improve timing.

Commands to invoke the OpenLANE flow include new lef and perform synthesis:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
run_synthesis
```
Go, to Desktop/work/tools/openlane_working_dir/openlane and create a file pre_sta.conf. The contents of the file are:

```bash
set_cmd_units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -max /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib
read_liberty -min /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib
read_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/results/synthesis/picorv32a.synthesis.v
link_design picorv32a
read_sdc /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```
Contents of `my_base.sdc`

```bash
set ::env(CLOCK_PORT) clk
set ::env(CLOCK_PERIOD) 12.000
set ::env(SYNTH_DRIVING_CELL) sky130_fd_sc_hd__inv_8
set ::env(SYNTH_DRIVING_CELL_PIN) Y
set ::env(SYNTH_CAP_LOAD) 17.65
create_clock [get_ports $::env(CLOCK_PORT)]  -name $::env(CLOCK_PORT)  -period $::env(CLOCK_PERIOD)
set IO_PCT  0.2
set input_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
set output_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
puts "\[INFO\]: Setting output delay to: $output_delay_value"
puts "\[INFO\]: Setting input delay to: $input_delay_value"


set clk_indx [lsearch [all_inputs] [get_port $::env(CLOCK_PORT)]]
#set rst_indx [lsearch [all_inputs] [get_port resetn]]
set all_inputs_wo_clk [lreplace [all_inputs] $clk_indx $clk_indx]
#set all_inputs_wo_clk_rst [lreplace $all_inputs_wo_clk $rst_indx $rst_indx]
set all_inputs_wo_clk_rst $all_inputs_wo_clk


# correct resetn
set_input_delay $input_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] $all_inputs_wo_clk_rst
#set_input_delay 0.0 -clock [get_clocks $::env(CLOCK_PORT)] {resetn}
set_output_delay $output_delay_value  -clock [get_clocks $::env(CLOCK_PORT)] [all_outputs]

# TODO set this as parameter
set_driving_cell -lib_cell $::env(SYNTH_DRIVING_CELL) -pin $::env(SYNTH_DRIVING_CELL_PIN) [all_inputs]
set cap_load [expr $::env(SYNTH_CAP_LOAD) / 1000.0]
puts "\[INFO\]: Setting load to: $cap_load"
set_load  $cap_load [all_outputs]

```

Commands to run STA:
```bash
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```
   ![Alt text](Day4/22.png)
   
   ![Alt text](Day4/23.png)

We now try to optimise synthesis.
Go to new terminal and run the following commands:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
prep -design picorv32a -tag 13-11_21-03 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_SIZING) 1
set ::env(SYNTH_MAX_FANOUT) 4
echo $::env(SYNTH_DRIVING_CELL)
run_synthesis
```
   ![Alt text](Day4/24.png)

Commands to run STA:
```bash
cd Desktop/work/tools/openlane_working_dir/openlane
sta pre_sta.conf
```

   ![Alt text](Day4/25.png)

   ![Alt text](Day4/26.png)

### Basic timing ECO

NOR gate of drive strength 2 is driving 5 fanouts

   ![Alt text](Day4/27.png)

Run the following commands to optimise timing:
```bash
report_net -connections _13111_
replace_cell _16171_ sky130_fd_sc_hd__nor3_2
report_checks -fields {net cap slew input_pins} -digits 4
```

   ![Alt text](Day4/28.png)

We can observe that the tns has reduced to -402.45 from -3851.51 and wns has reduced to -5.44 from -36.63

Clock tree synthesis TritonCTS and signal integrity

Clock Tree Synthesis (CTS) techniques vary based on design needs:

Balanced Tree CTS: Uses a balanced binary-like tree for equal path lengths, minimizing clock skew but with moderate power efficiency.

H-tree CTS: Employs an "H"-shaped structure, good for large areas and power efficiency.

![image](https://github.com/user-attachments/assets/a98268f1-881a-4551-92df-80d3c5e72bb4)



### Clock Tree Synthesis (CTS) Topologies

### Star CTS:

    Distributes the clock from a central point.
    Minimizes skew but requires more buffers near the source.

### Global-Local CTS:

    Combines star and tree topologies.
    Uses a global tree for clock domains and local trees within domains.
    Balances global and local timing.

### Mesh CTS:

    Employs a grid pattern ideal for structured designs.
    Balances simplicity and skew.

### Adaptive CTS:

    Dynamically adjusts based on timing and congestion.
    Offers flexibility but adds complexity.

### Crosstalk

    Definition:
        Interference from overlapping electromagnetic fields between adjacent circuits, causing unwanted signals.

    Impact in VLSI:
        Leads to data corruption, timing issues, and higher power consumption.

    Mitigation Strategies:
        Optimized layout and routing.
        Shielding.
        Clock gating to reduce dynamic power and minimize crosstalk effects.

![image](https://github.com/user-attachments/assets/f22893c8-4733-4152-bb5c-65613601719f)

Clock Net Shielding

    Purpose:
        Prevents glitches and reduces interference by isolating the clock network.

    Method:
        Uses shields connected to VDD or GND that do not switch.
        Dedicated routing layers and clock buffers are often employed.

    Benefits:
        Isolates clocks from other signals.
        Helps prevent cross-domain interference.
        Avoids metastability and maintains synchronization through clock domain isolation.

![image](https://github.com/user-attachments/assets/11eb37f6-6e2f-46b2-9a1e-78f4a255a48d)

Now to insert this updated netlist to PnR flow and we can use write_verilog and overwrite the synthesis netlist but before that we are going to make a copy of the old old netlist:

Run the following commands:
```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/results/synthesis/
ls
cp picorv32a.synthesis.v picorv32a.synthesis_old.v
ls
```
Commands to write verilog:
```bash
write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/results/synthesis/picorv32a.synthesis.v
exit
```
Verified that the netlist is overwritten

   ![Alt text](Day4/29.png)

Now, run the following commands:

```bash
cd Desktop/work/tools/openlane_working_dir/openlane
docker
./flow.tcl -interactive
prep -design picorv32a -tag 13-11_21-03 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
set ::env(SYNTH_STRATEGY) "DELAY 3"
set ::env(SYNTH_SIZING) 1
run_synthesis
init_floorplan
place_io
tap_decap_or
run_placement
run_cts
```
   ![Alt text](Day4/30.png)

   ![Alt text](Day4/31.png)

   ![Alt text](Day4/32.png)
   
   ![Alt text](Day4/33.png)

### Setup Timing Analysis Using Real Clocks

    Real Clock Factors:
        Clock Skew: Difference in clock signal arrival times at various parts of the circuit due to physical delays.
            Affects setup and hold timing margins.
        Clock Jitter: Variability in the clock period caused by power, temperature, and noise fluctuations.
            Leads to uncertainty in clock edge timing.

    Importance:
        Both clock skew and jitter are crucial for accurate timing analysis.
        Ensures the design performs reliably in real-world conditions.

![image](https://github.com/user-attachments/assets/7c582e91-e8af-4318-a258-aed3e329a4b8)

![image](https://github.com/user-attachments/assets/1f111d25-88b0-411a-bc3f-0626c00175c7)

Now, enter the following commands for Post-CTS OpenROAD timing analysis:
```bash
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/13-11_21-03/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/cts/picorv32a.cts.def
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
exit
```
   ![Alt text](Day4/34.png)

   ![Alt text](Day4/35.png)

Now, enter the following commands for exploring post-CTS OpenROAD timing analysis by removing 'sky130_fd_sc_hd__clkbuf_1' cell from clock buffer list variable 'CTS_CLK_BUFFER_LIST':

```bash
echo $::env(CTS_CLK_BUFFER_LIST)
set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0]
echo $::env(CTS_CLK_BUFFER_LIST)
echo $::env(CURRENT_DEF)
set ::env(CURRENT_DEF) /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/placement/picorv32a.placement.def
run_cts
echo $::env(CTS_CLK_BUFFER_LIST)
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/13-11_21-03/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/cts/picorv32a.cts.def
write_db pico_cts1.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -fields {slew transd net cap input_pins} -format full_clock_expanded -digits 4
report_clock_skew -hold
report_clock_skew -setup
exit
echo $::env(CTS_CLK_BUFFER_LIST)
set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky130_fd_sc_hd__clkbuf_1]
echo $::env(CTS_CLK_BUFFER_LIST)
```

   ![Alt text](Day4/36.png)
   
   ![Alt text](Day4/37.png)
   
   ![Alt text](Day4/38.png)

</details>

# Day-5
<details>

## Maze Routing and Lee's Algorithm

Routing creates physical connections between pins, and Maze Routing algorithms, like Lee's algorithm, find efficient paths on a routing grid.

#### Lee's Algorithm:

    Starts at the source pin and assigns incremental labels to neighboring cells until the target pin is reached.
    Prioritizes L-shaped routes, using zigzag paths if necessary.
    Effective for finding the shortest path between two pins.
    Can be slow for large-scale designs, leading to the adoption of faster alternatives for complex routing tasks.

![image](https://github.com/user-attachments/assets/d4ec6fcb-b2c8-4877-b0c7-435367501d16)

Design Rule Check:
Command to generate Power Distribution Network (PDN):
```bash
gen_pdn
```
Now, in a new terminal

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/tmp/floorplan/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read 14-pdn.def &
```
   ![Alt text](Day5/1.png)

   ![Alt text](Day5/2.png)


## Power Distribution Network (PDN) Generation

When the PDN generation command is issued, the system uses the design_cts.def file to create the PDN.

### Components of the PDN:

    Power Rings:
        Power is drawn from VDD and VSS pads to the power rings.
    Straps:
        Horizontal and vertical straps connect to the power rings to distribute power.
        Straps channel power to the rails connected to standard cells.
    Rails:
        Placed at standard cell height intervals, aligning with multiples of the track pitch (2.72 in this design).
        Ensure proper power delivery to all standard cells.

### Design Details:

    Metal Layers:
        Straps are placed on metal layers 4 and 5.
        Standard cell rails are on metal layer 1.
    Vias:
        Used to interconnect different metal layers.
        Ensure seamless power flow from pads to cells across the metal levels.
	
![image](https://github.com/user-attachments/assets/3953a0b9-ebd8-4f2e-b920-0375ccae0de3)

Now, we perfrom detailed routing using TritonRoute:
```bash
echo $::env(CURRENT_DEF)
echo $::env(ROUTING_STRATEGY)
run_routing
```
   ![Alt text](Day5/3.png)

Now, in a new terminal
```bash
cd Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/results/routing/
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.def &
```
   ![Alt text](Day5/4.png)

Expanded layout showing the custom inverter sky130_varinv

   ![Alt text](Day5/5.png)

Fast route guide present in `openlane/designs/picorv32a/runs/13-11_21-03/tmp/routing`

   ![Alt text](Day5/6.png)

## TritonRoute

### TritonRoute Detailed Routing

    Overview:
        Follows pre-processed global route guides.
        Uses a MILP-based panel routing scheme.

    Routing Types:
        Intra-Layer Parallel Routing:
            Simultaneous routing within a single layer.
        Inter-Layer Sequential Routing:
            Routing from bottom to top metal layers.

    Layer Preferences:
        Respects each metal layer's preferred direction as defined in the LEF file (e.g., met1 horizontal, met2 vertical).
        Minimizes overlapping and potential capacitance issues.

    Benefits:
        Improved signal integrity.
![image](https://github.com/user-attachments/assets/d185ab41-152c-4665-b69c-a79c00ce2bc8)

![image](https://github.com/user-attachments/assets/a1126f94-0b67-4ce9-af7d-8a41fbee7292)

### Feature: Pre-Processed Route Guides

    Overview:
        TritonRoute uses pre-processed route guides from the global router.

    Function:
        Provides an initial path outline for efficient, detailed routing.
        Optimizes for connectivity and minimizes conflicts by following predefined paths.

 ![image](https://github.com/user-attachments/assets/7c1f3543-6406-4e60-a03f-c2bb7ac3c644)

### Feature: Inter-Guide Connectivity

    Overview:
        Ensures seamless connections between adjacent route guides.

    Function:
        Maintains continuity across guide boundaries.
        Allows signals to pass smoothly from one guide to the next.
        Reduces routing gaps and improves overall connectivity throughout the design.

 
 ![image](https://github.com/user-attachments/assets/d572bdd4-40d6-471a-aa72-8f3c71c4875b)



**Intra-layer Routing**: Routing within a single metal layer, performed in parallel to optimize path efficiency.

**Inter-layer Routing**: Sequential routing across metal layers, starting from the bottom, following layer direction rules to minimize interference

![image](https://github.com/user-attachments/assets/802f0c92-99e7-491f-a0ba-59fa63ce661e)

### TritonRoute method to handle connectivity
#### TritonRoute Connectivity

    Overview:
        Ensures robust connectivity by following global route guides.

    Routing Management:
        Intra-Layer Routing:
            Connects paths within each layer in parallel.
        Inter-Layer Routing:
            Links paths sequentially across layers, from bottom to top.

    Benefits:
        Structured approach helps avoid congestion.
        Maintains consistent signal flow throughout the design.
![image](https://github.com/user-attachments/assets/93f385ac-1c37-49d1-b355-db0012df1634)

### Routing Topology Algorithm

A routing topology algorithm defines the structure and path configuration of connections between pins in an integrated circuit. It aims to create an efficient, minimal-cost layout by determining the best routing paths and shapes.

![image](https://github.com/user-attachments/assets/52eec0f5-2114-4c67-8508-2062c7819584)

Commands for SPEF extraction Post-Route parasitic extraction using SPEF extractor

```bash
cd Desktop/work/tools/openlane_working_dir/openlane/scripts/spef_extractor
python3 main.py -l /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/tmp/merged.lef -d /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/13-11_21-03/results/routing/picorv32a.def
```
   ![Alt text](Day5/7.png)


Commands for Post-Route OpenSTA timing analysis with the extracted parasitics of the route:
```bash
openroad
read_lef /openLANE_flow/designs/picorv32a/runs/13-11_21-03/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/routing/picorv32a.def
write_db pico_route.db
read_db pico_route.db
read_verilog /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/synthesis/picorv32a.synthesis_preroute.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
link_design picorv32a
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
read_spef /openLANE_flow/designs/picorv32a/runs/13-11_21-03/results/routing/picorv32a.spef
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded -digits 4
exit
```
   ![Alt text](Day5/8.png)
   
   ![Alt text](Day5/9.png)
   
   ![Alt text](Day5/10.png)

---

</details>
</details>

 
# Task-14: OpenRoad Physical Design
<details>

# Path to Zetta-Scale Computing

## Introduction

### Bombe
The Bombe was an early electro-mechanical device created during World War II to break German Enigma-encrypted messages. Developed by Alan Turing and Gordon Welchman at Bletchley Park in the UK, it worked by systematically testing different rotor settings of the Enigma machine. By using known plaintext patterns, it dramatically reduced the number of possible keys, speeding up the decryption process and playing a crucial role in the Allied victory.

### ENIAC (Electronic Numerical Integrator and Computer)
The ENIAC, completed in 1945 by John Presper Eckert and John Mauchly at the University of Pennsylvania, was the first general-purpose electronic digital computer. Originally designed to compute artillery firing tables for the U.S. Army, ENIAC used vacuum tubes and was fully electronic. It required manual reconfiguration for different tasks as it lacked a stored-program capability. ENIAC demonstrated the immense computational power of electronic machines.

### EDVAC (Electronic Discrete Variable Automatic Computer)
EDVAC, conceptualized by Eckert, Mauchly, and John von Neumann, was completed in 1949 and was among the first computers to implement the stored-program concept. Using binary representation and storing both data and instructions in memory, EDVAC marked a significant improvement over ENIAC. This design innovation simplified programming and laid the foundation for the modern computer architecture.

## 50 Years of Microprocessor Trend Data

![Microprocessor Trend Data](image)

### Key Metrics

- **Transistors (Orange Triangles):** The number of transistors on a microprocessor chip has exponentially increased, following Moore's Law. This increase enabled more complex and capable processors, reaching billions of transistors by the 2020s.
- **Single-Thread Performance (Blue Circles):** Measured by SpecINT, it shows the computational power of a single processor core. Performance steadily increased due to architectural improvements, parallelism, and clock speeds, but growth slowed after 2005 due to physical limitations.
- **Frequency (Green Diamonds):** Processor clock speed rose steadily until the early 2000s but then stagnated due to heat dissipation issues.
- **Typical Power (Red Triangles):** Power consumption increased with transistor density and frequency, becoming a critical design challenge by the mid-2000s.
- **Number of Logical Cores (Black Dots):** Multi-core processors became common in the mid-2000s as a response to stagnating single-thread performance. Increasing core counts improved parallel processing and overall performance.

### Key Milestones

- **iPhone Release (~2007):** Marked the rise of mobile computing, where power efficiency became as important as performance, driving innovations in low-power processor designs.
- **Datacenter-Scale Computing (Post-2010):** The rise of cloud computing and large-scale data centers focused on energy efficiency, scalability, and parallelism.

## Path to Zetta-Scale Computing

![Path to Zetta-Scale Computing](image)

This chart shows the progression of computing performance from gigascale in 1984 to the projected zettascale by 2035, measured in FLOPS (floating-point operations per second).

### Key Performance Levels

- **Gigascale (10⁹ FLOPS):** The starting point in 1984, marking the capabilities of early supercomputers.
- **Terascale (10¹² FLOPS):** Achieved around 1997, marking systems like the Cray XT5's teraflop performance with significant power consumption.
- **Petascale (10¹⁵ FLOPS):** Achieved in 2008 with systems like the Cray XK6 Titan, reaching 27 petaflops.
- **Exascale (10¹⁸ FLOPS):** Reached by systems like the Cray Shasta Frontier in 2021, delivering 1.5 exaflops with advanced GPU and CPU integration.
- **Zettascale (10²¹ FLOPS):** Expected around 2035, enabling unprecedented computational tasks in fields like climate modeling and AI, with projected power consumption of 50-100 MW.

## CMOS Evolution and Next-Gen Candidates

![CMOS Evolution](image)

This diagram explores the development of CMOS technology and next-generation materials and processes aimed at continuing Moore's Law.

### Channel Material

#### Current Trends

- **Silicon (Si):** The main material for traditional CMOS transistors, with strained SiGe used for high-performance applications.

#### Future Materials

- **2D Materials:** Such as MoS₂, offering better electrical properties at smaller scales.
- **Germanium (Ge):** Offers higher electron mobility, boosting performance for small-node transistors.

### Patterning

#### Current Techniques

- **Deep Ultraviolet (DUV) Lithography:** The standard for defining transistor features, using ArF and KrF lasers.

#### Next-Gen

- **Extreme Ultraviolet (EUV) Lithography:** Key for sub-7nm nodes, with High-NA EUV improving resolution for even smaller transistors.

### Gate Stack Material

#### Current Materials

- **High-K Metal Gates (HKMG):** Used to reduce leakage current and improve switching performance.

#### Next-Gen Candidates

- **NC-FET (Negative Capacitance FET):** Uses ferroelectric materials for lower voltage operation and reduced power consumption.
- **TFET (Tunnel FET):** Utilizes quantum tunneling to switch, significantly reducing power consumption.

### Interconnection Material

#### Current Materials

- **Copper (Cu):** The main material for interconnects, minimizing power loss and delays.

#### Next-Gen Materials

- **Ruthenium (Ru) and Compound Metals:** Potential to reduce resistance and improve performance in ultra-small transistors.
- **Topological Semi-Metals:** Offering unique properties for lower resistivity at the atomic scale.

### Device Structure

#### Current Architectures

- **FinFET and Planar Transistors:** Used to maintain performance at smaller nodes, with FinFETs providing better control over short-channel effects.

#### Next-Gen Architectures

- **3DS-FET (3D Stacked FET):** Stacks devices vertically, reducing footprint and improving performance.
- **MBC-FET (Multi-Bridge Channel FET):** Enhances drive current by creating multiple channels.
- **VFET (Vertical FET):** Uses vertical channels to improve density and reduce power consumption.

### Design Co-Optimization

- **DTCO (Design-Technology Co-Optimization):** Integrates new design techniques with advanced process technologies, including backside interconnects for improved signal integrity.
- **STCO (System-Technology Co-Optimization):** Optimizes both system architecture and technology, using chiplets for modular, customized designs.

## FinFETs

![FinFETs](image)

This diagram shows the transition from planar transistors to advanced architectures like FinFET and Gate-All-Around (GAA).

### Planar Transistor (Traditional)

- Early design with a flat channel and gate, limited in performance as scaling continues.

### FinFET (2011)

- Vertical fin-shaped channel with the gate wrapping around three sides, providing better control and reducing leakage.

### Gate-All-Around (GAA) Transistor (2025?)

- Gate completely surrounds the channel, typically using stacked nanosheets or nanowires, offering superior control and enabling further scaling beyond FinFET limitations.

These advancements mark significant milestones in semiconductor technology, driving continuous improvements in computing performance and efficiency.


Why FinFETs and Gate-All-Around Transistors?
------------------------------------------

This diagram outlines the benefits of FinFETs and Gate-All-Around (GAA) transistors in comparison to conventional planar designs:

Planar Transistors:
Challenges:
- Sub-channel leakage happens when current escapes beneath the gate.
- This leads to reduced efficiency and increased power consumption.

FinFET Transistors:
- The gate wraps around the channel (the fin) on three sides, offering superior control over the channel.
Benefits:
- Minimizes sub-threshold leakage.
- Enhances drive current (I_{ON}).
- Reduces transistor area while maintaining high performance.

Gate-All-Around (GAA) Transistors:
- The gate fully surrounds the channel, providing exceptional electrostatic control.
Advantages:
- Improves performance in short-channel devices by decreasing drain capacitance and boosting gate capacitance.
- Supports scaling efficiency, following the relationship (S ∝ (1 + C_d / C_{ox})).
- Delivers reduced sub-threshold slope and improved performance at smaller node sizes.

Graph Comparison:
- The graph demonstrates the performance benefits of FinFET and GAA compared to planar transistors.
- It highlights improved efficiency and reduced sub-threshold slope as the transistors shrink.

Reduced Leakage:
- Tri-Gate transistors significantly reduce leakage current compared to planar transistors, even at the same gate voltage.
- The reduction in leakage lowers off-current and power dissipation.

Higher Drive Current:
- Tri-Gate transistors provide higher drive current at the same off-current, resulting in improved performance and better efficiency in modern electronics.

FEOL Innovations:
- FEOL stands for the Front-End of Line, which involves the creation of active devices such as transistors and capacitors on the silicon wafer.
- FEOL innovations enable the progress of Moore's Law, producing smaller, more efficient, and powerful transistors.

CMOS Technology Milestones
---------------------------

Dennard Scaling:
- Dennard Scaling suggests that power density remains constant as transistor sizes shrink.
- It enabled voltage scaling with smaller gate lengths, as seen in the graph on the bottom-left.

Key Technology Nodes and Innovations:
- ~1 µm ("End of Scaling"): Initiation of CMOS miniaturization.
- 180 nm (Voltage Scaling): Voltage scaling begins with reduced drive voltage.
- 130 nm (Cu BEOL): Copper interconnects introduced for improved conductivity.
- 90 nm (Uniaxial Strained Si NMOS): Strained silicon enhances electron mobility.
- 65 nm (eSiGe CVD ULK): Embedded SiGe improves PMOS performance.
- 45 nm (HK-first MG-last): High-k dielectrics and metal gates enhance leakage control.
- 32 nm (HKMG with Raised S/D NMOS): Implementation of HKMG and raised S/D regions.

SEM Images:
- Left Image: Cross-sectional view of a transistor with high-k materials and SiGe for enhanced PMOS performance.
- Right Image: Shows raised source/drain regions and gate channel in PMOS transistors at smaller nodes.

Drive Voltage Scaling Graph:
- The graph shows the relationship between gate length (x-axis, logarithmic scale) and drive voltage (y-axis, logarithmic scale). Ideal scaling behavior suggests voltage should decrease linearly with gate length. However, real-world trends for low-power and high-performance devices deviate due to leakage currents and power density challenges.

Key Technology Nodes and Innovations:
- 22 nm: Introduction of FinFET (Tri-Gate) transistors for reduced leakage and better gate control.
- 14 nm: Unidirectional metal routing for higher density and implementation of SADP for precise layout.
- 10 nm: Advanced patterning techniques such as SA-SDB, LELELE, and SAQP for tighter geometries.
- 7 nm: Adoption of Extreme Ultraviolet Lithography (EUV) to reduce patterning errors.
- 5 nm: Integration of SiGe for PMOS, enhancing hole mobility with EUV SA-LELE.
- 3 nm / 2 nm / 1.4 nm: Introduction of Gate-All-Around (GAA) nanosheet transistors, stacking nanosheets or nanowires horizontally to improve electrostatic control.
- Sub-1 nm: Development of CFET (Complementary FET), which vertically stacks NMOS and PMOS to save space, and the use of 2D materials like MoS₂ for atomic-scale channel thickness.

Advanced Transistor Design Techniques:
Double Diffusion Break (DDB): This technique creates a gap between the source and drain regions of a transistor filled with insulating material. It reduces the effective transistor width, enabling smaller cell sizes and higher transistor density, which are crucial for improved scalability in advanced nodes.

Single Diffusion Break (SDB): Similar to DDB but less aggressive, SDB introduces a gap on only one side of the transistor, offering a balance between size reduction and transistor performance.

Contact Over Field Gate (COFG): By placing the gate contact directly over the field oxide region, this technique minimizes lateral spacing between transistors, reducing cell sizes without significantly impacting performance.

Contact Over Active Gate (COAG): A more aggressive version of COFG, COAG places the gate contact directly over the active gate region, enabling further size reductions and higher transistor density.

Back-Side Power Delivery Network (BS-PDN): This method routes power supply rails on the backside of the chip, allowing for more transistors in the same area while also improving power delivery efficiency and reducing resistance.

Technology Evolution:
Planar Technology: In early planar technology nodes (100nm and above), there was significant variability in threshold voltage (Vt), around 130mV. This was attributed to process variations, temperature fluctuations, and line-edge roughness.

FinFET Technology: FinFETs, introduced around 22nm, provided better control over the channel, reducing Vt variability to around 14mV.

Nanowire Technology (NW): With advancements below 14nm, nanowire technology further reduced Vt variability to approximately 7mV, thanks to precise control over nanowire dimensions.

Parasitic Resistance:
Parasitic Resistance Overview: The breakdown of parasitic resistance, 
𝑅
𝐸
𝑋
𝑇
R 
EXT
​
 , in transistors shows different contributing components like contact resistance, BEOL resistance, and source/drain extension resistance. Reducing these resistances is critical for performance in advanced nodes.

Improvements: Methods to improve contact resistance, like reducing the Schottky barrier height (
𝜙
𝑏
ϕ 
b
​
 ) and increasing doping concentration (
𝑁
𝑑
N 
d
​
 ), are essential for improving the performance of transistors as they scale down.

Capacitance and Spacer Materials:
Capacitance Evolution: As technology nodes shrink, parasitic capacitance becomes more significant. At 22nm, gate-source capacitance (
𝐶
𝑔
C 
g
​
 ) was less significant, but by 7nm, it dominated. Spacer materials play a crucial role in reducing capacitance. Low-
𝑘
k materials, like air spacers, help decouple parasitic effects, significantly improving performance.

Material Transition: The evolution of spacer materials, from SiOCN to SiCO, reduces gate-contact capacitance and improves performance in nodes 14nm and below.

2D Materials and Transistor Scaling:
2D Materials (MoS₂): 2D materials like MoS₂ offer uniform atomic-scale thickness, making them ideal for scaling. They also have a higher effective mass (
𝑚
∗
m 
∗
 ) compared to silicon, which can help suppress tunneling leakage as gate lengths shrink.

Direct Source-to-Drain Tunneling: As gate lengths decrease, electron tunneling from source to drain increases, resulting in higher leakage currents. Materials with high effective mass and bandgap, like MoS₂, help mitigate this issue, providing up to 100x reduction in leakage compared to silicon.

All-2D MOSFETs: Transistors made entirely from 2D materials, such as MoS₂ for the channel and graphene for contacts, promise significant improvements in performance and scalability. These devices exploit the unique properties of 2D materials for ultra-low leakage and precise control over the transistor characteristics.

Planar MOSFETs
Planar MOSFETs, the traditional architecture, have a simple structure where the gate sits above the channel. In this design, the contact width ((W_C)) and gate width ((W_G)) are nearly equal, resulting in a ratio of (W_C / W_G \approx 1). This leads to a low parasitic resistance, with (R_{EXT}) being much smaller than (R_{ch}) ((R_{EXT} / R_{ch} < 1)). As a result, planar MOSFETs suffer minimal performance degradation due to parasitic resistance.

FinFETs
FinFETs, a 3D transistor design, introduce vertical fins with the gate wrapping around them for improved control. However, the effective contact width decreases relative to the gate width, leading to (W_C / W_G \approx 1/3). Consequently, the parasitic resistance becomes comparable to the channel resistance ((R_{EXT} / R_{ch} \approx 1)), which begins to impact the performance of the device as it scales.

Gate-All-Around (GAA) FETs
Gate-All-Around (GAA) FETs, which use nanosheets or nanowires, offer even better electrostatic control by fully surrounding the channel with the gate. However, the contact width further decreases compared to the gate width, resulting in (W_C / W_G \approx 1/6). This causes a significant increase in parasitic resistance, with (R_{EXT}) being approximately three times the channel resistance ((R_{EXT} / R_{ch} \approx 3)). While GAA FETs improve transistor density, the higher parasitic resistance becomes a challenge for maintaining performance.

Complementary FETs (CFETs)
Complementary FETs (CFETs) take transistor stacking to the next level by vertically integrating NMOS and PMOS transistors. This approach maximizes space efficiency in advanced nodes but inherits the high parasitic resistance of GAA FETs. With (W_C / W_G) remaining small, the (R_{EXT} / R_{ch}) ratio is around 3, posing similar challenges to those faced by GAA FETs.

Explanation of Parasitic Resistance

image

The image highlights the breakdown of parasitic resistance ((R_{EXT})) and approaches for reducing it in transistors. Here is a detailed explanation:

Components of Parasitic Resistance ((R_{EXT})) The leftmost diagram illustrates the various contributors to (R_{EXT}) in a transistor:
(R_{CA-BEOL}): Resistance from the contact in the Back-End-Of-Line (BEOL).
(R_{CA}): Resistance at the contact area.
(R_{CA-TS}): Resistance at the contact to the transition structure.
(R_{TS}): Resistance in the transition structure.
(R_{MOL}): Middle-Of-Line resistance (includes lateral and vertical contributions).
(R_C): Contact resistance at the metal-semiconductor interface.
(R_{EPI}): Epitaxial layer resistance (contributes to current spreading).
(R_{FEOL}): Front-End-Of-Line resistance from the source/drain extensions.
Initial vs. Improved (R_{EXT}) Breakdown The two pie charts in the center show the contributions of different resistance components for NFETs and PFETs before and after improvements:
NFET:
Initial: Majority of (R_{EXT}) comes from (R_C) (63%) and (R_{CA-BEOL}) (31%).
Improved: Significant reduction in (R_C) (48%) and (R_{CA-BEOL}) (12%).
PFET:
Initial: (R_{FEOL}) (50%) and (R_C) (45%) dominate.
Improved: Major reduction in (R_{FEOL}) (78%) and (R_C) (16%).
Improving Ohmic/Tunneling Contacts The right section discusses methods to improve the metal-semiconductor interface:
Key Objectives:

Low Schottky Barrier Height (SBH) ((\phi_b)): Reduces the energy barrier for carrier injection, enabling better contact conductivity.
High Doping Concentration ((N_d)): Increases carrier density at the interface, reducing contact resistance.
Equation for Specific Contact Resistivity ((\rho_c)): [ \rho_c \propto \exp\left(\frac{2\phi_b}{\hbar} \sqrt{\frac{\epsilon_s m_x}{N_d}}\right) ] This equation shows how lowering (\phi_b) and increasing (N_d) can reduce (\rho_c).

Metal-Semiconductor Energy Diagram:

The energy diagram shows how a reduction in (\phi_b) (Schottky Barrier Height) facilitates easier carrier injection from the metal to the semiconductor.
image

The bar chart on the left shows how the composition of (C_{eff}) evolves from 22nm to 7nm technology nodes:

At 22nm, the dominant contributor to (C_{eff}) is (C_{fr}) (56%), while parasitic capacitances (C_{pc-ca}) (25%) and (C_{g}) (19%) contribute less.
At 14nm and 10nm, parasitic capacitances ((C_{pc-ca}) and (C_{fr})) start dominating, with (C_{fr}) decreasing to 38% and 25%, respectively, while (C_{pc-ca}) increases.
At 7nm, (C_{g}) becomes the most significant contributor (45%), with (C_{pc-ca}) and (C_{fr}) still present but reduced. This highlights the increasing impact of parasitic capacitance as node sizes shrink.
Plot Descriptions:

The first scatter plot shows a reduction in normalized delay for a ring oscillator when using SiBCN spacers instead of SiN spacers, indicating improved performance.
The second scatter plot demonstrates an 8% reduction in (C_{eff}) with SiBCN spacers, which corresponds to the delay improvement observed in the first plot.
The rightmost figure shows the evolution of spacer materials from SiOCN to SiCO. This material transition aims to significantly reduce the gate-contact capacitance across nodes. At 14nm and beyond, low-(k) spacers improve performance by decoupling parasitic effects and maintaining capacitance at manageable levels.
The bottom middle image shows a cross-sectional TEM view of a transistor with air spacers around the gate: i) Air, with its extremely low dielectric constant ((k \approx 1)), significantly reduces parasitic capacitance. The adjacent plot quantifies this improvement, showing a 15% reduction in (C_{eff}) when using air spacers compared to solid spacers.


Key Properties of 2D Layered Materials (Compared to Silicon):

Uniform Atomic Scale Thickness: A single layer of MoS₂ is approximately 0.65 nm thick, offering an ideal thickness for scaling compared to silicon.
Higher Effective Mass (( m^ )):* MoS₂ has an effective mass of about 0.55 times the mass of a free electron (( m_0 )), whereas silicon’s effective mass is around 0.22 ( m_0 ).
Bandgap: Additionally, 2D materials like MoS₂ possess favorable bandgaps for electronic devices, with a monolayer bandgap of ~1.85 eV, which reduces to ~1.5 eV for a bilayer.


Transistor Scaling:

To achieve smaller gate lengths, devices must address various physical and material constraints to ensure reliable operation.
Challenges for Scaling:

Direct Source-to-Drain Tunneling: As the gate length decreases, electrons can tunnel directly from the source to the drain, bypassing the gate control. To mitigate this, materials with a high effective mass (( m^* )) are needed.
Surface Roughness and Thickness Variations: Variability at atomic scales can cause performance issues. Uniform, atomically thin materials are essential for minimizing such variations.
Capacitance Ratios (( C_D ) and ( C_{ox} )): The capacitance of the depletion region (( C_D )) must remain low relative to the gate oxide capacitance (( C_{ox} )) to improve gate control. Materials with a low in-plane dielectric constant (( \epsilon )) are necessary for this.
Diagrams:

The left shows the transistor structure with key components like the source, drain, gate, oxide, and silicon substrate.
The right illustrates two scenarios:
a. Thermionic Emission: Electrons cross the energy barrier as intended.
b. Direct Tunneling: At extremely small gate lengths, electrons tunnel directly from source to drain, leading to leakage.
Key Takeaway:

New channel materials, such as 2D materials, are required to overcome these challenges while maintaining high performance and scalability.


Concept of Direct Source-to-Drain Tunneling: As the gate length ((L_G)) in MOSFETs decreases, direct tunneling of electrons from the source to the drain becomes significant, leading to increased leakage currents. This leakage is influenced by material properties, such as the effective mass ((m^*)) and the bandgap ((E_G)).

A higher (m^) in MoS₂ suppresses tunneling leakage compared to silicon. The graph shows the leakage current ((I_{SD, \text{leak}})) as a function of gate length ((L_G)) for various channel thicknesses ((T_{CH})). MoS₂ exhibits lower leakage at smaller gate lengths compared to silicon, achieving up to 100x reduction in leakage due to its higher (m^), larger (E_G), and lower dielectric constant ((\epsilon)).

The superior performance of MoS₂ in minimizing leakage currents results in significant energy savings, making it a promising material for future transistor scaling.

image

The MoS₂ transistor with a 1 nm gate length represents a breakthrough in miniaturization, featuring a thin MoS₂ channel for its excellent electronic properties. A single-walled carbon nanotube (SWCNT) serves as the ultra-small gate electrode, while Zirconium Dioxide (ZrO₂) acts as a high-k dielectric, reducing leakage and ensuring precise control. Built on a SiO₂ substrate with an n⁺ silicon back gate, the transistor uses the CNT gate to deplete a small region of the MoS₂ channel, enabling efficient modulation. This innovative design showcases the potential of 2D materials and nanoscale gates in advancing transistor technology.



The slide illustrates the structure and fabrication of an All-2D MOSFET (Metal-Oxide-Semiconductor Field-Effect Transistor), where all key components, including the channel, gate, and contacts, are made using two-dimensional materials. This device leverages the exceptional properties of 2D materials to improve performance and scalability. Below is a breakdown of the key components and the fabrication process:

Graphene Contacts (S, D, G): Graphene is used as the source, drain, and gate electrodes. Its high conductivity and 2D nature make it ideal for ensuring low-resistance electrical contacts. MoS₂ Channel:

Molybdenum Disulfide (MoS₂) serves as the semiconductor channel. MoS₂ is widely used in 2D MOSFETs due to its excellent on/off current ratio and atomic-scale thickness. h-BN Dielectric:

Hexagonal Boron Nitride (h-BN) acts as the insulating dielectric layer. It is a 2D material with excellent insulating properties and high thermal stability, making it suitable for separating the graphene gate from the MoS₂ channel. Si/SiO₂ Substrate:

The device is fabricated on a silicon dioxide (SiO₂) layer on top of a silicon substrate, which provides mechanical support and a global back gate. Fabrication Process:

A layer of graphene is deposited on the SiO₂ substrate, which will later serve as the gate electrode.
Graphene is patterned to define the source and drain regions, leaving gaps for the channel.
A monolayer of MoS₂ is transferred onto the patterned graphene, forming the channel region.
An h-BN layer is added on top of the MoS₂ as the gate dielectric.
A top layer of graphene is deposited and aligned as the gate electrode.
The completed device is contacted using metallic electrodes (Ni/Au) for testing.


The All-2D MOSFET exhibits excellent electrical performance:

Transfer Characteristics (I(_D) vs V(_G)): Achieves a high on/off current ratio (>10⁵), demonstrating strong gate control for effective switching.
Output Characteristics (I(D) vs V({DS})): Smooth current modulation with increasing V(G) and V({DS}), indicating good output performance.
Mobility: Field-effect mobility remains constant with increasing gate electric field, showing minimal scattering and high-quality interfaces in the 2D materials stack.
These results highlight the potential of 2D materials like MoS₂, graphene, and h-BN for scalable, high-performance transistor applications.



The diagram on the top left shows a non-planar transistor with key components:

Gate: Controls the flow of current through the channel.
Channel: Region where current flows between the source (S) and drain (D).
Body: Underlying region connected to the substrate.
STI (Shallow Trench Isolation): Insulates neighboring devices.
The biggest challenge is to form a single-crystalline semiconductors on a non-planar surface is difficult using conventional semiconductor fabrication techniques.


Single-Layer CMOS (a): This is the traditional CMOS design where NMOS and PMOS transistors are fabricated on a single silicon layer. Each transistor operates in the same planar layer, with devices connected laterally.

Monolithic 3D CMOS (b): In this design, NMOS and PMOS transistors are stacked in two separate layers, enabling higher density. The P-Channel (PMOS) is placed on top of the N-Channel (NMOS), separated by an oxide layer. This approach reduces the footprint and allows better performance due to shorter interconnects.

Single-Layer CMOS Logic (c): Shows logic gates (inverter, 2-input NAND, and 2-input NOR) built using traditional single-layer CMOS. Transistors are laid out horizontally, with interconnections taking more space.

Monolithic 3D CMOS Logic (d): Logic gates are constructed with two transistor layers (Layer 1 and Layer 2), reducing the area required for interconnections. Vertical integration improves performance and reduces delay by shortening signal paths.



Dual Damascene Cu, used for the 7nm technology node with a 36nm pitch, combines vias (vertical connections) and lines (horizontal connections) in a single patterning step. It relies on copper (Cu) for interconnections; however, as dimensions shrink, challenges such as gap filling and maintaining reliability become increasingly significant.

Single Damascene Cu, used for the 5nm technology node with a 28nm pitch, involves splitting the creation of vias (vertical connections) and lines (horizontal connections) into separate steps. This approach addresses the challenges of smaller dimensions, with a primary focus on reducing resistance (R) in both lines and vias to maintain optimal performance.

Barrier and via metal optimization, introduced at the 3nm technology node with a 20-24nm pitch, focuses on reducing the thickness of barrier layers (insulating layers) to minimize resistance while maintaining robust and reliable via connections. This optimization is essential to meet the performance and scaling demands of advanced nodes.

At sub-18nm pitch, subtractive RIE and alternative metals like ruthenium (Ru) are introduced to address the reliability and scaling challenges faced by traditional copper interconnects. Subtractive Reactive Ion Etching (RIE) enables more precise patterning of interconnects, while the use of Ru provides improved performance and durability at such advanced dimensions.

For future nodes with pitches below 15nm, post-Damascene interconnects featuring tall, barrier-less designs are envisioned. This approach enhances electromigration (EM) reliability, ensuring durable and robust connections despite the continued shrinking of dimensions, thereby addressing key challenges in advanced interconnect scaling.



The image shows how a selective barrier, typically tantalum nitride (TaN), can improve copper interconnects in semiconductor manufacturing. This barrier reduces resistance, enhances reliability by preventing copper ion migration, and aids in controlling copper thickness. The process involves cleaning the copper surface, depositing TaN using atomic layer deposition (ALD), and removing sacrificial layers. This technique is crucial for advancing semiconductor technology and ensuring reliable, high-performance devices.



Back-Side Power Delivery Network (BS-PDN)

In advanced semiconductor manufacturing, efficient power delivery is critical to the performance and reliability of integrated circuits. Traditional Front-Side Power Delivery Networks (FS-PDNs) often suffer from high IR-drop, which can limit device performance and reliability. To address this challenge, Back-Side Power Delivery Networks (BS-PDNs) have emerged as a promising solution.

BS-PDNs involve routing power supply rails on the backside of the chip, enabling shorter and wider power lines. This configuration significantly reduces IR-drop, leading to improved power delivery efficiency. As a result, BS-PDNs offer several advantages:

Reduced IR-drop: Lower voltage drops across the power network, leading to improved performance and reliability.
Decreased standard cell area: More efficient power delivery allows for smaller standard cell sizes.
Improved performance: Lower IR-drop leads to faster switching speeds and reduced power dissipation.

Installing and setting up ORFS:
```bash
git clone --recursive https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts
cd OpenROAD-flow-scripts
sudo ./setup.sh
```
![Screenshot from 2024-11-26 11-48-05](https://github.com/user-attachments/assets/b5879cbb-60f0-4bba-a809-47057d4e7d11)

```bash
./build_openroad.sh --local
```

![Screenshot from 2024-11-26 11-47-02](https://github.com/user-attachments/assets/f698f503-ebaa-4f8f-ba63-0e93184dbaed)

![Screenshot from 2024-11-26 11-46-47](https://github.com/user-attachments/assets/dcd8ba59-ebc7-46ea-a094-b53ecf4a7e92)


Verify Installation

```bash
source ./env.sh
yosys -help
openroad -help
cd flow
make
```

![Screenshot from 2024-11-26 11-51-12](https://github.com/user-attachments/assets/1f094116-40a4-444f-82eb-d00da89bf9ca)

![Screenshot from 2024-11-26 11-51-30](https://github.com/user-attachments/assets/f74fd72a-07f6-4afc-b3f8-26f7729f71f1)

![Screenshot from 2024-11-26 11-53-31](https://github.com/user-attachments/assets/c95070e1-3711-4b3e-b55a-57c7d723cbb1)


```bash
make gui_final
```

![Screenshot from 2024-11-26 11-54-59](https://github.com/user-attachments/assets/f359ef04-59f9-4511-b7a7-a7f6de9df6ea)


ORFS Directory Structure and File formats

![Screenshot from 2024-11-26 11-56-28](https://github.com/user-attachments/assets/d9efd62d-c989-4b1b-9318-7b28891f097f)


```bash
├── OpenROAD-flow-scripts             
│   ├── docker           -> It has Docker based installation, run scripts and all saved here
│   ├── docs             -> Documentation for OpenROAD or its flow scripts.  
│   ├── flow             -> Files related to run RTL to GDS flow  
|   ├── jenkins          -> It contains the regression test designed for each build update
│   ├── tools            -> It contains all the required tools to run RTL to GDS flow
│   ├── etc              -> Has the dependency installer script and other things
│   ├── setup_env.sh     -> Its the source file to source all our OpenROAD rules to run the RTL to GDS flow
```
Now, go to flow directory
![Screenshot from 2024-11-26 11-56-56](https://github.com/user-attachments/assets/2c7c3b61-e458-4e77-ad2d-682c32e2c361)

```bash
├── flow           
│   ├── design           -> It has built-in examples from RTL to GDS flow across different technology nodes
│   ├── makefile         -> The automated flow runs through makefile setup
│   ├── platform         -> It has different technology note libraries, lef files, GDS etc 
|   ├── tutorials        
│   ├── util            
│   ├── scripts
```
```bash
├── flow           
│   ├── design           -> It has built-in examples from RTL to GDS flow across different technology nodes
│   ├── makefile         -> The automated flow runs through makefile setup
│   ├── platform         -> It has different technology note libraries, lef files, GDS etc 
|   ├── tutorials        
│   ├── util            
│   ├── scripts
```
Automated RTL2GDS Flow for VSDBabySoC:

Initial Steps:

We need to create a directory vsdbabysoc inside OpenROAD-flow-scripts/flow/designs/sky130hd
Now create a directory vsdbabysoc inside OpenROAD-flow-scripts/flow/designs/src and include all the verilog files here.
Now copy the folders gds, include, lef and lib from the VSDBabySoC folder in your system into this directory.
The gds folder would contain the files avsddac.gds and avsdpll.gds
The include folder would contain the files sandpiper.vh, sandpiper_gen.vh, sp_default.vh and sp_verilog.vh
The gds folder would contain the files avsddac.lef and avsdpll.lef
The lib folder would contain the files avsddac.lib and avsdpll.lib
Now copy the constraints file(vsdbabysoc_synthesis.sdc) from the VSDBabySoC folder in your system into this directory.
Now copy the files(macro.cfg and pin_order.cfg) from the VSDBabySoC folder in your system into this directory.
Now, create a config.mk file whose contents are shown below:

```bash
export DESIGN_NICKNAME = vsdbabysoc
export DESIGN_NAME = vsdbabysoc
export PLATFORM    = sky130hd

# export VERILOG_FILES_BLACKBOX = $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/IPs/*.v
# export VERILOG_FILES = $(sort $(wildcard $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/*.v))
# Explicitly list the Verilog files for synthesis
export VERILOG_FILES = $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/vsdbabysoc.v \
                       $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/rvmyth.v \
                       $(DESIGN_HOME)/src/$(DESIGN_NICKNAME)/clk_gate.v

export SDC_FILE      = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/vsdbabysoc_synthesis.sdc

export vsdbabysoc_DIR = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)

export VERILOG_INCLUDE_DIRS = $(wildcard $(vsdbabysoc_DIR)/include/)
# export SDC_FILE      = $(wildcard $(vsdbabysoc_DIR)/sdc/*.sdc)
export ADDITIONAL_GDS  = $(wildcard $(vsdbabysoc_DIR)/gds/*.gds.gz)
export ADDITIONAL_LEFS  = $(wildcard $(vsdbabysoc_DIR)/lef/*.lef)
export ADDITIONAL_LIBS = $(wildcard $(vsdbabysoc_DIR)/lib/*.lib)
# export PDN_TCL = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/pdn.tcl

# Clock Configuration (vsdbabysoc specific)
# export CLOCK_PERIOD = 20.0
export CLOCK_PORT = CLK
export CLOCK_NET = $(CLOCK_PORT)

# Floorplanning Configuration (vsdbabysoc specific)
export FP_PIN_ORDER_CFG = $(wildcard $(DESIGN_DIR)/pin_order.cfg)
# export FP_SIZING = absolute

export DIE_AREA   = 0 0 1600 1600
export CORE_AREA  = 20 20 1590 1590

# Placement Configuration (vsdbabysoc specific)
export MACRO_PLACEMENT_CFG = $(wildcard $(DESIGN_DIR)/macro.cfg)
export PLACE_PINS_ARGS = -exclude left:0-600 -exclude left:1000-1600: -exclude right:* -exclude top:* -exclude bottom:*
# export MACRO_PLACEMENT = $(DESIGN_HOME)/$(PLATFORM)/$(DESIGN_NICKNAME)/macro_placement.cfg

export TNS_END_PERCENT = 100
export REMOVE_ABC_BUFFERS = 1

# Magic Tool Configuration
export MAGIC_ZEROIZE_ORIGIN = 0
export MAGIC_EXT_USE_GDS = 1

# CTS tuning
export CTS_BUF_DISTANCE = 600
export SKIP_GATE_CLONING = 1

# export CORE_UTILIZATION=0.1  # Reduce this value to allow more whitespace for routing.
```
Now go to terminal and run the following commands:

```bash
cd OpenROAD-flow-scripts
source env.sh
cd flow
```
Commands for synthesis:

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk synth
```
![Screenshot from 2024-11-26 12-46-44](https://github.com/user-attachments/assets/a5bf9280-6624-4ad8-9320-0d87f34d7484)

![Screenshot from 2024-11-26 12-47-12](https://github.com/user-attachments/assets/ab047957-50a3-4845-918b-77606d9032c0)


Synthesis netlist:
![Screenshot from 2024-11-26 12-48-08](https://github.com/user-attachments/assets/e73ba263-b13c-4400-a310-6ae09b3e1a1c)

Syntheiss log:

![Screenshot from 2024-11-26 12-48-58](https://github.com/user-attachments/assets/78f884ec-386f-4793-beab-53a2e53c68d7)

Synthesis Check:
![Screenshot from 2024-11-26 12-49-53](https://github.com/user-attachments/assets/70d54f8d-3530-4a53-99c9-a015315bd154)

Synthesis Stats:
![Screenshot from 2024-11-26 12-50-56](https://github.com/user-attachments/assets/18f98e9f-7139-48da-abff-aedbfa8d96cb)
![Screenshot from 2024-11-26 12-51-24](https://github.com/user-attachments/assets/88b4578f-1d42-43fb-bcd6-dedfb2bcfa20)

Commands for floorplan:

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk floorplan
```
![Screenshot from 2024-11-26 12-57-06](https://github.com/user-attachments/assets/dbd40112-49ba-419a-a835-b8959e94826c)
![Screenshot from 2024-11-26 13-03-09](https://github.com/user-attachments/assets/79ed1a82-a010-4438-94a7-94c00ca411a5)

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_floorplan
```
![Screenshot from 2024-11-26 13-05-10](https://github.com/user-attachments/assets/b3a0d1a1-7ec6-47af-a683-55f68aeb0858)

![Screenshot from 2024-11-26 13-04-51](https://github.com/user-attachments/assets/730cb79d-d582-4708-8ca4-f1c5b43381e5)

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk place
```
![Screenshot from 2024-11-26 13-06-03](https://github.com/user-attachments/assets/5391928a-5cac-4a06-8d33-d757947edce7)
![Screenshot from 2024-11-26 13-07-38](https://github.com/user-attachments/assets/609750d6-2801-49ea-b63b-986ac084a020)

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_place
```
![Screenshot from 2024-11-26 13-08-00](https://github.com/user-attachments/assets/76e7c04d-cd5e-4392-8010-acc78dba3e6f)
![Screenshot from 2024-11-26 13-08-18](https://github.com/user-attachments/assets/27bb514f-4e4f-4989-aa2c-8f3f43235618)

Heatmap:

![Screenshot from 2024-11-26 13-10-51](https://github.com/user-attachments/assets/19c9f662-8943-4b99-99f9-f19ac3e4e7a4)

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk cts
```
![Screenshot from 2024-11-26 13-13-20](https://github.com/user-attachments/assets/99b717bf-fa40-4181-9455-b6bb08c0c33c)
![Screenshot from 2024-11-26 13-14-05](https://github.com/user-attachments/assets/fcd4e39e-afd8-4815-954f-d642492b4c8e)

```bash
make DESIGN_CONFIG=./designs/sky130hd/vsdbabysoc/config.mk gui_cts
```

![Screenshot from 2024-11-26 13-14-30](https://github.com/user-attachments/assets/e6603f40-f41a-4f9f-8029-6af1b0db627d)

![Screenshot from 2024-11-26 13-16-46](https://github.com/user-attachments/assets/38b50727-c7af-4db1-915a-5887102c83e0)

CTS final report:
![Screenshot from 2024-11-26 13-17-29](https://github.com/user-attachments/assets/cdcab1f5-455d-4f19-91c6-c1099371b4c5)


```bash

==========================================================================
cts final report_tns
--------------------------------------------------------------------------
tns 0.00

==========================================================================
cts final report_wns
--------------------------------------------------------------------------
wns 0.00

==========================================================================
cts final report_worst_slack
--------------------------------------------------------------------------
worst slack 5.51

==========================================================================
cts final report_clock_skew
--------------------------------------------------------------------------
Clock clk
   1.01 source latency core.CPU_dmem_rd_data_a5[5]$_DFF_P_/CLK ^
  -0.87 target latency core.CPU_Xreg_value_a4[17][5]$_SDFFE_PP0P_/CLK ^
   0.56 clock uncertainty
   0.00 CRPR
--------------
   0.70 setup skew


==========================================================================
cts final report_checks -path_delay min
--------------------------------------------------------------------------
Startpoint: core.CPU_Xreg_value_a4[28][16]$_SDFFE_PP0P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a3[16]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.33    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.06    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.21    0.22    0.26    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.22    0.00    0.29 ^ clkbuf_3_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     2    0.04    0.06    0.20    0.49 ^ clkbuf_3_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_0_0_CLK (net)
                  0.06    0.00    0.49 ^ clkbuf_4_0__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.15    0.16    0.23    0.72 ^ clkbuf_4_0__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_0__leaf_CLK (net)
                  0.16    0.00    0.72 ^ clkbuf_leaf_163_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.18    0.90 ^ clkbuf_leaf_163_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_163_CLK (net)
                  0.06    0.00    0.90 ^ core.CPU_Xreg_value_a4[28][16]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
     2    0.01    0.10    0.35    1.25 ^ core.CPU_Xreg_value_a4[28][16]$_SDFFE_PP0P_/Q (sky130_fd_sc_hd__dfxtp_1)
                                         core.CPU_Xreg_value_a4[28][16] (net)
                  0.10    0.00    1.25 ^ _14460_/A1 (sky130_fd_sc_hd__a221oi_2)
     1    0.01    0.04    0.08    1.34 v _14460_/Y (sky130_fd_sc_hd__a221oi_2)
                                         _01868_ (net)
                  0.04    0.00    1.34 v _14465_/B (sky130_fd_sc_hd__nand4_2)
     1    0.01    0.09    0.11    1.45 ^ _14465_/Y (sky130_fd_sc_hd__nand4_2)
                                         _01873_ (net)
                  0.09    0.00    1.45 ^ _14466_/A3 (sky130_fd_sc_hd__o32ai_4)
     1    0.05    0.13    0.15    1.60 v _14466_/Y (sky130_fd_sc_hd__o32ai_4)
                                         _01874_ (net)
                  0.13    0.01    1.61 v _14468_/A2 (sky130_fd_sc_hd__o21ai_0)
     1    0.00    0.07    0.17    1.78 ^ _14468_/Y (sky130_fd_sc_hd__o21ai_0)
                                         core.CPU_src2_value_a2[16] (net)
                  0.07    0.00    1.78 ^ core.CPU_src2_value_a3[16]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  1.78   data arrival time

                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.33    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.06    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.21    0.22    0.26    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.22    0.00    0.29 ^ clkbuf_3_4_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     2    0.05    0.07    0.21    0.50 ^ clkbuf_3_4_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_4_0_CLK (net)
                  0.07    0.00    0.50 ^ clkbuf_4_9__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.15    0.16    0.23    0.73 ^ clkbuf_4_9__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_9__leaf_CLK (net)
                  0.16    0.00    0.73 ^ clkbuf_leaf_20_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     7    0.04    0.06    0.18    0.91 ^ clkbuf_leaf_20_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_20_CLK (net)
                  0.06    0.00    0.91 ^ core.CPU_src2_value_a3[16]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                          0.90    1.81   clock uncertainty
                          0.00    1.81   clock reconvergence pessimism
                         -0.03    1.78   library hold time
                                  1.78   data required time
-----------------------------------------------------------------------------
                                  1.78   data required time
                                 -1.78   data arrival time
-----------------------------------------------------------------------------
                                  0.00   slack (MET)



==========================================================================
cts final report_checks -path_delay max
--------------------------------------------------------------------------
Startpoint: core.CPU_imm_a3[30]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.33    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.06    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.21    0.22    0.26    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.22    0.00    0.29 ^ clkbuf_3_4_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     2    0.05    0.07    0.21    0.50 ^ clkbuf_3_4_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_4_0_CLK (net)
                  0.07    0.00    0.50 ^ clkbuf_4_8__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.17    0.18    0.24    0.74 ^ clkbuf_4_8__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_8__leaf_CLK (net)
                  0.18    0.00    0.74 ^ clkbuf_leaf_15_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.04    0.06    0.19    0.93 ^ clkbuf_leaf_15_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_15_CLK (net)
                  0.06    0.00    0.93 ^ core.CPU_imm_a3[30]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_4)
    51    0.25    0.71    0.80    1.73 ^ core.CPU_imm_a3[30]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_4)
                                         core.CPU_imm_a3[10] (net)
                  0.71    0.04    1.77 ^ _15428_/B (sky130_fd_sc_hd__ha_2)
     8    0.02    0.10    0.48    2.25 v _15428_/SUM (sky130_fd_sc_hd__ha_2)
                                         _07555_ (net)
                  0.10    0.00    2.25 v _10014_/A (sky130_fd_sc_hd__or4_1)
     2    0.01    0.13    0.59    2.84 v _10014_/X (sky130_fd_sc_hd__or4_1)
                                         _04385_ (net)
                  0.13    0.00    2.84 v _10056_/C (sky130_fd_sc_hd__nor3_1)
     2    0.01    0.28    0.25    3.10 ^ _10056_/Y (sky130_fd_sc_hd__nor3_1)
                                         _04426_ (net)
                  0.28    0.00    3.10 ^ _10059_/C (sky130_fd_sc_hd__and3_2)
     4    0.03    0.15    0.32    3.41 ^ _10059_/X (sky130_fd_sc_hd__and3_2)
                                         _04429_ (net)
                  0.15    0.00    3.42 ^ _10251_/A2 (sky130_fd_sc_hd__o2111ai_4)
     2    0.01    0.18    0.14    3.56 v _10251_/Y (sky130_fd_sc_hd__o2111ai_4)
                                         _04612_ (net)
                  0.18    0.00    3.56 v _10252_/B (sky130_fd_sc_hd__and2_1)
     3    0.02    0.12    0.29    3.85 v _10252_/X (sky130_fd_sc_hd__and2_1)
                                         _04613_ (net)
                  0.12    0.00    3.85 v _10342_/A1 (sky130_fd_sc_hd__o21ai_1)
     1    0.01    0.21    0.23    4.08 ^ _10342_/Y (sky130_fd_sc_hd__o21ai_1)
                                         _04699_ (net)
                  0.21    0.00    4.08 ^ _10343_/B (sky130_fd_sc_hd__xnor2_2)
     1    0.02    0.11    0.14    4.22 v _10343_/Y (sky130_fd_sc_hd__xnor2_2)
                                         _04700_ (net)
                  0.11    0.00    4.22 v _10344_/B1 (sky130_fd_sc_hd__a22oi_4)
    33    0.19    1.25    0.95    5.17 ^ _10344_/Y (sky130_fd_sc_hd__a22oi_4)
                                         _04701_ (net)
                  1.25    0.04    5.21 ^ _11183_/B2 (sky130_fd_sc_hd__o221ai_1)
     1    0.00    0.19    0.19    5.40 v _11183_/Y (sky130_fd_sc_hd__o221ai_1)
                                         _00753_ (net)
                  0.19    0.00    5.40 v hold2072/A (sky130_fd_sc_hd__dlygate4sd3_1)
     1    0.00    0.06    0.62    6.02 v hold2072/X (sky130_fd_sc_hd__dlygate4sd3_1)
                                         net2171 (net)
                  0.06    0.00    6.02 v core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  6.02   data arrival time

                         11.30   11.30   clock clk (rise edge)
                          0.00   11.30   clock source latency
     1    0.33    0.00    0.00   11.30 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.06    0.03   11.33 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.21    0.22    0.26   11.59 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.22    0.00   11.59 ^ clkbuf_3_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     2    0.04    0.06    0.20   11.79 ^ clkbuf_3_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_0_0_CLK (net)
                  0.06    0.00   11.79 ^ clkbuf_4_1__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.15    0.16    0.23   12.02 ^ clkbuf_4_1__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_1__leaf_CLK (net)
                  0.16    0.00   12.02 ^ clkbuf_leaf_144_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.18   12.21 ^ clkbuf_leaf_144_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_144_CLK (net)
                  0.06    0.00   12.21 ^ core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                         -0.56   11.64   clock uncertainty
                          0.00   11.64   clock reconvergence pessimism
                         -0.11   11.53   library setup time
                                 11.53   data required time
-----------------------------------------------------------------------------
                                 11.53   data required time
                                 -6.02   data arrival time
-----------------------------------------------------------------------------
                                  5.51   slack (MET)



==========================================================================
cts final report_checks -unconstrained
--------------------------------------------------------------------------
Startpoint: core.CPU_imm_a3[30]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

Fanout     Cap    Slew   Delay    Time   Description
-----------------------------------------------------------------------------
                          0.00    0.00   clock clk (rise edge)
                          0.00    0.00   clock source latency
     1    0.33    0.00    0.00    0.00 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.06    0.03    0.03 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.21    0.22    0.26    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.22    0.00    0.29 ^ clkbuf_3_4_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     2    0.05    0.07    0.21    0.50 ^ clkbuf_3_4_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_4_0_CLK (net)
                  0.07    0.00    0.50 ^ clkbuf_4_8__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.17    0.18    0.24    0.74 ^ clkbuf_4_8__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_8__leaf_CLK (net)
                  0.18    0.00    0.74 ^ clkbuf_leaf_15_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.04    0.06    0.19    0.93 ^ clkbuf_leaf_15_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_15_CLK (net)
                  0.06    0.00    0.93 ^ core.CPU_imm_a3[30]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_4)
    51    0.25    0.71    0.80    1.73 ^ core.CPU_imm_a3[30]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_4)
                                         core.CPU_imm_a3[10] (net)
                  0.71    0.04    1.77 ^ _15428_/B (sky130_fd_sc_hd__ha_2)
     8    0.02    0.10    0.48    2.25 v _15428_/SUM (sky130_fd_sc_hd__ha_2)
                                         _07555_ (net)
                  0.10    0.00    2.25 v _10014_/A (sky130_fd_sc_hd__or4_1)
     2    0.01    0.13    0.59    2.84 v _10014_/X (sky130_fd_sc_hd__or4_1)
                                         _04385_ (net)
                  0.13    0.00    2.84 v _10056_/C (sky130_fd_sc_hd__nor3_1)
     2    0.01    0.28    0.25    3.10 ^ _10056_/Y (sky130_fd_sc_hd__nor3_1)
                                         _04426_ (net)
                  0.28    0.00    3.10 ^ _10059_/C (sky130_fd_sc_hd__and3_2)
     4    0.03    0.15    0.32    3.41 ^ _10059_/X (sky130_fd_sc_hd__and3_2)
                                         _04429_ (net)
                  0.15    0.00    3.42 ^ _10251_/A2 (sky130_fd_sc_hd__o2111ai_4)
     2    0.01    0.18    0.14    3.56 v _10251_/Y (sky130_fd_sc_hd__o2111ai_4)
                                         _04612_ (net)
                  0.18    0.00    3.56 v _10252_/B (sky130_fd_sc_hd__and2_1)
     3    0.02    0.12    0.29    3.85 v _10252_/X (sky130_fd_sc_hd__and2_1)
                                         _04613_ (net)
                  0.12    0.00    3.85 v _10342_/A1 (sky130_fd_sc_hd__o21ai_1)
     1    0.01    0.21    0.23    4.08 ^ _10342_/Y (sky130_fd_sc_hd__o21ai_1)
                                         _04699_ (net)
                  0.21    0.00    4.08 ^ _10343_/B (sky130_fd_sc_hd__xnor2_2)
     1    0.02    0.11    0.14    4.22 v _10343_/Y (sky130_fd_sc_hd__xnor2_2)
                                         _04700_ (net)
                  0.11    0.00    4.22 v _10344_/B1 (sky130_fd_sc_hd__a22oi_4)
    33    0.19    1.25    0.95    5.17 ^ _10344_/Y (sky130_fd_sc_hd__a22oi_4)
                                         _04701_ (net)
                  1.25    0.04    5.21 ^ _11183_/B2 (sky130_fd_sc_hd__o221ai_1)
     1    0.00    0.19    0.19    5.40 v _11183_/Y (sky130_fd_sc_hd__o221ai_1)
                                         _00753_ (net)
                  0.19    0.00    5.40 v hold2072/A (sky130_fd_sc_hd__dlygate4sd3_1)
     1    0.00    0.06    0.62    6.02 v hold2072/X (sky130_fd_sc_hd__dlygate4sd3_1)
                                         net2171 (net)
                  0.06    0.00    6.02 v core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
                                  6.02   data arrival time

                         11.30   11.30   clock clk (rise edge)
                          0.00   11.30   clock source latency
     1    0.33    0.00    0.00   11.30 ^ pll/CLK (avsdpll)
                                         CLK (net)
                  0.06    0.03   11.33 ^ clkbuf_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     8    0.21    0.22    0.26   11.59 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_CLK (net)
                  0.22    0.00   11.59 ^ clkbuf_3_0_0_CLK/A (sky130_fd_sc_hd__clkbuf_16)
     2    0.04    0.06    0.20   11.79 ^ clkbuf_3_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_3_0_0_CLK (net)
                  0.06    0.00   11.79 ^ clkbuf_4_1__f_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    11    0.15    0.16    0.23   12.02 ^ clkbuf_4_1__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_4_1__leaf_CLK (net)
                  0.16    0.00   12.02 ^ clkbuf_leaf_144_CLK/A (sky130_fd_sc_hd__clkbuf_16)
    12    0.04    0.06    0.18   12.21 ^ clkbuf_leaf_144_CLK/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_leaf_144_CLK (net)
                  0.06    0.00   12.21 ^ core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
                         -0.56   11.64   clock uncertainty
                          0.00   11.64   clock reconvergence pessimism
                         -0.11   11.53   library setup time
                                 11.53   data required time
-----------------------------------------------------------------------------
                                 11.53   data required time
                                 -6.02   data arrival time
-----------------------------------------------------------------------------
                                  5.51   slack (MET)



==========================================================================
cts final report_check_types -max_slew -max_cap -max_fanout -violators
--------------------------------------------------------------------------

==========================================================================
cts final max_slew_check_slack
--------------------------------------------------------------------------
0.033627547323703766

==========================================================================
cts final max_slew_check_limit
--------------------------------------------------------------------------
1.5

==========================================================================
cts final max_slew_check_slack_limit
--------------------------------------------------------------------------
0.0224

==========================================================================
cts final max_fanout_check_slack
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_fanout_check_limit
--------------------------------------------------------------------------
1.0000000150474662e+30

==========================================================================
cts final max_capacitance_check_slack
--------------------------------------------------------------------------
0.007003525737673044

==========================================================================
cts final max_capacitance_check_limit
--------------------------------------------------------------------------
0.144663006067276

==========================================================================
cts final max_capacitance_check_slack_limit
--------------------------------------------------------------------------
0.0484

==========================================================================
cts final max_slew_violation_count
--------------------------------------------------------------------------
max slew violation count 0

==========================================================================
cts final max_fanout_violation_count
--------------------------------------------------------------------------
max fanout violation count 0

==========================================================================
cts final max_cap_violation_count
--------------------------------------------------------------------------
max cap violation count 0

==========================================================================
cts final setup_violation_count
--------------------------------------------------------------------------
setup violation count 0

==========================================================================
cts final hold_violation_count
--------------------------------------------------------------------------
hold violation count 0

==========================================================================
cts final report_checks -path_delay max reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_imm_a3[30]$_DFF_P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.29    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.21    0.50 ^ clkbuf_3_4_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.24    0.74 ^ clkbuf_4_8__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.19    0.93 ^ clkbuf_leaf_15_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.93 ^ core.CPU_imm_a3[30]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_4)
   0.80    1.73 ^ core.CPU_imm_a3[30]$_DFF_P_/Q (sky130_fd_sc_hd__dfxtp_4)
   0.52    2.25 v _15428_/SUM (sky130_fd_sc_hd__ha_2)
   0.59    2.84 v _10014_/X (sky130_fd_sc_hd__or4_1)
   0.25    3.10 ^ _10056_/Y (sky130_fd_sc_hd__nor3_1)
   0.32    3.41 ^ _10059_/X (sky130_fd_sc_hd__and3_2)
   0.15    3.56 v _10251_/Y (sky130_fd_sc_hd__o2111ai_4)
   0.29    3.85 v _10252_/X (sky130_fd_sc_hd__and2_1)
   0.23    4.08 ^ _10342_/Y (sky130_fd_sc_hd__o21ai_1)
   0.14    4.22 v _10343_/Y (sky130_fd_sc_hd__xnor2_2)
   0.95    5.17 ^ _10344_/Y (sky130_fd_sc_hd__a22oi_4)
   0.23    5.40 v _11183_/Y (sky130_fd_sc_hd__o221ai_1)
   0.62    6.02 v hold2072/X (sky130_fd_sc_hd__dlygate4sd3_1)
   0.00    6.02 v core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_/D (sky130_fd_sc_hd__dfxtp_1)
           6.02   data arrival time

  11.30   11.30   clock clk (rise edge)
   0.00   11.30   clock source latency
   0.00   11.30 ^ pll/CLK (avsdpll)
   0.29   11.59 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20   11.79 ^ clkbuf_3_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.23   12.02 ^ clkbuf_4_1__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.18   12.21 ^ clkbuf_leaf_144_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00   12.21 ^ core.CPU_Xreg_value_a4[16][25]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
  -0.56   11.64   clock uncertainty
   0.00   11.64   clock reconvergence pessimism
  -0.11   11.53   library setup time
          11.53   data required time
---------------------------------------------------------
          11.53   data required time
          -6.02   data arrival time
---------------------------------------------------------
           5.51   slack (MET)



==========================================================================
cts final report_checks -path_delay min reg to reg
--------------------------------------------------------------------------
Startpoint: core.CPU_Xreg_value_a4[28][16]$_SDFFE_PP0P_
            (rising edge-triggered flip-flop clocked by clk)
Endpoint: core.CPU_src2_value_a3[16]$_DFF_P_
          (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: min

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.29    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.20    0.49 ^ clkbuf_3_0_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.23    0.72 ^ clkbuf_4_0__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.18    0.90 ^ clkbuf_leaf_163_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.90 ^ core.CPU_Xreg_value_a4[28][16]$_SDFFE_PP0P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.35    1.25 ^ core.CPU_Xreg_value_a4[28][16]$_SDFFE_PP0P_/Q (sky130_fd_sc_hd__dfxtp_1)
   0.08    1.34 v _14460_/Y (sky130_fd_sc_hd__a221oi_2)
   0.11    1.45 ^ _14465_/Y (sky130_fd_sc_hd__nand4_2)
   0.15    1.60 v _14466_/Y (sky130_fd_sc_hd__o32ai_4)
   0.18    1.78 ^ _14468_/Y (sky130_fd_sc_hd__o21ai_0)
   0.00    1.78 ^ core.CPU_src2_value_a3[16]$_DFF_P_/D (sky130_fd_sc_hd__dfxtp_1)
           1.78   data arrival time

   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock source latency
   0.00    0.00 ^ pll/CLK (avsdpll)
   0.29    0.29 ^ clkbuf_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.21    0.50 ^ clkbuf_3_4_0_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.23    0.73 ^ clkbuf_4_9__f_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.18    0.91 ^ clkbuf_leaf_20_CLK/X (sky130_fd_sc_hd__clkbuf_16)
   0.00    0.91 ^ core.CPU_src2_value_a3[16]$_DFF_P_/CLK (sky130_fd_sc_hd__dfxtp_1)
   0.90    1.81   clock uncertainty
   0.00    1.81   clock reconvergence pessimism
  -0.03    1.78   library hold time
           1.78   data required time
---------------------------------------------------------
           1.78   data required time
          -1.78   data arrival time
---------------------------------------------------------
           0.00   slack (MET)



==========================================================================
cts final critical path target clock latency max path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path target clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path source clock latency min path
--------------------------------------------------------------------------
0

==========================================================================
cts final critical path delay
--------------------------------------------------------------------------
6.0214

==========================================================================
cts final critical path slack
--------------------------------------------------------------------------
5.5091

==========================================================================
cts final slack div critical path delay
--------------------------------------------------------------------------
91.492012

==========================================================================
cts final report_power
--------------------------------------------------------------------------
Group                  Internal  Switching    Leakage      Total
                          Power      Power      Power      Power (Watts)
----------------------------------------------------------------
Sequential             6.74e-03   7.65e-04   1.45e-08   7.50e-03  37.7%
Combinational          1.84e-03   3.95e-03   2.96e-08   5.79e-03  29.1%
Clock                  3.86e-03   2.77e-03   3.22e-09   6.63e-03  33.3%
Macro                  0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
Pad                    0.00e+00   0.00e+00   0.00e+00   0.00e+00   0.0%
----------------------------------------------------------------
Total                  1.24e-02   7.48e-03   4.74e-08   1.99e-02 100.0%
                          62.4%      37.6%       0.0%
```
</details>
