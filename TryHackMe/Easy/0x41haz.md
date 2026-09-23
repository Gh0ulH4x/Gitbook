## 📌 Initial File Analysis
We begin by identifying the binary type:
`file 0x41haz-1640335532346.0x41haz`
### Initial Output:
`ELF 64-bit MSB *unknown arch 0x3e00* (SYSV)`

⚠️ This is suspicious.
- x86-64 should be **LSB (Little Endian)**
- Architecture `0x3e` corresponds to x86-64
- But it was being interpreted incorrectly
This indicates **intentional ELF header manipulation**.

---
## 🧠 Step 1 – Inspect ELF Header
`xxd 0x41haz-1640335532346.0x41haz | head`
We observed:
`7f 45 4c 46 02 02 01`
Breakdown:

|Byte|Meaning|
|---|---|
|7f 45 4c 46|ELF Magic|
|02|64-bit|
|02|Big Endian ❌|
|01|Version|

The endian flag was set to `02` (Big Endian).
For x86-64 it must be:
`01 → Little Endian`

---
## 🔧 Step 2 – Fix the ELF Header
We patched byte at offset `0x05`:
`printf '\x01' | dd of=0x41haz-1640335532346.0x41haz bs=1 seek=5 count=1 conv=notrunc`
Re-check:
`file 0x41haz-1640335532346.0x41haz`
### Correct Output:
`ELF 64-bit LSB pie executable, x86-64, stripped`
Now the binary is valid.

---
## 🔎 Step 3 – Static Analysis
We extract strings:
`strings -t x 0x41haz-1640335532346.0x41haz`
Relevant output:
`2008 ======================= 2020 Hey , Can You Crackme ? 2050 It's jus a simple binary 206b Tell Me the Password : 2088 Is it correct , I don't think so. 20aa Nope 20af Well Done !!`
No password visible.
This means:
- No plain `strcmp("password")`
- Likely manual comparison

---
## 🔍 Step 4 – Locate Success Path
Search where `Well Done !!` is referenced:
`objdump -d binary | grep 20af`
Found at:
`122d: lea 0xe7b(%rip),%rdi  # 20af`
So password check happens **before 0x122d**.

---
## 🔬 Step 5 – Analyze Password Logic
Disassembly shows:
`cmpl $0xd,-0x8(%rbp)`
`0xd = 13`
✔ Password must be 13 characters.

---
### Comparison Loop
`movzbl -0x16(%rbp,%rax,1),%edx movzbl -0x40(%rbp,%rax,1),%eax cmp %al,%dl jne fail`
Meaning:
`if (input[i] != secret[i])     fail`
So the secret is stored at:
`-0x16(%rbp)`

---
## 🔥 Step 6 – Extract Secret From Stack Initialization
Above the loop:
`48 b8 32 40 40 32 35 24 67 66 mov %rax,-0x16(%rbp)  c7 45 f2 73 54 26 40 66 c7 45 f6 4c 00`
### Decode (Little Endian!)

First 8 bytes:
`32 40 40 32 35 24 67 66`
Hex → ASCII:
`2@@25$gf`
Next 4 bytes:
`73 54 26 40`
ASCII:
`sT&@`
Final byte:
`4c`
ASCII:
`L`

---
## ✅ Final Password

`2@@25$gfsT&@L`
Length = 13 ✔

---
## 🧪 Verification

```bash
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

./0x41haz-1640335532346.0x41haz
=======================
Hey , Can You Crackme ?
=======================
It's jus a simple binary

Tell Me the Password :
[password omitted]
Well Done !!
```
Input:
`2@@25$gfsT&@L`
Output:
`Well Done !!`
```Flag
[flag omitted]
```
## END