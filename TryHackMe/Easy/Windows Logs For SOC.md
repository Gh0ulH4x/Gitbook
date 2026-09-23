## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
SOC analysts spend most of their time triaging alerts and hunting threats - using the logs in SIEM. To tell good from bad, analysts have to know the logs well: how they look, how to interpret them, and what malicious action they indicate. This room begins your long journey into Windows logging - a key skill for any SOC analyst or DFIR professional.

## Learning Objectives

- Understand how to find and interpret important Windows event logs
- Learn invaluable for monitoring log sources like Sysmon and PowerShell
- Prepare for using the mentioned logs in [SOC-SIM](https://tryhackme.com/soc-sim) and the following rooms
- Practice your log analysis skills on multiple event log datasets
```

## Credentials
| **Username** | Administrator |
| ------------ | ------------- |
| **Password** | [password omitted]       |
| **IP (RDP)** | 10.10.235.2   |
## Purpose
```Purpose 
Whenever you start a program, create a file, or just log in to your laptop, the event is processed by your OS. Then, the OS can log the event, meaning it will append a line to some journal, stating the time, action details, and the user behind the action. Every recorded event is called a log, and proper logging ensures that all user and system activity is recorded, thus helping SOC with the following activities:

- **Incident Response**: Logs can show when and how the attack occurred
- **Threat Hunting**: Logs allow you to search for signs of malicious activity
- **Alerting and Triage**: Logs are a building block of any alert or detection rule
```

`Looking to Windows` #Windows_Logs
Windows is an interesting OS as it has very powerful logging capabilities but requires a lot of knowledge to read and understand the logs. Your first challenge may be to just open the logs, as they are stored in a binary format inside the `C:\Windows\System32\winevt\Logs` folder:

## Another Format
```Reading Event Logs
Reading Event Logs
We will use **Event Viewer** for this room, a built-in tool that allows you to view and manage event logs. To open Event Viewer, search for "Event Viewer" using Windows Search or press `Win + R`, type `eventvwr`, and press Enter. Once the tool is loaded, you may see all system logs parsed, grouped, and ready for analysis:

1. **Log Sources**: Every EVTX file corresponds to a single item on the left panel
2. **Log List**: Each row you see is a single event that contains a few properties you can sort by:
    - **Keywords**: For some events, indicates if the action was successful or not
    - **Date and Time**: The timestamp when the event occurred (system time, not UTC!)
    - **Event ID**: A unique number for the event name (e.g. a failed login is always 4625)
3. **Log Details**: The actual content of the log, in a plaintext or XML format ("Details" tab)
4. **Filters Menu**: Use the "Filter Current Log" and "Find" buttons to filter the logs
```
## SOC Analyst 
|**Event ID**|**Purpose**|**Logging**|**Limitations**|
|---|---|---|---|
|**4624  <br>**(Successful Logon)|Detect suspicious RDP/network logins and identify the attack starting point|Logged on the target machine, the one you are trying to access|**Noisy**. You will see hundreds of logon events per minute on loaded servers|
|**4625  <br>**(Failed Logon)|Detect brute force, password spraying, or vulnerability scanning|Logged on the target machine, the one you are trying to access|**Inconsistent**. The logs have lots of caveats that may trick you into the wrong understanding of the event|
These are the Events IDs for an successful Login & unsuccessful Login 
so its will be beneficial in future must learn it for future

## More ID 
| **Event ID**                   | **Description**                                                | **Malicious Usage**                                                                                                                                                                                        |
| ------------------------------ | -------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **4720** / **4722** / **4738** | A user account was  <br>created / enabled / changed            | Attackers might create a backdoor account or even enable an old one to avoid detection                                                                                                                     |
| **4725** / **4726**            | A user account was  <br>disabled / deleted                     | In some advanced cases, threat actors may disable privileged SOC accounts to slow down their actions                                                                                                       |
| **4723** / **4724**            | A user changed their password /  <br>User's password was reset | Given enough permissions, threat actors might reset the password and then access the required user                                                                                                         |
| **4732** / **4733**            | A user was added to /  <br>removed from a security group       | Attackers often add their backdoor accounts to privileged groups like "[Administrators](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups#administrators)" |
## More 
| **Event ID**                                        | **Security Log Alternative**                                                          | **Event Purpose**                                                                     |
| --------------------------------------------------- | ------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **11 / 13  <br>**(File Create / Registry Value Set) | **4656** for file changes and **4657** for registry changes, both disabled by default | Detect files dropped by malware or its changes to the registry (e.g. for persistence) |
| **3 / 22  <br>**(Network Connection / DNS Query)    | No direct alternative, requires additional firewall and DNS configuration             | Detect traffic from untrusted processes or to known malicious destinations            |


## Path 
File Path = `C:\Users\thm.bob\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine`

```Flag
cd .\Documents\
ls
get-localuser > users.txt
get-smbshare > shares.txt
ipconfig /all > network.txt
echo "[flag omitted]" > flag.txt
get-mppreference > defender.txt
clear-recyclebin
```


```Q/A
Q1 Looking at the last screenshot, which event ID describes a successful login?  
A1 Security / 4624
Q2 Which IP performed a brute force of the THM-PC?
A2 10.10.53.248
Q3 Which user has been breached as a result of the attack?
A3 Administrator
Q4 What was the Logon ID of the malicious RDP login?  
A4 0x183C36D
Q5 Which user was created by the attacker soon after the RDP login?
A5 svc_sysrestore
Q6 Which two privileged groups was the backdoor user added to?
A6 Backup Operators, Remote Desktop Users
Q7 Does the Logon ID field match what you saw in the previous task (Yea/Nay)?
A7 Yea
Q8 Which web browser does Sarah use to browse the web? 
A8 Google Chrome
Q9 Which file did Sarah download from the browser?
A9 C:\Users\sarah.miller\Downloads\ckjg.exe
Q10 Which URL was the file downloaded from?//Hint= Explore Other Events Logs
A10 http://gettsveriff.com/bgj3/ckjg.exe
Q11 Which file was created by the downloaded malware to persist on the host?
A11 C:\Users\sarah.miller\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\DeleteApp.url
Q12 What is the Command & Control server malware connected to?
A12 193.46.217.4:7777
Q13 Finally, which domain does the malicious IP correspond to? //Virustotal
A13 hkfasfsafg.click 
Q14 Which PowerShell command was executed first? // Admin History 
A14 Get-ComputerInfo // Inside the Path
C:\Users\Administrator\AppData\Roaming\Windows\powershell\PSReadline\Context_History.txt
Q15 When did the Administrator run the first PS command? (Format: April 18, 2025)
A15 May 18, 2025 // Inside the properties
Q16 Can you find the flag stored in the PowerShell history? (Format: [flag omitted])
A16 [flag omitted] 
```