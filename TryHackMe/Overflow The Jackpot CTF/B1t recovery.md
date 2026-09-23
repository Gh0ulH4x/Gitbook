**Room:** [TryHackMe — CTF: Jackpot Overflow](https://tryhackme.com/room/thm-ctf-jackpot-overflow)

**Category:** Crypto **Files provided:** `challenge.py`, `encrypted.bin` **Flag format:** `THM{...}` **Flag:** _submitted on TryHackMe — omitted here_

### 1. The Challenge Source

```python
import os
from pwn import *

key = os.urandom(4)
flag = b"THM{FAKE_FLAG_FOR_TESTING}"

encrypted = xor(flag, key)

with open("encrypted.bin", "wb") as f:
    f.write(encrypted)
```

The flag is encrypted with `pwntools`' `xor()` helper and a random 4-byte key. `pwn.xor` performs a repeating-key XOR: if the key is shorter than the plaintext, it cycles the key bytes across the whole message (`key[0], key[1], key[2], key[3], key[0], key[1], ...`). `encrypted.bin` is 46 bytes long — i.e. the real flag is 46 bytes, not the 27-byte placeholder shown in the source.

### 2. The Vulnerability

This is a textbook known-plaintext attack against repeating-key XOR:

- The key is only 4 bytes, so it fully repeats every 4 bytes of ciphertext.
- We know the flag must start with `THM{` (the fixed flag format for this platform).
- Since `ciphertext[i] = plaintext[i] XOR key[i % 4]`, we can recover the entire key just from the first 4 bytes:

```
key[i] = ciphertext[i] XOR "THM{"[i]
for i in 0..3
```

Once the 4-byte key is known, it can be XORed cyclically against the whole ciphertext to recover the full plaintext — no brute force of the keyspace (2³² random bytes) is needed at all. The "randomness" of the key is irrelevant once 4 bytes of plaintext are known, because the key length equals the size of the known-plaintext crib.

### 3. Recovering the Flag

```python
data = open('encrypted.bin', 'rb').read()
known = b'THM{'

# Derive the 4-byte repeating key from the known prefix
key = bytes([data[i] ^ known[i] for i in range(4)])
print('key:', key)

# Decrypt the full ciphertext by repeating the key
flag = bytes([data[i] ^ key[i % 4] for i in range(len(data))])
print(flag)
```

Output:

```
key: [omitted]
[flag omitted]
```

### 4. Flag

_submitted on TryHackMe — omitted here_

### Lesson

Repeating-key XOR is only as strong as its key length relative to any known or guessable plaintext. Here, the mandatory flag prefix (`THM{`) exactly matched the key length, so recovering 4 bytes of plaintext was enough to fully break the cipher — regardless of how the key was generated. Proper encryption schemes (e.g. AES in an authenticated mode, or at minimum a key as long as the message with true one-time-pad discipline) don't have this weakness.