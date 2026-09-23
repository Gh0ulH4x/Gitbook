## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Practice your skills in reversing and get the flag bypassing the login
```
## File
```bash
ls
burp.json                          Downloads     Rooms              Tools
Challenge_1609966715991.Challenge  Instructions  Scripts
CTFBuilder                         Pictures      snap
Desktop                            Postman       thinclient_drives
root@ip-10-48-79-146:~# file Challenge_1609966715991.Challenge 
Challenge_1609966715991.Challenge: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=b80ce38cb25d043128bc2c4e1e122c3d4fbba7f7, for GNU/Linux 3.2.0, not stripped
root@ip-10-48-79-146:~# 
```
## Strings
```bash
strings Challenge_1609966715991.Challenge 
/lib64/ld-linux-x86-64.so.2
strcpy
exit
__isoc99_scanf
puts
printf
__cxa_finalize
strcmp
__libc_start_main
libc.so.6
GLIBC_2.7
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u/UH
Made by H
4non
https://H
github.cH
om/n0obiH
AGB6js5dH
9dkGf
[]A\A]A^A_
Insert your username: 
Welcome
Authentication Error
THM{%d%d}
;*3$"
GCC: (Debian 10.2.0-16) 10.2.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.0
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
Challenge.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
strcpy@@GLIBC_2.2.5
puts@@GLIBC_2.2.5
vuln
_edata
printf@@GLIBC_2.2.5
__libc_start_main@@GLIBC_2.2.5
__data_start
strcmp@@GLIBC_2.2.5
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
__isoc99_scanf@@GLIBC_2.7
exit@@GLIBC_2.2.5
__TMC_END__
_ITM_registerTMCloneTable
__cxa_finalize@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.gnu.build-id
.note.ABI-tag
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.got.plt
.data
.bss
.comment
```
## R2
```bash
$ r2 -A Challenge_1609966715991.Challenge
[x] Analyze all flags starting with sym. and entry0 (aa)
[x] Analyze function calls (aac)
[x] Analyze len bytes of instructions for references (aar)
[x] Check for objc references
[x] Check for vtables
[x] Type matching analysis for all functions (aaft)
[x] Propagate noreturn information
[x] Use -AA or aaaa to perform additional experimental analysis.
[0x000010a0]> afl
0x000010a0    1 42           entry0
0x000010d0    4 41   -> 34   sym.deregister_tm_clones
0x00001100    4 57   -> 51   sym.register_tm_clones
0x00001140    5 57   -> 50   entry.fini0
0x00001180    1 5            entry.init0
0x00001000    3 23           sym._init
0x00001380    1 1            sym.__libc_csu_fini
0x00001050    1 6            sym.imp.printf
0x00001070    1 6            sym.imp.__isoc99_scanf
0x00001030    1 6            sym.imp.strcpy
0x00001060    1 6            sym.imp.strcmp
0x00001040    1 6            sym.imp.puts
0x00001080    1 6            sym.imp.exit
0x00001384    1 9            sym._fini
0x00001320    4 93           sym.__libc_csu_init
0x000012f6    1 31           main
0x00001185    4 260          sym.vuln
0x00001289   10 109          sym.gfl
[0x000010a0]> pdf @ sym.gfl
 ; CALL XREF from main @ 0x1309
\u250c 109: sym.gfl ();
\u2502           ; var int64_t var_8h @ rbp-0x8
\u2502           ; var int64_t var_4h @ rbp-0x4
\u2502           0x00001289      55             push rbp
\u2502           0x0000128a      4889e5         mov rbp, rsp
\u2502           0x0000128d      4883ec10       sub rsp, 0x10
\u2502           0x00001291      c745fcd5c852.  mov dword [var_4h], 0x52c8d5
\u2502       \u250c\u2500< 0x00001298      eb4f           jmp 0x12e9
\u2502       \u2502   ; CODE XREF from sym.gfl @ 0x12f0
\u2502      \u250c\u2500\u2500> 0x0000129a      817dfc788a63.  cmp dword [var_4h], 0x638a78
\u2502     \u250c\u2500\u2500\u2500< 0x000012a1      7542           jne 0x12e5
\u2502     \u2502\u254e\u2502   0x000012a3      c745f8741400.  mov dword [var_8h], 0x1474
\u2502    \u250c\u2500\u2500\u2500\u2500< 0x000012aa      eb30           jmp 0x12dc
\u2502    \u2502\u2502\u254e\u2502   ; CODE XREF from sym.gfl @ 0x12e3
\u2502   \u250c\u2500\u2500\u2500\u2500\u2500> 0x000012ac      817df8302100.  cmp dword [var_8h], 0x2130
\u2502  \u250c\u2500\u2500\u2500\u2500\u2500\u2500< 0x000012b3      7523           jne 0x12d8
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012b5      8b55f8         mov edx, dword [var_8h]
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012b8      8b45fc         mov eax, dword [var_4h]
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012bb      89c6           mov esi, eax
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012bd      488d3d790d00.  lea rdi, qword str.THM__d_d ; 0x203d ; "THM{%d%d}" ; const char *format
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012c4      b800000000     mov eax, 0
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012c9      e882fdffff     call sym.imp.printf         ; int printf(const char *format)
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012ce      bf00000000     mov edi, 0                  ; int status
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   0x000012d3      e8a8fdffff     call sym.imp.exit           ; void exit(int status)
\u2502  \u2502\u254e\u2502\u2502\u254e\u2502   ; CODE XREF from sym.gfl @ 0x12b3
\u2502  \u2514\u2500\u2500\u2500\u2500\u2500\u2500> 0x000012d8      8345f801       add dword [var_8h], 1
\u2502   \u254e\u2502\u2502\u254e\u2502   ; CODE XREF from sym.gfl @ 0x12aa
\u2502   \u254e\u2514\u2500\u2500\u2500\u2500> 0x000012dc      817df80e2700.  cmp dword [var_8h], 0x270e
\u2502   \u2514\u2500\u2500\u2500\u2500\u2500< 0x000012e3      7ec7           jle 0x12ac
\u2502     \u2502\u254e\u2502   ; CODE XREF from sym.gfl @ 0x12a1
\u2502     \u2514\u2500\u2500\u2500> 0x000012e5      8345fc01       add dword [var_4h], 1
\u2502      \u254e\u2502   ; CODE XREF from sym.gfl @ 0x1298
\u2502      \u254e\u2514\u2500> 0x000012e9      817dfc88d077.  cmp dword [var_4h], 0x77d088
\u2502      \u2514\u2500\u2500< 0x000012f0      7ea8           jle 0x129a
\u2502           0x000012f2      90             nop
\u2502           0x000012f3      90             nop
\u2502           0x000012f4      c9             leave
\u2514           0x000012f5      c3             ret
[0x000010a0]> 
```
- Got this 
```Points 
Initial values - mov dword [var_4h], 0x52c8d5
- So:
var_4 = 0x52c8d5
- Then it loops until:
cmp var_4, 0x638a78
- So final value must be:
0x638a78
- Inside that, another loop:
mov var_8 = 0x1474
- Loop until:
cmp var_8, 0x2130
- So final value:
0x2130
# When BOTH match:
mov edx, var_8 mov eax, var_4 printf("THM{%d%d}", eax, edx)
```
## Python3
```python
int("638a78",16)
int("2130",16)
```
## Output
```bash
python3 File.py
65235128496
```
## Flag
```Flag
[flag omitted]
```