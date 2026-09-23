# Level 19 → Level 20

## Description
```Description
## Level Goal

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.

## Helpful Reading Material

- [setuid on Wikipedia](https://en.wikipedia.org/wiki/Setuid)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit19
Password: cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```

## Let's Explore
```bash
bandit19@bandit:~$ ls -la
total 36
drwxr-xr-x   2 root     root      4096 Jul 28 19:03 .
drwxr-xr-x 150 root     root      4096 Jul 28 19:06 ..
-rwsr-x---   1 bandit20 bandit19 14884 Jul 28 19:03 bandit20-do
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Jul 28 18:47 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
bandit19@bandit:~$ file bandit20-do 
bandit20-do: setuid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, BuildID[sha1]=35d353cf6d732f515a73f50ed205265fe1e68f90, for GNU/Linux 3.2.0, not stripped
```

Now Lets Try Something Else Becuase we Know strings & Direct cat Doesnt work
```bash
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
bandit19@bandit:~$ 
```

We Already know we are taking about SUID and here `bandit20-do` can actually run command as `Bandit20` user
Got the Password
## Password
```Password
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```
