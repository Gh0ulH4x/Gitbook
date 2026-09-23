#CVE-2019-18634
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```
is, at the time of writing, the latest offering from Joe Vennix - the same guy who brought us the security bypass vulnerability that we used in the Security Bypass room. This one is slightly more technical, using a Buffer Overflow attack to get root permissions. It has been patched, but affects versions of sudo earlier than 1.8.26.  

I mentioned briefly that you can add things to the `/etc/sudoers` file in order to give lower-privileged users extra permissions. For this exploit we're more interested in one of the other options available: specifically an option called `pwfeedback`. This option is purely aesthetic, and is usually turned off by default (with the exception of ElementaryOS and Linux Mint - although they will likely now also stop using it). If you have used Linux before then you might have noticed that passwords typed into the terminal usually don't show any output at all; `pwfeedback` makes it so that whenever you type a character, an asterisk is displayed on the screen. Inside the `/etc/sudoers` file it is specified like this:

Here's the catch. When this option is turned on, it's possible to perform a attack on the sudo command. To explain it really simply, when a program accepts input from a user it stores the data in a set size of storage space. A buffer overflow attack is when you enter so much data into the input that it spills out of this storage space and into the next "box," overwriting the data in it. As far as we're concerned, this means if we fill the password box of the sudo command up with a _lot_ of garbage, we can inject our own stuff in at the end. This could mean that we get a shell as root! This exploit works regardless of whether we have any sudo permissions to begin with, unlike in CVE-2019-14287 where we had to have a very specific set of permissions in the first place.
```

## Github Repo
```Repository
https://github.com/saleemrashid/sudo-cve-2019-18634/tree/master
```

Commands For Execution
```bash
gcc -o <output-file> <source-file>
```

#sudo-Buffer-overflow
Command Execution in shell
```bash
./exploit 
[sudo] password for tryhackme: Sorry, try again.
# 
# ls
exploit
# cd /root
# ls
root.txt
# cat root
cat: root: No such file or directory
# cat root.txt
[flag omitted]
```

I got The flag
```flag
[flag omitted]
```