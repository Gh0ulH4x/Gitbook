# Level 20 → Level 21

## Description
```Description
## Level Goal

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

**NOTE:** Try connecting to your own network daemon to see if it works as you think

## Commands you may need to solve this level

ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit20
Password: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

## Let's Explore
```bash
bandit20@bandit:~$ ls -la
total 36
drwxr-xr-x   2 root     root      4096 Jul 28 19:03 .
drwxr-xr-x 150 root     root      4096 Jul 28 19:06 ..
-rw-r--r--   1 root     root       220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root     root      3851 Jul 28 18:47 .bashrc
-rw-r--r--   1 root     root       807 Mar 31  2024 .profile
-rwsr-x---   1 bandit21 bandit20 15608 Jul 28 19:03 suconnect
bandit20@bandit:~$ file suconnect 
suconnect: setuid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, BuildID[sha1]=a95f034b2749e585fbeed4f260f85a4b150934c2, for GNU/Linux 3.2.0, not stripped
```

After investigation I get to know I need two Terminals for this so I just used `TMUX` with same `SSH` login
And in one terminal I started the `NetCat` listening and from other I used to start the Sender using `suconnet`

Terminal 1
```bash
bandit20@bandit:~$ nc -l -p 1234
```

Terminal2 
```bash
bandit20@bandit:~$ ./suconnect 1234
```

Then From Listening Machine I send The `bandit20` Password and this happen
Terminal 1
```bash
bandit20@bandit:~$ nc -l -p 1234
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
```

And on `Suconnect`
Terminal 2
```bash
bandit20@bandit:~$ ./suconnect 1234
Read: 0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
Password matches, sending next password
```

And its sends back the Password For the Next user
Terminal 1
```bash
bandit20@bandit:~$ nc -l -p 1234
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO
EeoULMCra2q0dSkYj561DX7s1CpBuOBt                //Password
```
Got the Password
## Password
```Password
EeoULMCra2q0dSkYj561DX7s1CpBuOBt
```
