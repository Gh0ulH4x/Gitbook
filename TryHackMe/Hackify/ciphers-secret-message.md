## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
One of the Ciphers' secret messages was recovered from an old system alongside the encryption algorithm, but we are unable to decode it.

**Order:** Can you help void to decode the message?

**Message** : a_up4qr_kaiaf0_bujktaz_qm_su4ux_cpbq_ETZ_rhrudm
```

## Cipher's Algorithm

```python
from secret import FLAG

def enc(plaintext):
    return "".join(
        chr((ord(c) - (base := ord('A') if c.isupper() else ord('a')) + i) % 26 + base) 
        if c.isalpha() else c
        for i, c in enumerate(plaintext)
    )

with open("message.txt", "w") as f:
    f.write(enc(FLAG))
```

Which can be decrypt using this algorithm by code 
```python
def dec(ciphertext):
    result = ""
    for i, c in enumerate(ciphertext):
        if c.isalpha():
            base = ord('A') if c.isupper() else ord('a')
            result += chr((ord(c) - base - i) % 26 + base)
        else:
            result += c
    return result

cipher = "a_up4qr_kaiaf0_bujktaz_qm_su4ux_cpbq_ETZ_rhrudm"
print("THM{" + dec(cipher) + "}")
```

Got the Flag
```Flag
[flag omitted]
```