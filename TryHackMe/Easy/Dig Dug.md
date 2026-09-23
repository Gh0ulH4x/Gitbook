## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Turns out this machine is a DNS server - it's time to get your shovels out!
Oooh, turns out, this MACHINE_IP machine is also a DNS server! If we could `dig` into it, I am sure we could find some interesting records! But... it seems weird, this only responds to a special type of request for a `givemetheflag.com` domain?
**Access this challenge** by deploying both the vulnerable machine by pressing the green "Start Machine" button located within this task, and the TryHackMe AttackBox by pressing the  "Start AttackBox" button located at the top-right of the page.
Use some common DNS enumeration tools installed on the AttackBox to get the DNS server on **MACHINE_IP** to respond with the flag.
```

## IP-Address
```IP-Address
10.201.55.191
```

## RustScan
```bash
$ rustscan -a 10.201.55.191 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
```

## Dig 
its an tool used for network and domain reconnaissance 
```bash
$ dig @10.201.55.191 givemetheflag.com
; <<>> DiG 9.20.11-4-Debian <<>> @10.201.55.191 givemetheflag.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 14634
;; flags: qr aa; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;givemetheflag.com.             IN      A

;; ANSWER SECTION:
givemetheflag.com.      0       IN      TXT     "[flag omitted]"

;; Query time: 267 msec
;; SERVER: 10.201.55.191#53(10.201.55.191) (UDP)
;; WHEN: Fri Aug 08 00:12:34 EDT 2025
;; MSG SIZE  rcvd: 86

```

## Got The Flag
```Flag
[flag omitted]
```