## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
We have just launched a website developed by a freelance developer. The source code was not shared with us, and the developer has since disappeared without handing it over.
Despite this, traces of the development process and earlier versions of the website may still exist online.
You are only given the website's primary domain as a starting point: **marvenly.com**
```
## Address
```
marvenly.com
```
- Lets Surf the Website 
### Browser 
- Website not Reachable 
### Dig
```bash
$ dig marvenly.com
; <<>> DiG 9.20.20-1-Debian <<>> marvenly.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 37200
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1
;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; MBZ: 0x0005, udp: 4096
;; QUESTION SECTION:
;marvenly.com.			IN	A
;; Query time: 0 msec
;; SERVER: 192.168.40.2#53(192.168.40.2) (UDP)
;; WHEN: Sun Apr 05 12:51:44 EDT 2026
;; MSG SIZE  rcvd: 41
```
- Got something on `CRT.sh`
```Markdown 
| Logged At  | Common Name      | Extra Domains Found | Issuer    |
|------------|------------------|---------------------|--------- --|
| 2026-01-19 | uat-testing.marvenly.com | admin.marvenly.com                   | Let's Encrypt (E7/E8) |
| 2022-07-23 | marvenly.com             | www.marvenly.com                     | Let's Encrypt (R3)    |
```
- Got the Sub-Domain `uat-testing.marvenly.com` & `admin.marvenly.com`
### Sub-Domain
```URL
- http://uat-testing.marvenly.com
- http://admin.marvenly.com
```
- Searching `Sub-Domain` on `Google` Give me `Github`
- Github -  `https://github.com/notvibecoder23/marvenly_site`
### OSINT
```Inside Git Logs
- Found Hidden Msg - git # 33c59e5feedcbcbfee7a1f6d3a435225698f616f -Removed my signature, ready for deployment
  - [flag omitted]
- Reason To Abandoon the Project - git # 88baf1db29d7530a51c7bc13ae9f3c1b9a1eae25
  - The project was marked as abandoned due to a payment dispute
```
- Last Mail, I got Using .patch technique
```html
From 33c59e5feedcbcbfee7a1f6d3a435225698f616f Mon Sep 17 00:00:00 2001
From: notvibecoder23 <freelancedevbycoder23@gmail.com>
Date: Tue, 20 Jan 2026 00:32:28 +0800
Subject: [PATCH] Removed my signature, ready for deployment

---
 index.html | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)

diff --git a/index.html b/index.html
index 3589c09..212db1f 100644
--- a/index.html
+++ b/index.html
@@ -458,7 +458,7 @@ <h2 class="section-title">Get In Touch</h2>
     <footer>
         <div class="container">
             <p>&copy; 2026 Marvenly. All rights reserved.</p>
-            <p>Website developed by notvibecoder23</p>
+            <!-- removed the signature, but I'm leaving something as my hidden signature [flag omitted] -->
         </div>
     </footer>
 </body>
```

### Developer Mail
```mail
freelancedevbycoder23@gmail.com
```
# END