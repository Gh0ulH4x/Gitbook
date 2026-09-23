
**Scenario:** A KAPE triage collection was pulled from a guest laptop (Room 214, registered to "Vera") before the machine was wiped. **Flag:** `[flag omitted]`

_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

---

## 1. Initial Triage

The provided archive is a **KAPE** (Kroll Artifact Parser and Extractor) collection rooted at `KAPE/C/`, mirroring a live Windows filesystem:

```
KAPE/C/Windows/System32/config/     ← SAM, SYSTEM, SOFTWARE, SECURITY, DEFAULT hives
KAPE/C/Users/vera/                  ← NTUSER.DAT, Documents\backup (100 MB, no extension)
KAPE/C/Users/vera/AppData/Local/Microsoft/Windows/UsrClass.dat
KAPE/C/Users/vera/AppData/Roaming/Microsoft/Protect/<SID>/  ← DPAPI masterkey files
KAPE/C/Users/vera/AppData/Local/Google/Chrome For Testing/User Data/  ← full browser profile
```

Two things immediately stood out:

1. **`Documents\backup`** — exactly 104,857,600 bytes (100 MB), no file extension, and **uniformly high entropy (~7.9999 bits/byte)** across the whole file — the signature of an encrypted container, not a normal document.
2. A full **Chrome ("Chrome For Testing") profile**, including `History`, `Login Data`, `Web Data`, and `Preferences` — plus the DPAPI `Protect` folder needed to decrypt anything Chrome encrypted at rest.

---

## 2. Following Vera's Browser Trail

Querying Chrome's `History` SQLite database (`urls` table) in visit order showed a short, telling session:

|Time (rel.)|URL|Title|
|---|---|---|
|1|`http://bytelotus.thm:8080/login`|Error response|
|2|`http://bytelotus.thm:8080/`|**SecureVault Portal**|
|3|google: "chrome cves"||
|4|google: "how to exfiltrate data red teaming"||
|5|google: "tryhackme"||

Vera visited a **"SecureVault Portal"** on host `bytelotus.thm`, then searched for exploits and exfiltration techniques — behavior consistent with someone poking at (or working for) an internal target, and not too careful about covering her tracks.

Checking Chrome's `Login Data` (the saved-password store) turned up a saved credential for that exact portal:

```
origin: http://bytelotus.thm:8080/
username: VeraSecretVault
password: <encrypted, v10 prefix — AES-256-GCM, Windows DPAPI-protected>
```

This matches the story hint exactly — _"a browser will remember things for you that you never told anyone else"_ — Chrome had the password saved, we just needed to decrypt it.

---

## 3. Decrypting the DPAPI Chain

Chrome on Windows encrypts saved passwords with a per-profile AES key that is itself wrapped by **Windows DPAPI**, tied to the logged-in user's password. To recover the saved password we need, in order:

1. **The user's logon password** (used to derive the DPAPI "prekey")
2. **The DPAPI masterkey file** (`AppData\Roaming\Microsoft\Protect\<SID>\<GUID>`)
3. **Chrome's `Local State`** file (holds the DPAPI-wrapped AES key)
4. **Chrome's `Login Data`** (holds the AES-GCM–encrypted password itself)

### 3.1 Cracking Vera's account password

The KAPE collection included the SAM/SYSTEM hives, so the local NTLM hash for `vera` was dumped offline with `impacket`'s `secretsdump` helpers:

```python
from impacket.examples.secretsdump import LocalOperations, SAMHashes
lo = LocalOperations('SYSTEM')
bootkey = lo.getBootKey()
sh = SAMHashes('SAM', bootkey, isRemote=False)
sh.dump()
```

```
vera:1000:[lmhash omitted]:[nthash omitted]:::
```

That NTLM hash cracked instantly against `rockyou.txt` with `hashcat -m 1000`:

```
[hash omitted]:[password omitted]
```

### 3.2 Decrypting the DPAPI masterkey

With the account SID (`S-1-5-21-2529683458-431225740-1723070931-1000`, taken from the `Protect` folder name) and the cracked password, `pypykatz` derives the DPAPI "prekey" candidates and uses them to decrypt the masterkey file:

```bash
pypykatz dpapi prekey password <SID> [password omitted] -o prekey.txt
pypykatz dpapi masterkey <Protect>/<GUID> prekey.txt -o mk_decrypted.txt
```

This yields the decrypted 64-byte DPAPI masterkey for Vera's profile.

### 3.3 Decrypting the Chrome saved password

`pypykatz` has a dedicated helper that combines the masterkey, Chrome's `Local State`, and `Login Data` to decrypt saved credentials directly:

```bash
pypykatz dpapi chrome mk_decrypted.txt "Local State" --logindata "Login Data"
```

```
user: VeraSecretVault
pass: [password omitted]
url: http://bytelotus.thm:8080/login
```

_(This lines up with the second story hint — "some things aren't as locked away as she thought" — a chain of Windows-native protections (NTLM → DPAPI → Chrome's own encryption) unraveled entirely offline, without ever touching a live system.)_

---

## 4. Identifying and Cracking the Hidden Container

The recovered password strongly resembles a **VeraCrypt** volume password (and the username is literally "vera" — a fitting hint). VeraCrypt volumes are deliberately headerless / indistinguishable from random data, which matches the `backup` file's entropy profile perfectly.

### 4.1 Why standard tools didn't work here

`cryptsetup --type tcrypt --veracrypt` (the usual Linux tool for mounting VeraCrypt volumes) requires the kernel's `algif_skcipher` crypto API, which isn't available inside this sandboxed analysis environment (no privileged kernel module access). So the volume header had to be decrypted **in pure Python** instead.

### 4.2 Manually decrypting the VeraCrypt header

VeraCrypt volume header layout (first 512 bytes of the file):

|Offset|Size|Field|
|---|---|---|
|0|64|Salt|
|64|448|Encrypted header area (AES-XTS, "sector 0")|

Header key derivation (standard, non-system, no custom PIM):

```
PBKDF2-HMAC-SHA512(password, salt, iterations=500000, dklen=64)
  → key1 = dk[0:32]   (AES data key)
  → key2 = dk[32:64]  (AES tweak key, for XTS)
```

The encrypted header is then decrypted with **AES-XTS**, and success is verified by checking for the `VERA` magic string at the start of the plaintext:

```python
dk = hashlib.pbkdf2_hmac('sha512', b'[password omitted]', salt, 500000, dklen=64)
key1, key2 = dk[:32], dk[32:]
header = xts_decrypt(key1, key2, encrypted_header, sector=0)
assert header[:4] == b'VERA'   # ✅ matched on first try
```

It matched immediately — confirming the password and default KDF parameters (SHA-512, 500,000 iterations, single AES-256 cipher).

Parsing the rest of the decrypted header gives the volume geometry and the **master key** used to encrypt the actual data (not just the header):

```
vol_size              = 104,595,456 bytes
data_offset            = 131,072   (encrypted data starts here)
encrypted_area_size    = 104,595,456 bytes
sector_size            = 512
master_key (AES-256 XTS) = 64 bytes (data key + tweak key)
```

### 4.3 Decrypting the whole volume

Using the recovered master key, every 512-byte sector of the volume is decrypted with AES-XTS (tweak based on absolute sector number). A vectorized NumPy implementation (batching per-sector tweak XOR + a single bulk AES-ECB call per chunk) decrypts the full 100 MB volume in about **10 seconds**:

```python
decrypt_volume('backup', '[password omitted]', 'vc_full.img')
```

The output is a valid disk image:

```
$ file vc_full.img
vc_full.img: DOS/MBR boot sector ... FAT (32 bit) ... unlabeled
```

---

## 5. Mounting and Extracting the Payload

The recovered FAT32 image was browsed directly with `mtools` (no `mount` privileges needed):

```bash
$ mdir -i vc_full.img -/
::/secret_financial_documents/
    important_invoice_byte_lotus.pdf
    transactions_q3.csv
::/$RECYCLE.BIN/
::/System Volume Information/
```

`transactions_q3.csv` was a mundane decoy — plausible-looking vendor transactions for "Byte Lotus" properties.

`important_invoice_byte_lotus.pdf`, however, embeds a rasterized invoice image. Extracting it (`pdfimages -all`) and reading the line-item description reveals the flag rendered directly into the "invoice":

```
NO.  DESCRIPTION                              QTY   PRICE   TOTAL
1.   Flag: [flag omitted]         1    $100    $100
```

Careful glyph comparison against the rest of the document (this PDF uses a monospace font) confirmed the exact characters:

- `1` (digit, has a top serif/flag) vs. `l` (lowercase L, plain vertical stroke) — confirmed against "Hote**l** Cleaning" elsewhere in the invoice.
- `0` vs. `O` — visually identical in this font (no slash), resolved using the leetspeak pattern already established in the rest of the flag (`1t`=it, `w4s`=was, `V3r4`=Vera, `A11`=All, `Al0ng`=Along).

**Flag:** `[flag omitted]` — _"It was Vera all along?!"_

---

## 6. Attack / Investigation Chain Summary

```
KAPE triage collection
        │
        ▼
Chrome History → visited bytelotus.thm:8080 "SecureVault Portal"
        │
        ▼
Chrome Login Data → saved credential "VeraSecretVault" (DPAPI-encrypted)
        │
        ▼
SAM hive → vera's NTLM hash → cracked with rockyou.txt → "[password omitted]"
        │
        ▼
DPAPI masterkey (Protect folder) decrypted using "[password omitted]"
        │
        ▼
Local State + Login Data decrypted → password:
   "[password omitted]"
        │
        ▼
Documents\backup (100MB, high entropy) = VeraCrypt container
        │
        ▼
Manual AES-XTS + PBKDF2-HMAC-SHA512 (500,000 iter) header decrypt
        │
        ▼
Master key recovered → full volume decrypted → FAT32 image
        │
        ▼
secret_financial_documents\important_invoice_byte_lotus.pdf
        │
        ▼
[flag omitted]
```

---

## Tools Used

- `unzip`, `file`, `python3` (entropy calculation) — initial triage
- `impacket` (`secretsdump` helpers) — offline SAM/NTLM hash extraction
- `hashcat` + `rockyou.txt` — NTLM password cracking
- `pypykatz` — DPAPI masterkey decryption and Chrome credential decryption
- Custom Python (`hashlib.pbkdf2_hmac`, `pycryptodome` AES-ECB, `numpy`) — from-scratch VeraCrypt header/volume decryption (AES-XTS), since kernel-level `cryptsetup`/`algif_skcipher` wasn't available in the sandbox
- `mtools` (`mdir`, `mcopy`) — reading the recovered FAT32 image without root/mount privileges
- `pdftotext` / `pdfimages` / `Pillow` — extracting and reading the flag image embedded in the PDF