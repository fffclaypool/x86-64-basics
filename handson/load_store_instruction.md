## Main memory, lord, store instruction
Main memory is a device for storing data. The stored data is specified by an integer value called an address, and the following two operations can be performed.

- load: retrieve byte data stored in the location specified by the address.
- store: stores byte data at the location specified by the address.


In the AT&T notation of x86_64, each of them is written as follows:

- load: mov address, register (acquire the data identified by the address from the data in the main memory and store it in the register.)
- store: mov register, address (stores the data contained in the register in the area identified by the address in the main memory.)

In x86_64, both load and store instructions are "mov" instructions. There is no mnemonic distinction, just a change in the order of the operands.

When looking at the mov instruction, it is necessary to distinguish which of the following it is.

- mov instruction (put numerical data directly into a register.)
- mov instruction (copies the data in a register to another register.)
- mov instruction (load)
- mov instruction (store)

The following is a summary of how the distinction is made.

- Immediate mov is an immediate value with '$' followed by a register name starting with %: "mov $15, %rax"
- For inter-register copy, both operands contain register names starting with %: "mov %rax, %rdx"
- Load has the first operand contain a number without a '$' or a register name enclosed in round brackets: "mov (%rax), %rdx" or "mov 15, %rax"
- The second operand of the store is a number without '$' or a register name enclosed in '()' parentheses: "mov %rdx, (%rax)" or "mov %rax, 15"

Let's take a quick look at the behavior of this mov instruction.

```Assembly
	.globl	main
main:
	mov $99, %rax
	mov %rax,0      # Store the value of rax (99) in the area identified by address 0.
	mov 0, %r8      # Extracts a value from the area identified by address 0 and stores the value in r8.
	ret
```

```
(gdb) start
Temporary breakpoint 1 at 0x4004ed
Starting program: /home/fff_claypool/src/x8664_asm_language/a.out
Temporary breakpoint 1, 0x00000000004004ed in main ()
Missing separate debuginfos, use: debuginfo-install glibc-2.17-322.el7_9.x86_64
(gdb) stepi
0x00000000004004f4 in main ()
(gdb) stepi
Program received signal SIGSEGV, Segmentation fault.
0x00000000004004f4 in main ()
(gdb) stepi
Program terminated with signal SIGSEGV, Segmentation fault.
The program no longer exists.
(gdb)
```

The program was terminated as a result of the OS's memory protection function working. There is only one or a few pieces of main memory in each computer.  A few main memories are shared by many programs.If a single memory is shared by several programs without any mechanism, it is not good because other programs' data can be read and written.If a single memory is shared by several programs without any mechanism, it is not good because other programs' data can be read and written. For this reason, most operating systems have a function to protect the memory. Let's keep in mind that the OS has a mechanism to protect memory, and that main memory is not always freely available.

An OS with memory protection features has a memory allocation interface that allows the OS to allocate a portion of its main memory for free use. There are several ways of allocating memory, but the easiest way is to simply start a program; when the OS is instructed to start a program, it allocates the necessary memory to the program before starting it. The so-called executable file contains information about the size of memory required at startup, and the OS determines the size of memory used by the program by looking at that information.

On Linux, you can find out the memory size used by the executable by executing the command readelf with -l.

```
$ gcc add.s
$ readelf -l a.out
Elf file type is EXEC (Executable file)
Entry point 0x400400
There are 9 program headers, starting at offset 64
Program Headers:
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  PHDR           0x0000000000000040 0x0000000000400040 0x0000000000400040
                 0x00000000000001f8 0x00000000000001f8  R E    8
  INTERP         0x0000000000000238 0x0000000000400238 0x0000000000400238
                 0x000000000000001c 0x000000000000001c  R      1
      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
  LOAD           0x0000000000000000 0x0000000000400000 0x0000000000400000
                 0x0000000000000694 0x0000000000000694  R E    200000
  LOAD           0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
                 0x000000000000021c 0x0000000000000220  RW     200000
  DYNAMIC        0x0000000000000e28 0x0000000000600e28 0x0000000000600e28
                 0x00000000000001d0 0x00000000000001d0  RW     8
  NOTE           0x0000000000000254 0x0000000000400254 0x0000000000400254
                 0x0000000000000044 0x0000000000000044  R      4
  GNU_EH_FRAME   0x0000000000000590 0x0000000000400590 0x0000000000400590
                 0x000000000000002c 0x000000000000002c  R      4
  GNU_STACK      0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x0000000000000000 0x0000000000000000  RWE    10
  GNU_RELRO      0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
                 0x00000000000001f0 0x00000000000001f0  R      1
 Section to Segment mapping:
  Segment Sections...
   00
   01     .interp
   02     .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .text .fini .rodata .eh_frame_hdr .eh_frame
   03     .init_array .fini_array .jcr .dynamic .got .got.plt .data .bss
   04     .dynamic
   05     .note.ABI-tag .note.gnu.build-id
   06     .eh_frame_hdr
   07
   08     .init_array .fini_array .jcr .dynamic .got
```

Notice the following part.

```
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  PHDR           0x0000000000000040 0x0000000000400040 0x0000000000400040
                 0x00000000000001f8 0x00000000000001f8  R E    8
  INTERP         0x0000000000000238 0x0000000000400238 0x0000000000400238
                 0x000000000000001c 0x000000000000001c  R      1
      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
  LOAD           0x0000000000000000 0x0000000000400000 0x0000000000400000
                 0x0000000000000694 0x0000000000000694  R E    200000
  LOAD           0x0000000000000e10 0x0000000000600e10 0x0000000000600e10
                 0x000000000000021c 0x0000000000000220  RW     200000
```

"LOAD" is an area that stores information about the memory to be used when executing a program, and the program will start after allocating the necessary memory by looking at the information in "LOAD".
