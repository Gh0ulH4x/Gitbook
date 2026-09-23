## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```bash
One of our developers accidentally committed some sensitive code to our GitHub repository. Well, at least, that is what they told us...
Oh no, not again! One of our developers accidentally committed some sensitive code to our GitHub repository. Well, at least, that is what they told us... the problem is, we don't remember what or where! Can you track down what we accidentally committed?
Access this challenge by deploying the machine attached to this task by pressing the green "Start Machine" button. You will need to use the in-browser view to complete this room. Don't see anything? Press the "Show Split Screen" button at the top of the page.
The files you need are located in **/home/ubuntu/commited** on the VM attached to this task.  
Check out similar content on TryHackMe:
[Git Happens](https://tryhackme.com/room/githappens)
[Git and Crumpets](https://tryhackme.com/room/gitandcrumpets)****
```

## IP-Address
```IP-Address
10.49.133.165
```
## Github Repo
```bash
ubuntu@thm-comitted:~$ ls
Desktop  Documents  Downloads  Pictures  commited
ubuntu@thm-comitted:~$ cd commited/
ubuntu@thm-comitted:~/commited$ ls
commited.zip
ubuntu@thm-comitted:~/commited$ unzip commited.zip 
Archive:  commited.zip
   creating: commited/
   creating: commited/.git/
   creating: commited/.git/logs/
   creating: commited/.git/logs/refs/
   creating: commited/.git/logs/refs/heads/
  inflating: commited/.git/logs/refs/heads/dbint  
  inflating: commited/.git/logs/refs/heads/master  
  inflating: commited/.git/logs/HEAD  
   creating: commited/.git/refs/
   creating: commited/.git/refs/tags/
   creating: commited/.git/refs/heads/
 extracting: commited/.git/refs/heads/dbint  
 extracting: commited/.git/refs/heads/master  
   creating: commited/.git/info/
  inflating: commited/.git/info/exclude  
 extracting: commited/.git/COMMIT_EDITMSG  
   creating: commited/.git/objects/
   creating: commited/.git/objects/da/
 extracting: commited/.git/objects/da/b5a1b99756122e83df66ea1a86d81257b2ec47  
   creating: commited/.git/objects/dc/
 extracting: commited/.git/objects/dc/1ca4ca1d54e7a4ac6757c9b98bd1be0a8ed2f0  
   creating: commited/.git/objects/26/
 extracting: commited/.git/objects/26/bcf1aa99094bf2fb4c9685b528a55838698fbe  
   creating: commited/.git/objects/69/
 extracting: commited/.git/objects/69/d6211898e43bfe15ab5a4cad1690b9be1115f8  
   creating: commited/.git/objects/74/
 extracting: commited/.git/objects/74/2b40ee5d0597b0595f60998305605186ab29db  
   creating: commited/.git/objects/9e/
 extracting: commited/.git/objects/9e/cdc566de145f5c13da74673fa3432773692502  
   creating: commited/.git/objects/info/
   creating: commited/.git/objects/3a/
 extracting: commited/.git/objects/3a/8cc16f919b8ac43651d68dceacbb28ebb9b625  
   creating: commited/.git/objects/b0/
 extracting: commited/.git/objects/b0/eda7db60a1cb0aea86f053816a1bfb7e2d6c67  
   creating: commited/.git/objects/94/
 extracting: commited/.git/objects/94/a7ea670b13f698012abd246ab08b76d95643c8  
   creating: commited/.git/objects/28/
 extracting: commited/.git/objects/28/c36211be8187d4be04530e340206b856198a84  
   creating: commited/.git/objects/c7/
 extracting: commited/.git/objects/c7/18c75179d46ba5a1d21dc351a39c0dfb257d3d  
   creating: commited/.git/objects/08/
 extracting: commited/.git/objects/08/178a40f4b3585566b539985399f51bbcc7ae22  
   creating: commited/.git/objects/6e/
 extracting: commited/.git/objects/6e/1ea88319ae84175bfe953b7791ec695e1ca004  
   creating: commited/.git/objects/0b/
 extracting: commited/.git/objects/0b/8b1d537ea651d504d29c1556d7dcbcf76a5d57  
   creating: commited/.git/objects/pack/
   creating: commited/.git/objects/40/
 extracting: commited/.git/objects/40/754840a68f85ad8d963f1556a3f24b51cef4fa  
   creating: commited/.git/objects/16/
 extracting: commited/.git/objects/16/1979c948240de867b5c0a4079d7e2c7f6d4e04  
   creating: commited/.git/objects/c5/
 extracting: commited/.git/objects/c5/6c470a2a9dfb5cfbd54cd614a9fdb1644412b5  
   creating: commited/.git/objects/38/
 extracting: commited/.git/objects/38/0dd9b32cc8429638c09cc857cc0ef2ff8f8e50  
   creating: commited/.git/objects/df/
 extracting: commited/.git/objects/df/e24c9e9ae78d8339e44d0c9e32dde9b9efe148  
   creating: commited/.git/objects/45/
 extracting: commited/.git/objects/45/b137061d385e2f5c05cccc7fd13873f2ce18b1  
   creating: commited/.git/objects/0e/
 extracting: commited/.git/objects/0e/1d395f33767d795f3ff66ceac6c792629d40d2  
   creating: commited/.git/objects/b3/
 extracting: commited/.git/objects/b3/7056e8583abc13547fe146c7ee9d905ac8488c  
   creating: commited/.git/objects/44/
 extracting: commited/.git/objects/44/f3cb396ce178127b2dca6fa903113152710129  
 extracting: commited/.git/objects/44/7ef7f1f03534fbea17f61ef3c2e610fcf23693  
 extracting: commited/.git/objects/44/1daaaa600aef8021f273c8c66404d5283ed83e  
   creating: commited/.git/objects/54/
 extracting: commited/.git/objects/54/d0271a615735240d22dcd737b4bf26cbe9d43f  
   creating: commited/.git/objects/4e/
 extracting: commited/.git/objects/4e/ca752261f327712539fc04f81e7f335a69b429  
 extracting: commited/.git/objects/4e/16af9349ed8eaa4a29decd82a7f1f9886a32db  
   creating: commited/.git/objects/fd/
 extracting: commited/.git/objects/fd/132b91ad2a752dd39ce22bc1c55c0e5c38ab84  
  inflating: commited/.git/index     
   creating: commited/.git/hooks/
  inflating: commited/.git/hooks/fsmonitor-watchman.sample  
  inflating: commited/.git/hooks/pre-push.sample  
  inflating: commited/.git/hooks/pre-merge-commit.sample  
  inflating: commited/.git/hooks/pre-rebase.sample  
  inflating: commited/.git/hooks/post-update.sample  
  inflating: commited/.git/hooks/prepare-commit-msg.sample  
  inflating: commited/.git/hooks/update.sample  
  inflating: commited/.git/hooks/commit-msg.sample  
  inflating: commited/.git/hooks/applypatch-msg.sample  
  inflating: commited/.git/hooks/pre-applypatch.sample  
  inflating: commited/.git/hooks/pre-commit.sample  
  inflating: commited/.git/hooks/pre-receive.sample  
  inflating: commited/.git/config    
 extracting: commited/.git/HEAD      
   creating: commited/.git/branches/
  inflating: commited/.git/description  
  inflating: commited/main.py        
  inflating: commited/Readme.md      
```

## Github Log
```bash
ubuntu@thm-comitted:~/commited/commited$ git log --oneline --decorate --all
28c3621 (HEAD -> master) Finished
4e16af9 (dbint) Reminder Added.
c56c470 Oops
3a8cc16 DB check
6e1ea88 Note added
9ecdc56 Database management features added.
26bcf1a Create database logic added
b0eda7d Connecting to db logic added
441daaa Initial Project.

ubuntu@thm-comitted:~/commited/commited$ git show c56c470
commit c56c470a2a9dfb5cfbd54cd614a9fdb1644412b5
Author: fumenoid <fumenoid@gmail.com>
Date:   Sun Feb 13 00:46:39 2022 -0800

    Oops

diff --git a/main.py b/main.py
index 54d0271..0e1d395 100644
--- a/main.py
+++ b/main.py
@@ -4,7 +4,7 @@ def create_db():
     mydb = mysql.connector.connect(
     host="localhost",
     user="root", # Username Goes Here
-    password="[password omitted]" # Password Goes Here
+    password="" # Password Goes Here
     )
 
     mycursor = mydb.cursor()
@@ -16,7 +16,7 @@ def create_tables():
     mydb = mysql.connector.connect(
     host="localhost",
     user="root", #username Goes here
:
```

## Flag
```Flag
[flag omitted]
```

# END