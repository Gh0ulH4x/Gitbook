## Description
_Flags have been omitted from this writeup per platform guidelines._

```Description
Will you find your MD5 match?
My Dearest Hacker,

Matchmaker is a playful, hash-powered experience that pairs you with your ideal dog by comparing MD5 fingerprints. Upload a photo, let the hash chemistry do its thing, and watch the site reveal whether your vibe already matches one of our curated pups. The algorithm is completely transparent, making every match feel like a wink from fate instead of random swipes.

Come get your dog today!

You can access the web app here: `http://10.49.142.154`
```
## IP-Address
```IP-Address
http://10.49.142.154
```
## Web
- We have to upload file on the website with the same `md5` hash in order to gain the flag
- There is a Dog jpg file which is present on website for reference 
- For that we use `github` md5collgen
```bash
git clone https://github.com/zhijieshi/md5collgen.git
cd md5collgen && make
then run command
./md5collgen -p <original-File> -o collision1.jpt collision2.jpg
```
- By upload both files one by one on the website we are able to trigger the situation
```Flag
[flag omitted]
```
# END