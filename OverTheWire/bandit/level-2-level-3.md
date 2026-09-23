# Level 2 → Level 3

## Description
```Description
# Bandit Level 2 → Level 3
## Level Goal
The password for the next level is stored in a file called **–spaces in this filename–** located in the home directory
## Commands you may need to solve this level

[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html) , [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html) , [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html) , [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) , [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html) , [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)

## Helpful Reading Material

- [Google Search for “spaces in filename”](https://www.google.com/search?q=spaces+in+filename)
```
## Credentials

```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit2
Password: 263JGJPfgU6LtdEvgfWU1XP5yac29mFx
```

## Lets Explore

```bash
bandit2@bandit:~$ ls
--spaces in this filename--                                                       
bandit2@bandit:~$ ls -la
total 24
drwxr-xr-x   2 root    root    4096 Jul 28 19:03 .                                 drwxr-xr-x 150 root    root    4096 Jul 28 19:06 ..                                -rw-r--r--   1 root    root     220 Mar 31  2024 .bash_logout                      
-rw-r--r--   1 root    root    3851 Jul 28 18:47 .bashrc                           
-rw-r--r--   1 root    root     807 Mar 31  2024 .profile                          
-rw-r-----   1 bandit3 bandit2   33 Jul 28 19:03 --spaces in this filename--       
bandit2@bandit:~$ cat ./--spaces\ in\ this\ filename--                             
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx  
```
## Password
```Password
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
```
