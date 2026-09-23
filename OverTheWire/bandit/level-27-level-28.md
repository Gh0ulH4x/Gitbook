# Level 27 → Level 28

## Description
```Description
## Level Goal

There is a git repository at `ssh://bandit27-git@localhost/home/bandit27-git/repo` via the port `2220`. The password for the user `bandit27-git` is the same as for the user `bandit27`.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit27
Password: upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB
```

## Let's Explore
```bash
bandit27@bandit:~$ mkdir /tmp/h000dy
bandit27@bandit:~$ ls
bandit27@bandit:~$ cd /tmp/h000dy
bandit27@bandit:/tmp/h000dy$ git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
Cloning into 'repo'...
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit27/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit27/.ssh/known_hosts).
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit27-git@localhost's password: 
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), done.
bandit27@bandit:/tmp/h000dy$ ls
repo
bandit27@bandit:/tmp/h000dy$ cd repo
bandit27@bandit:/tmp/h000dy/repo$ ls -la
total 16
drwxrwxr-x 3 bandit27 bandit27 4096 Aug 15 07:59 .
drwxrwxr-x 3 bandit27 bandit27 4096 Aug 15 07:59 ..
drwxrwxr-x 8 bandit27 bandit27 4096 Aug 15 07:59 .git
-rw-rw-r-- 1 bandit27 bandit27   68 Aug 15 07:59 README
bandit27@bandit:/tmp/h000dy/repo$ cat REAd`
> ^C
bandit27@bandit:/tmp/h000dy/repo$ cat REAd
cat: REAd: No such file or directory
bandit27@bandit:/tmp/h000dy/repo$ cat README 
The password to the next level is: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
bandit27@bandit:/tmp/h000dy/repo$ 
```

Got the Password
## Password
```Password
Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```
