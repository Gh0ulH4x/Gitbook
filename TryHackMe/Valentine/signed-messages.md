## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
Their messages are secret, unless you find the key.
My Dearest Hacker,

LoveNote built it's reputation on trust. Every message, every action, signed and verified by the system itself. LoveNoe claims that no message can be forged, no identity faked.

Yet an internal leak suggests the platform may be trusting something it shouldn't. With Valentine's Day fast approaching, the consequences of a broken trust system could be disastrous.

You can find the web application here: `http://10.49.144.44:5000`
```
## IP-Address
```IP-Address
http://10.49.144.44:5000
```
## Web
```Welcome 
System Administrator
@admin                                                    2026-02-16 01:50:30
Welcome to LoveNote
Welcome to LoveNote! Send encrypted love messages this Valentine's Day. Your communications are secured with industry-standard RSA-2048 digital signatures.
```
- Found a Page `Debug`
```bash
System Debug Logs
[2026-02-06 14:23:15] Development mode: ENABLED
[2026-02-06 14:23:15] Using deterministic key generation
[2026-02-06 14:23:15] Seed pattern: {username}_lovenote_2026_valentine
[DEBUG] Seed converted to bytes for cryptographic processing
[DEBUG] Seed hashed using SHA256 to produce large numeric material
[DEBUG] Prime derivation step 1:
[DEBUG] Converting SHA256(seed) into a large integer
[DEBUG] Checking consecutive integers until a valid prime is reached
[DEBUG] Prime p selected
[DEBUG] Prime derivation step 2:
[DEBUG] Modifying seed with PKI-related constant (SHA256(seed + b"pki"))
[DEBUG] Hashing modified seed with SHA256
[DEBUG] Converting hash into a large integer
[DEBUG] Checking consecutive integers until a valid prime is reached
[DEBUG] Prime q selected
[2026-02-06 14:23:16] RSA modulus generated from p × q
[2026-02-06 14:23:16] RSA-2048 key pair successfully constructed
[2026-02-06 14:23:17] Public and private keys saved to disk
```
- Note 
```bash
Development Notice
This debug endpoint shows internal system logs from the key generation service. The logs reveal implementation details that should not be exposed in production.
```
- Target 
```Target
need to find out the digital signature HEX 
- username - System Administrator (@admin)
- message 
Welcome to LoveNote! Send encrypted love messages this Valentine's Day. Your communications are secured with industry-standard RSA-2048 digital signatures.
- digital signature HEX
```
## Digital Signature
```Sign
019dca5e86a524db6f435aeaf759362ea0b49c30ffb87e1af7a9e12e3d8fd56a789e9d861fda41a915fe34aecddb7b71b28134bc93bfaba8c15755dea3cddd11
```
## Flag
```Flag
[flag omitted]
```
## END
