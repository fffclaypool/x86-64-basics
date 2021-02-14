## Registers and arithmetic operations
Using vim, create a text file named add.s and write the following in it.

```Assembly
	.globl	main
main:
	add $1, %rax
        ret
```

Then, use gcc to convert it to an executable file.

```
$ gcc add.s 
$ ll
total 16
-rw-rw-r--. 1 fff_claypool fff_claypool   45 Feb 13 08:18 add.s
-rwxrwxr-x. 1 fff_claypool fff_claypool 8272 Feb 13 08:18 a.out
```

Next, start gdb with a.out.

```
$ gdb a.out
GNU gdb (GDB) Red Hat Enterprise Linux 7.6.1-120.el7
Copyright (C) 2013 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-redhat-linux-gnu".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>...
Reading symbols from /home/fff_claypool/src/x8664_asm_language/a.out...(no debugging symbols found)...done.
(gdb) start
Temporary breakpoint 1 at 0x4004ed
Starting program: /home/fff_claypool/src/x8664_asm_language/a.out
Temporary breakpoint 1, 0x00000000004004ed in main ()
Missing separate debuginfos, use: debuginfo-install glibc-2.17-322.el7_9.x86_64
(gdb) info registers
rax            0x4004ed 4195565
rbx            0x0      0
rcx            0x400500 4195584
rdx            0x7fffffffe418   140737488348184
rsi            0x7fffffffe408   140737488348168
rdi            0x1      1
rbp            0x0      0x0
rsp            0x7fffffffe328   0x7fffffffe328
r8             0x7ffff7dd5e80   140737351868032
r9             0x0      0
r10            0x7fffffffde60   140737488346720
r11            0x7ffff7a2f460   140737348039776
r12            0x400400 4195328
r13            0x7fffffffe400   140737488348160
r14            0x0      0
r15            0x0      0
rip            0x4004ed 0x4004ed <main>
eflags         0x246    [ PF ZF IF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
gs             0x0      0
(gdb) 
```

The gdb command "info registers" is a command to display the values of registers.

From left to right, it shows the register name, value (hexadecimal), and value (decimal).



```
(gdb) disassemble
Dump of assembler code for function main:
=> 0x00000000004004ed <+0>:     add    $0x1,%rax
   0x00000000004004f1 <+4>:     retq
   0x00000000004004f2 <+5>:     nopw   %cs:0x0(%rax,%rax,1)
   0x00000000004004fc <+15>:    nopl   0x0(%rax)
End of assembler dump.
```

```
=> 0x00000000004004ed <+0>:     add    $0x1,%rax
```

You can see that the line is pointed to by an arrow with =>, as in This indicates the position where the program is currently stopped.

In gdb, there is a command called stepi (step instruction) that executes only one instruction. After executing one instruction, let's display the register again as info register.

```
(gdb) info register
rax            0x4004ed 4195565
rbx            0x0      0
rcx            0x400500 4195584
rdx            0x7fffffffe3d8   140737488348120
rsi            0x7fffffffe3c8   140737488348104
rdi            0x1      1
rbp            0x0      0x0
rsp            0x7fffffffe2e8   0x7fffffffe2e8
r8             0x7ffff7dd5e80   140737351868032
r9             0x0      0
r10            0x7fffffffde20   140737488346656
r11            0x7ffff7a2f460   140737348039776
r12            0x400400 4195328
r13            0x7fffffffe3c0   140737488348096
r14            0x0      0
r15            0x0      0
rip            0x4004ed 0x4004ed <main>
eflags         0x246    [ PF ZF IF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
gs             0x0      0
(gdb) stepi
0x00000000004004f1 in main ()
(gdb) info register
rax            0x4004ee 4195566
rbx            0x0      0
rcx            0x400500 4195584
rdx            0x7fffffffe3d8   140737488348120
rsi            0x7fffffffe3c8   140737488348104
rdi            0x1      1
rbp            0x0      0x0
rsp            0x7fffffffe2e8   0x7fffffffe2e8
r8             0x7ffff7dd5e80   140737351868032
r9             0x0      0
r10            0x7fffffffde20   140737488346656
r11            0x7ffff7a2f460   140737348039776
r12            0x400400 4195328
r13            0x7fffffffe3c0   140737488348096
r14            0x0      0
r15            0x0      0
rip            0x4004f1 0x4004f1 <main+4>
eflags         0x206    [ PF IF ]
cs             0x33     51
ss             0x2b     43
ds             0x0      0
es             0x0      0
fs             0x0      0
gs             0x0      0
(gdb)
```

After executing stepi, we can see that the value of rax has increased by 1. This is the effect of the add instruction: when you execute the instruction add $1, %rax, the number of instructions in the rax register increases by one.

Here is an introduction to the mov instruction.

```Assembly
	.globl	main
main:
	mov $1, %rax
        ret
```

```
(gdb) start
Temporary breakpoint 1 at 0x4004ed
Starting program: /home/fff_claypool/src/x8664_asm_language/a.out
Temporary breakpoint 1, 0x00000000004004ed in main ()
Missing separate debuginfos, use: debuginfo-install glibc-2.17-322.el7_9.x86_64
(gdb) disassemble
Dump of assembler code for function main:
=> 0x00000000004004ed <+0>:     mov    $0x1,%rax
   0x00000000004004f4 <+7>:     retq
   0x00000000004004f5 <+8>:     nopw   %cs:0x0(%rax,%rax,1)
   0x00000000004004ff <+18>:    nop
End of assembler dump.
(gdb) p $rax
$1 = 4195565
(gdb) stepi
0x00000000004004f4 in main ()
(gdb) disassemble
Dump of assembler code for function main:
   0x00000000004004ed <+0>:     mov    $0x1,%rax
=> 0x00000000004004f4 <+7>:     retq
   0x00000000004004f5 <+8>:     nopw   %cs:0x0(%rax,%rax,1)
   0x00000000004004ff <+18>:    nop
End of assembler dump.
(gdb) p $rax
$2 = 1
(gdb)
```

The value of rax will be 1.

Many operations can be performed between registers.

```Assembly
	.globl	main
main:
	mov $1, %rax
	mov $2, %r8
	add %r8, %rax
	ret
```

Then, the value of the r8 register can be added to the rax register.

```
(gdb) start
Temporary breakpoint 1 at 0x4004ed
Starting program: /home/fff_claypool/src/x8664_asm_language/a.out
Temporary breakpoint 1, 0x00000000004004ed in main ()
Missing separate debuginfos, use: debuginfo-install glibc-2.17-322.el7_9.x86_64
(gdb) stepi
0x00000000004004f4 in main ()
(gdb) stepi
0x00000000004004fb in main ()
(gdb) p $rax
$1 = 1
(gdb) p $r8
$2 = 2
(gdb) disassemble
Dump of assembler code for function main:
   0x00000000004004ed <+0>:     mov    $0x1,%rax
   0x00000000004004f4 <+7>:     mov    $0x2,%r8
=> 0x00000000004004fb <+14>:    add    %r8,%rax
   0x00000000004004fe <+17>:    retq
   0x00000000004004ff <+18>:    nop
End of assembler dump.
(gdb) stepi
0x00000000004004fe in main ()
(gdb) p $rax
$3 = 3
(gdb) p $r8
$4 = 2
(gdb)
```

Let's make sure that the value of the rax register is 1 + 2 = 3.

It's subtraction with sub, multiplication with imul, and bitwise and, or, xor with and, or, xor.

```Assembly
	.globl	main
main:
	mov $1, %rax
	mov $2, %r8
	sub %r8, %rax

	mov $3, %rax
	mov $4, %r8
	imul %r8, %rax

	mov $0xaa, %rax
	mov $0x0f, %r8
	and %r8, %rax

	mov $0xaa, %rax
	mov $0x0f, %r8
	or %r8, %rax

	mov $0xaa, %rax
	mov $0x0f, %r8
	xor %r8, %rax

	ret
```

```
(gdb) start
Temporary breakpoint 1 at 0x4004ed
Starting program: /home/fff_claypool/src/x8664_asm_language/a.out
Temporary breakpoint 1, 0x00000000004004ed in main ()
Missing separate debuginfos, use: debuginfo-install glibc-2.17-322.el7_9.x86_64
(gdb) stepi 3
0x00000000004004fe in main ()
(gdb) p $rax
$1 = -1
(gdb) stepi 3
0x0000000000400510 in main ()
(gdb) p $rax
$2 = 12
(gdb) stepi 3
0x0000000000400521 in main ()
(gdb) p /x $rax
$3 = 0xa
(gdb) stepi 3
0x0000000000400532 in main ()
(gdb) p /x $rax
$4 = 0xaf
(gdb) stepi 3
0x0000000000400543 in main ()
(gdb) p /x $rax
$5 = 0xa5
(gdb)
```

gdb's stepi command repeats the command as many times as the number passed as the argument. stepi 3 means to execute three instructions.

### Appendix
We use the "disas" command in gdb to display the assembler code, but by default it is in AT&T notation.

If you want to change to intel notation, run the command as follows.

```
(gdb) show disassembly-flavor
The disassembly flavor is "att".
(gdb) set disassembly-flavor intel
(gdb) show disassembly-flavor
The disassembly flavor is "intel".
```
