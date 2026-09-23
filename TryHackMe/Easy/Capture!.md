## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
# Capture!
Can you bypass the login form?
```
#### Attachment 
- Usernames.txt
- Passwords.txt
#### IP-Address
```IP
10.49.185.44
```
#### Enumeration
- Rust-Scan
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 38:6f:75:03:00:c8:4f:69:d0:d4:83:12:7e:82:1d:e4 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDCMfjI9OC95b4/rIZNsOTcIa8B5gkByBtyxMBIwTFGSuAT0whluF73HnXxzOAnYuuIiL3JROJuF7AwuqX5Wslaest6f9cZDE3bQZQdnqG9dfaxmoEpMOn89RWrw6m+2ktOLscYJnF1Xg0JjQduYen2IYN3oGdTtou9tekLueSnHjiLyWsjMIlWTunbnQQ6MMcko4J4uTLdSTaV9NAJVM26Fotm32d0Ed1Ns82/8AFN7iZxv/vQ/0vWPqAkLVxKGru9f8s0+A0Jzycq5eeqlkXM1TVlr40JsiIXxhLA3zBRWmamrXVx890G7/F8FoO2NvS5txhhtSdS4MWN4ajFwv8AbVy8ZqtJQWlh+NklC9Clh6XM6iWK67PxonVwhaADd9pjLTlAPZ1K9O7DAaJ8X9q1WA+RTIMDvcgNIQr3FudhWm7nSa1ALBEft9QHvgo8VFVgj2YBnFRtkNmIA66FDazy8IMpHUqx/EVHrpnoN+5j7sYf9N/2B3NDW+qZOJoNG7c=
|   256 38:4f:64:8d:4b:9f:03:c5:14:63:d1:0c:6d:1b:b4:b7 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBAl0X/+nFqgaJZ44qr09YRetfidtEsGBTqjh1CyaXiW7xijeDCFb9mtZtoCngOAjmCsZh5ebDueTwsbn9A2CFwo=
|   256 b7:a9:43:3b:11:0a:6a:27:0e:37:56:c1:c4:d6:4c:fa (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPlsgjNdUQmpkA9kCTK4oXafgsUBlrmouPVRb3PnstZj
80/tcp open  http    syn-ack ttl 62 Werkzeug httpd 2.2.2 (Python 3.8.10)
| http-methods:
|_  Supported Methods: HEAD GET OPTIONS
|_http-server-header: Werkzeug/2.2.2 Python/3.8.10
| http-title: Site doesn't have a title (text/html; charset=utf-8).
|_Requested resource was /login
```
- Found the Login Page Directly 
```bash
GET /login HTTP/1.1
Host: 10.49.185.44
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.9
Accept-Encoding: gzip, deflate
Connection: close
```
- Login Request
```bash
POST /login HTTP/1.1
Host: 10.49.185.44
Content-Length: 29
Cache-Control: max-age=0
Origin: http://10.49.185.44
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/147.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.9
Referer: http://10.49.185.44/login
Accept-Encoding: gzip, deflate
Connection: close
username=hello&password=[password omitted]
```
- It Look Easy Brute-Force Room but not so Easy 
- After the Second Request there is a `captcha` which validates the request and it changes so we need to write a script with following things
```bash
Script Requirements
- First Dummy Request
  - Capture captcha from the Response
- Second Legit Request
  - With Solved captcha 
  - Enumerating Username using Username.txt
    - Error: The user '<User>' does not exist
  - Enumerating Password using passwords.txt
    - Error : Invalid password for user '<User>'
```
---
#### Python Script
```python
Uploaded on Github by Named - 'Capture.py'
```
- Result
```bash
$ python Capture.py
[+] USER: natalie
[+] natalie:sk8board
```
- Login Using These Creds and Got the Flag
## Flag
```Flag
[flag/hash omitted]
```
# END