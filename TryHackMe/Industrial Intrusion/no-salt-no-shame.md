# Crypto
_Flags have been omitted from this writeup per platform guidelines._


```Description 
To “secure” the maintenance logs, Virelia’s gateway vendor encrypted every critical entry with AES-CBC—using the plant’s code name as the passphrase and a fixed, all-zero IV. Of course, without any salt or integrity checks, it’s only obscurity, not true security. Somewhere in those encrypted records lies the actual shutdown command.

**Passphrase:** `VIRELIA-WATER-FAC`

Download the encrypted log file attached to this task and get the flag!
```
File name `shutdown.log-1750934543756.enc`
and After giving Description to `chatgpt` we got decode code 
`Challenge Summary
- **Encryption**: AES-CBC    
- **Passphrase**: `VIRELIA-WATER-FAC`
- **IV**: 16 bytes of zero (`\x00` * 16)    
- **Salt/Integrity**: None (no protection → vulnerable to decryption if you have the key)
```python.py
from Crypto.Cipher import AES
from hashlib import sha256

# Replace with your encrypted file
with open("encrypted_log.bin", "rb") as f:
    ciphertext = f.read()

# Derive AES key from passphrase
passphrase = "VIRELIA-WATER-FAC"
key = sha256(passphrase.encode()).digest()

# Fixed IV: 16 bytes of zero
iv = b"\x00" * 16

# Decrypt
cipher = AES.new(key, AES.MODE_CBC, iv)
plaintext = cipher.decrypt(ciphertext)

# Try to decode and find the flag
print(plaintext.decode(errors="ignore"))
```

after running this code 
```bash 
python3 python.py shutdown.log-1750934543756
```
I got the Flag 
Flag 
```Flag
[flag omitted]
```
