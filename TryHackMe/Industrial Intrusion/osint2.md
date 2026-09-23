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

After Investigating `stage0.virelia-water.it.com` & `185.199.108.153` on `VirusTotal` 
```Output 
185.199.109.153, 185.199.108.153, 185.199.111.153, 185.199.110.153  (185.199.108.0/22)
AS 54113( FASTLY )
All belongs to FASTLY
Data:
Version: V3
Serial Number: 9077344147316ef99599767aeafdf1b9
Thumbprint: 8cff59e58ec4fa76feaf2dc5c0d4136a772df991
Signature Algorithm:
Issuer:  C=GB, ST=Greater Manchester,  L=Salford, O=Sectigo Limited, CN=Sectigo RSA Domain Validation Secure Server CA
Validity
Not Before: 2025-03-07 00:00:00
Not After: 2026-03-07 23:59:59
Subject: [CN=*.github.io]
Subject Public Key Info:
Public Key Algorithm : RSA
Public-Key: (2048 bit)
```

After visiting `https://stage0.virelia-water.it.com/` I checked for `Source-Code` and I got lead to 
```Link
<script src="https://raw.githubusercontent.com/SanTzu/uplink-config/refs/heads/main/init.js"></script>
```
which shows us
```Script
var beacon = {
  session_id: "O-TX-11-403",
  fallback_dns: "uplink-fallback.virelia-water.it.com",
  token: "JBSWY3DPEBLW64TMMQQQ=="
};
```
And This String `JBSWY3DPEBLW64TMMQQQ==` is `base32` encoded which is decoded as `Hello World!`
And This `uplink-fallback.virelia-water.it.com`  we uploaded on `VirusTotal`

```Output
eyJzZXNzaW9uIjoiVC1DTjEtMTcyIiwiZmxhZyI6IlRITXt1cGxpbmtfY2hhbm5lbF9jb25maXJtZWR9In0
```

Which is a `Base64` encoded which is decoded as 
```Flag 
{"session":"T-CN1-172","flag":"[flag omitted]"}
```