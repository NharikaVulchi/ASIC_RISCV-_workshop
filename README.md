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


<details>
<summary>
Fetch and Decode
</summary>

Processor is designed in three steps:
1. Fetch
2. Decode
3. Execute


We implement the below CPU :


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/deff1229-b196-4806-82da-dbd1586fe04a)

**LAB 1 PC**


1. PC is incremented to the next instruction after each execution
2. PC in RISC-V is a 32 bit register, which is byte addressable , so we increment it by 4 to move to the next instruction, since instructions are also 32-bit
   

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/97879317-6ed1-4a4f-8dbf-2b9ed5d6714b)


Below code is consdiered for designing PC 


```
|cpu
      @0
         $reset = *reset;
         
         $pc[31:0] = >>1$reset ? 32'b0 : >>1$pc + 32'd4;
```         


Implementation in makerchip:


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/e9c2d453-cb98-4a90-9a4d-509e75d30614)


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/a10dfab3-efc8-4117-af8b-c80eb7455c63)

**LAB 2 Fetch instruction**

Fetches the instruction from Instruction Memory and gives to the Decoder


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/61544c1a-6015-4bec-a026-05201cf79c72)

Code:

```
|cpu
  @0
     $reset = *reset;
     $pc[31:0] = >>1$reset ? 0 : (>>1$pc + 32'd4);
  
  @1 
     $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];
     $imem_rd_en = !$reset;
     $instr[31:0] = $imem_rd_data[31:0];
     
  ?$imem_rd_en
     @1
        $imem_rd_data[31:0] = imem[$imem_rd_addr]$instr;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/c9fe82fb-4877-4073-bcf2-518ab324f353)


**Instruction decode**

Type of instruction is decided by the opdcode of the instruction.There are 6 instructions type in RISC-V :

1. Register (R) type
2. Immediate (I) type
3. Store (S) type
4. Branch (B) type
5. Upper immediate (U) type
6. Jump (J) type


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/71de7b79-4b1c-402a-b36b-9488f123f061)

**LAB 3 Instruction Type Decode Logic:**


```
@1
         $is_u_instr = $instr[6:2] ==? 5'b0x101;
         
         $is_s_instr = $instr[6:2] ==? 5'b0100x;
         
         $is_r_instr = $instr[6:2] ==? 5'b01011 ||
                       $instr[6:2] ==? 5'b011x0 ||
                       $instr[6:2] ==? 5'b10100;
         
         $is_j_instr = $instr[6:2] ==? 5'b11011;
         
         $is_i_instr = $instr[6:2] ==? 5'b0000x ||
                       $instr[6:2] ==? 5'b001x0 ||
                       $instr[6:2] ==? 5'b11001;
         
         $is_b_instr = $instr[6:2] ==? 5'b11000;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/82433378-8e70-419f-b372-d465e390d4e1)


**LAB 4 Decode Immediate Part of the Instruction:**

We take the immediate value from the instruction based on the type of instruction given in the below table. We use the concatenation operator to fetch the bits in the immediate value.


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/eef79df6-b136-4728-9fef-53680ea7750e)

Code:

```
 $imm[31:0] = $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
	$is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
	$is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25], $instr[11:8], 1'b0} :
	$is_u_instr ? {$instr[31:12], 12'b0} :
	$is_j_instr ? {{12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0} :
                                    32'b0;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/7ecc5aee-cc4f-4622-979b-cd1f8176d544)


**LAB 5 Decode instruction based on Instruction Type**

Code:

```
// Instruction field decode based on type
         $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
         ?$rs2_valid
            $rs2[4:0] = $instr[24:20];
            
         $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         ?$rs1_valid
            $rs1[4:0] = $instr[19:15];
         
         $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];
            
         $funct7_valid = $is_r_instr ;
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25];
            
         $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
         ?$rd_valid
            $rd[4:0] = $instr[11:7];
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/0cb6ae76-fc56-4bda-b9a9-98a1532ab0bd)


**LAB 6 Decode individual Instruction**

Code:

```
   // Individual Instruction decode
         $dec_bits[10:0] = {$funct7[5], $funct3, $opcode};
         $is_beq = $dec_bits ==? 11'bx_000_1100011;
         $is_bne = $dec_bits ==? 11'bx_001_1100011;
         $is_blt = $dec_bits ==? 11'bx_100_1100011;
         $is_bge = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
         $is_addi = $dec_bits ==? 11'bx_000_0010011;
         $is_add = $dec_bits ==? 11'b0_000_0110011;
```



![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/b4fba7d0-cd09-40bf-8b9f-8d4c311d13f9)


</details>



<details>
<summary>
Control Logic
</summary>

**LAB 1 Register file read**

After we decode the instruction we read the data from the register files into ALU to faciliate the ALU operation on the registers.

The below figure shows a register file with **read_enable** and **data_output** registers.We map the address of the data registers to be read from the source registers we get from the instruction decode stage.



![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/eeabc266-b35d-4eed-811b-439c7c8c429c)


We assign the **src1_value** and **src2_value** to the output data from the register files

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/1dae8967-d24e-42cd-ba64-f6473562c5c7)



Code:

```
$rf_wr_en = 1'b0;
$rf_wr_index[4:0] = 5'b0;
$rf_wr_data[31:0] = 32'b0;
$rf_rd_en1 = $rs1_valid;
$rf_rd_index1[4:0] = $rs1;
$rf_rd_en2 = $rs2_valid;
$rf_rd_index2[4:0] = $rs2;

//assigning values
$src1_value[31:0] = $rf_rd_data1; 
$src2_value[31:0] = $rf_rd_data2;
```

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/18034dd4-49d3-41a3-b650-c900b92bbe83)



**LAB 2 ALU Operations**


We consider two operations **add** and **addi** 

 ![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/7bc1708e-7413-45e0-aa93-24cfd21c6a8a)

Code:

```
$result[31:0] = $is_addi ? $src1_value + $imm :
                         $is_add ? $src1_value + $src2_value :
                         32'bx ;
```

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/2d3f95ec-f7e2-4894-aa69-8ab4ca4cf691)



**LAB 3 Register File Write**

After the ALU operation we store the values back into the resgister file. Below figure gives the detailed description.


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/f2fb3384-ac6e-4985-b3f8-102cfd6473de)

Code

```
$rf_wr_en = $rd_valid && $rd != 5'b0;
$rf_wr_index[4:0] = $rd;
$rf_wr_data[31:0] = $result;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/5a1fd8a4-144f-46c7-bd1a-dfa9b13536dd)


**ARRAY**

Arrays are typically implemented using a combination of registers and memory. Arrays can be stored in memory, where each element is stored at a specific memory address. The processor can use load and store instructions to access these elements. For example, you might load an element from an array in memory into a register, perform operations on it, and then store the result back into memory.

read operation  involves, read_enable and read_index signals 
write operation involves write_enable, write_index and write_data


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/39e87031-64d9-43bf-a643-bd199b96ae95)

**CPU reads the instructions that are written in the previous cylce**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/8ef1f0cb-ae1e-4708-852c-532172255851)


**Branch Instructions**

Branches are conditional in RISC-V

Jump statements are unconditional


Condition is a operation on two source registers

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/2748966b-29a8-48df-b7e7-d782a56ea990)

Code:

```
//BRANCH INSTRUCTIONS 1
         $taken_branch = $is_beq ? ($src1_value == $src2_value):
                     $is_bne ? ($src1_value != $src2_value):
                     $is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])):
                     $is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])):
                     $is_bltu ? ($src1_value < $src2_value):
                     $is_bgeu ? ($src1_value >= $src2_value):
                                1'b0;
     
     
//BRANCH INSTRUCTIONS 2
$br_target_pc[31:0] = $pc +$imm;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/c88a6b27-213b-4146-b303-7f4e76dc124f)



**Test Bench**


Code :

```
*passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9) ;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/6035d984-bb85-456a-bd6b-3b07f445d4d7)

</details>

## DAY 5 Complete Pipelined RISC-V CPU micro-architecture

<details>
<summary>
Pipelining the CPU
</summary>

Waterfall Logic Diagram : There is one logic in previous stage with respect to the one which is in the current stage. S0, Instead of feeding back the logic, we feed it into the INSTRUCTION that is waiting in the next stage.

Dependency of one instruction on another is clearly seen in this diagram.

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/27d11ada-974e-4e88-9463-f1e70b140c73)

Performance of CPU is increased with increased clock cycle frequency. This, creates problems in dependancies between each instruction with another. Hazards:
1. Branch with the control flow hazard
2. Read after write hazard

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/6ad96eab-8f82-45cb-8fa9-ad636d5bcaca)

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/2cbd2ab0-ffa9-4a4c-b294-eeaf4d9155ef)

In our example, if we consider 3 cycle latency between each instruction , we can solve the hazards that are involved (read afer write and branch )

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/f11b77db-c385-4726-9f4b-152d63e9661d)

3_cycle latency: using **valid** after 3 cycles

Code:

```
         //3 cycle valid signal
         $start = !$reset && >>1$reset;
         $valid = $reset ? 1'b0 : 
                  $start ? 1'b1 : 
                  >>3$valid ;
```


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/84121a80-b4c2-4252-b249-b53cbf28d70c)


</details>

<details>
<summary>
Lab works
</summary>
The above case can be improved by taking care of invalid cycles

**LAB ALU CODE**


![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/0573d796-a47a-437a-8cf1-bb9356b6986c)


**LAB Load Data**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/ce5a140c-cf4f-4724-8b9d-41d82c70fd05)


**LAB Control Logic for Jump instructions**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/d4751ddc-589c-4239-8899-fdb26d280b2c)
</details>




## Final pipelined CPU code


```

  \m4_TLV_version 1d: tl-x.org
  \SV
  //  This code can be found in: https://github.com/stevehoover/RISC-V_MYTH_Workshop

   m4_include_lib(['https://raw.githubusercontent.com/stevehoover/RISC-V_MYTH_Workshop/c1719d5b338896577b79ee76c2f443ca2a76e14f/tlv_lib/risc-v_shell_lib.tlv'])

  \SV
 m4_makerchip_module   // (Expanded in Nav-TLV pane.)
  \TLV

  // /====================\
  // | Sum 1 to 9 Program |
  // \====================/
  //
  // Program for MYTH Workshop to test RV32I
  // Add 1,2,3,...,9 (in that order).
  //
  // Regs:
  //  r10 (a0): In: 0, Out: final sum
   //  r12 (a2): 10
  //  r13 (a3): 1..10
  //  r14 (a4): Sum
   // 
  // External to function:
  m4_asm(ADD, r10, r0, r0)             // Initialize r10 (a0) to 0.
  // Function:
  m4_asm(ADD, r14, r10, r0)            // Initialize sum register a4 with 0x0
  m4_asm(ADDI, r12, r10, 1010)         // Store count of 10 in register a2.
  m4_asm(ADD, r13, r10, r0)            // Initialize intermediate sum register a3 with 0
  // Loop:
   m4_asm(ADD, r14, r13, r14)           // Incremental addition
   m4_asm(ADDI, r13, r13, 1)            // Increment intermediate register by 1
   m4_asm(BLT, r13, r12, 1111111111000) // If a3 is less than a2, branch to label named <loop>
   m4_asm(ADD, r10, r14, r0)            // Store final result to register a0 so that it can be read by main program
   m4_asm(SW, r0, r10, 10000)           // Store the final result value to byte address 16
   m4_asm(LW, r17, r0, 10000)           // Load the final result value from adress 16 to x17

  // Optional:
  // m4_asm(JAL, r7, 00000000000000000000) // Done. Jump to itself (infinite loop). (Up to 20-bit signed immediate plus implicit 0 bit (unlike JALR) provides byte address; last immediate bit should also be 0)
   m4_define_hier(['M4_IMEM'], M4_NUM_INSTRS)

   |cpu
@0
   $reset = *reset;
   
   //NEXT PC LOGIC       
   
   $pc[31:0] = >>1$reset ? 32'b0 :
               >>3$valid_taken_branch ? >>3$br_target_pc :
               >>3$valid_load ? >>3$inc_pc :
               >>3$valid_jump && >>3$is_jal ? >>3$br_target_pc :
               >>3$valid_jump && >>3$is_jalr ? >>3$jalr_target_pc :
               >>1$inc_pc ;
@1   
   $inc_pc[31:0] = $pc + 32'd4;
   
   
@3
   //CYCLE VALID INSTRUCTIONS
   $valid = !(>>1$valid_taken_branch || >>2$valid_taken_branch || 
              >>1$valid_load || >>2$valid_load ||  
              >>1$valid_jump || >>2$valid_jump) ;
   
   $valid_load = $valid && $is_load ;
   
   $valid_jump = $is_jump && $valid ;
   
   
   
   //INSTRUCTION FETCH LOGIC
@1 
   $imem_rd_addr[M4_IMEM_INDEX_CNT-1:0] = $pc[M4_IMEM_INDEX_CNT+1:2];
   $imem_rd_en = !$reset;
   $instr[31:0] = $imem_rd_data[31:0];
   
   
   //INSTRUCTION TYPES DECODE   
@1
   $is_u_instr = $instr[6:2] ==? 5'b0x101;
   
   $is_s_instr = $instr[6:2] ==? 5'b0100x;
   
   $is_r_instr = $instr[6:2] ==? 5'b01011 ||
                 $instr[6:2] ==? 5'b011x0 ||
                 $instr[6:2] ==? 5'b10100;
   
   $is_j_instr = $instr[6:2] ==? 5'b11011;
   
   $is_i_instr = $instr[6:2] ==? 5'b0000x ||
                 $instr[6:2] ==? 5'b001x0 ||
                 $instr[6:2] ==? 5'b11001;
   
   $is_b_instr = $instr[6:2] ==? 5'b11000;
   
   
   //INSTRUCTION IMMEDIATE DECODE
   
   $imm[31:0] = $is_i_instr ? {{21{$instr[31]}}, $instr[30:20]} :
                $is_s_instr ? {{21{$instr[31]}}, $instr[30:25], $instr[11:7]} :
                $is_b_instr ? {{20{$instr[31]}}, $instr[7], $instr[30:25], $instr[11:8], 1'b0} :
                $is_u_instr ? {$instr[31:12], 12'b0} :
                $is_j_instr ? {{12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0} :
                              32'b0;
   `BOGUS_USE($imm)
   
   $opcode[6:0] = $instr[6:0];
   
   
   //INSTRUCTION FIELD DECODE
   
   $rs2_valid = $is_r_instr || $is_s_instr || $is_b_instr;
   ?$rs2_valid
      $rs2[4:0] = $instr[24:20];
      
   $rs1_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
   ?$rs1_valid
      $rs1[4:0] = $instr[19:15];
   
   $funct3_valid = $is_r_instr || $is_i_instr || $is_s_instr || $is_b_instr;
   ?$funct3_valid
      $funct3[2:0] = $instr[14:12];
      
   $funct7_valid = $is_r_instr ;
   ?$funct7_valid
      $funct7[6:0] = $instr[31:25];
      
   $rd_valid = $is_r_instr || $is_i_instr || $is_u_instr || $is_j_instr;
   ?$rd_valid
      $rd[4:0] = $instr[11:7];
      
   `BOGUS_USE($rd)
   
@2
   //INSTRUCTION DECODE
   $dec_bits[10:0] = {$funct7[5], $funct3, $opcode};
   $is_beq = $dec_bits ==? 11'bx_000_1100011;
   $is_bne = $dec_bits ==? 11'bx_001_1100011;
   $is_blt = $dec_bits ==? 11'bx_100_1100011;
   $is_bge = $dec_bits ==? 11'bx_101_1100011;
   $is_bltu = $dec_bits ==? 11'bx_110_1100011;
   $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
   $is_addi = $dec_bits ==? 11'bx_000_0010011;
   $is_add = $dec_bits ==? 11'b0_000_0110011;
   
   $is_load = $opcode == 7'b0000011;
   
   $is_xori = $dec_bits ==? 11'bx_100_0010011;
   $is_xor = $dec_bits ==? 11'b0_100_0110011;
   $is_sw = $dec_bits ==? 11'bx_010_0100011;
   $is_sub = $dec_bits ==? 11'b1_000_0110011;
   $is_srli = $dec_bits ==? 11'b0_101_0010011;
   $is_srl = $dec_bits ==? 11'b0_101_0110011;
   $is_srai = $dec_bits ==? 11'b1_101_0010011;
   $is_sra = $dec_bits ==? 11'b1_101_0110011;
   $is_sltu = $dec_bits ==? 11'b0_011_0110011;
   $is_sltiu = $dec_bits ==? 11'bx_011_0010011;
   $is_slti = $dec_bits ==? 11'bx_010_0010011;
   $is_slt = $dec_bits ==? 11'b0_010_0110011;
   $is_slli = $dec_bits ==? 11'b0_001_0010011;
   $is_sll = $dec_bits ==? 11'b0_001_0110011;
   $is_sh = $dec_bits ==? 11'bx_001_0100011;
   $is_sb = $dec_bits ==? 11'bx_000_0100011;
   $is_ori = $dec_bits ==? 11'bx_110_0010011;
   $is_or = $dec_bits ==? 11'b0_110_0110011;
   $is_lui = $dec_bits ==? 11'bx_xxx_0110111;
   $is_jalr = $dec_bits ==? 11'bx_000_1100111;
   $is_jal = $dec_bits ==? 11'bx_xxx_1101111;
   $is_auipc = $dec_bits ==? 11'bx_xxx_0010111;
   $is_andi = $dec_bits ==? 11'bx_111_0010011;
   $is_and = $dec_bits ==? 11'b0_111_0110011;
   
   $jalr_target_pc[31:0] = $src1_value +$imm ;
   
@3
   $is_jump = $is_jal || $is_jalr ;
   
   `BOGUS_USE ($is_beq $is_bne $is_blt $is_bge $is_bltu $is_bgeu $is_addi $is_add)
   
   
@2
   //REGISTER FILE READ
   
   $rf_rd_en1 = $rs1_valid;
   $rf_rd_index1[4:0] = $rs1;
   $rf_rd_en2 = $rs2_valid;
   $rf_rd_index2[4:0] = $rs2;
   
@3
   //REGISTER FILE WRITE
   $rf_wr_en = ($rd_valid && $rd != 5'b0 && $valid) || >>2$valid_load;
   $rf_wr_index[4:0] = >>2$valid_load ? >>2$rd : $rd;
   $rf_wr_data[31:0] = >>2$valid_load ? >>2$ld_data : $result;
   
@2   
   $src1_value[31:0] = (>>1$rf_wr_index == $rf_rd_index1) && >>1$rf_wr_en ? >>1$result :  
                       $rf_rd_data1;
   
   $src2_value[31:0] = (>>1$rf_wr_index == $rf_rd_index2) && >>1$rf_wr_en ? >>1$result :
                       $rf_rd_data2;
   
@4
   //MINI 1-R/W MEMORY
   $dmem_wr_en = $is_s_instr && $valid ;
   $dmem_addr[3:0] = $result[5:2] ;
   $dmem_wr_data[31:0] = $src2_value ;
   $dmem_rd_en = $is_load ;
   
@5
   //LOAD DATA
   $ld_data[31:0] = $dmem_rd_data ;
   
   
@3   
   //ARITHMETIC AND LOGIC UNIT (ALU)
   
   $sltu_rslt[31:0] = $src1_value < $src2_value ;
   $sltiu_rslt[31:0]  = $src1_value < $imm ;
   
   $result[31:0] = $is_andi ? $src1_value & $imm :
                   $is_ori ? $src1_value | $imm :
                   $is_xori ? $src1_value ^ $imm :
                   ($is_addi || $is_load || $is_s_instr) ? $src1_value + $imm :
                   $is_slli ? $src1_value << $imm[5:0] :
                   $is_srli ? $src1_value >> $imm[5:0] :
                   $is_and ? $src1_value & $src2_value :
                   $is_or ? $src1_value | $src2_value :
                   $is_xor ? $src1_value ^ $src2_value :
                   $is_add ? $src1_value + $src2_value :
                   $is_sub ? $src1_value - $src2_value :
                   $is_sll ? $src1_value << $src2_value[4:0] :
                   $is_srl ? $src1_value >> $src2_value[4:0] :
                   $is_sltu ? $src1_value | $src2_value :
                   $is_sltiu ? $src1_value < $imm :
                   $is_lui ? {$imm[31:12], 12'b0} :
                   $is_auipc ? $pc + $imm :
                   $is_jal ? $pc + 4 :
                   $is_jalr ? $pc + 4 :
                   $is_srai ? {{32{$src1_value[31]}}, $src1_value} >> $imm[4:0] :
                   $is_slt ? ($src1_value[31] == $src2_value[31]) ? $sltu_rslt : {31'b0, $src1_value[31]} :
                   $is_slti ? ($src1_value[31] == $imm[31]) ? $sltiu_rslt : {31'b0, $src1_value[31]} :
                   $is_sra ? {{32{$src1_value[31]}}, $src1_value} > $src2_value[4:0] :
                   32'bx ;
   
   
   //BRANCH INSTRUCTIONS 1
   $taken_branch = $is_beq ? ($src1_value == $src2_value):
                   $is_bne ? ($src1_value != $src2_value):
                   $is_blt ? (($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31])):
                   $is_bge ? (($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31])):
                   $is_bltu ? ($src1_value < $src2_value):
                   $is_bgeu ? ($src1_value >= $src2_value):
                              1'b0;
   
   $valid_taken_branch = $valid && $taken_branch;
            
@2
   //BRANCH INSTRUCTIONS 2
   $br_target_pc[31:0] = $pc +$imm;
   
   
   //TESTBENCH
   *passed = |cpu/xreg[17]>>5$value == (1+2+3+4+5+6+7+8+9) ;
   
   
// Note: Because of the magic we are using for visualisation, if visualisation is enabled below,
//       be sure to avoid having unassigned signals (which you might be using for random inputs)
//       other than those specifically expected in the labs. You'll get strange errors for these.

   // Assert these to end simulation (before Makerchip cycle limit).
  *passed = *cyc_cnt > 40;
   *failed = 1'b0;

  // Macro instantiations for:
   //  o instruction memory
   //  o register file
  //  o data memory
   //  o CPU visualization
   |cpu
m4+imem(@1)    // Args: (read stage)
m4+rf(@2, @3)  // Args: (read stage, write stage) - if equal, no register bypass is required
m4+dmem(@4)    // Args: (read/write stage)

m4+cpu_viz(@4)    // For visualisation, argument should be at least equal to the last stage of CPU logic
                 // @4 would work for all labs
  \SV
endmodule

```




**THE FINAL CPU CORE**

![image](https://github.com/NharikaVulchi/ASIC_RISCV-_workshop/assets/83216569/f79587b9-6f14-42c7-8251-22dea8a3cb7b)


## Contributors

Niharika Vulchi, IIIT B


Kunal Ghosh, VSD


Steve Hoover, Founder of Redwood EDA, LLC


## References

https://github.com/RISCV-MYTH-WORKSHOP/RISC-V-CPU-Core-using-TL-Verilog

https://github.com/stevehoover/RISC-V_MYTH_Workshop/blob/master/risc-v_shell.tlv

https://www.makerchip.com/sandbox/0o2fXh1wp/0Wnh2g

https://www.makerchip.com/sandbox/0zpfRh43g/08qhEg

https://www.makerchip.com/sandbox/0o2fXh1wp/0g5hr5









