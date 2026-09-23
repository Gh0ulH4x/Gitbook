# Level 21 → Level 22

## Description
```Description
## Level Goal

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

## Commands you may need to solve this level

cron, crontab, crontab(5) (use “man 5 crontab” to access this)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit21
Password: EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```

## Let's Explore
We already Know its about cronjobs so lets start from there
```bash
bandit21@bandit:~$ ls -l /etc/cron.d/
total 40
-r--r----- 1 root root  47 Jul 28 19:04 behemoth4_cleanup
-rw-r--r-- 1 root root 123 Jul 28 18:57 clean_tmp
-rw-r--r-- 1 root root 120 Jul 28 19:03 cronjob_bandit22
-rw-r--r-- 1 root root 122 Jul 28 19:03 cronjob_bandit23
-rw-r--r-- 1 root root 120 Jul 28 19:03 cronjob_bandit24
-rw-r--r-- 1 root root 201 Apr  8  2024 e2scrub_all
-r--r----- 1 root root  48 Jul 28 19:05 leviathan5_cleanup
-rw------- 1 root root 138 Jul 28 19:05 manpage3_resetpw_job
-rwx------ 1 root root  52 Jul 28 19:07 otw-tmp-dir
-rw-r--r-- 1 root root 396 Jan  9  2024 sysstat
bandit21@bandit:~$ cat /etc/cron.d/cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:~$ ls -la /usr/bin/cronjob_bandit22.sh
-rwxr-x--- 1 bandit22 bandit21 130 Jul 28 19:03 /usr/bin/cronjob_bandit22.sh
bandit21@bandit:~$ nano /usr/bin/cronjob_bandit22.sh
Unable to create directory /home/bandit21/.local/share/nano/: No such file or directory
It is required for saving/loading search history or cursor positions.

bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
```

Now When We know Serious Information, Then Lets Enumerate
```bash
bandit21@bandit:~$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:~$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
bandit21@bandit:~$ ls -la /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
-rw-r--r-- 1 bandit22 bandit22 33 Aug 12 00:21 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
bandit21@bandit:~$ 
```

No need We just Discovered the password Just by redirecting to the file where other file is present to store password

## Password
```Password
tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q
```
