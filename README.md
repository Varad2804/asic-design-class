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
