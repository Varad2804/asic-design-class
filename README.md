# asic-design-class
This is Varad Bharadiya (IMT2021532). Course - Physical Design of ASICs ( VLS508)

# Sum Calculation in C of numbers from 1 to N.

This project demonstrates how to compile and run a simple C program that calculates the sum of numbers from 1 to 10 using both GCC and the RISC-V compiler. Additionally, it covers basic debugging using Spike on a RISC-V architecture.

## Compiling the C Code with GCC

### Steps:

1. **Navigate to Home Directory**
   ```bash
   cd
   ```
   ![Alt text](images/.png)

3. Create a .c file that calculates sum of numbers from 1 to N.
   Add image2
4. Now run this programme using the gcc compiler
   
    ```bash
   gcc sum1ton.c
   ./a.out
    ```
    Add image3.
   
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
   Add image4

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
   Add image5
3. 






   


