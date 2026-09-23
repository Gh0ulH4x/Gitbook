# Level 17 → Level 18

## Description
```Description
## Level Goal

There are 2 files in the homedirectory: **passwords.old and passwords.new**. The password for the next level is in **passwords.new** and is the only line that has been changed between **passwords.old and passwords.new**

**NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19**

## Commands you may need to solve this level

cat, grep, ls, diff
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit17
Password: EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
```

## Let's Explore
```bash
bandit17@bandit:~$ cat /etc/bandit_pass/bandit17
EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
```

First Got the User Password
```Password
EReVavePLFHtFlFsjn3hyzMlvSuSAcRD
```

After getting User Password Look for Next User password
```bash
bandit17@bandit:~$ ls
passwords.new  passwords.old
bandit17@bandit:~$ diff passwords.old passwords.new 
42c42
< Vhc9EKhF5JOIiQsrq2hjyvDaALKaGKCh
---
> x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

First file is old and next file is new and .new file is the password

## Password
```Password
x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```
