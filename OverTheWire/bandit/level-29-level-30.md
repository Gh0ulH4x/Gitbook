# Level 29 → Level 30

## Description
```Description
## Level Goal

There is a git repository at `ssh://bandit29-git@localhost/home/bandit29-git/repo` via the port `2220`. The password for the user `bandit29-git` is the same as for the user `bandit29`.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit29
Password: 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7
```

## Let's Explore
```bash
bandit29@bandit:~$ ls
bandit29@bandit:~$ ls -la
total 20
drwxr-xr-x   2 root root 4096 Jul 28 19:03 .
drwxr-xr-x 150 root root 4096 Jul 28 19:06 ..
-rw-r--r--   1 root root  220 Mar 31  2024 .bash_logout
-rw-r--r--   1 root root 3851 Jul 28 18:47 .bashrc
-rw-r--r--   1 root root  807 Mar 31  2024 .profile
bandit29@bandit:~$ mkdir /tmp/h00dy
mkdir: cannot create directory ‘/tmp/h00dy’: File exists
bandit29@bandit:~$ mkdir /tmp/h000dy
bandit29@bandit:~$ cd /tmp/h000dy
bandit29@bandit:/tmp/h000dy$ git clone ssh://bandit29-git@localhost:2220/home/bandit29-git/repo
Cloning into 'repo'...
The authenticity of host '[localhost]:2220 ([127.0.0.1]:2220)' can't be established.
ED25519 key fingerprint is SHA256:C2ihUBV7ihnV1wUXRb4RrEcLfXC5CXlhmAAM/urerLY.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Could not create directory '/home/bandit29/.ssh' (Permission denied).
Failed to add the host to the list of known hosts (/home/bandit29/.ssh/known_hosts).
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit29-git@localhost's password: 
remote: Enumerating objects: 16, done.
remote: Counting objects: 100% (16/16), done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 16 (delta 2), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (16/16), done.
Resolving deltas: 100% (2/2), done.
bandit29@bandit:/tmp/h000dy$ ls
repo
bandit29@bandit:/tmp/h000dy$ cd repo
bandit29@bandit:/tmp/h000dy/repo$ ls
README.md
bandit29@bandit:/tmp/h000dy/repo$ ls -la
total 16
drwxrwxr-x 3 bandit29 bandit29 4096 Aug 15 08:47 .
drwxrwxr-x 3 bandit29 bandit29 4096 Aug 15 08:47 ..
drwxrwxr-x 8 bandit29 bandit29 4096 Aug 15 08:47 .git
-rw-rw-r-- 1 bandit29 bandit29  131 Aug 15 08:47 README.md
bandit29@bandit:/tmp/h000dy/repo$ cat README.md 
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: <no passwords in production!>

bandit29@bandit:/tmp/h000dy/repo$ got log --oneline
Command 'got' not found, but can be installed with:
apt install got
Please ask your administrator.
bandit29@bandit:/tmp/h000dy/repo$ git log --oneline
ef0ecc7 (HEAD -> master, origin/master, origin/HEAD) fix username
57d742e initial commit of README.md
bandit29@bandit:/tmp/h000dy/repo$ git show ef0ecc7
commit ef0ecc7f61244b1ebf5165e010230d84b7f731a5 (HEAD -> master, origin/master, origin/HEAD)
Author: Ben Dover <noone@overthewire.org>
Date:   Mon Jul 28 19:03:50 2025 +0000

    fix username

diff --git a/README.md b/README.md
index 2da2f39..1af21d3 100644
--- a/README.md
+++ b/README.md
@@ -3,6 +3,6 @@ Some notes for bandit30 of bandit.
 
 ## credentials
 
-- username: bandit29
+- username: bandit30
 - password: <no passwords in production!>
 
bandit29@bandit:/tmp/h000dy/repo$ git show 57d742e
commit 57d742e7ab75d17d5f6cac17134f4f4c186e5e91
Author: Ben Dover <noone@overthewire.org>
Date:   Mon Jul 28 19:03:50 2025 +0000

    initial commit of README.md

diff --git a/README.md b/README.md
new file mode 100644
index 0000000..2da2f39
--- /dev/null
+++ b/README.md
@@ -0,0 +1,8 @@
+# Bandit Notes
+Some notes for bandit30 of bandit.
+
+## credentials
+
+- username: bandit29
+- password: <no passwords in production!>
+
bandit29@bandit:/tmp/h000dy/repo$ git log --oneline --decorate --all
ef0ecc7 (HEAD -> master, origin/master, origin/HEAD) fix username
4a754d1 (origin/dev) add data needed for development
8168418 (origin/sploits-dev) add some silly exploit, just for shit and giggles
57d742e initial commit of README.md
db8b78e add gif2ascii
bandit29@bandit:/tmp/h000dy/repo$ git show 4a754d1
commit 4a754d10ab4e0246b06b76cb0a561257a3b6bf22 (origin/dev)
Author: Morla Porla <morla@overthewire.org>
Date:   Mon Jul 28 19:03:50 2025 +0000

    add data needed for development

diff --git a/README.md b/README.md
index 1af21d3..bc6ad3d 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for bandit30 of bandit.
 ## credentials
 
 - username: bandit30
-- password: <no passwords in production!>
+- password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

Got the Password 
## Password
```Password
qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```
