```Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

Objective
After participating in one too many incident response activities, PicoSecure has decided to conduct a threat simulation and detection engineering engagement to bolster its malware detection capabilities. You have been assigned to work with an external penetration tester in an iterative purple-team scenario. The tester will be attempting to execute malware samples on a simulated internal user workstation. At the same time, you will need to configure PicoSecure's security tools to detect and prevent the malware from executing.
Following the **Pyramid of Pain's** ascending priority of indicators, your objective is to increase the simulated adversaries' cost of operations and chase them away for good. Each level of the pyramid allows you to detect and prevent various indicators of attack.
```

Room Prerequisites
```Rooms
- The Pyramid of Pain - https://tryhackme.com/room/pyramidofpainax
- MITRE - https://tryhackme.com/room/mitre
Connection Details
Please click **Start Machine** to deploy the application, and navigate to [https://10-10-240-118.p.thmlabs.com](https://10-10-240-118.p.thmlabs.com/) once the URL has been populated.
```
On Visiting `https://10-10-240-118.p.thmlabs.com` I got a Mail on my Inbox 
```MAIL
Hey there.
I'm Sphinx, and I will be working with you on conducting threat simulation and detection engineering tests. I will attempt to execute malware samples on a simulated compromised user account to see if PicoSecure's security tools can detect the attacks.
This will be an iterative process; as your detection methods become more sophisticated, I will upgrade my malware samples to increase the difficulty of detection.
I will start with something simple, using "sample1.exe".
Scan this file using the Malware Sandbox tool and review the generated report. Maybe there's a unique way for you to distinguish this file and add a detection rule to block it. Once you manage to do so, I'll be in touch again.
Tip: You can access the various security tools by toggling the side menu (click the menu icon  in the top left). You can revert your progress anytime with the  "Revert Room" option in the side menu.
-Sphinx 
with Attachment = Sample.exe(sandbox)
```
which redirect us to `https://10-10-240-118.p.thmlabs.com/sandbox` where we have tools like 
- Malware Sandbox
- Detect Hashes
- Firewall Rule Manager
- DNS Rule Manager
-  Sigma Rule Builder

```WORKING
- Malware Sandbox
Select a file from the drop-down menu. The automated analysis engine will execute the suspicious file on the target sandbox system to detect malware and malicious behaviour.

- Detect Hashes
Manually add a hash to the blocklist
If you've discovered a hash value related to a malicious file or executable, you can submit it here. Submitted hashes will automatically update PicoSecure's EDR detection signatures and improve its ability to detect and block similar threats.

- Firewall Rule Manager
Create an IP firewall rule
Welcome to PicoSecure's host-based firewall manager. You can view existing configuration rules from this management console or add a new rule using the configuration wizard.
These firewall rules control incoming or outgoing traffic to and from an endpoint.

- DNS Rule Manager
Create a DNS filter rule
Welcome to PicoSecure's host-based DNS traffic manager. You can view existing configuration rules from this management console or add a new rule using the configuration wizard.
The DNS rules configured below control incoming or outgoing traffic to and from an endpoint.

- Sigma Rule Builder
Rule Builder
Welcome to PicoSecure's custom Sigma rule builder, powered by Sigma GPT!
This wizard will walk you through various options to generate a Sigma rule that will be automatically deployed to PicoSecure's SIEM solution.
```

Firstly we use `Malware Sandbox` with  `Sample1.exe` 
```Result_SAMPLE1.EXE
General Info -             sample1.exe
File Name	               sample1.exe
File Size                  202.50 KB
File Type	               PE32+ executable (GUI) x86-64, for MS Windows
Analysis Date	           September 5, 2023
OS	                       Windows 10x64 v1803
Tags	                   Trojan.Metasploit.A
MIME	                   application/x-dosexec
MD5	                       cbda8ae000aa9cbe7c8b982bae006c2a
SHA1	                   83d2791ca93e58688598485aa62597c0ebbf7610
SHA256	           9c550591a25c6228cb7d74d970d133d75c961ffed2ef7180144859cc09efca8c
```
After Exploring with the `Sample1.exe`, we used `Detect Hashes` with `MD5` and block the Execution of `Sample1.exe`  and got 1 mail with the First flag 
```Mail
Hey again,
Good work. That detection you added blocked my malware from executing. Since file hashes and digests are unique to each file, they are, by far, the highest confidence indicators out there. You can be sure it's my malware sample the next time you see that hash.
However, by design, that is also one of the significant downfalls of simply relying on hashes for detection mechanisms. Since they are so susceptible to change, I only need to alter a single bit of the file, and the detection rule you added will fail.
In fact, all I did this time was recompile the malware, and I generated a new file hash and executed it without issue. See if you can come up with a new way to detect sample2.exe !
 Here's your flag: [flag omitted]
-Sphinx 
With Attachkment = Sample2.exe with Sandbox 
```
First Flag
```Flag1
[flag omitted]
```
Then again start Working with the `SAmple2.exe` with `Malware Detection` / `Sandbox` and got the output 
```Result_SAMPLE.EXE
General Info -             sample2.exe
File Name	               sample2.exe
File Size	               202.73 KB
File Type	               PEXE - PE32+ executable (GUI) x86-64, for MS Windows
Analysis Date	           September 5, 2023
OS	                       Windows 10x64 v1803
Tags	                   Trojan.Metasploit.A
MIME	                   application/x-dosexec
MD5	                       4d661bf605d6b0b15915a533b572a6bd
SHA1	                   6878976974c27c8547cfc5acc90fb28ad2f0e975
SHA256	           d576245e85e6b752b2fdffa43abaab1b2e1383556b0169fd04924d6cebc1cdf9
```
Then We use `Detect Hashes` with `MD5` 
which gives us error 
`You'll have to use something other than hashes for the next sample.`
then we look with `SHA1` & `SHA256` we got same output after scroll down on `Malware Detection Sandbox` I got Some more Useful Results 
```Results_SAMPLE.EXE
HTTP       requests
PID       Process	   Method	    IP	         URL
1927	  sample2.exe	GET	154.35.10.113:4444	http://154.35.10.113:4444/uvLk8YI32
Connections
PID	     Process	     IP	           Domain	             ASN
1927	sample2.exe	154.35.10.113:4444	 -	        Intrabuzz Hosting Limited
1927	sample2.exe	40.97.128.3:443      -	          Microsoft Corporation
1927	sample2.exe	40.97.128.4:443	     -	          Microsoft Corporation
```
After Analyzing above result its clearly shows we have  to use `Firewall Rule Manager` and need to create a new rule to block the `SAMPLE2.EXE` to get inside the system 
```Create_New_Rule
Create Firewall Rule
Type:                     Egress
Source IP:*               Any
Destination IP:*          154.35.10.113
Action:                   Deny
```
Next, Received the `New Mail` from `Sphinx` 

```Mail 
Huh.
It seems like you stopped me again. You must have found the IP address to which my malware sample connected. Clever!
This method isn't bulletproof, though, as it's trivial for a motivated adversary to get around it using a new public IP address. I just signed up for a cloud service provider and now have access to many more public IPs!
This time, you'll need to detect sample3.exe another way. I already have my server running from a new IP address and have plenty more backups to failover in case they get blocked!
Good luck. 😈
Here's your flag: [flag omitted]
-Sphinx
With Attachment Sample3.exe  
```

Flag 
```Flag2
[flag omitted]
```
and then we start working on `Sample3.exe` with `Malware Detection` we got output
```Result_SAMPLE3.EXE
General Info -             sample3.exe
File Name	               sample3.exe
File Size	               207.12 KB
File Type	               PEXE - PE32+ executable (GUI) x86-64, for MS Windows
Analysis Date	           September 5, 2023
OS	                       Windows 10x64 v1803
Tags	                   Trojan.Metasploit.A
MIME	                   application/x-dosexec
MD5	                       e31f0c62927d9d5a897b4c45e3c64dbc
SHA1	                   a92d3de6b1e3ab295f10587ca75f15318cb85a7b
SHA256	           acb9b1260bcd08a465f9f300ac463b9b1215c097ebe44610359bb80881fe6a05
```
Network output
```
HTTP requests
PID	     Process	       Method	      IP	                 URL
1021	 sample3.exe	    GET	     62.123.140.9:1337	http://emudyn.bresonicz.info:1337/kzn293la
1021	sample3.exe	        GET	     62.123.140.9:80	http://emudyn.bresonicz.info/backdoor.exe

Connections
PID	     Process	         IP	             Domain	                 ASN
1021	sample3.exe	 40.97.128.4:443	services.microsoft.com	 Microsoft Corporation
1021	sample3.exe	 62.123.140.9:1337	emudyn.bresonicz.info	 XplorIta Cloud Services
1021	sample3.exe	 62.123.140.9:80	emudyn.bresonicz.info	 XplorIta Cloud Services
2712	backdoor.exe 62.123.140.9:80	emudyn.bresonicz.info	XplorIta Cloud Services
DNS requests
Domain	                                IP
services.microsoft.com	            40.97.128.4
emudyn.bresonicz.info	            62.123.140.9
```
After Analyzing this we discover that we need to create a `DNS Filter Rule` as domains are present 
```DNS-NEW-RULE 
Create DNS Rule
Rule Name:*                   Sample3.exe
Category:                     Malware
Domain Name*                  emudyn.bresonicz.info
Action:                       Deny
Denying a domain will automatically include all its subdomains, but denying a subdomain will not automatically include the main domain. Deny rules will take precedence over any preconfigured allow rules.
```
After creating a new Domain we got 
`Nice work! The DNS filter rule prevented sample3.exe from connecting to the tester's command-and-control server. Check your inbox for the next steps`
and another `MAIL` from `Sphinx` 
```Mail
Greetings again,
It looks like you were able to block my domain this time because every new IP address I try gets detected. You're causing me a bit of trouble now because I have to purchase and register some new domain names and modify DNS records. Some attackers might get mildly annoyed by this and find a new target, but I'm motivated to continue like many.
This time - blocking hashes, IPs, or domains won't help you. If you want to detect sample4.exe, consider what artifacts (or changes) my malware leaves on the victim's host system.
Good luck.
 Here's your flag: [flag omitted]
-Sphinx
with Another Attachment Sample4.exe  
```
Flag
```FLag3
[flag omitted]
```
and then we start working on `Sample4.exe` with `Malware Detection` we got output
```GENERAL_SAMPLE4.EXE
General Info  -             sample4.exe
File Name	                sample4.exe
File Size	                219.46 KB
File Type	                PEXE - PE32+ executable (GUI) x86-64, for MS Windows
Analysis Date	            September 5, 2023
OS	                        Windows 10x64 v1803
Tags	                    None
MIME	                    application/x-dosexec
MD5	                        5f29ff19d99fe244eaf5835ce01a4631
SHA1	                    cd12d2328f700ae1ba1296a5f011bfc5a49f456d
SHA256	           a80cffb40cea83c1a20973a5b803828e67691f71f3c878edb5a139634d7dd422
```
Network Activity
```Network_SAMPLE4.EXE
HTTP(S) requests         TCP/UDP connections          DNS requests      Threats
2                            3                             1              0
                       HTTP requests
PID	         Process	       Method	         IP	              URL
3806	  sample4.exe	        GET	      102.23.20.118:1337	http://cranes0ft.iniware.xyz:1337/ab9z83ja
3806	  sample4.exe	        GET	      102.23.20.118:80	http://cranes0ft.iniware.xyz/backdoor.exe
                     Connections
PID	         Process	  IP	             Domain	                 ASN
3806	 sample4.exe	102.23.20.118:1337	cranes0ft.iniware.xyz	XplorIta Cloud Services
3806	 sample4.exe	102.23.20.118:80	cranes0ft.iniware.xyz	XplorIta Cloud Services
1367	 backdoor.exe	102.23.20.118:80	cranes0ft.iniware.xyz	XplorIta Cloud Services
```
DNS Activity 
```SAMPLE4.EXE
DNS      requests
Domain	                          IP
cranes0ft.iniware.xyz	       102.23.20.118
```
Registry Activity 
```Registry_SAMPLE4.EXE
Total events           Read events           Write events         Delete events
3                        1                       2                     0
Modification events
(PID) Process: (3806) sample4.exe	           Key:                                        HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection
Operation: write	                     Name: DisableRealtimeMonitoring
Value: 1

(PID) Process: (1928) explorer.exe	           Key:                                      HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced
Operation: write	                     Name: EnableBalloonTips
Value: 1

(PID) Process: (9876) notepad.exe	           Key:                                 HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts\.txt
Operation: read	                         Name: Progid
Value: txtfile
```
After Analyzing all the factors and Activity and previous Samples its clear that it doesn't work in `Detect Hashes` & `Firewall Rule Manager` & `DNS Rule Manager`. so we Move on to `Sigma Rule Builder`
as our next work 
we need to create an Sigma Rules Builder 
```STEPS
Step 1:  I want to create a rule that focuses on:
System Event logs | Web Server Logs  |  VPN Logs  |  Application logs
Step 2: Sysmon Event > Logs I want to target this Sysmon event: 
Process Creation | File Creation & Modification | Network Connection | Registry modification
Step 3: Registry Modifications
Set the rule conditions and options:
Registry Key:*       HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection
Registry Name:*      DisableRealtimeMonitoring
Value:*               1
ATT&CK ID:*          Defense Evasion (TA0005)
At PicoSecure, we require that all Sysmon detection rules map to the MITRE ATT&CK framework. This ensures that our SOC team has the context to facilitate a more effective threat detection, analysis, and response.

STEPS = Sysmon Event > Registry Modifications > Set the rule conditions
```
Output 
```Output
title: Modification of Windows Defender Real-Time Protection
id: windows_registry_defender_disable_realtime
description: |
  Detects modifications or creations of the Windows Defender Real-Time Protection DisableRealtimeMonitoring registry value.
references:
  - https://attack.mitre.org/tactics/TA0005/
tags:
  - attack.ta0005
  - sysmon
detection:
  selection:
    EventID: 4663
    ObjectType: Key
    ObjectName: 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Defender\Real-Time Protection'
    NewValue: 'DisableRealtimeMonitoring=1'
  condition: selection
falsepositives:
  - Legitimate changes to Windows Defender settings.
level: high
```
After creating a new `Sigma Rule` we got 
`Nice work! The DNS filter rule prevented sample3.exe from connecting to the tester's command-and-control server. Check your inbox for the next steps`
and another `MAIL` from `Sphinx` 
```MAIL
Hey.
I'm not sure what you managed to do this time, but you seriously threw a wrench into my malware sample! I spent ages trying to reconfigure my attack tools and methodologies to get around your detection - SUPER ANNOYING!
Having my team develop new techniques used in my adversary tools was a time-consuming effort and a significant cost. It's good that we have a substantial budget for this engagement, but many threat actors would have given up and found a new victim by now.
I finally have sample5.exe for you to detect. Different approach this time. In this sample, all of the "heavy lifting" and instruction occurs on my back-end server, so I can easily change the types of protocols I use and the artifacts I leave on the host. You'll have to find something unique or abnormal about the behaviour of my tool to detect it.
I attached the logs of the outgoing network connections from the last 12 hours on the victim machine. That may help you correlate something.
I don't know what to do if you can stop me at this level.
 Here's your flag: [flag omitted]
-Annoyed Sphinx
Again With attachment outgoing_connections.log
```
Flag
```Flag4
[flag omitted]
```
and then we start working on `outgoing_connections.log` with `Viewer log file`
```Anomalous_entries_SAMPLE5.EXE
|Time           |Destination          |Port    |Size   |Suspicion                 |
|---------------|---------------------|--------|-------|--------------------------|
|  09:23:45     |   43.10.65.115      |443     |21541  |Unusual data burst        |
|  13:32:17     |   72.15.61.98       |443     |26084  |Data exfil?               |
|  14:55:33     |   208.45.72.16      |443     |45091  |Highly suspicious         |
|  15:40:10     |   101.55.20.79      |443     |95021  |Likely large data transfer|
|  16:18:55     |    194.92.18.10     | 80     |8004   |HTTP, not encrypted       |
|  17:09:30     |    77.23.66.214     |443     |9584   |Possible payload download |
|  17:27:42     |    156.29.88.77     |443     |10293  |Suspicious activity       |
```
### Malicious Indicators:
- **`51.102.10.19`** → Persistent, periodic 97-byte HTTPS traffic → **Likely beaconing** (C2 heartbeat)
- **High-volume connections to multiple IPs** → **Possible data exfiltration**
as our Previous work 
we need to create an another `Sigma Rules Builder` 
```STEPS
Step 1:  I want to create a rule that focuses on:
System Event logs | Web Server Logs  |  VPN Logs  |  Application logs
Step 2: Sysmon Event > Logs I want to target this Sysmon event: 
Process Creation | File Creation & Modification | Network Connection | Registry modification
Step 3: Network Connection
Set the rule conditions and options:

This rule will detect network connections made from a host machine with specific conditions, such as remote IP, port, size of the connection, and how often it occurs (frequency).
Remote IP:*                       any
Remote Port:*                     any
Size (bytes):*                     97
Frequency (seconds):*             1800
ATT&CK ID:*                        Command and Control (TA0011)
At PicoSecure, we require that all Sysmon detection rules map to the MITRE ATT&CK framework. This ensures that our SOC team has the context to facilitate a more effective threat detection, analysis, and response.

STEPS = Sysmon Event > Network Connections > Set the rule conditions
```
Output
```Output 
title: Alert on Suspicious Beacon Network Connections
id: network_connections_criteria_sysmon
description: |
  Detects network connections with specific criteria in Sysmon logs: remote IP, remote port, size, and frequency.
references:
  - https://attack.mitre.org/tactics/TA0011/
tags:
  - attack.ta0011
  - sysmon
detection:
  selection:
    EventID: 3
    RemoteIP: '*'
    RemotePort: '*'
    Size: 97
    Frequency: 1800 seconds
  condition: selection
falsepositives:
  - Legitimate network traffic may match this criteria.
level: high
```
After creating a new `Sigma Rule` we got 
`Nice work! The DNS filter rule prevented sample3.exe from connecting to the tester's command-and-control server. Check your inbox for the next steps`
and another `MAIL` from `Sphinx` 
```Mail
Hello again,
You managed to detect sample5.exe ! I'm very impressed. But also very annoyed! Because now, I need to go back to the drawing board and create a brand new tool to do what I need to do. If I can't find another one quickly, this will be another significant investment. Also, I will need to train myself all over again on how to use it!
I can keep this up one or two times, but there's no way I can continue after this. The reward no longer outweighs the cost, and I would instead find an easier target with detection capabilities much lower on the pyramid.
For my last trick, I have sample6.exe. This time, you will need more than artifacts or tool detection to help you. You'll need to focus on something extremely hard for me to change subconsciously - my techniques and procedures.
I've attached the recorded command logs from all my previous samples to understand better what actions I tend to perform on my victims to extract info once I have remote access. Good luck!
 Here's your flag: [flag omitted]
-Very Annoyed Sphinx! 😫
with an attachment Commands.log
```
Flag 
```Flag5
[flag omitted]
```
and then we start working on `Commands.log` with `Viewer log file`
```Commands_Logs
dir c:\ >> %temp%\exfiltr8.log
dir "c:\Documents and Settings" >> %temp%\exfiltr8.log
dir "c:\Program Files\" >> %temp%\exfiltr8.log
dir d:\ >> %temp%\exfiltr8.log
net localgroup administrator >> %temp%\exfiltr8.log
ver >> %temp%\exfiltr8.log
systeminfo >> %temp%\exfiltr8.log
ipconfig /all >> %temp%\exfiltr8.log
netstat -ano >> %temp%\exfiltr8.log
net start >> %temp%\exfiltr8.log
```
after analyzing it 
we need to create an another `Sigma Rules Builder` 
```Sigma_Rule_Builder
Step 1:  I want to create a rule that focuses on:
System Event logs | Web Server Logs  |  VPN Logs  |  Application logs
Step 2: Sysmon Event > Logs I want to target this Sysmon event: 
Process Creation | File Creation & Modification | Network Connection | Registry modification
Step 3: Process Creation
Set the rule conditions and options:
This rule will detect network connections made from a host machine with specific conditions, such as remote IP, port, size of the connection, and how often it occurs (frequency).
Process Name*                       cmd.exe
CommandLine*                        Contains
String*                             systeminfo >> %temp%\exfiltr8.log
ATT&CK ID*                          TA0007 (Discovery)
At PicoSecure, we require that all Sysmon detection rules map to the MITRE ATT&CK framework. This ensures that our SOC team has the context to facilitate a more effective threat detection, analysis, and response.

STEPS = Sysmon Event > Process Creation > Set the rule conditions
```
Output 
```Output
🎉 Congrats on completing Summit! 🥳 Check your inbox for the final flag!
```
Well, We received another `Mail` from `Sphinx`
```Mail
Well, that's it. I have officially given up.
Throughout the engagement, you managed to chase me to the very top of the Pyramid of Pain, and I have to say, it's not fun up here!
You detected my samples file hashes, IPs, domains, host artifacts, tools, and now my own behavioural techniques! To continue, I have no choice but to completely retrain myself and conduct extensive research to figure out how you're catching me. And with that, I don't think you'll ever see me again. Enjoy the final flag; you've earned it!
 Here's your flag: [flag omitted]
-A significantly defeated Sphinx!
```
Flag 
```Flag6
[flag omitted]
```

```Q/A
Q1. What is the first flag you receive after successfully detecting sample1.exe?
A1. [flag omitted]

Q2. What is the second flag you receive after successfully detecting sample2.exe?
A2. [flag omitted]

Q3. What is the third flag you receive after successfully detecting sample3.exe?
A3. [flag omitted]

Q4. What is the fourth flag you receive after successfully detecting sample4.exe?
A4. [flag omitted]

Q5. What is the fifth flag you receive after successfully detecting sample5.exe?
A5. [flag omitted]

Q6. What is the final flag you receive from Sphinx?
A6. [flag omitted]
```
