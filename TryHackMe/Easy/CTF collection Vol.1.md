## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Sharpening up your CTF skill with the collection. The first volume is designed for beginner.
Just another random CTF room created by me. Well, the main objective of the room is to test your CTF skills. For your information, vol.1 consists of 20 tasks and all the challenges are extremely easy. Stay calm and Capture the flag. :)

Note: All the challenges flag are formatted as `[flag omitted]`, unless stated otherwise
```
## Cipher First
```Cipher text
Can you decode the following?
VEhNe2p1NTdfZDNjMGQzXzdoM19iNDUzfQ==
```
- Base64 `[flag omitted]`
## Image Cipher 
```bash 
$wget "http://download/image"
$ strings 'Find_me_1577975566801.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIA2YR2KKQMWLXEMXW4%2F20260412%2Feu-west-1%2Fs3%2Faws4_request&X-Amz-Date=20260412T001748Z&X-Amz-Expires=120&X-Amz-Signat'
JFIF
Exif
0231
0100
[flag omitted]
;CREATOR: gd-jpeg v1.0 (using IJG JPEG v62), quality = 60
 .)10.)-,3:J>36F7,-@WAFLNRSR2>ZaZP`JQRO
&O5-5OOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOOO
,U0&
```
- Got the Flag `[flag omitted]`
## Another Image StegHide
```bash
$ wget http://download/image2
$ steghide info Extinction_1577976250757.jpg\?X-Amz-Algorithm=AWS4-HMAC-SHA256\&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD\&X-Amz-Credential=AKIA2YR2KKQMWLXEMXW4%2F20260412%2Feu-west-1%2Fs3%2Faws4_request\&X-Amz-Date=20260412T002005Z\&X-Amz-Expires=120\&X-Amz-Sig
"Extinction_1577976250757.jpg?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIA2YR2KKQMWLXEMXW4%2F20260412%2Feu-west-1%2Fs3%2Faws4_request&X-Amz-Date=20260412T002005Z&X-Amz-Expires=120&X-Amz-Sig":
  format: jpeg
  capacity: 1.3 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase:
  embedded file "Final_message.txt":
    size: 79.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```
- Hidden File
```bash
─$ stegseek Extenction.jpg /usr/share/wordlists/rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek
[i] Found passphrase: ""
[i] Original filename: "Final_message.txt".
[i] Extracting to "Extenction.jpg.out".
the file "Extenction.jpg.out" does already exist. overwrite ? (y/n)
y
$ ls
Extenction.jpg  Extenction.jpg.out
$ strings Extenction.jpg.out
It going to be over soon. Sleep my child.
[flag omitted]
```
 - Got the Flag `[flag omitted]`
## Next Challenge 
```bash
Huh, where is the flag? [flag omitted]
```
- Flag is hidden under white tape
- Flag `[flag omitted]`

## Next Challenge
- Got an QR Code
```bash
- UPload QR code on google and got the flag
- [flag omitted]
```
 -  Flag `[flag omitted]`

## Next Challenge
```bash
hello_1577977122465.hello
- File Downloaded
$ ls
hello_1577977122465.hello
$ strings hello_1577977122465.hello
/lib64/ld-linux-x86-64.so.2
libc.so.6
puts
printf
__cxa_finalize
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u/UH
[]A\A]A^A_
[flag omitted]
Hello there, wish you have a nice day
```
- Flag `[flag omitted]`
## Next Challenge
```bash
Can you decode it?
3agrSy1CewF9v8ukcSkPSYm3oKUoByUpKG4L
```
- Identify Cipher & Decode
- `Base58` & `[flag omitted][flag omitted]`

## Next Challenge
```bash
Left, right, left, right... Rot 13 is too mainstream. Solve this

MAF{atbe_max_vtxltk}
```
- Rot 13 - amount 7 
- Flag - `[flag omitted]`
## Next Challenge
```bash
No downloadable file, no ciphered or encoded text. Huh .......  
Answer the questions below
I''m hungry now... I need the flag
Got flag inside Inspect html
[flag omitted]
```
- Flag `[flag omitted]`
## Next challenge
```bash
- Got a File
$ binwalk spoil_1577979329740.PNG

DECIMAL HEXADECIMAL   DESCRIPTION

75     0x4B            Zlib compressed data, default compression

$ binwalk -e spoil_1577979329740.PNG
it will create a folder
$ ls -la
total 80
drwxrwxr-x 2 kali kali  4096 Apr 11 21:23 .
drwxrwxr-x 3 kali kali  4096 Apr 11 21:23 ..
-rw-rw-r-- 1 kali kali     0 Apr 11 21:23 4B
-rw-rw-r-- 1 kali kali 70684 Apr 11 21:23 4B.zlib

$ file 4B.zlib
4B.zlib: zlib compressed data

> For a PNG image, the header must always start with “**.PNG….**” or the hex values “**0x89 0x50 0x4E 0x47 0x0D 0x0A 0x1A 0x0A**”.

We are spoiled for choice when it comes to using a Hex editor to inspect a binary file or image and verify the Magic. Some choices include:

https://bvi.sourceforge.net/quick.htmlor bview
- hexeditor
```
- Flag After editing the header 
```Flag
[flag omitted]
```
### Next Challenge
```bash
_site:”reddit.com” intext:”THM” intitle:”tryhackme_
```
- Flag 
```Flag
[flag omitted]
```

### Next Challenge
```bash
What is this?
++++++++++[>+>+++>+++++++>++++++++++<<<<-]>>>++++++++++++++. — — — — — — .+++++.>+++++++++++++++++++++++.<<++++++++++++++++++.>> — — — — — — — — — -. — — — — -.++++++++++++++.++++++++++++.<++++++++++++++++++.+++++++++.<+++.+.> — — .>++++.
```
- Brain fuck Cipher 
```Decoded 
[flag omitted]
```

## Next Challenge
```bash
Exclusive strings for everyone!

S1: 44585d6b2368737c65252166234f20626d  
S2: 1010101010101010101010101010101010
```
- XOR Calculator
```Flag
[flag omitted]
```

### Next Challenge
```bash
- Download file
file file.jpg
file.jpg: JPEG image data, JFIF standard 1.02, aspect ratio, density 1x1, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=6, xresolution=86, yresolution=94, resolutionunit=2, software=Picasa 3.0], baseline, precision 8, 1024x686, components 3
```
- Check Further
```bash
$ string file.jpg
hello_there.txtUT
()JL.
LS#P
hello_there.txtUT

$ binwalk -e file.jpg

DECIMAL     HEXADECIMAL  DESCRIPTION
--------------------------------------
265845        0x40E75         Zip archive data, at least v2.0 to extract, uncompressed size: 69, name: hello_there.txt

WARNING: One or more files failed to extract: either no utility was found or it''s unimplemented

$ ls
file.jpg  _file.jpg.extracted

$ cd _file.jpg.extracted

$ ls
40E75.zip  hello_there.txt

$ cat hello_there.txt
Thank you for extracting me, you are the best!

[flag omitted]
```
#### Next Challenge
```bash
- Voice
- How good is your listening skill?

P/S: The flag formatted as [flag omitted], the flag should be in All CAPS
```
- Flag
```Flag
[flag omitted]
```
#### Next Challenge
```bash
Sometimes we need a 'machine' to dig the past

Targetted website: https://www.embeddedhacker.com/[(opens in new tab)](http://www.embeddedhacker.com/)  
Targetted time: 2 January 2020
```
- Checked wayback-machine
```Flag
[flag omitted]
```
#### Next Challenge
```bash
Can you solve the following? By the way, I lost the key. Sorry >.<  

MYKAHODTQ{RVG_YVGGK_FAL_WXF}

Flag format: TRYHACKME{FLAG IN ALL CAP}
```
- Cyberchef `Vigenere Decode`
```Flag
TRYHACKME{YOU_FOUND_THE_KEY}
```
#### Next Challenge
```bash
Decode the following text.

581695969015253365094191591547859387620042736036246486373595515576333693
```
- Convert to bytes
```bash
$ python3 -c "print(bytes.fromhex(hex(581695969015253365094191591547859387620042736036246486373595515576333693)[2:]))"
b'[flag omitted]'
```
#### Next Challenge
```bash
- Wireshark File
  I just hacked my neighbor's WiFi and try to capture some packet. He must be up to no good. Help me find it.
```
- Decode - http protocol filter for neighbour 
```bash
Frame 1827: Packet, 455 bytes on wire (3640 bits), 455 bytes captured (3640 bits) on interface eth0, id 0
    Section number: 1
    Interface id: 0 (eth0)
        Interface name: eth0
    Encapsulation type: Ethernet (1)
    Arrival Time: Jan  2, 2020 23:43:10.966417752 EST
    UTC Arrival Time: Jan  3, 2020 04:43:10.966417752 UTC
    Epoch Arrival Time: 1578026590.966417752
    [Time shift for this packet: 0.000000000 seconds]
    [Time delta from previous captured frame: 1.371518 milliseconds]
    [Time delta from previous displayed frame: 1.753335 milliseconds]
    [Time since reference or first frame: 52.509987109 seconds]
    Frame Number: 1827
    Frame Length: 455 bytes (3640 bits)
    Capture Length: 455 bytes (3640 bits)
    [Frame is marked: False]
    [Frame is ignored: False]
    [Protocols in frame: eth:ethertype:ip:tcp:http:data-text-lines]
    Character encoding: ASCII (0)
    [Coloring Rule Name: HTTP]
    [Coloring Rule String: http || tcp.port == 80 || http2]
Ethernet II, Src: VMware_fb:30:11 (00:0c:29:fb:30:11), Dst: VMware_2d:ec:c9 (00:0c:29:2d:ec:c9)
    Destination: VMware_2d:ec:c9 (00:0c:29:2d:ec:c9)
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Source: VMware_fb:30:11 (00:0c:29:fb:30:11)
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
    Type: IPv4 (0x0800)
    [Stream index: 3]
Internet Protocol Version 4, Src: 192.168.247.140, Dst: 192.168.247.130
    0100 .... = Version: 4
    .... 0101 = Header Length: 20 bytes (5)
    Differentiated Services Field: 0x00 (DSCP: CS0, ECN: Not-ECT)
        0000 00.. = Differentiated Services Codepoint: Default (0)
        .... ..00 = Explicit Congestion Notification: Not ECN-Capable Transport (0)
    Total Length: 441
    Identification: 0x523a (21050)
    10. .... = Flags: 0x2, Don't fragment
        0... .... = Reserved bit: Not set
        .1.. .... = Don't fragment: Set
        ..0. .... = More fragments: Not set
    ...0 0000 0000 0000 = Fragment Offset: 0
    Time to Live: 64
    Protocol: TCP (6)
    Header Checksum: 0x76a4 [validation disabled]
    [Header checksum status: Unverified]
    Source Address: 192.168.247.140
    Destination Address: 192.168.247.130
    [Stream index: 22]
Transmission Control Protocol, Src Port: 80, Dst Port: 36654, Seq: 1, Ack: 441, Len: 389
    Source Port: 80
    Destination Port: 36654
    [Stream index: 42]
    [Stream Packet Number: 6]
    [Conversation completeness: Complete, WITH_DATA (31)]
        ..0. .... = RST: Absent
        ...1 .... = FIN: Present
        .... 1... = Data: Present
        .... .1.. = ACK: Present
        .... ..1. = SYN-ACK: Present
        .... ...1 = SYN: Present
        [Completeness Flags: ·FDASS]
    [TCP Segment Len: 389]
    Sequence Number: 1    (relative sequence number)
    Sequence Number (raw): 3347955900
    [Next Sequence Number: 390    (relative sequence number)]
    Acknowledgment Number: 441    (relative ack number)
    Acknowledgment number (raw): 1048054246
    1000 .... = Header Length: 32 bytes (8)
    Flags: 0x018 (PSH, ACK)
        0. .... .... = Reserved: Not set
        ...0 .... .... = Accurate ECN: Not set
        .... 0... .... = Congestion Window Reduced: Not set
        .... .0.. .... = ECN-Echo: Not set
        .... ..0. .... = Urgent: Not set
        .... ...1 .... = Acknowledgment: Set
        .... .... 1... = Push: Set
        .... .... .0.. = Reset: Not set
        .... .... ..0. = Syn: Not set
        .... .... ...0 = Fin: Not set
        [TCP Flags: ·······AP···]
    Window: 235
    [Calculated window size: 30080]
    [Window size scaling factor: 128]
    Checksum: 0x35b2 [unverified]
    [Checksum Status: Unverified]
    Urgent Pointer: 0
    Options: (12 bytes), No-Operation (NOP), No-Operation (NOP), Timestamps
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - No-Operation (NOP)
            Kind: No-Operation (1)
        TCP Option - Timestamps: TSval 103045, TSecr 367886694
            Kind: Time Stamp Option (8)
            Length: 10
            Timestamp value: 103045
            Timestamp echo reply: 367886694
    [Timestamps]
        [Time since first frame in this TCP stream: 2.483474 milliseconds]
        [Time since previous frame in this TCP stream: 1.371518 milliseconds]
    [SEQ/ACK analysis]
        [iRTT: 467.533 microseconds]
        [Bytes in flight: 389]
        [Bytes sent since last PSH flag: 389]
    [Client Contiguous Streams: 1]
    [Server Contiguous Streams: 1]
    TCP payload (389 bytes)
Hypertext Transfer Protocol
    HTTP/1.1 200 OK\r\n
        Response Version: HTTP/1.1
        Status Code: 200
        [Status Code Description: OK]
        Response Phrase: OK
    Date: Fri, 03 Jan 2020 04:43:14 GMT\r\n
    Server: Apache/2.2.22 (Ubuntu)\r\n
    Last-Modified: Fri, 03 Jan 2020 04:42:12 GMT\r\n
    ETag: "e1bb7-20-59b34eee33e0c"\r\n
    Accept-Ranges: bytes\r\n
    Vary: Accept-Encoding\r\n
    Content-Encoding: gzip\r\n
    Content-Length: 52\r\n
        [Content length: 52]
    Keep-Alive: timeout=5, max=100\r\n
    Connection: Keep-Alive\r\n
    Content-Type: text/plain\r\n
    \r\n
    [Request in frame: 1825]
    [Time since request: 1.753335 milliseconds]
    [Request URI: /flag.txt]
    [Full request URI: http://192.168.247.140/flag.txt]
    Content-encoded entity body (gzip): 52 bytes -> 32 bytes
    File Data: 32 bytes
Line-based text data: text/plain (3 lines)
    [flag omitted]\n
    \n
    Found me!\n
```
# END
