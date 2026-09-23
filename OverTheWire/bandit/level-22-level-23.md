# Level 22 → Level 23

## Description
```Description
## Level Goal

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE:** Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.

## Commands you may need to solve this level

cron, crontab, crontab(5) (use “man 5 crontab” to access this)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit22
Password: tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```

## Let's Explore
Before Start we also know about this machine that its also related to cron jobs
```bash
bandit22@bandit:~$ ls -la /etc/cron.d/
total 60
drwxr-xr-x   2 root root  4096 Jul 28 19:07 .
drwxr-xr-x 132 root root 12288 Jul 30 17:08 ..
-r--r-----   1 root root    47 Jul 28 19:04 behemoth4_cleanup
-rw-r--r--   1 root root   123 Jul 28 18:57 clean_tmp
-rw-r--r--   1 root root   120 Jul 28 19:03 cronjob_bandit22
-rw-r--r--   1 root root   122 Jul 28 19:03 cronjob_bandit23
-rw-r--r--   1 root root   120 Jul 28 19:03 cronjob_bandit24
-rw-r--r--   1 root root   201 Apr  8  2024 e2scrub_all
-r--r-----   1 root root    48 Jul 28 19:05 leviathan5_cleanup
-rw-------   1 root root   138 Jul 28 19:05 manpage3_resetpw_job
-rwx------   1 root root    52 Jul 28 19:07 otw-tmp-dir
-rw-r--r--   1 root root   102 Mar 31  2024 .placeholder
-rw-r--r--   1 root root   396 Jan  9  2024 sysstat
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

After analyzing we got that password is present in directory /tmp/ and the file name is encoded by md5sum which is 
```bash
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
bandit22@bandit:~$ echo I am user bandit23 | md5sum | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:~$ cd /tmp/8ca319486bfbbc3663ea0fbe81326349
-bash: cd: /tmp/8ca319486bfbbc3663ea0fbe81326349: Not a directory
bandit22@bandit:~$ cat /tmp 8ca319486bfbbc3663ea0fbe8132^C
bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
bandit22@bandit:~$ 
```

Got the Password
## Password
```Password
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```
