# Level 6 → Level 7

## Description
```Description
## Level Goal
The password for the next level is stored **somewhere on the server** and has all of the following properties:

- owned by user bandit7
- owned by group bandit6
- 33 bytes in size
## Commands you may need to solve this level
```
## Credentials

```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit6
Password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
```

Lets Explore
```bash
ssh bandit6@bandit.labs.overthewire.org -p 2220
Password: HWasnPhtq9AVKe0dmk45nxy20cvUa6EG
bandit6@bandit:/$ find / -type f -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
bandit6@bandit:/$ cat /var/lib/dpkg/info/bandit7.password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
bandit6@bandit:/$ 
```
## Password
```Password
morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj
```
