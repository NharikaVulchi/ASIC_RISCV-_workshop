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
Application Binary Interface and RISC-V instructions
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


**Base integer instructions**

1. Instructions which work on signed and unsigned 64 bit integers.
2. These are the part of RISCVI core.
3. Types:
   		1. R-type: Instructions that operates on registers

   
   		2. I-type: Instructions that operate on immediate value

   
   		3. S-type : Instructions that involve storing back to memory


5 Bits are utilized to represent the register address in an instruction, so we have a total of 2^5 which is 32 number of registers in RISC-V.

**Each register has a specific ABI code to access it**. This is shown in the below figure


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/e07adae8-5dc3-4e21-bb9e-9a86fe99866c)
</details>


<details>
<summary>
C program with ASM function call
</summary>
Following is the flowchart representing the Assembly code for sum of numbers

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1140d3de-e995-4d4f-93ba-94895fd0a524)

We try to use **main** from C program to load the required values to the ASM code 

**C code:**

```
#include <stdio.h>

extern int load(int x, int y);

int main() {
	int result=0;
	int count = 9;
	result = load(0x0, count+1);.global load
	printf("Sum of number 1 to %d is %d\n", count,result);
}
```


**ASM code:**


```
.section .text
.global load
.type load, @function

load:
	add	a4,a0,zero
	add	a2,a0,a1
	add	a3,a0,zero
loop:
	add	a4,a3,a4
	addi	a3,a3,1
	blt	a3,a2,loop
	add	a0,a4,zero
	ret
```


**Output:**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/9b542751-14d1-48d4-8d50-854a4d0d0e36)


**Lab works:**

![Screenshot from 2023-08-20 10-43-02](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/bc51d090-a383-4d2b-a33c-0f6544241d1f)


![Screenshot from 2023-08-20 10-43-23](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/379f1f1c-29d5-491f-b235-7ff104606895)

![Screenshot from 2023-08-20 10-46-46](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/f77442e5-5f99-4964-a88f-ef768520be9e)


</details>

<details>

<summary>
Build C program using RISC-V
</summary>

We use the **rv32im.sh** file to view the following result and binary file which is sent to the CPU:

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/60b980f4-596d-4a65-ba76-a00a1bb7e7c4)

</details>


## DAY 3 Digital Logic with TL-Verilog and Makerchip

<details>
<summary>
Introduction to TL verilog
</summary>
TL Verilog introduces new abstractions that allow designers to describe circuits in a more abstract and concise manner while still maintaining a clear connection to the underlying hardware. Key features of TL verilog includes:
1. Pipeline Abstractions: TL Verilog introduces the concept of pipelines, which are easy-to-define sequences of operations. This abstraction makes it simpler to describe complex pipelined circuits without having to manually manage all the stages and their interactions.
2. Modular Arithmetic: TL Verilog provides built-in support for modular arithmetic, making it easier to handle wraparound behavior often found in digital circuits.
3. Predicated Execution: TL Verilog allows operations to be conditionally executed based on a predicate. This helps in designing circuits that depend on certain conditions before proceeding with an operation.
4. Synchronous and Combinational Blocks: TL Verilog introduces two types of blocks: synchronous and combinational. Synchronous blocks represent clocked logic, while combinational blocks represent combinational logic. This separation makes the design intent clearer.
5. Testbenches and Verification: TL Verilog simplifies the process of creating testbenches for simulation and verification. The higher level of abstraction often results in more concise and understandable testbench code.
6. Automatic Pipelining: The TL Verilog compiler automatically infers pipeline stages based on the code structure. This helps in achieving better performance with minimal effort from the designer.
7. Data Types: TL Verilog introduces new data types like queue and stack, which simplify the description of data movement and storage within circuits.
8. Higher Abstraction Level: TL Verilog enables designers to describe the intended behavior of the circuit more directly, often in a way that's closer to their intuitive understanding of the problem.
</details>

<details>
<summary>
Combinational Logic
</summary>
Combinational logic deals with the manipulation of binary inputs to produce binary outputs based on a predefined logical function. It involves designing circuits that perform specific operations without any memory or feedback. The outputs of combinational logic circuits depend solely on the current inputs, and there is no concept of past states influencing the current behavior.

Key concepts include Logic gates, truth tables, boolean algebra, multiplexers, encoders, decoders and logic expressions.

**LABS**


**Example 1: Pythagoras theorem**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/183e4324-7844-48f3-b46c-5041a15c640e)


**Example 2: inverter**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1dac5a92-1577-4289-9743-d47301735689)


**Example 3: XOR gate**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/90ace840-badd-43fa-993d-670e0123e3fe)


**Example 4: Addition**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1251264a-11b6-4115-aa96-51c360144583)


**Example 5: Multiplexer**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/9ed8f938-ba1a-4612-b284-7ebf7c869b40)


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/dab3cb4d-d4aa-439e-94a8-32955e20d579)


**Example 6: Calculator**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/2fe821c6-d056-4b67-a330-1f5ae28462a4)


</details>

<details>
<summary>
Sequential Logic
</summary>

**Sequenced by a clock signal.**

The cicuit enters a known state in response to a reset signal.


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/8fc25c8e-faf7-4993-9829-5c94b0bd8579)

**Free running counter : Fibonacci Series**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/c514b6ec-acd7-4d54-a511-30e196a25076)

**Sequential Calculator :**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/8e036851-31b9-43e2-907d-9010288802d3)


</details>
<details>
<summary>
Pipeline design
</summary>

Pipeline divides the execution into a specific number of stages.


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/b8a7bf3c-8cd7-440f-845b-378d8b1e9a2a)


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/10b60931-973e-4421-a6f7-81a08c5fe165)


Pipeline computation can increase the clock frequency.

**Identifiers**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/bd30b81e-ae16-416d-8555-34195879e251)

**Lab on Cycle calculator with 1 stage pipeline**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/9f150f3b-bbac-4378-8370-8fa847a8513f)

</details>

<details>
<summary>
Validity
</summary>
Validity helps us to decode to see the signals which computes values inside a pipeline. 

	
This provides cleaner design, easier debug, better error debugging.


 When we know the instant of when a flip flop holds a meaningful value , we can improve the frequency of clock and hence save power.

 ![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/c53a29ac-5a65-42aa-aec9-30618b49e293)


 **Lab 1** Calculating total distance using **valid** signal

 ![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/6a0d8e75-5a3b-4671-9cc0-05ed961018fe)


**Lab 2** Calculator with **valid**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/40eeebc0-c998-4caa-bf6c-aed5433530b8)


**Lab 3** Calculator with memory and recall

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/934d43ca-f88f-4ac7-b544-187c840c436a)


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/b5e3445b-c788-45bf-9281-c8a8791ef8ba)


</details>

<details>
<summary>
Hierarchy
</summary>

**Conway's game of life**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/99c4f653-bc04-4483-9255-b9f9151579bb)


</details>


## DAY 4 Basic RISC-V CPU micro architecture


<details>
<summary>
Introduction
</summary>

**CPU Microarchitecture**

1. PC is a pointer which points to the Instruction Memory
2. **Dec** decodes the instruction generated
3. Branch instructions have a offset immediate value which is sent to MUX
4. Adder computes next PC with the given offset value
5. **RF** and **RD** are the two source registers for arithmetic instructions
6. **DMem** is data memory
7. **ALU** performs the operation on the intruction.
8. **ld** instruction access the **DMem** and brings the data

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/fd077199-3002-40b4-a4c5-9745e4b838f9)

</details>



















