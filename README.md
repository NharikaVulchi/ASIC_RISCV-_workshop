## DAY 1 Introduction to RISC-V ISA and GNU compiler toolchain

<details>
<summary>
Introduction to RISC-V
</summary>
RISC-V is an open-source instruction set architecture (ISA) designed with simplicity and versatility. It features a modular structure, enabling custom extensions for diverse applications. Its load-store memory model and compact register set streamline execution. Privilege levels ensure secure operation. RISC-V suits embedded systems to high-performance computing, fostering innovation through open collaboration and customization.

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
