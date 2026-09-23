```Hello there,  
  
I am the CEO and one of the co-founders of futurevera.thm. In Futurevera, we believe that the future is in space. We do a lot of space research and write blogs about it. We used to help students with space questions, but we are rebuilding our support.  

Recently blackhat hackers approached us saying they could takeover and are asking us for a big ransom. Please help us to find what they can takeover.  
  
Our website is located at [https://futurevera.thm](https://futurevera.thm/)

Hint: Don't forget to add the 10.10.35.82 in /etc/hosts for futurevera.thm ; )
``` 
This CTF description

IP Address  and what i change 
```bash 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

nano /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.10.35.82     futurevera.thm
                                 
```
Then Browse the website at  https://futurevera.thm
and I didn't find anything in source code and javaScript, then I launch Gobuster to find thing 
but its domain enumeration its necessary to find sub Domain rather than directory so 
#Sub_Domain_Enumeration 
``` bash 
gobuster vhost -u https://futurevera.thm/ \ 
  -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt \
  --append-domain \
  --no-tls-validation \
  -t 50 \
  -o vhost_results.txt

===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
===============================================================
Found: support.futurevera.thm Status: 421 [Size: 411]
Found: blog.futurevera.thm Status: 421 [Size: 408]
Progress: 4989 / 4990 (99.98%)
```
Then I found 2 more Sub-Domain, And I inserted these two in /etc/hosts 
```bash
sudo nano /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.10.35.82     futurevera.thm
10.10.35.82     blog.futurevera.thm support.futurevera.thm

```
Then I surf on Browser and I go on browser and go to https://blog.futurevera.thm & https://support.futurevera.thm 
And we find nothing there to in website 
then  I Check SSL certificate and i Found out a new Sub-Domain 
```Bash 
openssl s_client -connect support.futurevera.thm:443 -servername support.futurevera.thm </dev/null 2>/dev/null | openssl x509 -noout -text | grep -A1 "Subject Alternative Name"

            X509v3 Subject Alternative Name: 
                DNS:secrethelpdesk934752.support.futurevera.thm
                                                                   
```
then again
```Bash 
Sudo nano /etc/hosts
127.0.0.1       localhost
127.0.1.1       kali
::1             localhost ip6-localhost ip6-loopback
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
10.10.35.82     futurevera.thm
10.10.35.82     blog.futurevera.thm support.futurevera.thm 
10.10.35.82     secrethelpdesk934752.support.futurevera.thm
```
and then I surf on Browser at https://secrethelpdesk934752.support.futurevera.thm but its redirect me to https://futurevera.thm then i just changed one thing changed from https to http then i surf on browser at http://secrethelpdesk934752.support.futurevera.thm and I got redirected to  
```Redirect 
http://[flag omitted].s3-website-us-west-3.amazonaws.com/
```
and flag was 
```flag 
[flag omitted]
```
