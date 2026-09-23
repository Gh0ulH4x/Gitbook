```Description
_Flags have been omitted from this writeup per platform guidelines._

ZeroTrace intercepts a suspicious HMI login module on the plant floor. Reverse the binary logic to reveal the access key and slip past digital defences.

Files materials   http://10.10.229.193/access_granted/access_granted.zip
```

**Category:** Reverse Engineering  
**Difficulty:** Medium  
**Target:** `10.10.30.154:9009`  
**Binary:** `access_granted` (ELF)
Initial Recon - File 
```bash 
Wget http://10.10.229.193/access_granted/access_granted.zip
unzip access_granted.zip
```
File Type 
```bash
root@ip-10-10-9-202:~/access# file access_granted
access_granted: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=e9097542628490c31042bb6a07667b49f6d44c39, for GNU/Linux 3.2.0, not stripped
```
Strings 
```bash
strings access_granted | grep -E '^.{10}$'
[]A\A]A^A_     ← likely binary junk
industrial     ✅ valid 10-char string
crtstuff.c     ← compiler internal string
print_flag     ← function name
```

##### Hint: 
### Best Candidate for `pass`
The string:
`industrial`

lets decode the file using Ghidra 
```Ghidra_access_granted_File_type 
Format: Executable and Linking Format (ELF) ▼ Language: x86:LE: 64: default:gcc Destination Folder: Access Granted:/ Program Name: access granted
```

Code `Main()` 
```Mail()
undefined8 main(void) {
    int iVar1;
    long in_FS_OFFSET;
    char local_38[40];
    long local_10;
    local_10 = *(long *)(in_FS_OFFSET + 0x28);
    setvbuf(stdout, 0, 2, 0);
    setvbuf(stdin, 0, 2, 0);
    printf("Enter the password : ");
    read(0, local_38, 0x1f);  // Read 31 bytes into buffer
    printf("\nprocessing...");
    iVar1 = strncmp(pass, local_38, 10);  // Compare first 10 bytes
    if (iVar1 == 0) {
        puts("Access Granted!");
        print_flag();  // <- FLAG is revealed here
    } else {
        puts("\nWrong Password!");
    }
    if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
        __stack_chk_fail();
    }
    return 0;
}
```
and `Print_Flag()`
```Function
void print_flag(void) {
    FILE *__stream;
    long in_FS_OFFSET;
    char local_98[136];
    long local_10;
    local_10 = *(long *)(in_FS_OFFSET + 0x28);
    __stream = fopen("flag.txt", "r");
    if (__stream == 0) {
        puts("Flag file not found!");
        exit(1);
    }
    fgets(local_98, 0x80, __stream);
    puts(local_98);  // <- Flag is printed here
    fclose(__stream);
    if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
        __stack_chk_fail();
    }
    return;
}
```
After analyzing both codes with strings the best suitable `PASSWORD` is `industrial`

```bash  
root@ip-10-10-9-202:~/access# nc 10.10.30.154 9009
Enter the password : industrial
processing...Access Granted!
[flag omitted]
```
We Got the Flag 
```Flag
[flag omitted]
```
