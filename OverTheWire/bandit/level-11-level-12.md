# Level 11 → Level 12

## Description
```Description
## Level Goal

The password for the next level is stored in the file **data.txt**, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions

## Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Helpful Reading Material

- [Rot13 on Wikipedia](https://en.wikipedia.org/wiki/ROT13)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit11
Password: dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr
```

## Lets Explore
```bash
bandit11@bandit:~$ ls
data.txt
bandit11@bandit:~$ cat data.txt 
Gur cnffjbeq vf 7k16JArUVv5LxVuJfsSVdbbtaHGlw9D4
bandit11@bandit:~$ tr 'A-Za-z' 'N-ZA-Mn-za-m' <data.txt  > decode.txt
-bash: decode.txt: Permission denied
bandit11@bandit:~$ tr 'A-Za-z' 'N-ZA-Mn-za-m' <data.txt  > /tmp/decode.txt
bandit11@bandit:~$ cat /tmp/decode.txt
The password is 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

#Rot13
```bash
tr 'A-Za-z' 'N-ZA-Mn-za-m' < Filename > Decode.txt
```
## Password
```bash
7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```
