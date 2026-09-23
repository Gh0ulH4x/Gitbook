# Level 8 → Level 9

## Description
```Description
## Level Goal

The password for the next level is stored in the file **data.txt** and is the only line of text that occurs only once

## Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

## Helpful Reading Material

- [Piping and Redirection](https://ryanstutorials.net/linuxtutorial/piping.php)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit8
Password: dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc
```
## Lets Explore
```bash
$ sort data.txt | uniq -u
4CKMh1JI91bUIZZPXDqGanal4xvAg0
```

## Password
```Password
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM
```
