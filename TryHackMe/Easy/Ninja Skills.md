## Description
```Description
Practise your Linux skills and complete the challenges.
Let's have some fun with Linux. Deploy the machine and get started.
**This machine may take up to 3 minutes to configure.**
(If you prefer to SSH into the machine, use the credentials new-user as the username and password)
Answer the questions about the following files:
- 8V2L
- bny0
- c4ZX
- D8B3
- FHl1
- oiMO
- PFbD
- rmfX
- SRSq
- uqyw
- v2Vb
- X1Uy
The aim is to answer the questions as efficiently as possible.
```

## IP-Address
```IP-Address
10.201.66.123
```

## SSH
```bash
ssh new0user@10.201.66.123
Pass new-user
```

Now Lets find out these files into the system 
```bash
find / -type f -iname "8V2L" 2>/dev/null
find / -type f -iname "bny0" 2>/dev/null
find / -type f -iname "c4ZX" 2>/dev/null
find / -type f -iname "D8B3" 2>/dev/null
find / -type f -iname "FHl1" 2>/dev/null
find / -type f -iname "oiMO" 2>/dev/null
find / -type f -iname "PFbD" 2>/dev/null
find / -type f -iname "rmfX" 2>/dev/null
find / -type f -iname "SRSq" 2>/dev/null
find / -type f -iname "uqyw" 2>/dev/null
find / -type f -iname "v2Vb" 2>/dev/null
find / -type f -iname "X1Uy" 2>/dev/null
```

Except the Second one we got the All Files Path 
```bash
$ find / -type f -iname "8V2L" 2>/dev/null
/etc/8V2L
$ find / -type f -iname "c4ZX" 2>/dev/null
/mnt/c4ZX
$ find / -type f -iname "D8B3" 2>/dev/null
/mnt/D8B3
$ find / -type f -iname "FHl1" 2>/dev/null
/var/FHl1
$ find / -type f -iname "oiMO" 2>/dev/null
/opt/oiMO
$ find / -type f -iname "PFbD" 2>/dev/null
/opt/PFbD
$ find / -type f -iname "rmfX" 2>/dev/null
/media/rmfX
$ find / -type f -iname "SRSq" 2>/dev/null
/etc/ssh/SRSq
$ find / -type f -iname "uqyw" 2>/dev/null
/var/log/uqyw
$ find / -type f -iname "v2Vb" 2>/dev/null
/home/v2Vb
$ find / -type f -iname "X1Uy" 2>/dev/null
```

Now Lets check To whom they actually belong
```bash
ls -la /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb
```

Got our Response 
```bash
$ ls -la /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb
-rwxrwxr-x 1 new-user new-user   13545 Oct 23  2019 /etc/8V2L
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /etc/ssh/SRSq
-rw-rw-r-- 1 new-user best-group 13545 Oct 23  2019 /home/v2Vb
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /media/rmfX
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /mnt/c4ZX
-rw-rw-r-- 1 new-user best-group 13545 Oct 23  2019 /mnt/D8B3
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /opt/oiMO
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /opt/PFbD
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /var/FHl1
-rw-rw-r-- 1 new-user new-user   13545 Oct 23  2019 /var/log/uqyw
```

we need to identify which file actually contain an IP-Address
```bash
$ grep -HnEo '([0-9]{1,3}\.){3}[0-9]{1,3}' /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb
/opt/oiMO:43:1.1.1.1
```
now we need to find if any file sha1 hash `9d54da7584015647ba052173b84d45e8007eba94` is same as this for this we need to run an for loop 
```bash
 for f in /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb; do
>     sha1sum "$f"
> done | grep '9d54da7584015647ba052173b84d45e8007eba94'
9d54da7584015647ba052173b84d45e8007eba94  /mnt/c4ZX
```
and we got the file 
Now we need to information about the word count of each file which we done using WC command
```bash
 wc -l /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb 
   209 /etc/8V2L
   209 /mnt/c4ZX
   209 /mnt/D8B3
   209 /var/FHl1
   209 /opt/oiMO
   209 /opt/PFbD
   209 /media/rmfX
   209 /etc/ssh/SRSq
   209 /var/log/uqyw
   209 /home/v2Vb
  2090 total
```
There is no such file which actually cross 230 lines which mean the file which we cant detect is the file having 230 lines 
we are right file is actually `bny0` which contain 230+lines also now we need to identify which file has the owner ID 502
for this we going to use for loop
```bash
for f in /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb; do
    echo "$(stat -c '%u %U %n' "$f")"
done
```
by running this loop we know about the owner of every file which is new-user but there is another file missing which we cant locate and that file owned by newer-user that has the file owner ID 502 after some time spending we found the missing file too \
```bash
 find / -uid 502 2>/dev/null
/var/spool/mail/newer-user
/home/newer-user
/X1Uy
```
which is the owned by newer-user
```bash
ls -l /etc/8V2L /mnt/c4ZX /mnt/D8B3 /var/FHl1 /opt/oiMO /opt/PFbD /media/rmfX /etc/ssh/SRSq /var/log/uqyw /home/v2Vb /X1Uy 
-rwxrwxr-x 1 new-user new-user 13545 Oct 23 2019 /etc/8V2L 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /etc/ssh/
-rw-rw-r-- 1 new-user best-group 13545 Oct 23 2019 /home/v2Vb 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /media/rmfX 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /mnt/c4ZX 
-rw-rw-r-- 1 new-user best-group 13545 Oct 23 2019 /mnt/D8B3 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /opt/oiMO 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /opt/PFbD 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /var/FHl1 
-rw-rw-r-- 1 new-user new-user 13545 Oct 23 2019 /var/log/uqyw 
-rw-rw-r-- 1 newer-user new-user 13545 Oct 23 2019 /X1Uy 
```

By this we get to know that file 8V2L is executable by everyone

# END 
