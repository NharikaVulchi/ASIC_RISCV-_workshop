## DAY 1 Introduction to RISC-V ISA and GNU compiler toolchain

<details>
<summary>
Introduction to RISC-V
</summary>
RISC-V is an open-source instruction set architecture (ISA) designed with simplicity and versatility. It features a modular structure, enabling custom extensions for diverse applications. Its load-store memory model and compact register set streamline execution. Privilege levels ensure secure operation. RISC-V suits embedded systems to high-performance computing, fostering innovation through open collaboration and customization. It is a 64 bit architecture.

Applications to Hardware: There are 3 major steps of how an application can be run on hardware, which are as follows:

**Operating System:**

Interface between hardware and user.

**Compiler**

Converts the high level language to respective instruction set which are hardware specific such as MIPS, Intel or RISC-V. 

**Assembler**

Converts the output from compiler, to binary language which are further fed to the hardware.

The below figure shows detailed description:


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/a693e7e6-736a-4423-a8b1-4e3da4f9fc7d)


The detailed ISA can be found here   https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf
</details>


<details>
<summary>
RISC-V Compiler
</summary>
Let us start with a C program to find the sum of numbers from 1 to n. Code:


```

#include <stdio.h>

int main () {
	int i,sum = 0, n = 6;
	for (i = 1; i <=n; ++i) {
		sum += i;
	}
	printf("The sum of the number from 1 to %d is %d\n", n,sum);
	return 0;
	}
```

Use the below commands to compile and view the assembly code for the above C program:


```
riscv64-unknown-elf-gcc  -o <object_name.o> <filename.c>
riscv64-unknown-elf-objdump -d <object_name.o>
```

Below are the figure showing disassemble object file along with the main function:


![Screenshot from 2023-08-19 15-20-32](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/5b6fc6c1-11aa-4655-88ad-237033912613)



![Screenshot from 2023-08-19 15-07-04](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1aa4afcd-3d4c-45da-ab2c-f6a7091e7043)


See the output using **spike**:

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/a75b9634-faa6-4fda-8410-ebf38fd993a2)

**lui** instruction : load upper immediate

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/f5b0e7f1-9af5-4502-9f6b-96b121ecd52a)

**spike-d** is used to debug. we can execute each instruction in detail and check the change in the register values accordingly in each step.

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/6fb52576-c1ee-4017-80b7-6999bcbaadf4)


**addi** command:

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/6ab1b5f4-b53d-4b53-8388-ea008558bffe)


</details>


<details>
<summary>
Integer - Signed and Unsigned
</summary>

**Integer Number Representation :**


Unsigned Numbers


Minimum value : 0


Maximum Value : 2^64 -1


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/d993ccfd-0ca6-4ca3-bda4-0fd4e0c5e445)

Negative numbers are represented using 2's complement representation:

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/d00de84e-956e-4e5b-ae9b-859f5be23000)


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1caecc5b-b1cb-4a65-ac1a-096e402bc58e)


**Lab 1**

Highest unsigned number that can be represented by RISC-V:Consider the below code 


```
#include <stdio.h>
#include <math.h>
int main() {
unsigned long long int max = (unsigned long long int) (pow(2,64) -1);
printf("highest number represented by unsigned long long int is %llu\n", max);
return 0;
}
```


Ouput :


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/60e3ddf4-05e2-428f-b78a-5b00a6c5d5f5)


**Lab 2**


For the below code we expect the output to be a negative umber, but we get 0 because **unsigned** can not represent negative numbers


```
#include <stdio.h>
#include <math.h>
int main() {
unsigned long long int max = (unsigned long long int) (pow(2,64) * -1);
printf("highest number represented by unsigned long long int is %llu\n", max);
return 0;
}
```



![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/95571c5c-e994-4e5f-9958-891272cf4faf)


**Lab 3**

Let us observe the highest and lowest number for signed integers:


```
#include <stdio.h>
#include <math.h>
int main() {
long long int max = (int) (pow(2,63) -1);
long long int min = (int) (pow(2,63) * -1);
printf("highest number represented by long long int is %lld\n", max);
printf("lowest number represented by long long int is %lld\n", min);
return 0;
```

Output:

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1cd71d67-d462-4454-a928-1dc42c8056fb)


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/e16c77b8-2eed-4378-ae9c-f1836a01c405)


</details>

## DAY 2 INTRODUCTION TO ABI AND BASIC VERIFICATION FLOW

<details>
<summary>
Application Binary Interface
</summary>
1. The application user can access the hardware resources using system calls, this is knows as the Application Binary Interface. This interface is done via the registers of the hardware.
2. RISC-V architecture provides this interface.
3. Length of the registers is given by **xlen** which can be 32 bit wide or 64 bit wide.
4. Number of registers are 32

**Registers in RISC-V 64 bit architecture**

Two ways to load data into register:


1. 64 bit data can be directly entered into the register
2. 64 bit data can be loaded into the memory


RISC-V is a **little endian** based memory addressing system. The MSB of the data sits to the leftmost bit in the register while storing the data.


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/218b1a02-a324-48ad-bc57-dd99349aa4c9)


**Load Instruction** 

1. All the instructions are 32 bit wide.
2. The **ld** instruction loads the immediate double word value into a register
![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1e716672-eaed-4f3f-8f11-2928b8440f65)


**Add instruction**

Add the contents of specified registers and stores into the source register. The instruction used is **add**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/0c88d6b1-79e3-45f4-a367-094cf6440b22)


**Store instruction**

Stores doubleword back to memory. Syntax and example is shown below using **sd** instruction.


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/c27c6e75-ff46-40a4-b969-c2c1c2bb2802)







</details>
