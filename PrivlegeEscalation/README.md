# Why Privlege Escalation?

Admin or root credentials can be hard to come by, but users with lower privleges might be an easier initial attack vector. Typical access to a systems will put you in as a low privlege users, but there are techniques available to you so you can have complete control over the system. On a linux machine you're looking to get 'root' privleges, and on windows it 'administartor/SYSTEM'


# PrivEsc Check List

Once you have access and enumerate the the box, you have to experiment diffrent techniques to get more privleges. Some resources that provide a checklist for techniques are HackTricks https://book.hacktricks.xyz/  and the github repo Payload all the things https://github.com/swisskyrepo/PayloadsAllTheThings

# Enumeration Scripts

Manual checking of opportunities to escalted privleges on a system can be time consuming. Scripts to automatically check for linux excalation are 'LinEnum' and 'linuxprivchecker'. Scripts for windows are 'Seatbelt' and 'JAWS'. A useful tool to enumaerating servers is the 'Privilege Escalation Awesome Scripts SUITE (PEASS)'. It is well maintained and updated with new scripts to enumarate both Linux and Windows machine. 

Note: Autmating these processes create a lot of noise. Antivirus software or monitioring systems may be triggered with use of these scripts, so manual chekcing is still a viable avenue.


# Kernel exploits

The kernel is one of the first programs to run when a computer first turns on, after the boot loader. The kernel program has the level of prilvlge on a system and has complete control over all resources. It manages the interaction between hardware and the various programs running in the operating system. If you encounter a system with outdated operating system, you should first look for kernel exploits on said version using 'searchsploit' or good ole google. For example, the exploit 'DirtyCow' is listed as 'CVE-2016-5196' and can be dowloaded and deployed to a vulnerable system. Exploiting such low level software on a system can make it vary unstable, so its best to test it in a lab and see the consequences. 

# Vulnerable Software

Software used on a server may also be exploited to excalted privleges. The linux command 'dpkg -l' will show applicaitons and the 'C:\Program Files' path on Windows shows the applications on the server. Look for old versions of the software and check for exploits.

# User Privleges

Operating systems allow for multiple users that have different levels of access to resources. Checking the privleges of the user you have access to on a machine may allow you to avoid privlege escalation all together if you are logged in as a privleged user. 

The 'sudo' command on unix systems, short for "Super user do" allows users to execute commands or access root-only directoires without being the root user. You preface a root level command with sudo lke so: 'sudo tcpdump' and you will be promted to enter the root user password. You can check the sudo privleges with 'sudo -l'. This command lists the programs and directories and whether or not the current user needs a password.  The command 'sudo su {user}' switches users. When you find an applicable you have sudo privleges over without a password, you search in the website 'GTFOBins' for said application and how to exploit it to escalte your privlege level. 'LOLBAS' is a similar site compliing the programs you can to perform functions like executing commands or dowloading files in the context of a privleged user. 

# Exposed Credentials

Certain files in a system my house passwords or encrytion keys. Configuration files, log files and user history file (bash_history in linux and PSReadLine in windows) are common examples. Enumeration scripts mentioned above often check these files. You can also check for password reuse, so that maybe the sql password a user used also it used for sudo.

# SSH Keys

Keys stored in /home/user/.ssh/id_rsa or /root/.ssh/id_rsa allow users to ssh into systems without entering a password. If you have read prilevges to that file, you can copy it to you machine and ssh into the machine as the root user or whicheve user you found the key of 

``` bash
davidIngraham@htb[/htb]$ vim id_rsa #reading/viewing ssh key
davidIngraham@htb[/htb]$ chmod 600 id_rsa #changing permission of the file, ssh may not work unless there are restriced file settings of the id_rsa file
davidIngraham@htb[/htb]$ ssh root@10.10.10.10 -i id_rsa #sshing into the root user with the -i flag and passing in the id_rsa

root@10.10.10.10# now you are root user
```

If you have write access to the /home/users/.ssh folder you can generate your own public key and place it in the authorized_keys file.  







