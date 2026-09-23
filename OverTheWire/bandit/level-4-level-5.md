# Level 4 → Level 5

## Description
```Description
# Bandit Level 4 → Level 5

## Level Goal

The password for the next level is stored in the only human-readable file in the **inhere** directory. Tip: if your terminal is messed up, try the “reset” command.

## Commands you may need to solve this level

[ls](https://manpages.ubuntu.com/manpages/noble/man1/ls.1.html) , [cd](https://manpages.ubuntu.com/manpages/noble/man1/cd.1posix.html) , [cat](https://manpages.ubuntu.com/manpages/noble/man1/cat.1.html) , [file](https://manpages.ubuntu.com/manpages/noble/man1/file.1.html) , [du](https://manpages.ubuntu.com/manpages/noble/man1/du.1.html) , [find](https://manpages.ubuntu.com/manpages/noble/man1/find.1.html)
```
## Credentials


```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit4
Password: 2WmrDFRmJIq3IPxneAaMGhap0pFhF3NJ
```

## Lets Explore
```bash
bandit4@bandit:~/inhere$ ls -la
total 48
drwxr-xr-x 2 root    root    4096 Jul 28 19:03 .
drwxr-xr-x 3 root    root    4096 Jul 28 19:03 ..
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file00
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file01
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file02
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file03
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file04
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file05
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file06
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file07
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file08
-rw-r----- 1 bandit5 bandit4   33 Jul 28 19:03 -file09
bandit4@bandit:~/inhere$ cat ./-file01
�1��56�XI�C�F��AO���0���5֊bandit4@bandit:~/inhere$ cat ./-file02
▒�J�GNzR▒��M$ԞoL�D��*��@��G����bandit4@bandit:~/inhere$ cat ./-file03
�����JY h�㲆��e���.��1▒�!��Owibandit4@bandit:~/inhere$ cat ./-file04
ٯ��GN����Jj▒�C��TnR�z��f�i�fbandit4@bandit:~/inhere$ cat ./-file05
��,��(bandit4@bandit:~/inhere$ cat ./-file06
���dX��bandit4@bandit:~/inhere$ cat ./-file07
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
bandit4@bandit:~/inhere$ cat ./-file08
>�
  � �8��<�L؞���3�����=
����bandit4@bandit:~/inhere$ cat ./-file09
n�G���O�c��A.�i�^�$�rr����bandit4@bandit:~/inhere$ 
```
Got the Password
## Password
```Password
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw
```
