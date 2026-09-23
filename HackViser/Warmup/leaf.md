## Description
```Description
The Server-Side Template Injection (SSTI) vulnerability occurs when a web application does not sufficiently check user data in the template engine. This allows attackers to manipulate the template engine to execute unwanted commands on the server.  
  
Recommended for practicing discovering and exploiting the SSTI vulnerability and taking over the server with bind shell.
```

## IP-Address
```IP_Address
172.20.22.43
```
Lets start with network scan

### RustScan
```bash
$rustscan -a 172.20.22.43 -b 450 -t 2000 --ulimit 5000 -- -e tun0 -Pn -A 
PORT      STATE SERVICE REASON         VERSION
80/tcp    open  http    syn-ack ttl 63 Apache httpd 2.4.56 ((Debian))
|_http-title: Modish Tech
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.56 (Debian)
3306/tcp  open  mysql   syn-ack ttl 63 MySQL (unauthorized)
33060/tcp open  mysqlx  syn-ack ttl 63 MySQL X protocol listener
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
```

## Lets Explore On Browser
Navigate to http://172.20.22.43
Got to see a e commerce website 
After visiting the website i notice that we can inject into the comment section of the product so i first tried 
```
{{7*7}}
and i got 49
which conclude that our input work on backend and its consider as command so i tried multiple times on different - dfferent product but conclusion same so i start looking for others and i focus on url 
every product comes with the id and i see there is no product with id=2 so i tried this on url 
```

I navigate to http://172.20.22.43/product.php?id=2 and comment there then i redirect to 
http://172.20.22.43/comment.php
and got the information about the database and other
```Details
Database error: SQLSTATE[23000]: Integrity constraint violation: 1452 Cannot add or update a child row: a foreign key constraint fails (`modish_tech`.`comments`, CONSTRAINT `comments_ibfk_1` FOREIGN KEY (`product_id`) REFERENCES `products` (`id`))
```

Which is 
```IN_Details
- Database Name: `modish_tech`
- Table: `comments`
- Related Table: `products`
- Foreign key constraint: `comments.product_id` → `products.id
```

# END
