# Checkmate

## Description
```Description
Exploit weak password practices across Marco’s internal systems to achieve full compromise.
```
## IP-Address
```IP-Address
10.49.167.124
```
## Enumeration
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 bb:5d:67:1b:d5:3f:79:e1:25:2b:93:b5:c0:96:55:e2 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBPoTJM7ZCtP9LnG83V3/2PXJT1dEJaLJgx5NuV1tu3bY6c/sjR4tRKreJaYcQDITfdLNQoqv1PZRuR9jD/92ekg=
|   256 2f:9a:5e:96:28:dc:c0:4c:15:f8:f4:21:0b:af:f1:5b (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIAcLZg4EtNAPuSQsLnfekwOHVIQBRa+RBb7IsUYX8u69
5000/tcp open  http    syn-ack ttl 62 Werkzeug httpd 3.1.6 (Python 3.12.3)
|_http-server-header: Werkzeug/3.1.6 Python/3.12.3
| http-methods:
|_  Supported Methods: GET OPTIONS HEAD
|_http-title: Operation Checkmate
5001/tcp open  http    syn-ack ttl 62 Werkzeug httpd 3.1.6 (Python 3.12.3)
|_http-title: FirewallOS \xE2\x80\x94 Sign in
| http-methods:
|_  Supported Methods: HEAD OPTIONS GET
|_http-server-header: Werkzeug/3.1.6 Python/3.12.3
5002/tcp open  http    syn-ack ttl 62 Werkzeug httpd 3.1.6 (Python 3.12.3)
|_http-server-header: Werkzeug/3.1.6 Python/3.12.3
| http-methods:
|_  Supported Methods: OPTIONS GET HEAD
|_http-title: Engineering Careers
5003/tcp open  http    syn-ack ttl 62 Werkzeug httpd 3.1.6 (Python 3.12.3)
|_http-title: social.thm \xE2\x80\x94 Log in
| http-methods:
|_  Supported Methods: GET HEAD OPTIONS
|_http-server-header: Werkzeug/3.1.6 Python/3.12.3
```
- Port `5000`
```bash
- Level 1 - Instructions
Focus on the intended techniques and clues provided throughout the room. Blind brute-forcing against this main application on port 5000 is out of scope and may trigger a temporary cooldown.
```
###### Using burp
```bash

```
