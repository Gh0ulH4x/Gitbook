```Description
_Flags have been omitted from this writeup per platform guidelines._

ZeroTrace intercepts a stripped-down authentication module running on a remote industrial gateway. Assembly scrolls across glowing monitors as she unpacks the logic behind the plant’s digital checkpoint.

Files materials http://10.10.229.193/auth/auth.zip
MACHINE_IP 9005
```
Overview
```Overview
We’re provided with:
- A remote service: `nc 10.10.33.77 9005`
- A local ELF binary: `auth` (64-bit, dynamically linked, **not stripped** — nice!)
The goal is simple:
> Submit the correct 8-byte "unlock code" to the service and retrieve the flag.
```
Download the File
```bash
wget http://10.10.229.193/auth/auth.zip
```
Unzip the File
```bash 
unzip auth.zip
```
File Info 
```bash
File auth 
ELF 64-bit LSB shared object, x86-64, not stripped
```
Lets Investigate using 
🔬 Static Analysis with Ghidra
Load into Ghidra
Step 1: Locate main()
```bash
Ghidra nicely resolves `main()` thanks to symbol info. Inside `main()`, we see:
`printf("[?] Enter unlock code: "); fgets(local_158, 0x40, stdin); // input from user`
```
`Main()`
```bash 
#include <stdio.h>
#include <string.h>
int main(void) {
    char input[64];                  // Raw user input buffer
    char transformed[8];            // Buffer for transformed first 8 bytes
    char flag_buf[256];             // Flag read buffer
    FILE *fp;
    size_t input_len;
    long canary = *(long *)(__builtin_frame_address(0) + 0x28);
    const char expected[8] = {      // Expected transformed value
        0x01, 0x23, 0x45, 0x67,
        0x89, 0xab, 0xcd, 0xef
    };
    printf("[?] Enter unlock code: ");
    if (fgets(input, sizeof(input), stdin) == NULL) {
        fwrite("Error reading input\n", 1, 0x14, stderr);
        return 1;
    }
    input[strcspn(input, "\r\n")] = '\0';
    input_len = strnlen(input, sizeof(input));
    if (input_len != 8) {
        puts("[!] Access Denied!");
        return 1;
    }
    memcpy(transformed, input, 8);
    for (int i = 0; i < 8; i++) {
        transformed[i] ^= 0x55;
    }
    if (memcmp(transformed, expected, 8) == 0) {
        fp = fopen("flag.txt", "r");
        if (!fp) {
            perror("fopen");
            return 1;
        }
        if (fgets(flag_buf, sizeof(flag_buf), fp) == NULL) {
            fwrite("Error reading flag\n", 1, 0x13, stderr);
        } else {
            printf("[+] Access Granted! Flag: %s", flag_buf);
        }
        fclose(fp);
        return 0;
    } else {
        puts("[!] Access Denied!");
        return 1;
    }
    if (canary != *(long *)(__builtin_frame_address(0) + 0x28)) {
        __stack_chk_fail();
    }
    return 1;
}
```
After Analyzing 
## Reverse the `transform()` Function

The `transform()` logic is trivial:

```python
void transform(char *input, size_t len) {     for (int i = 0; i < len; i++) {         input[i] ^= 0x55;     } }
```
So the unlock code must be: `transform(input) == 0xefcdab8967452301`

> A string that, when XORed byte-by-byte with `0x55`, equals `0xefcdab8967452301`.

## Solving It: XOR Reversal

We simply XOR each byte with `0x55`:
```python
target = [0x01, 0x23, 0x45, 0x67, 0x89, 0xab, 0xcd, 0xef] unlock_code = bytes([b ^ 0x55 for b in target]) print(unlock_code)
```
Output
```python
b'Tv\x102\xdc\xfe\x98\xba'
```
So the unlock code is:
```
\x54\x76\x10\x32\xdc\xfe\x98\xba
```
## 🧪 Sending the Payload

Because of non-printable characters, we send it with `echo -ne`:
```bash
echo -ne '\x54\x76\x10\x32\xdc\xfe\x98\xba\n' | nc 10.10.33.77 9005
```
### ✅ Output:

```css
[+] Access Granted! Flag: [flag omitted]
```

```Flag
[flag omitted]
```
