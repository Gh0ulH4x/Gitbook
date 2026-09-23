# crypto 
_Flags have been omitted from this writeup per platform guidelines._


```Description
Three months after the Virelia Water Control Facility was breached, OT traffic is finally back online—supposedly “fully remediated.” During a routine audit, Black Echo’s red team intercepted two back‐to‐back telemetry packets between a pump controller and the SCADA server. Curiously, both packets were encrypted under AES‐GCM using the same 16-byte nonce (number used once). The first packet is just regular facility telemetry; the second contains a hidden sabotage command with the kill-switch flag. Your job is to recover that flag and stop the attack.

Each file is formatted as:

`[16 bytes GCM nonce] ∥ [96 bytes ciphertext] ∥ [16 bytes GCM tag]`

We know that the first plaintext (96 bytes) is the facility’s standard telemetry string, exactly:

`BEGIN TELEMETRY VIRELIA;ID=ZTRX0110393939DC;PUMP1=OFF;VALVE1=CLOSED;PUMP2=ON;VALVE2=CLOSED;END;`

The second packet follows the same format but carries the kill switch command and flag. We need you to decrypt the contents of cipher2.bin so that we can recover and disable the kill switch.
```

Done using `Google Colab`
by uploading file on colab 
and run command on colab
```bash 
!pwd
/content
!ls
'cipher1(1).bin'  'cipher2(1).bin'   sample_data
```

then we create a python code to decrypt the Flag 
```python.py
# Load the files
with open("cipher1(1).bin", "rb") as f1, open("cipher2(1).bin", "rb") as f2:
    data1 = f1.read()
    data2 = f2.read()

# Split into nonce (16), ciphertext (96), tag (16)
nonce1, ct1, tag1 = data1[:16], data1[16:112], data1[112:128]
nonce2, ct2, tag2 = data2[:16], data2[16:112], data2[112:128]

# Confirm nonce reuse
if nonce1 != nonce2:
    print("Nonces are different. This attack won't work.")
else:
    print("Nonces are reused. Proceeding with recovery...")

# Known plaintext for cipher1
known_plaintext = b"BEGIN TELEMETRY VIRELIA;ID=ZTRX0110393939DC;PUMP1=OFF;VALVE1=CLOSED;PUMP2=ON;VALVE2=CLOSED;END;"

# Recover second plaintext: pt2 = ct1 ^ ct2 ^ pt1
recovered_plaintext2 = bytes([a ^ b ^ c for a, b, c in zip(ct1, ct2, known_plaintext)])

# Output result
print("Recovered plaintext from sabotage packet:\n")
print(recovered_plaintext2.decode(errors="ignore"))
```

Execute this command next  
```bash
python3 python.py
Nonces are reused. Proceeding with recovery... Recovered plaintext from sabotage packet: BEGIN TELEMETRY VIRELIA;ID=TRX0110393939DC;PUMP=ON;VALVE=OPEN;TEMP=1.0;KILL=[flag omitted]
```
and Got the Flag 
```Flag 
[flag omitted]
```

