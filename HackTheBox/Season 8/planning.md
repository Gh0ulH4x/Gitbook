## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Machine Information

As is common in real life pentests, you will start the Planning box with credentials for the following account: admin / [password omitted]
```

## Credentials
```Credentials
IP-Address: 10.10.11.68
Username: admin
Password: [password omitted]
```

## RustScan
```bash
$ rustscan -a 10.10.11.68 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63
```
## Gobuster
```bash

```