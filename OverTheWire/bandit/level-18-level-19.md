# Level 18 → Level 19

## Description
```Description
## Level Goal

The password for the next level is stored in a file **readme** in the homedirectory. Unfortunately, someone has modified **.bashrc** to log you out when you log in with SSH.
## Commands you may need to solve this level
ssh, ls, cat
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit18
Password: x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
```

## Let's Explore
```bash
└─$ ssh bandit18@bandit.labs.overthewire.org   -p 2220         
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO
  Enjoy your stay!

Byebye !
Connection to bandit.labs.overthewire.org closed.
```

Got `Byebye!` because someone just screw up with the .bashrc so we findout the alternative
```bash
$ ssh bandit18@bandit.labs.overthewire.org -p 2220 cat readme
                         _                     _ _ _   
                        | |__   __ _ _ __   __| (_) |_ 
                        | '_ \ / _` | '_ \ / _` | | __|
                        | |_) | (_| | | | | (_| | | |_ 
                        |_.__/ \__,_|_| |_|\__,_|_|\__|
                                                       

                      This is an OverTheWire game server. 
            More information on http://www.overthewire.org/wargames

bandit18@bandit.labs.overthewire.org's password: x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO

Content of readme
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8   
```

Sending Cat command is worth it we Got the Password

## Password
```Password
cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8
```
