# Level 28 → Level 29

## Description
```Description
## Level Goal

There is a git repository at `ssh://bandit28-git@localhost/home/bandit28-git/repo` via the port `2220`. The password for the user `bandit28-git` is the same as for the user `bandit28`.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit29
Password: Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
```

## Let's Explore
```bash
bandit28@bandit:/tmp/h00dy$ ls -la
total 560
drwxrwxr-x   2 bandit28 bandit28   4096 Aug 15 08:04 .
drwxrwx-wt 338 root     root     565248 Aug 15 08:05 ..
bandit28@bandit:/tmp/h00dy$ git clone ssh://bandit28-git@localhost:2220/home/bandit28-git/repo
Cloning into 'repo'...
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit28/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit28/.ssh/known_hosts).
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit28-git@localhost's password: 
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 9 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (9/9), done.
Resolving deltas: 100% (2/2), done.
bandit28@bandit:/tmp/h00dy$ ls
repo
bandit28@bandit:/tmp/h00dy$ cd repo
bandit28@bandit:/tmp/h00dy/repo$ ls
README.md
bandit28@bandit:/tmp/h00dy/repo$ cat README.md 
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx

bandit28@bandit:/tmp/h00dy/repo$ ls -la
total 16
drwxrwxr-x 3 bandit28 bandit28 4096 Aug 15 08:05 .
drwxrwxr-x 3 bandit28 bandit28 4096 Aug 15 08:05 ..
drwxrwxr-x 8 bandit28 bandit28 4096 Aug 15 08:05 .git
-rw-rw-r-- 1 bandit28 bandit28  111 Aug 15 08:05 README.md
bandit28@bandit:/tmp/h00dy/repo$ 
```
## Checked Git Commits
```bash
bandit28@bandit:/tmp/h00dy/repo$ ls -la
total 16
drwxrwxr-x 3 bandit28 bandit28 4096 Aug 15 08:05 .
drwxrwxr-x 3 bandit28 bandit28 4096 Aug 15 08:05 ..
drwxrwxr-x 8 bandit28 bandit28 4096 Aug 15 08:05 .git
-rw-rw-r-- 1 bandit28 bandit28  111 Aug 15 08:05 README.md
bandit28@bandit:/tmp/h00dy/repo$ git log --oneline
03c4d9b (HEAD -> master, origin/master, origin/HEAD) fix info leak
5ce2fc3 add missing data
428350b initial commit of README.md
bandit28@bandit:/tmp/h00dy/repo$ git show 5ce2fc3
commit 5ce2fc341ca715d03961781a40988477cc95de7d
Author: Morla Porla <morla@overthewire.org>
Date:   Mon Jul 28 19:03:48 2025 +0000

    add missing data

diff --git a/README.md b/README.md
index 7ba2d2f..d4e3b74 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials
 
 - username: bandit29
-- password: <TBD>
+- password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
 
bandit28@bandit:/tmp/h00dy/repo$ 
```

Got The Password
```Password
4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```
