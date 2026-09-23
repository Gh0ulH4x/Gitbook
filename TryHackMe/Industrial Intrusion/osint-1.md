
```Description 
_Flags have been omitted from this writeup per platform guidelines._

Hexline, we need your help investigating the phishing attack from 3 months ago. We believe the threat actor managed to hijack our domain `virelia-water.it.com` and used it to host some of their infrastructure at the time. Use your OSINT skills to find information about the infrastructure they used during their campaign.
```

Website `virelia-water.it.com` and search on web for it looking for its infrastructure owner 
first checked on virusTotal which gives us 
```Output 
Date resolved           Detections           Resolver           IP
2025-06-27                2/ 94             VirusTotal       185.199.108.153
2025-06-27                1/ 94             VirusTotal       185.199.110.153
2025-06-27                2/ 94             VirusTotal       185.199.109.153
2025-06-27                1/ 94             VirusTotal       185.199.111.153
                               # Subdomains(1)

stage0.virelia-water.it.com    0/ 94       185.199.109.153
                                          185.199.111.153
                                          185.199.110.153
                                          
https://www.virustotal.com/gui/domain/stage0.virelia-water.it.com
```

Then Realized that we taking about network here so then I run 
`https://dnshistory.org/historical-dns-records/cname/virelia-water.it.com`
which gives me an output 
```## DNS Records
Domain: [virelia-water.it.com](https://dnshistory.org/dns-records/virelia-water.it.com)  
Record: cname  
2025-06-19 -> 2025-06-21 [solstice-tech1.github.io](https://dnshistory.org/dns-records/solstice-tech1.github.io.)
```
after searching on `Github` I found an user name ` solstice-tech1` which has 2 repository 
```Repo
staging-panel  - https://github.com/solstice-tech1/staging-panel
ot-auth-mirror - https://github.com/solstice-tech1/ot-auth-mirror
```
where we Deep Down in Repo `https://github.com/solstice-tech1/ot-auth-mirror/blob/main/index.html`
whose code is 
```<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="refresh" content="0;url=https://54484d7b5375357373737d.virelia-water.it.com/reset">
  <title>Redirecting...</title>
</head>
<body>
  <p>Redirecting to internal password reset portal...</p>
</body>
</html>
```
where redirect `URL` = `https://54484d7b5375357373737d.virelia-water.it.com/reset` must be something after separating `54484d7b5375357373737d` from `Link` and checking on `cipher-Identifier` is shows `ASCII Code` 
which decoded `54484d7b5375357373737d`  into `[flag omitted]` 

Flag 
```Flag
[flag omitted]
```
