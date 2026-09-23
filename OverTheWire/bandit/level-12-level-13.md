# Level 12 → Level 13

## Description
```Description
## Level Goal

The password for the next level is stored in the file **data.txt**, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work. Use mkdir with a hard to guess directory name. Or better, use the command “mktemp -d”. Then copy the datafile using cp, and rename it using mv (read the manpages!)

## Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd, mkdir, cp, mv, file

## Helpful Reading Material

- [Hex dump on Wikipedia](https://en.wikipedia.org/wiki/Hex_dump)
```

## Credentials
```Credentials
SSH Information  
Host: bandit.labs.overthewire.org  
Port: 2220
username: bandit12
Password: 7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4
```

## Lets Explore
```bash
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cp ~/data.txt .
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data.txt 
00000000: 1f8b 0808 84c9 8768 0203 6461 7461 322e  .......h..data2.
00000010: 6269 6e00 0142 02bd fd42 5a68 3931 4159  bin..B...BZh91AY
00000020: 2653 592b 25cc 1e00 0017 7fff f9df a1ff  &SY+%...........
00000030: f67f f7ff befe dded befd efaf dcff b7ff  ................
00000040: bff7 abbd bf6f eb0f b79f bbf9 b001 3998  .....o........9.
00000050: 1034 0c80 01a3 d40d 1a06 8680 6800 0c80  .4..........h...
00000060: 0003 41a0 01a3 41a3 4188 1a68 3400 01a0  ..A...A.A..h4...
00000070: 7a80 01a3 2686 87a1 9432 7a88 1a32 1a68  z...&....2z..2.h
00000080: d343 41a6 4068 6868 0d00 d1a3 1000 1a00  .CA.@hhh........
00000090: 0d03 4680 64f5 068d 1a0d 191a 3403 4c80  ..F.d.......4.L.
000000a0: 00c8 d1a6 8320 0d0d 1a31 0626 4d1a 3350  ..... ...1.&M.3P
000000b0: c8d3 434d 01a0 07a4 3403 4068 1a32 7a86  ..CM....4.@h.2z.
000000c0: 8680 068f 51a0 c800 0000 1a68 1a68 6464  ....Q......h.hdd
000000d0: 000d 00c8 0000 2043 046e b096 140b 6718  ...... C.n....g.
000000e0: d116 a448 9806 2099 d59f 5645 922e ca16  ...H.. ...VE....
000000f0: 4088 ac23 c692 555d 9b97 31aa 2c30 bd07  @..#..U]..1.,0..
00000100: f8d0 0bc9 7c91 2400 27c8 1b72 3a00 359a  ....|.$.'..r:.5.
00000110: 345c 4fef 37b9 1f81 5ff9 80b8 502d 28d1  4\O.7..._...P-(.
00000120: 099a 381e e83f d9e4 2291 bf26 0e74 b58d  ..8..?.."..&.t..
00000130: 5a7c 5449 9599 cb96 005a b473 4542 e02d  Z|TI.....Z.sEB.-
00000140: e1ab e8f5 2614 0b78 9cff 3fb6 8d81 645d  ....&..x..?...d]
00000150: 39be 981f b351 84be 8d26 4185 299e 8e1a  9....Q...&A.)...
00000160: 605e c7bc 40c1 1534 e890 b70c 23ac 8e82  `^..@..4....#...
00000170: 5150 ebff 40fc 5b5b ca8e 40eb 948b 0f8d  QP..@.[[..@.....
00000180: 8e5e 18af 6133 189a a02f 3e5a 36ea c32b  .^..a3.../>Z6..+
00000190: 0379 7b59 0191 fe0c 5736 7b08 ae9f b16b  .y{Y....W6{....k
000001a0: 7e58 14a0 ed44 f1d3 a873 dee3 589b 1e26  ~X...D...s..X..&
000001b0: db78 5e49 bbc1 0940 cbb3 5c5c 201d 7a23  .x^I...@..\\ .z#
000001c0: e821 e953 c38d 98ea a49c fc01 5466 241f  .!.S........Tf$.
000001d0: 3a72 e52d 8351 afc1 982a af8c d2f4 01ae  :r.-.Q...*......
000001e0: 2611 2982 048f 308d 3f8c 4d21 b9b0 122d  &.)...0.?.M!...-
000001f0: 5661 3c9c c7d1 a074 8e42 992a 3d6f 10f2  Va<....t.B.*=o..
00000200: 2386 9456 3055 2853 1039 5606 1450 782b  #..V0U(S.9V..Px+
00000210: ca43 2fed b01d 5b10 5cd4 0c9f a115 10b0  .C/...[.\.......
00000220: d187 f73d c330 8411 3a76 857e 59b3 fccf  ...=.0..:v.~Y...
00000230: 528d b640 56da a5a7 5623 a28d a900 9f5f  R..@V...V#....._
00000240: f116 55bd 7cb4 6112 a32c 2a57 f145 c107  ..U.|.a..,*W.E..
00000250: fc5d c914 e142 40ac 9730 78f7 9850 1142  .]...B@..0x..P.B
00000260: 0200 00                                  ...
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ 
```

Actual File Content
Now Lets Know About the Hex dump 
```bash
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ xxd -r data.txt  data.bin
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data.bin 
data.bin: gzip compressed data, was "data2.bin", last modified: Mon Jul 28 19:03:32 2025, max compression, from Unix, original size modulo 2^32 578
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ 
```
And Got Know About that file actual name is data2.bin which is compressed by `gunzip`
So lets Work on Further
```bash
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ mv data.bin data2.gz
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ gunzip data2.gz 
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data2  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data2
data2: bzip2 compressed data, block size = 900k
```
After Further Unzip the file we got to know that the file further more compressed by 
`bzip2`
So lets unzip by it
```bash
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data2
data2: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ mv data2 data2.bz
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ mv data2.bz data2.bz2
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ bunzip2 data2.bz2 
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data2  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data2 
data2: gzip compressed data, was "data4.bin", last modified: Mon Jul 28 19:03:32 2025, max compression, from Unix, original size modulo 2^32 20480
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ 
```
And Again we get that Its Further more compressed so lets decompressed it
```bash
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ mv data2 data4.gz
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ gunzip data4.gz 
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data4  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data4
data5.bin0000644000000000000000000002400015041744604011245 0ustar  rootrootdata6.bin0000644000000000000000000000SS��ڍ2���#�4604011253 0ustar  rootrootBZh91AY&SY��A����j@�}�� [#�ta���@f �
�@�Luゥ    ������i��@�2
      H�▒�$*V���������l:r�R��W��6�����F�g�b]�f&�U��L�������>���F;3#���"=��<����ϪW
�h@�:#��U$4\�JR��ue�rE8P���A�bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data4 
data4: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ 
```
Its Partially Decompressed but further need to decompressed using tar
```bash
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ tar xf data4
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data4  data5.bin  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data5.bin 
SS��ڍ2���#�00644000000000000000000000033515041744604011253 0ustar  rootrootBZh91AY&SY��A����j@�}�� [#�ta���@f �
�@�Luゥ    ������i��@�2
      H�▒�$*V���������l:r�R��W��6�����F�g�b]�f&�U��L�������>���F;3#���"=��<����ϪW
�h@�:#��U$4\�JR��ue�rE8P���A�bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data5.bin 
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ tar xf data5.bin
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data4  data5.bin  data6.bin  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data6.bin 
SS��ڍ2���#��A����j@�}�� [#�ta���@f �
�@�Luゥ    ������i��@�2
      H�▒�$*V���������l:r�R��W��6�����F�g�b]�f&�U��L�������>���F;3#���"=��<����ϪW
�h@�:#��U$4\�JR��ue�rE8P���A�[?2004hbandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data6.bin 
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ mv data6.bin data6.bz2
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ bunzip2 data6.bz2 
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data4  data5.bin  data6  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data6 
data8.bin0000644000000000000000000000011715041744604011253 0ustar  rootroo�ɇhdata9.bin                                                               �HU(H,..�/JQ�,Vp�7M)w+N6HN�.6*KȰ�2q)w��>�2A1bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data6 
data6: POSIX tar archive (GNU)
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ tar xf data6
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data4  data5.bin  data6  data8.bin  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data8.bin 
�ɇhdata9.bin
�.6*K   q)w��>�2A1bandit12@bandit:/tmp/tmp.PFcyGRILlY$ file data8.bin 
data8.bin: gzip compressed data, was "data9.bin", last modified: Mon Jul 28 19:03:32 2025, max compression, from Unix, original size modulo 2^32 49
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ mv data8.bin data8.gz
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ gunzip data8.gz 
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ ls
data4  data5.bin  data6  data8  data.txt
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ cat data8 
The password is FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
bandit12@bandit:/tmp/tmp.PFcyGRILlY$ 
```
by decompressing more and more I got the actual Password
## Password 
```Password
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn
```
