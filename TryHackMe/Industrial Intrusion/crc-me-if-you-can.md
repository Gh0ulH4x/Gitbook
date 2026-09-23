
# Crypto
```Description 
Three months after the Virelia Water Control Facility was “remediated,” flickering sensors and phantom alerts persist. A covert second-stage implant still lurks, waiting for its kill switch. As a hired red-team specialist for Black Echo, your mission is to forge a legitimate control frame that disables the implant before the real attacker flips it on.
Start the VM attached to this task and use Netcat to interact with the CRC-Oracle and the Control server:
- **Port 1501** – CRC-Oracle: Send any payload (except the kill switch) and get back the exact framed packet: `nc MACHINE_IP 1501`
- **Port 1500** – Control: Send a fully framed packet; get `FAIL` or the flag on success: `nc MACHINE_IP 1500`
You can download the challenge files from `http://MACHINE_IP/files.zip`, which contains:
- - `gateway_proto.py` – CRC-32 stub
    - `open_frame.bin` – example “OPEN” frame
    - `kill_switch.bin` – raw bytes of the shutdown command
```
 IP-Address
 ```bash 
root@ip-10-10-96-139:~# wget http://10.10.153.48/files.zip
--2025-06-28 01:29:25--  http://10.10.153.48/files.zip
Connecting to 10.10.153.48:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 851 [application/zip]
Saving to: \u2018files.zip\u2019
files.zip           100%[===================>]     851  --.-KB/s    in 0s      
2025-06-28 01:29:25 (60.4 MB/s) - \u2018files.zip\u2019 saved [851/851]
root@ip-10-10-96-139:~# unzip files.zip 
Archive:  files.zip
  inflating: gateway_proto.py        
 extracting: open_frame.bin          
 extracting: kill_switch.bin    
```
