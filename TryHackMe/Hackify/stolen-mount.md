## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
An intruder has infiltrated our network and targeted the NFS server where the backup files are stored. A classified secret was accessed and stolen. The only trace left behind is a packet capture (PCAP) file recorded during the incident. Your mission, should you accept it, is to discover the contents of the stolen data.

Note: Click the **Start Machine** button to spawn the Virtual Machine.

The packet capture (**challenge.pcapng**) is stored in the **~/Desktop** directory.
```

## Packet Monitor /Logs
```bash 
strings challenge.pcapng | uniq 
Linux 6.11.2-arm64
Dumpcap (Wireshark) 4.4.0
eth0	
Linux 6.11.2-arm64
desktop
Linux NFSv4.2 desktop
kernel.org
ALinux 6.11.2-arm64 #1 SMP Kali 6.11.2-1kali1 (2024-10-15) aarch64
	tryhackme
desktop
Linux NFSv4.2 desktop
kernel.org
ALinux 6.11.2-arm64 #1 SMP Kali 6.11.2-1kali1 (2024-10-15) aarch64
	tryhackme
nfs_share
"[}F
65534
"[}F
desktop
s.kY1
	nfs_share
creds.txt
9Archive Password
90eb7723a657b6597100aafef171d9f2 (md5)
hidden_stash.zip
secrets.pngUT	
```
Then open in Wireshark and add filter ` nfs.data` and then right click on the first packet and click on `follow > TCP Stream` and save the file as `Raw Data ` as `Hidden.zip` 
Here it requires Password which we already got through the strings 
`MD5= 90eb7723a657b6597100aafef171d9f2` which crackstation.net gives back output 
`avengers`
Yes Password is avengers
```bash
$ unzip hidden.zip 
Archive:  hidden.zip
warning [hidden.zip]:  36452 extra bytes at beginning or within zipfile
  (attempting to process anyway)
[hidden.zip] secrets.png password: 
password incorrect--reenter: averngers
  inflating: secrets.png 
```


Then Open The Image and Scan on google and got the flag 
```Flag
[flag omitted]
```