## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
The Hackfinitiy high school has been hit by DarkInjector's ransomware, and some of its critical files have been encrypted. We need you and Void to use your crypto skills to find the RSA private key and restore the files. After some research and reverse engineering, you discover they have forgotten to remove some debugging from their code. The ransomware saves this data to the tmp directory.

Click the start machine button below, the VM will open in your browser:
```
```More
Can you find the RSA private key?  
Note:  
**  
You can close the window prompting for a password after the VM has booted; this will not affect the challenge.  
If you close the ransomware note before solving the challenge, you might need to reboot the VM.**
```

First Look at the machine and we Find out that system is encrypted by an ransomeware
which encrypted the whole file system 
lets go to tmp first
```bash
ubuntu@tryhackme:~$ ls -la /tmp
total 96
drwxrwxrwt 17 root   root   12288 Jul  8 07:53 .
drwxr-xr-x 22 root   root    4096 Jul  8 07:43 ..
drwxrwxrwt  2 root   root    4096 Jul  8 07:43 .ICE-unix
-r--r--r--  1 root   root      11 Jul  8 07:43 .X0-lock
-r--r--r--  1 ubuntu ubuntu    11 Jul  8 07:43 .X1-lock
drwxrwxrwt  2 root   root    4096 Jul  8 07:43 .X11-unix
drwxrwxrwt  2 root   root    4096 Jul  8 07:43 .XIM-unix
drwxrwxrwt  2 root   root    4096 Jul  8 07:43 .font-unix
-rw-r--r--  1 ubuntu ubuntu   235 Jul  8 07:44 dock-replace.log
-rw-r--r--  1 root   root      16 Jul  8 07:44 encrypted_aes_key.bin
-rw-r--r--  1 root   root      50 Jul  8 07:44 public_key.txt
drwx------  3 root   root    4096 Jul  8 07:43 snap-private-tmp
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-ModemManager.service-hpTuNP
drwx------  3 root   root    4096 Jul  8 07:44 systemd-private-0b199d29cd9740599db4acb155b24398-colord.service-1T01Nx
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-polkit.service-M8uGfG
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-power-profiles-daemon.service-avBbts
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-switcheroo-control.service-jigZHD
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-systemd-logind.service-wMcXiE
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-systemd-resolved.service-tqW0Mx
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-systemd-timesyncd.service-XnoY3B
drwx------  3 root   root    4096 Jul  8 07:43 systemd-private-0b199d29cd9740599db4acb155b24398-upower.service-j6DxDD
drwx------  2 ubuntu ubuntu  4096 Jul  8 07:43 tigervnc.TuLCHr
```
After that lets see the public_key.txt Content 
```bash 
ubuntu@tryhackme:~$ cat /tmp/public_key.txt 
n=340282366920938460843936948965011886881
e=65537
```
what we are dealing with RSA
## 🔓 Goal: Recover RSA Private Key

You're dealing with **RSA** where `n = p * q`.  
The number `n = 340282366920938460843936948965011886881` is very **small**, so it's easily factorable.
After getting n & e values we can use  [https://www.dcode.fr/cipher-identifier]
in dcode if specifically use [https://www.dcode.fr/rsa-cipher]
where we put n & e values
 And Got Output
```Output
**✘**Wiener's attack: failure  
**✔** P,Q computed with N ((Self-Limited) Prime Factors Decomposition)  
**✔** D computed with P,Q,E  
N is relatively small, try a prime decomposition of N (dCode has tools for that)

|↑↓|↑↓|
|---|---|
|e|65537|
|n|340282366920938460843936948965011886881|
|d|196442361873243903843228745541797845217|
|p|18446744073709551533|
|q|18446744073709551557|
|φ|340282366920938460807043460817592783792|
```
Where d it the decryption value which we insert in GUI interface 
`196442361873243903843228745541797845217`
we got msg that decryption successful and after that we can open files available on `Desktop`
```bash 
ubuntu@tryhackme:~/Desktop$ ls
school_report.docx  student_grades.docx
```
after analyzing files we got Flag in `student_grades.docx`
```Flag
[flag omitted]
```
