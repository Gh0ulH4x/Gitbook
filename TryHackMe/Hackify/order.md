## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
We intercepted one of Cipher's messages containing their next target. They encrypted their message using a repeating-key XOR cipher. However, they made a critical error—every message always starts with the header:
```

```
ORDER:
```
Can you help void decrypt the message and determine their next target?  
Here is the message we intercepted:

## Encrypted Message
```Encrypted
First =
`1c1c01041963730f31352a3a386e24356b3d32392b6f6b0d323c22243f6373`

Second =
`1a0d0c302d3b2b1a292a3a38282c2f222d2a112d282c31202d2d2e24352e60`
```

## Source Code 
```Python 
from binascii import unhexlify

def is_printable(s):
    return all(32 <= c < 127 for c in s)

def xor_decrypt(ct, key):
    return bytearray([b ^ key[i % len(key)] for i, b in enumerate(ct)])

# Ciphertexts
c1 = bytearray.fromhex("1c1c01041963730f31352a3a386e24356b3d32392b6f6b0d323c22243f6373")

c2 = bytearray.fromhex("1a0d0c302d3b2b1a292a3a38282c2f222d2a112d282c31202d2d2e24352e60")

# Known plaintext for message 1
pt1 = b"ORDER: Attack at dawn. Target: "
  
# Recover the repeating key
key = bytearray([c1[i] ^ pt1[i] for i in range(len(pt1))])
  
# Try all alignments on message 2
for offset in range(len(key)):
    shifted_key = key[offset:] + key[:offset]
    decrypted = xor_decrypt(c2, shifted_key)
    if is_printable(decrypted):
    print(f"Possible flag (offset {offset}):", decrypted.decode())
```

## Output of the Code
```All_Possible_Flags
Possible flag (offset 0): ICIqfbxTlkqa{bjcfsBcmmzy~cke~w3 
`Possible flag (offset 1): [flag omitted]3 `
Possible flag (offset 2): _LGi~un[bsivmmd{~dTlcubnhle}f}. 
Possible flag (offset 6): ICIqfbxTlkqa{bjcfsBcmmzy~~`ate9 
Possible flag (offset 7): THM{the_hackfinity_highs~cke~w3 
Possible flag (offset 8): _LGi~un[bsivmmd{~dTlcubschool}. 
Possible flag (offset 12): ICIqfbxTlkqa{bjcfsB~fipkt~`ate9 
Possible flag (offset 13): THM{the_hackfinityBcmmzy~cke~w3 
Possible flag (offset 14): _LGi~un[bsivmmd{~y_highschool}. 
Possible flag (offset 15): [FUcc~jQpyt}igvq~dTlcubnhle}f`% 
Possible flag (offset 19): THM{the_hack{bjcfsBcmmzy~cke~w3 
Possible flag (offset 20): _LGi~un[bsikfinity_highschool}. 
Possible flag (offset 21): [FUcc~jQpyivmmd{~dTlcubnhle}f`% 
Possible flag (offset 25): THM{thxTlkqa{bjcfsBcmmzy~cke~w3 
Possible flag (offset 26): _LGi~he_hackfinity_highschool}. 
Possible flag (offset 27): [FUc~un[bsivmmd{~dTlcubnhle}f`%
```

There is an Pattern which indicates the flag
```Flag 
[flag omitted]
```