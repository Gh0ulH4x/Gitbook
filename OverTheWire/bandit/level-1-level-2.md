# Level 1 → Level 2

## Description
```Description
# Bandit Level 1 → Level 2

## Level Goal

The password for the next level is stored in a file called **-** located in the home directory

## Commands you may need to solve this level

[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html) , [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html) , [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html) , [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) , [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html) , [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

## Helpful Reading Material

- [Google Search for “dashed filename”](https://www.google.com/search?q=dashed+filename)
- [Advanced Bash-scripting Guide - Chapter 3 - Special Characters](https://linux.die.net/abs-guide/special-chars.html)
```
## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit1
Password: ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
```
## Lets Explore


```bash
~$ ls -la
total 24
-rw-r-----   1 bandit2 bandit1   33 Jul 28 19:03 -
drwxr-xr-x   2 root    root    4096 Jul 28 19:03 .
drwxr-xr-x 150 root    root    4096 Jul 28 19:06 ..
-rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root    root    3851 Jul 28 18:47 .bashrc
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile
bandit1@bandit:~$ cat ./-
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```
this `-` is not access until you used `./` as file is symbol in Linux/Unix it `-` is used for arguments, That's why we need to specify the path to let system know we are talking about file
and the password for next level
## Password
```password
263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```
