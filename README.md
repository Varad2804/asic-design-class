# asic-design-class
This is Varad Bharadiya (IMT2021532). Course - Physical Design of ASICs ( VLS508)

# Sum Calculation in C of numbers from 1 to N.

This project demonstrates how to compile and run a simple C program that calculates the sum of numbers from 1 to 10 using both GCC and the RISC-V compiler. Additionally, it covers basic debugging using Spike on a RISC-V architecture.

## Compiling the C Code with GCC

### Steps:

1. **Navigate to Home Directory**
   Open terminal
   use this command to get to home directory.
   ```bash
   cd
   ```
   Then open the leafpad editor for writing the c code.
   ![Alt text](images/image1.png)

3. Create a .c file that calculates sum of numbers from 1 to N.
   ![Alt text](images/image2.png)
4. Now run this programme using the gcc compiler
   
    ```bash
   gcc sum1ton.c
   ./a.out
    ```
   ![Alt text](images/image3.png)
   
## Compile using RISCV

### steps:
1. First check the code again using the cat command for correctness then Compile the Code using the RISCV gcc.
   ```bash
   cat sum1ton.c
   ```
Use the RISC-V GCC compiler:
   ```bash
   riscv64-unknown-elf-gcc -O1 -o sum1ton.o sum1ton.c
   ```
This generates the file sum1ton.o.
   ![Alt text](images/image4.png)

2. Generate Assembly Code
Now using the command shown in the figure or below, we'll get the assembly code of our c program. We'll again run the same command adding | less in the end for easy viewing of the assembly code.
Get the assembly code of the C program:
```bash
riscv64-unknown-elf-objdump -d sum1ton.o
```
Add | less to the end for easier viewing:
```bash
riscv64-unknown-elf-objdump -d sum1ton.o | less
```
   ![Alt text](images/image5.png)
3. We'll obtain the output once more, and this time we'll focus directly on the main section. To determine the number of instructions, we'll subtract the address of the first instruction of the current section from that of the next section, then divide the result by 4 (since the increment is 4 for each step). This method allows us to conclude that there are 15 sets of instructions when using O1.
   ![Alt text](images/image6.png)

To verify we can do it using a calculator:
   ![Alt text](images/image9.png)

4.Now we shift back to the first tab and compile the same code using RISCV again but this time with Ofast flag.

   ![Alt text](images/image7.png)
5. Now going back to tab 2 and running again the same commands to get the number of instructions, we see that number of instructions required are less to execute the same program.
   ![Alt text](images/image8.png)







   


