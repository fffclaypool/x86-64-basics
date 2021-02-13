## Hello World in x86-64 Assemby
### Compare asemmbly program to C program
Use nasm to create the program.
The Wikipedia article on nasm states that "the Netwide Assembler (NASM) is an assembler and disassembler for the Intel x86 architecture. It can be used to write 16-bit, 32-bit (IA-32) and 64-bit (x86-64) programs. "

```
$ sudo yum -y install nasm
```

Write Hello World program in C.

```Assembly
;------------------------------------
; hello.s
;   nasm -f elf64 hello.s
;   ld -o hello01 hello.o
;   ./hello
;------------------------------------

bits 64
section .text
global _start

_start:
        xor     eax, eax
        mov     edx, eax
        inc     eax         ; sys_write (01)
        mov     edi, eax    ; stdout    (01)
        mov     dl, len     ; length    (13)
        mov     rsi, msg    ; address
        syscall
        xor     edi, edi    ; return 0
        mov     eax, edi
        mov     al, 60      ; sys_exit
        syscall

section .data
        msg     db      'hello, world', 0x0A
        len     equ     $ - msg
```

Use the following command to perform Assemble and Link.

```
$ nasm -f elf64 hello.s # Use "elf64" to specify 64 bit.
$ ld -s -o hello hello.o
$ ./hello
hello, world
```

Write an equivalent program in C.

```C
  /* helloc.c */
  #include <stdio.h>
  int main(int argc, char* argv[]) {
      puts("hello, world\n");
      return 0;
  }
```

Execute compile.

```
$ gcc -o helloc helloc.c
$ ./helloc 
hello, world
```

The C file is about 16 times larger.

```
$ ll
total 28
-rwxrwxr-x. 1 fff_claypool fff_claypool  504 Feb 12 11:38 hello
-rwxrwxr-x. 1 fff_claypool fff_claypool 8360 Feb 12 11:39 helloc
-rw-rw-r--. 1 fff_claypool fff_claypool  125 Feb 12 11:39 helloc.c
-rw-rw-r--. 1 fff_claypool fff_claypool  864 Feb 12 11:38 hello.o
-rw-rw-r--. 1 fff_claypool fff_claypool  608 Feb 12 11:38 hello.s
$ expr `du -b helloc | awk '{print $1}'` / `du -b hello | awk '{print $1}'`
16
```

### Description of the process
Consider hellol.s which is the first hello.s with a few changes

```Assembly
;------------------------------------
; hellol.s
;   nasm -f elf64 hellol.s
;   ld -o hellol hellol.o
;   ./hellol
;------------------------------------

bits 64
section .text
global _start

_start:
        mov rax, 1      ; sys_write
        mov rdi, 1      ; stdout
        mov rsi, msg    ; address
        mov rdx, len    ; length (13)
        syscall

        mov rax, 60     ; sys_exit
        xor rdi, rdi    ; 0
        syscall

section .data
        msg     db      'hello, world', 0x0A
        len     equ     $ - msg
```

Both hello.s and this hellol.s use two system calls, one to display the string (number 1) and one to terminate the program (number 60).

To invoke a system call on x86-64 Linux, do the following: 
The contents of rcx and r11 are not saved (they are destroyed).

1. Set the system call number to rax.
2. If necessary, set the first argument to rdi.
3. If necessary, set the second argument to rsi.
4. If necessary, set the third argument to rdx.
5. If necessary, set the fourth argument to r10.
6. If necessary, set the fifth argument to r8.
7. If necessary, set the sixth argument to r9.
8. Execute the system call instruction (syscall).

Hellol.s is a program written in a straightforward manner according to the above rules. What the program does is as follows:

1. Set number 1 to rax to display strings in the write system call.
2. As the first argument of write, set 1 to rdi to indicate standard output.
3. As the second argument to write, set the first address of the string to rsi.
4. As the third argument of write, set the number of bytes in the string to rdx.
5. Execute the system call instruction ( syscall ) and display the characters.
6. exit To terminate with a system call, set number 60 to rax.
7. As the first argument of exit, set exit code 0 to rdi.
8. Execute the system call instruction ( syscall ) and exit the program.

### Disassemble
Disassemble the executable "hello" file.
Run the program with the option "-M x86-64,intel" to display it in Intel format.

```
$ objdump -d -M x86-64,intel hello
hello:     file format elf64-x86-64
Disassembly of section .text:
00000000004000b0 <.text>:
  4000b0:       31 c0                   xor    eax,eax
  4000b2:       89 c2                   mov    edx,eax
  4000b4:       ff c0                   inc    eax
  4000b6:       89 c7                   mov    edi,eax
  4000b8:       b2 0d                   mov    dl,0xd
  4000ba:       48 be d0 00 60 00 00    movabs rsi,0x6000d0
  4000c1:       00 00 00 
  4000c4:       0f 05                   syscall 
  4000c6:       31 ff                   xor    edi,edi
  4000c8:       89 f8                   mov    eax,edi
  4000ca:       b0 3c                   mov    al,0x3c
  4000cc:       0f 05                   syscall 
```

The middle column is the binary code that the x86-64 CPU will execute. There are 30 bytes.


```
$ objdump -d -M x86-64,intel hellol
hellol:     file format elf64-x86-64
Disassembly of section .text:
00000000004000b0 <.text>:
  4000b0:       48 b8 01 00 00 00 00    mov    rax,0x1
  4000b7:       00 00 00            
  4000ba:       48 bf 01 00 00 00 00    mov    rdi,0x1
  4000c1:       00 00 00            
  4000c4:       48 be ec 00 60 00 00    mov    rsi,0x6000ec
  4000cb:       00 00 00            
  4000ce:       48 ba 0d 00 00 00 00    mov    rdx,0xd
  4000d5:       00 00 00            
  4000d8:       0f 05                   syscall
  4000da:       48 b8 3c 00 00 00 00    mov    rax,0x3c
  4000e1:       00 00 00            
  4000e4:       48 31 ff                xor    rdi,rdi
  4000e7:       0f 05                   syscall
```

Similarly, the middle column is the binary code that the x86-64 CPU will execute. In the case of hellol, there are 57 bytes. The number of lines is only 8 for hellol compared to 11 for hello, but the binary code is almost twice the size. In the case of hellol, the binary code is almost twice as big as hello's, although the number of lines is only 8. In hello, we take advantage of the fact that the upper 32 bits of the 64-bit registers are automatically cleared to zero (zero expansion) when the 32-bit registers are manipulated. When zero-clearing registers with xor, specifying 32-bit registers also reduces the size by one byte of the REX prefix.

```
$ objdump -d -M x86-64,suffix hello
hello:     file format elf64-x86-64
Disassembly of section .text:
00000000004000b0 <.text>:
  4000b0:       31 c0                   xorl   %eax,%eax
  4000b2:       89 c2                   movl   %eax,%edx
  4000b4:       ff c0                   incl   %eax
  4000b6:       89 c7                   movl   %eax,%edi
  4000b8:       b2 0d                   movb   $0xd,%dl
  4000ba:       48 be d0 00 60 00 00    movabsq $0x6000d0,%rsi
  4000c1:       00 00 00 
  4000c4:       0f 05                   syscall 
  4000c6:       31 ff                   xorl   %edi,%edi
  4000c8:       89 f8                   movl   %edi,%eax
  4000ca:       b0 3c                   movb   $0x3c,%al
  4000cc:       0f 05                   syscall 
```

Also try disassembling hello in the AT&T format, which GNU as uses. The mnemonic is suffixed with a suffix indicating the operand size, and the register names are suffixed with %. Also, the order of the two operands is reversed.

### Appendix
#### System Call List

```
$ ausyscall --dump
Using x86_64 syscall table:
0       read
1       write
2       open
3       close
4       stat
5       fstat
6       lstat
7       poll
8       lseek
9       mmap
10      mprotect
11      munmap
12      brk
13      rt_sigaction
14      rt_sigprocmask
15      rt_sigreturn
16      ioctl
17      pread
18      pwrite
19      readv
20      writev
21      access
22      pipe
23      select
24      sched_yield
25      mremap
26      msync
27      mincore
28      madvise
29      shmget
30      shmat
31      shmctl
32      dup
33      dup2
34      pause
35      nanosleep
36      getitimer
37      alarm
38      setitimer
39      getpid
40      sendfile
41      socket
42      connect
43      accept
44      sendto
45      recvfrom
46      sendmsg
47      recvmsg
48      shutdown
49      bind
50      listen
51      getsockname
52      getpeername
53      socketpair
54      setsockopt
55      getsockopt
56      clone
57      fork
58      vfork
59      execve
60      exit
61      wait4
62      kill
63      uname
64      semget
65      semop
66      semctl
67      shmdt
68      msgget
69      msgsnd
70      msgrcv
71      msgctl
72      fcntl
73      flock
74      fsync
75      fdatasync
76      truncate
77      ftruncate
78      getdents
79      getcwd
80      chdir
81      fchdir
82      rename
83      mkdir
84      rmdir
85      creat
86      link
87      unlink
88      symlink
89      readlink
90      chmod
91      fchmod
92      chown
93      fchown
94      lchown
95      umask
96      gettimeofday
97      getrlimit
98      getrusage
99      sysinfo
100     times
101     ptrace
102     getuid
103     syslog
104     getgid
105     setuid
106     setgid
107     geteuid
108     getegid
109     setpgid
110     getppid
111     getpgrp
112     setsid
113     setreuid
114     setregid
115     getgroups
116     setgroups
117     setresuid
118     getresuid
119     setresgid
120     getresgid
121     getpgid
122     setfsuid
123     setfsgid
124     getsid
125     capget
126     capset
127     rt_sigpending
128     rt_sigtimedwait
129     rt_sigqueueinfo
130     rt_sigsuspend
131     sigaltstack
132     utime
133     mknod
134     uselib
135     personality
136     ustat
137     statfs
138     fstatfs
139     sysfs
140     getpriority
141     setpriority
142     sched_setparam
143     sched_getparam
144     sched_setscheduler
145     sched_getscheduler
146     sched_get_priority_max
147     sched_get_priority_min
148     sched_rr_get_interval
149     mlock
150     munlock
151     mlockall
152     munlockall
153     vhangup
154     modify_ldt
155     pivot_root
156     _sysctl
157     prctl
158     arch_prctl
159     adjtimex
160     setrlimit
161     chroot
162     sync
163     acct
164     settimeofday
165     mount
166     umount2
167     swapon
168     swapoff
169     reboot
170     sethostname
171     setdomainname
172     iopl
173     ioperm
174     create_module
175     init_module
176     delete_module
177     get_kernel_syms
178     query_module
179     quotactl
180     nfsservctl
181     getpmsg
182     putpmsg
183     afs_syscall
184     tuxcall
185     security
186     gettid
187     readahead
188     setxattr
189     lsetxattr
190     fsetxattr
191     getxattr
192     lgetxattr
193     fgetxattr
194     listxattr
195     llistxattr
196     flistxattr
197     removexattr
198     lremovexattr
199     fremovexattr
200     tkill
201     time
202     futex
203     sched_setaffinity
204     sched_getaffinity
205     set_thread_area
206     io_setup
207     io_destroy
208     io_getevents
209     io_submit
210     io_cancel
211     get_thread_area
212     lookup_dcookie
213     epoll_create
214     epoll_ctl_old
215     epoll_wait_old
216     remap_file_pages
217     getdents64
218     set_tid_address
219     restart_syscall
220     semtimedop
221     fadvise64
222     timer_create
223     timer_settime
224     timer_gettime
225     timer_getoverrun
226     timer_delete
227     clock_settime
228     clock_gettime
229     clock_getres
230     clock_nanosleep
231     exit_group
232     epoll_wait
233     epoll_ctl
234     tgkill
235     utimes
236     vserver
237     mbind
238     set_mempolicy
239     get_mempolicy
240     mq_open
241     mq_unlink
242     mq_timedsend
243     mq_timedreceive
244     mq_notify
245     mq_getsetattr
246     kexec_load
247     waitid
248     add_key
249     request_key
250     keyctl
251     ioprio_set
252     ioprio_get
253     inotify_init
254     inotify_add_watch
255     inotify_rm_watch
256     migrate_pages
257     openat
258     mkdirat
259     mknodat
260     fchownat
261     futimesat
262     newfstatat
263     unlinkat
264     renameat
265     linkat
266     symlinkat
267     readlinkat
268     fchmodat
269     faccessat
270     pselect6
271     ppoll
272     unshare
273     set_robust_list
274     get_robust_list
275     splice
276     tee
277     sync_file_range
278     vmsplice
279     move_pages
280     utimensat
281     epoll_pwait
282     signalfd
283     timerfd
284     eventfd
285     fallocate
286     timerfd_settime
287     timerfd_gettime
288     accept4
289     signalfd4
290     eventfd2
291     epoll_create1
292     dup3
293     pipe2
294     inotify_init1
295     preadv
296     pwritev
297     rt_tgsigqueueinfo
298     perf_event_open
299     recvmmsg
300     fanotify_init
301     fanotify_mark
302     prlimit64
303     name_to_handle_at
304     open_by_handle_at
305     clock_adjtime
306     syncfs
307     sendmmsg
308     setns
309     getcpu
310     process_vm_readv
311     process_vm_writev
312     kcmp
313     finit_module
314     sched_setattr
315     sched_getattr
316     renameat2
317     seccomp
318     getrandom
319     memfd_create
320     kexec_file_load
321     bpf
322     execveat
323     userfaultfd
324     membarrier
325     mlock2
326     copy_file_range
327     preadv2
328     pwritev2
329     pkey_mprotect
330     pkey_alloc
331     pkey_free
332     statx
333     io_pgetevents
334     rseq
```