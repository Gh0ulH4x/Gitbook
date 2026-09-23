## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Investigate the potential kernel backdoor implanted within the compromised system.
A high-value system has been compromised. Security analysts have detected suspicious activity within the kernel, but the attacker’s presence remains hidden. Traditional detection tools have failed, and the intruder has established deep persistence. Investigate a live system suspected of running a kernel-level backdoor.
```

## Kernel Forensic
🔍 1. Check loaded modules
```bash
lsmod | grep spatch
```
**Result:**
```bash
spatch                 12288  0
```

Suspicious module `spatch` is loaded.

---

### 🧾 2. Locate the kernel object file

```bash
find /lib/modules/$(uname -r)/ -type f -name '*.ko' | xargs grep -l spatch
```

**Result:**
```bash
/lib/modules/6.8.0-1016-aws/kernel/drivers/misc/spatch.ko
```

---
### 🧵 3. Strings analysis

```bash
`strings /lib/modules/$(uname -r)/kernel/drivers/misc/spatch.ko | less`
```

Interesting strings found:

```bash
get_flagH9 /tmp/cipher_output.txt /bin/sh echo "COMMAND" > /proc/cipher_bd 6[CIPHER BACKDOOR] Here's the secret: 54484d7b73757033725f736e33346b795f643030727d0a description=Cipher is always root
```

This reveals:

- A **custom /proc entry** `/proc/cipher_bd` exists.
- The module uses **usermodehelper** to execute `/bin/sh` commands from this interface.
- A **hardcoded flag** or **hex-encoded string** is visible.

---

### 🐚 4. Check `/proc/cipher_bd`

```bash
ls -l /proc/cipher_bd
```

If it exists and writable:

```bash
echo "whoami" > /proc/cipher_bd cat /tmp/cipher_output.txt
```

---

## ✅ Proof of Concept (PoC)

We’ll now **exploit the kernel module** by sending it a command via `/proc/cipher_bd`, and retrieving the result from `/tmp/cipher_output.txt`.

```bash
# Exploit the backdoor echo "id" > /proc/cipher_bd sleep 1 cat /tmp/cipher_output.txt
```

**Output:**

```bash
uid=0(root) gid=0(root) groups=0(root)
```

🧠 This proves that arbitrary **root-level shell commands** can be executed through the malicious module.

---

## 🎯 Flag Extraction

From `strings`, this was observed:

```bash
Here's the secret: 54484d7b73757033725f736e33346b795f643030727d0a
```

Let's decode it:

```bash
echo "54484d7b73757033725f736e33346b795f643030727d0a" | xxd -r -p
```

**Flag:**
```Flag
[flag omitted]
```