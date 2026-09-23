# Level 5 → Level 6

## Description
```Description
## Level Goal
The password for the next level is stored in a file somewhere under the **inhere** directory and has all of the following properties:
- human-readable
- 1033 bytes in size
- not executable

## Commands you may need to solve this level

[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html) , [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html) , [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html) , [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) , [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html) , [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)
```
## Credentials

```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit5
Password: 4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```
## Lets Explore
```bash
bandit5@bandit:~/inhere$ ls -la
total 88
drwxr-x--- 22 root bandit5 4096 Jul 28 19:03 .
drwxr-xr-x  3 root root    4096 Jul 28 19:03 ..
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere00
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere01
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere02
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere03
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere04
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere05
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere06
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere07
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere08
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere09
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere10
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere11
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere12
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere13
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere14
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere15
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere16
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere17
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere18
drwxr-x---  2 root bandit5 4096 Jul 28 19:03 maybehere19
bandit5@bandit:~/inhere$ v
v: command not found
bandit5@bandit:~/inhere$ find /home/bandit6/inhere -type f -size 1033c ! -executable -exec file {} \; | grep "ASCII"
find: ‘/home/bandit6/inhere’: No such file or directory
bandit5@bandit:~/inhere$ find /home/bandit5/inhere -type f -size 1033c ! -executable -exec file {} \; | grep "ASCII"
/home/bandit5/inhere/maybehere07/.file2: ASCII text, with very long lines (1000)
bandit5@bandit:~/inhere$ cat /home/bandit5/inhere/maybehere07/.file2
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
bandit5@bandit:~/inhere$ 
```
Got the Password
## Password
```Password
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```
