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

- EAX — Accumulator for operands and results data
- EBX — Pointer to data in the DS segment
- ECX — Counter for string and loop operations
- EDX — I/O pointer
- ESI — Pointer to data in the segment pointed to by the DS register; source pointer for string operations
- EDI — Pointer to data (or destination) in the segment pointed to by the ES register; destination pointer for string operations
- ESP — Stack pointer (in the SS segment)
- EBP — Pointer to data on the stack (in the SS segment)

##### Segment Registers
The segment registers (CS, DS, SS, ES, FS, and GS) hold 16-bit segment selectors. A segment selector is a special pointer that identifies a segment in memory. To access a particular segment in memory, the segment selector for that segment must be present in the appropriate segment register.

<img width="550" src="https://github.com/fffclaypool/x86_64_basics/blob/images/Figure_0002.png">

<img width="550" src="https://github.com/fffclaypool/x86_64_basics/blob/images/Figure_0003.png">

##### RFLAGS Register
In 64-bit mode, EFLAGS is extended to 64 bits and called RFLAGS. The upper 32 bits of RFLAGS register is reserved. The lower 32 bits of RFLAGS is the same as EFLAGS. The 32-bit EFLAGS register contains a group of status flags, a control flag, and a group of system flags.

##### RIP(Instruction Pointer Register)
In 64-bit mode, the RIP register becomes the instruction pointer. This register holds the 64-bit offset of the next instruction to be executed. 64-bit mode also supports a technique called RIP-relative addressing. Using this technique, the effective address is determined by adding a displacement to the RIP of the next instruction.

### FPU Registers
##### FPU Registers
The eight x87 FPU data registers, the x87 FPU control register, the status register, the x87 FPU instruction pointer register, the x87 FPU operand (data) pointer register, the x87 FPU tag register, and the x87 FPU opcode register provide an execution environment for operating on single-precision, doubleprecision, and double extended-precision floating-point values, word integers, doubleword integers, quadword integers, and binary coded decimal (BCD) values.

### MMX Registers
##### MMX Registers
The MMX register set consists of eight 64-bit registers , that are used to perform calculations on the MMX packed integer data types. Values in MMX registers have the same format as a 64-bit quantity in memory.

MMX is a Pentium microprocessor from Intel that is designed to run faster when playing multimedia applications. According to Intel, a PC with an MMX microprocessor runs a multimedia application up to 60% faster than one with a microprocessor having the same clock speed but without MMX. In addition, an MMX microprocessor runs other applications about 10% faster, probably because of increased cache. All of these enhancements are made while preserving compatibility with software and operating systems developed for the Intel Architecture.

### XMM Registers
##### XMM Registers
Eight 128-bit XMM data registers were introduced into the IA-32 architecture with SSE extensions. SSE instructions use the XMM registers only to operate on packed single-precision floating-point operands. SSE2 extensions expand the functions of the XMM.Data can be loaded into XMM registers or written from the registers to memory in 32-bit, 64-bit, and 128-bit increments. When storing the entire contents of an XMM register in memory (128-bit store), the data is stored in 16 consecutive bytes, with the low-order byte of the register being stored in the first byte in memory

##### MXCSR Registers
The 32-bit MXCSR register (see Figure 10-3) contains control and status information for SSE, SSE2, and SSE3 SIMD floating-point operations.This register contains:

- flag and mask bits for SIMD floating-point exceptions
- rounding control field for SIMD floating-point operations
- flush-to-zero flag that provides a means of controlling underflow conditions on SIMD floating-point operations
- denormals-are-zeros flag that controls how SIMD floating-point instructions handle denormal source operands

The contents of this register can be loaded from memory with the LDMXCSR and FXRSTOR instructions and stored in memory with STMXCSR and FXSAVE.

### YMM Registers
##### YMM Registers
The lower 128 bits of a YMM register is aliased to the corresponding XMM register. Legacy SSE instructions (i.e. SIMD instructions operating on XMM state but not using the VEX prefix, also referred to non-VEX encoded SIMD instructions) will not access the upper bits (255:128) of the YMM registers. AVX and FMA instructions with a VEX prefix and vector length of 128-bits zeroes the upper 128 bits of the YMM register. Upper bits of YMM registers (255:128) can be read and written by many instructions with a VEX.256 prefix. XSAVE and XRSTOR may be used to save and restore the upper bits of the YMM registers.

### Bounds Registers
Each of the BND0-BND3 register stores the lower and upper bounds (64 bits each) associated with the pointer to a memory buffer. They support execution of the Intel MPX instructions.

The bounds are unsigned effective addresses, and are inclusive. The upper bounds are architecturally represented in 1/’s complement form. Lower bound = 0, and upper bound = 0 (1’s complement of all 1s) will allow access to the entire address space. The bounds are considered as INIT when both lower and upper bounds are 0 (cover the entire address space). The two Intel MPX instructions which operate on the upper bound (BNDMK and BNDCU) account for the 1’s complement representation of the upper bounds.

### Address Space
Any task or program running on an IA-32 processor can address a linear address space of up to 4 GBytes (232 bytes) and a physical address space of up to 64 GBytes (236 bytes).
