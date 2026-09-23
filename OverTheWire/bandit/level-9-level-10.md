# Level 9 → Level 10

## Description
```Description
## Level Goal

The password for the next level is stored in the file **data.txt** in one of the few human-readable strings, preceded by several ‘=’ characters.

## Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit9
Password: 4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```

## Lets Explore
```bash
bandit9@bandit:~$ strings data.txt  | grep =
Pw=h
========== the
C%m=
y7{1Z=
========== passwordb
#[q?=p
F========== is;o|
@[W=
p?e=    v
 K=r
V9V=]
U========== FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
u5=R
```

## Password
```Password
FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey
```
