## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
We got our hands on a confidential case file from some self-declared "black hat hackers"... it looks like they have a secret invite code available within a QR code, but it's covered by some image in this PDF! If we want to thwart whatever it is they are planning, we need your help to uncover what that QR code says!

Access this challenge by deploying the machine attached to this task by pressing the green "Start Machine" button. This machine shows in Split View in your browser, if it doesn't automatically display you may need to click "Show Split View" in the top right.  
  
The file you need is located in /home/ubuntu/confidential on the VM.
```
Then we start looking for the file in the system 
```bash
ubuntu@thm-confidential:~/confidential$ ls
Repdf.pdf
ubuntu@thm-confidential:~/confidential$ pdfimages -png Repdf.pdf output
ubuntu@thm-confidential:~/confidential$ ls
Repdf.pdf  output-000.png  output-001.png  output-002.png
```
#pdfimages
after that we got output--000.png in which QR Code is present and after copy and paste on google we got the flag
```Flag
[flag omitted]
```

