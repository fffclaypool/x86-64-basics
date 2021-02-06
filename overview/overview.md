## What is x86-64 Architecture
x86-64 (also known as x64, x86_64, AMD64 and Intel 64) is a 64-bit version of the x86 instruction set, first released in 1999.
x86 is a family of instruction set architectures initially developed by Intel based on the Intel 8086 microprocessor and its 8088 variant.
The term "x86" came into being because the names of several successors to Intel's 8086 processor end in "86", including the 80186, 80286, 80386 and 80486 processors.
And of course, the term 64 derives from "64" bit.

## x86-64 Architecture Diagram

The basic architecture of the x86-64 is described in [Volume 1 of the System Developer’s Manual](https://software.intel.com/sites/default/files/managed/a4/60/253665-sdm-vol-1.pdf).
In Chapter 3, there is the following diagram.

<img width="650" src="https://github.com/fffclaypool/x86_64_basics/blob/images/Figure_0000.png">

### Basic Program Execution Registers
##### General-Purpose Registers
In 64-bit mode, there are 16 general purpose registers and the default operand size is 32 bits. However, generalpurpose registers are able to work with either 32-bit or 64-bit operands.

If a 32-bit operand size is specified: EAX,EBX, ECX, EDX, EDI, ESI, EBP, ESP, R8D - R15D are available. If a 64-bit operand size is specified: RAX, RBX, RCX, RDX, RDI, RSI, RBP, RSP, R8-R15 are available. R8D-R15D/R8-R15 represent eight new general-purpose registers. All of these registers can be accessed at the byte, word, dword, and qword level. REX prefixes are used to generate
64-bit operand sizes or to reference registers R8-R15.

<img width="750" src="https://github.com/fffclaypool/x86_64_basics/blob/images/Figure_0001.png">

##### Segment Registers
##### RFLAGS Register
##### RIP(Instruction Pointer Register)

### FPU Registers
##### Floating-Point Data Registers
##### Control Register
##### Status Register
##### Tag Register
##### Opcode Register(11-bits)
##### FPU Instruction Pointer Register
##### FPU Data (Operand) Pointer Register

### MMX Registers
##### MMX Registers

### XMM Registers
##### XMM Registers
##### MXCSR Registers

### YMM Registers
##### YMM Registers

### Bounds Registers

### Address Space
