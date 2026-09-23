# Level 30 → Level 31

##Description
```Description
## Level Goal

There is a git repository at `ssh://bandit30-git@localhost/home/bandit30-git/repo` via the port `2220`. The password for the user `bandit30-git` is the same as for the user `bandit30`.

Clone the repository and find the password for the next level.

## Commands you may need to solve this level
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit30
Password: qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL
```

## Let's Explore
```bash
bandit30@bandit:/tmp/h00dy/repo$ ls
README.md
bandit30@bandit:/tmp/h00dy/repo$ ls -la
total 16
drwxrwxr-x 3 bandit30 bandit30 4096 Aug 15 08:57 .
drwxrwxr-x 3 bandit30 bandit30 4096 Aug 15 08:57 ..
drwxrwxr-x 8 bandit30 bandit30 4096 Aug 15 08:57 .git
-rw-rw-r-- 1 bandit30 bandit30   30 Aug 15 08:57 README.md
bandit30@bandit:/tmp/h00dy/repo$ cat README.md 
just an epmty file... muahaha
bandit30@bandit:/tmp/h00dy/repo$ git tag
secret
bandit30@bandit:/tmp/h00dy/repo$ show secret
Command 'show' not found, but can be installed with:
apt install mailutils-mh  # version 1:3.16-1build1, or
apt install mmh           # version 0.4-6
apt install nmh           # version 1.8-1
Ask your administrator to install one of them.
bandit30@bandit:/tmp/h00dy/repo$ git show secret
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
bandit30@bandit:/tmp/h00dy/repo$ 
```

## Password
```Password
fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy
```
