# Flip — Writeup

_"Hey, do a flip!"_

**Flag:** `THM{FliP_DaT_B1t_oR_G3t_Fl1pP3d}`

---
## Challenge

- A TCP service (`app-1684527681671.py`, port `1337`) prompts for a username and password, builds the string:
```
access_username=<username>&password=<password>
```

- encrypts it with AES-CBC under a random per-connection key/IV, and hands back the ciphertext ("Leaked ciphertext"). It then asks you to submit a ciphertext of your choosing; it decrypts that with the _same_ key/IV and checks whether the decrypted plaintext contains:
```
admin&password=sUp3rPaSs1
```

If so: flag. If you try to just type that string in as your username/password up front, the server rejects it immediately — it checks the raw input against that exact substring before ever encrypting anything.

```python
def decrypt_data(encryptedParams,key,iv):
    cipher = AES.new(key, AES.MODE_CBC,iv)
    paddedParams = cipher.decrypt(unhexlify(encryptedParams))
    if b'admin&password=sUp3rPaSs1' in unpad(paddedParams,16,style='pkcs7'):
        return 1
    ...

def start(server):
    ...
    message = 'access_username=' + username +'&password=' + password
    if "admin&password=sUp3rPaSs1" in message:
        send_message(server, 'Not that easy :)\nGoodbye!\n')
    else:
        setup(server,username,password,key,iv)
```

So the forbidden string is blocked on the way **in**, but never checked on the way back **out** — only that the final decrypted bytes contain it somewhere. That's the opening for a bit-flipping attack.

---
## Vulnerability: CBC malleability

AES-CBC decryption is:

```
P[i] = AES_Decrypt(C[i]) XOR C[i-1]
```

Two consequences, with no key required:

- Flipping bits in `C[i-1]` produces a **fully predictable**, identical bit-flip in `P[i]`.
- The same flip **destroys** `P[i-1]` (since `C[i-1]` is also the AES-decrypt input for recovering `P[i-1]`), turning it into unrecoverable garbage. That's fine — we just need to not care about that block.

This means we can take any ciphertext we're handed and, without the key, surgically rewrite one specific 16-byte plaintext block into _anything we want_, at the cost of garbling the block immediately before it.

---
## Block layout

`"access_username="` is exactly 16 bytes — a clean block boundary:

|Block|Bytes|Content|
|---|---|---|
|0|0–15|`access_username=`|
|1|16–31|`username`|
|2|32–47|`&password=` + `password[0:6]`|
|3|48–63|`password[6:22]`|
|4+|64+|rest of password + PKCS7 padding|

Target string `admin&password=sUp3rPaSs1` is 26 bytes — it splits neatly as:

- `admin&password=s` → 16 bytes → lands entirely in **block 2**
- `Up3rPaSs1` → 9 bytes → lands in the first 9 bytes of **block 3**

## Why you can't just flip two blocks in a row

To forge block 3 you'd flip `C[2]` — but `C[2]` is also the AES-decrypt input for recovering block 2, so flipping it to fix block 3 destroys block 2 unpredictably (and you have no oracle to learn what it became, so you can't compensate). You cannot cleanly force two _adjacent_ plaintext blocks to both be exact chosen values.

**Workaround:** only forge the _first_ of the two blocks (block 2, via flipping `C[1]`). For the second block (block 3), don't flip anything — instead, choose your own submitted password so that block 3 is **already**, naturally, exactly `Up3rPaSs1XXXXXXX` before any tampering. Since nothing touches `C[2]`, block 3 decrypts normally from what you actually sent.

So the final submitted password is built as:

```
password = "Z"*6 + "Up3rPaSs1" + "X"*7 + "Y"*16
            ^^^^^   ^^^^^^^^^   ^^^^^   ^^^^^^^^
            |       |           |       extra filler so the real PKCS7
            |       |           |       padding block stays undisturbed
            |       |           padding to fill out block 3 (don't-care bytes)
            |       lands naturally in block 3 — NOT flipped, must already be correct
            overwritten entirely by our flip of block 2 — don't-care bytes
```

This message does **not** contain the literal forbidden string anywhere (`username` is junk, and `Up3rPaSs1` here is preceded by `&password=ZZZZZZ`, not `admin&password=s`), so it sails through the initial filter.

---
## The exploit

1. Send `username = "A"*16` (exactly one block, fully disposable).
2. Send the crafted `password` above.
3. Receive the leaked ciphertext; split into 16-byte blocks.
4. Reconstruct the exact plaintext we sent and slice out `P[2]_original = message[32:48]`.
5. Compute `delta = P[2]_original XOR "admin&password=s"`.
6. `C[1]_new = C[1]_original XOR delta`.
7. Replace block 1 in the ciphertext with `C[1]_new`; leave every other block untouched.
8. Submit the modified ciphertext back to the server.

Decryption then yields:

```
...&password=<garbage from block1>admin&password=sUp3rPaSs1XXXXXXX...
                                   \_______________________________/
                                   exact match to the forbidden string
```

— satisfying the `in` check without the server ever seeing that string on the way in.

---
## Solver script
- python
```python
#!/usr/bin/env python3
import socket, sys

HOST = sys.argv[1] if len(sys.argv) > 1 else "127.0.0.1"
PORT = int(sys.argv[2]) if len(sys.argv) > 2 else 1337
BLOCK = 16

def xor_bytes(a, b):
    return bytes(x ^ y for x, y in zip(a, b))

def recv_until(sock, marker):
    data = b""
    while marker not in data:
        chunk = sock.recv(4096)
        if not chunk:
            break
        data += chunk
    return data

def main():
    s = socket.create_connection((HOST, PORT))
    recv_until(s, b"username: ")

    username = "A" * 16
    s.sendall((username + "\n").encode())
    recv_until(s, b"password: ")

    password = "Z"*6 + "Up3rPaSs1" + "X"*7 + "Y"*16
    s.sendall((password + "\n").encode())

    leak_line = recv_until(s, b"\n")
    hex_ct = leak_line.decode().split("Leaked ciphertext: ")[1].strip()
    ct = bytes.fromhex(hex_ct)
    blocks = [ct[i:i+BLOCK] for i in range(0, len(ct), BLOCK)]

    message = "access_username=" + username + "&password=" + password
    p2_original = message[32:48].encode()
    p2_target = b"admin&password=s"

    delta = xor_bytes(p2_original, p2_target)
    blocks[1] = xor_bytes(blocks[1], delta)
    forged_ct = b"".join(blocks)

    recv_until(s, b"enter ciphertext: ")
    s.sendall((forged_ct.hex() + "\n").encode())

    print(s.recv(4096).decode(errors="replace"))
    s.close()

if __name__ == "__main__":
    main()
```

## Run

```bash
python3 solve.py <host> 1337
```

## Result

```
Leaked ciphertext: fb54ce0e2d64c2dc24caea310e4844e3a15344d67de60a95cb008f006322369...
[*] Ciphertext has 6 blocks
No way! You got it!
A nice flag for you: THM{FliP_DaT_B1t_oR_G3t_Fl1pP3d}
```

---
## Takeaways

- CBC mode provides confidentiality, **not integrity** — a ciphertext can be tampered with in a precisely controlled way without the key, as long as you can tolerate garbling the block immediately before your target.
- Checking attacker-controlled plaintext for a forbidden substring _before_ encryption is meaningless if the same plaintext can be reconstructed through a malleability attack on the _returned_ ciphertext.
- Fix: use an authenticated mode (AES-GCM, or CBC + HMAC verified before decryption) so any ciphertext tampering is detected and rejected before decryption ever happens.